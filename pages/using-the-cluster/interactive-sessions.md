---
title: "Interactive Sessions (JupyterLab)"
layout: single
nav_order: 2
parent: Using the Cluster
category: using-the-cluster
permalink: /using-the-cluster/interactive-sessions/
classes: [wide, left-aligned]
hide_hero: True
---

# Interactive Sessions (JupyterLab)

In addition to batch processing, the cluster also supports **interactive sessions**. These allow you to work directly on a compute node, which is essential for tasks like:

- Developing and debugging code
- Running Jupyter notebooks
- Performing tasks that require real-time feedback
- Testing configurations before submitting batch jobs

## Starting an Interactive Session

Use the `interactive` QoS tier for development work. This tier has the highest scheduling priority and will preempt lower-priority jobs to get you on a node quickly.

### Two-Step Method (salloc + srun)

```bash
# Step 1: Allocate resources
salloc --partition=general --qos=interactive --gres=gpu:1 --time=04:00:00

# Step 2: Start a shell on the allocated node
srun --pty bash -l
```

### One-Liner (srun)

```bash
srun --partition=general --qos=interactive --gres=gpu:1 --time=02:00:00 --pty bash -l
```

### Requesting Specific Hardware

```bash
# Request a specific GPU type
salloc --partition=general --qos=interactive --gres=gpu:1 --time=04:00:00 --constraint=h200

# Request multiple GPUs (interactive sessions are capped at 2)
salloc --partition=general --qos=interactive --gres=gpu:2 --time=04:00:00

# Request more memory
salloc --partition=general --qos=interactive --gres=gpu:1 --time=04:00:00 --mem=64G
```

### Using Your Lab Partition

If you have access to a lab partition, you can use the interactive QoS there as well:

```bash
salloc --partition=veitch --qos=interactive --gres=gpu:1 --time=04:00:00
```

## Interactive Tier Limits

The interactive tier is intentionally constrained to keep it responsive for everyone:

| Limit | Value | Reason |
|-------|-------|--------|
| Max sessions per user | **1** | Prevents agent-driven resubmission loops |
| Max wall time | **4 hours** | Long enough for real dev work, short enough to prevent hoarding |
| Max resources | **2 GPUs, 32 CPUs, 256 GB RAM** per user at a time | Keeps interactive capacity available for everyone |
| Fairshare cost | **8x** | Premium pricing discourages using interactive for batch work |

Interactive sessions are capped at **2 GPUs, 32 CPUs, and 256 GB of RAM per user** at a time. Requests above the cap are rejected at submit. The 8x fairshare cost applies to the entire allocation — a 4-hour session with 2 GPUs costs the same fairshare as 64 GPU-hours on the general tier — so request only what you need. If you need more than 2 GPUs (e.g., testing sharding across a full node), submit to `general` or `protected` instead.

If you need longer wall time or multiple concurrent jobs, use the `general` tier. If you need a shorter guaranteed (non-preemptable) job, use `protected`.

**DenyOnLimit:** If you try to submit a second interactive session while one is already running, the request will be **immediately rejected** with a clear error message. It will not queue silently.

## Best Practices

- **Don't rely on SSH disconnect alone.** A dropped SSH connection does not always release `salloc` cleanly. If you have to disconnect ungracefully, follow up with `scancel <jobid>` from a login node to confirm the allocation is freed.
- **Test preemption-handling code on `general` first.** Before relying on a wrapper script overnight, simulate preemption with `scancel --signal=USR1 --batch <jobid>` against a short `general` job and confirm a checkpoint appears and the job exits with code 99. See [Handling Preemption]({{ '/using-the-cluster/batch-jobs/#handling-preemption' | relative_url }}) in the batch jobs guide.

## Running JupyterLab

To run JupyterLab on a compute node:

### Step 1: Start an interactive session

```bash
salloc --partition=general --qos=interactive --gres=gpu:1 --time=04:00:00
```

### Step 2: Note the allocated node name

```bash
echo $SLURM_NODELIST
# Output: e.g., g003
```

### Step 3: Start JupyterLab on the compute node

```bash
srun jupyter lab --no-browser --port=8888 --ip=0.0.0.0
```

### Step 4: Create an SSH tunnel from your local machine

Open a new terminal on your local machine:

```bash
ssh -N -L 8888:g003:8888 <your-cnetid>@login.ds.uchicago.edu
```

Replace `g003` with the actual node name from Step 2.

### Step 5: Open JupyterLab

Open your browser and navigate to `http://localhost:8888`. Use the token displayed in the terminal output from Step 3.

## When to Use Interactive vs. Batch

| Scenario | Use |
|----------|-----|
| Writing and debugging code | `interactive` |
| Running a Jupyter notebook | `interactive` |
| Testing a new configuration | `interactive` |
| Training a model | `general` (batch) or `protected` (if short and must not be interrupted) |
| Hyperparameter sweep | `general` (batch, with job array) |
| Overnight experiment | `general` (batch) |
| Learning / first experiments | `protected` (guaranteed completion, no preemption) |

**Rule of thumb:** If you're actively sitting at a terminal and need immediate feedback, use `interactive`. If you can submit it and walk away, use `general`. If you're learning the cluster or need a short job guaranteed to finish, use `protected`.

See the [Submitting Batch Jobs guide]({{ '/using-the-cluster/batch-jobs/' | relative_url }}) for batch-job examples and preemption handling.

## Ending Your Session

When you're done, simply type `exit` to release the resources:

```bash
exit
```

This frees the GPUs and CPUs for other users. Please do not leave interactive sessions running idle — the 8x fairshare cost accrues the entire time, and the resources are unavailable to others.
