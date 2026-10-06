---
title: "Job Scheduling & Priority Policy"
layout: single
nav_order: 3
parent: Policies
category: policies
permalink: /policies/scheduling/
classes: [wide, left-aligned]
hide_hero: True
---

## Overview

The DSI Cluster uses a three-tier Quality of Service (QoS) system to balance competing demands for compute resources. This system ensures that interactive development work is always accessible while maximizing hardware utilization for batch computation.

Every job submitted to the cluster runs under one of three QoS tiers. Each tier offers a different tradeoff between scheduling speed, preemption protection, and fairshare cost.

For practical how-to examples, see the [Submitting Batch Jobs guide]({{ '/using-the-cluster/batch-jobs/' | relative_url }}) and the [Interactive Sessions guide]({{ '/using-the-cluster/interactive-sessions/' | relative_url }}).

## QoS Tiers

### `general` — Default Tier

All jobs use the `general` tier unless you explicitly request otherwise. This is the cheapest tier in terms of fairshare cost, but jobs are **preemptable** — they can be interrupted and requeued if a higher-priority job needs the resources.

| Property | Value |
|----------|-------|
| Fairshare cost | 1x (cheapest) |
| Preemptable by | `interactive` jobs |
| Max concurrent jobs | 24 per user |
| Max submitted jobs | 200 per user |
| Max wall time | Partition default (12h on general partition) |
| Grace period before preemption | 5 minutes |

**Use this for:** Hyperparameter sweeps, training runs, batch experiments, anything that can tolerate interruption.

**Preemption protection:** Jobs are guaranteed at least 5 minutes of runtime before becoming eligible for preemption. When preempted, you receive a 5-minute warning signal before the job is killed and automatically requeued. See [Handling Preemption](#handling-preemption) below.

### `protected` — Non-Preemptable Tier

The `protected` tier guarantees that your job will run to completion without interruption. It is designed for users who are learning the cluster or running work where preemption would be disruptive. It costs **4x** the fairshare of the `general` tier and is limited to one job at a time with a shorter wall time.

| Property | Value |
|----------|-------|
| Fairshare cost | 4x |
| Can preempt | Nothing |
| Preemptable by | Nothing |
| Max concurrent jobs | **1 per user** |
| Max submitted jobs | **1 per user** |
| Max wall time | **2 hours** |

**Use this for:** Learning and experimentation when you don't want to deal with preemption, short training runs that must complete without interruption, or any job where implementing checkpointing is impractical.

**Not for:** Long-running batch work or sweeps. The 1-job limit and 2-hour wall time are enforced — requests that exceed these limits are rejected immediately. For bulk work, use `general` (cheaper, more jobs allowed). For active development, use `interactive` (longer wall time, preempts general jobs).

### `interactive` — Development Tier

The `interactive` tier is designed for active, hands-on development: debugging code, running notebooks, testing configurations. It preempts `general` jobs and schedules with the highest priority. It is **tightly limited** to prevent misuse.

| Property | Value |
|----------|-------|
| Fairshare cost | 8x |
| Can preempt | `general` jobs |
| Preemptable by | Nothing |
| Max concurrent jobs | **1 per user** |
| Max submitted jobs | **1 per user** |
| Max wall time | **4 hours** |
| Max resources | **2 GPUs, 32 CPUs, 256 GB RAM per user** at a time |

**Use this for:** Interactive development, debugging, Jupyter notebooks, any task where you need a human sitting at a terminal with real-time feedback.

**Not for:** Batch training, sweeps, or any unattended computation. The 1-session limit and 4-hour wall time are enforced — requests that exceed these limits are rejected immediately. Interactive allocations are capped at **2 GPUs, 32 CPUs, and 256 GB of RAM per user** at a time; requests above the cap are rejected at submit. Within the cap, the 8x fairshare cost still applies to the whole allocation, so request only what you need. If you need more than 2 GPUs (e.g., testing sharding strategies across a full node), use the `general` or `protected` tier.

## How to Specify a QoS Tier

Add `--qos=<tier>` to your job submission command:

```bash
# Batch job on general tier (default — you can omit --qos entirely)
sbatch --qos=general train.sh

# Non-preemptable job (guaranteed to complete, 2h max)
sbatch --qos=protected --time=02:00:00 train.sh

# Interactive development session
salloc --qos=interactive --partition=general --gres=gpu:1 --time=04:00:00
```

If you do not specify `--qos`, your job defaults to `general`.

## Partitions

The default partition is `dev` — jobs submitted without `--partition` run there. The `dev` partition runs on node `g009` (it previously ran on `g010`, which is now the [session node]({{ '/using-the-cluster/login-nodes/#session-node-g010' | relative_url }})).

Not every node is in every partition; lab partitions are described under [Lab Partitions](#lab-partitions) below.

## CPU-only Jobs on GPU Nodes

GPU nodes reserve **2 CPU cores per GPU** for GPU jobs. A CPU-only job (one that requests no GPUs) on a GPU node can use at most:

```
CPU-only max = (cores on the node) − 2 × (GPUs on the node)
```

For example, on a node with 64 cores and 8 GPUs, a CPU-only job can use at most 64 − 16 = 48 cores. This keeps CPU-only work from leaving GPUs stranded without the cores needed to drive them.

If a CPU-only job requests more cores than this on every node it could run on, Slurm rejects it at submit with:

```
sbatch: error: Batch job submission failed: Requested node configuration is not available
```

To fix it, lower `--cpus-per-task` / `--ntasks` (or split the work across multiple jobs or a job array). See [Common Errors]({{ '/faq/common-errors/' | relative_url }}).

## Preemption Policy

### What Is Preemption?

Preemption is the process by which a higher-priority job reclaims resources from a lower-priority job. When your job is preempted:

1. You receive a warning signal (SIGUSR1) indicating preemption is imminent.
2. You have a **5-minute grace period** to save your work (checkpoint).
3. After the grace period, your job is killed and **automatically requeued**.
4. When resources become available, your requeued job starts again from the beginning of the script.

### Preemption Hierarchy

```
interactive ──preempts──> general
```

Only `interactive` jobs can preempt, and only `general` jobs can be preempted. Specifically:

- `interactive` jobs can preempt `general` jobs.
- `general` jobs **cannot preempt anything — including other `general` jobs**. Two `general` jobs coexist normally on the cluster: one does not kick the other out, even if one has higher fairshare priority. If GPUs are fully allocated, new `general` jobs simply wait in the queue.
- `protected` jobs cannot be preempted and cannot preempt other jobs. They run to completion.

In other words, preemption is a one-way interaction between exactly two tiers (`interactive` → `general`). Every other pairing is queue-based only.

### Preemption Protections

- **Minimum runtime guarantee:** Jobs run for at least **5 minutes** before becoming eligible for preemption. A job that just started will not be immediately preempted.
- **Grace period:** When preemption occurs, you receive a **5-minute** warning before your job is killed. This is your window to save a checkpoint.
- **Automatic requeue:** Preempted jobs are automatically placed back in the queue and will restart when resources are available.
- **Opt out entirely:** If preemption is a concern, use the `protected` tier. Your job is guaranteed to run to completion (up to the 2-hour wall time).

### Handling Preemption

To handle preemption gracefully, your job should implement checkpoint/restart logic. The standard contract is:

1. Submit your job with `--signal=B:USR1@300` and `--requeue`
2. In your script, trap SIGUSR1 and save a checkpoint
3. Exit with code 99 after checkpointing
4. On startup, check for an existing checkpoint and resume from it

Jobs that do not implement checkpointing will simply restart from the beginning when requeued. This is fine for short jobs, but for longer training runs, implementing checkpointing is strongly recommended.

See the [Submitting Batch Jobs guide]({{ '/using-the-cluster/batch-jobs/#handling-preemption' | relative_url }}) for complete examples including a wrapper script and PyTorch checkpointing code.

## Priority & Fairshare

### How Priority Is Calculated

Your job's scheduling priority is determined by a weighted combination of factors:

| Factor | Weight | Description |
|--------|--------|-------------|
| **Fairshare** | Dominant | Your recent usage relative to your share. Less usage = higher priority. |
| **QoS** | Significant | Higher QoS tiers (`interactive` > `protected` > `general`) get a priority boost. |
| **Partition** | Moderate | Lab partitions provide a priority boost on dedicated hardware. |
| **Age** | Moderate | Jobs that have been waiting longer get a priority boost. |
| **Job size** | Minor | Smaller jobs get a slight priority boost. |

### How Fairshare Works

Fairshare ensures that no single user (or research group) can monopolize the cluster indefinitely. The system tracks your recent resource consumption and adjusts your scheduling priority accordingly:

- **New or light users** have high fairshare priority — their jobs schedule quickly.
- **Heavy users** see their fairshare priority decline — their new jobs queue behind lighter users.
- **Recovery is automatic:** Usage decays with a half-life of 2 days. After a few days of lighter usage, your fairshare priority recovers.

### The Fairshare Cost of Premium Tiers

| Tier | Fairshare cost | Equivalent general-tier GPU-hours per 1 hour |
|------|----------------|-----------------------------------------------|
| `general` | 1x | 1 |
| `protected` | 4x | 4 |
| `interactive` | 8x | 8 |

This is intentional: premium tiers provide better service (guaranteed completion or preemption rights), and the cost discourages using them as the default.

**Practical impact:** Occasional use of `interactive` for development or `protected` for a short guaranteed job barely affects your fairshare. The system rewards users who put most of their work in the `general` tier and reserve premium tiers for when they truly need them.

## Fair Usage Expectations

### Job Arrays for Sweeps

If you are running hyperparameter sweeps or parameter scans, use job arrays with a concurrency throttle:

```bash
sbatch --array=0-999%20 --qos=general sweep.sh
```

The `%20` limits your sweep to 20 concurrent tasks. This is more efficient for Slurm to manage than submitting 1000 individual jobs, and the throttle prevents you from consuming excessive resources.

### Choose the Right Tier

- **Most work belongs in `general`.** It's cheap, and preemption is handled gracefully if you implement checkpointing.
- **Use `protected` when you need guaranteed completion** and are running a short job (under 2 hours) where preemption would be disruptive. Good for learners and one-off experiments.
- **Use `interactive` only for active development.** The 1-session and 4-hour limits are enforced.

### Queue Flooding

The cluster enforces per-user limits on job submission and priority accrual to prevent any single user from overwhelming the scheduling system. These limits are generous enough for legitimate use but prevent extreme queue flooding.

## Lab Partitions

The QoS tier system applies across all partitions, including lab-specific partitions (veitch, clab, litian, ai+s, SSLab, complementary-ai, Monsoon, DDRI). Lab partitions provide an additional scheduling advantage on dedicated hardware through partition-level priority.

If you have access to a lab partition, you benefit from both your QoS tier priority and your partition priority. For example, a `general`-tier job on the veitch partition has higher partition priority than a `general`-tier job on the general partition, because the veitch partition has a higher partition priority weight.
