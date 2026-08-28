---
title: Frequently Asked Questions
layout: single
permalink: /faq/faq/
# toc: true
toc_sticky: true
toc_label: "FAQ Contents"
header:
  overlay_color: "#800000"
  overlay_filter: "0.5"
---

This document contains some frequently asked questions about the cluster and its abilities. This document does not contain support information.

## Questions
<!-- TOC is auto-generated -->

<!-- toc -->
- [Questions](#questions)
  - [What are the requirements for getting access to this cluster?](#what-are-the-requirements-for-getting-access-to-this-cluster)
  - [I want to install additional software.](#i-want-to-install-additional-software)
  - [Is there a way to use the cluster via a GUI?](#is-there-a-way-to-use-the-cluster-via-a-gui)
  - [Can I use a containerized workflow, such as Docker?](#can-i-use-a-containerized-workflow-such-as-docker)
  - [What is the three-tier QoS system?](#what-is-the-three-tier-qos-system)
  - [The current time limit on jobs is too low for what I want to do. Can I increase the job time limit?](#the-current-time-limit-on-jobs-is-too-low-for-what-i-want-to-do-can-i-increase-the-job-time-limit)
  - [How many concurrent jobs can I run?](#how-many-concurrent-jobs-can-i-run)
  - [I feel like the cluster isn't working for me. Can we set up any additional configurations to allow for my specific job?](#i-feel-like-the-cluster-isnt-working-for-me-can-we-set-up-any-additional-configurations-to-allow-for-my-specific-job)
  - [There are processes not owned by me running on the GPU that I reserved. Is this expected?](#there-are-processes-not-owned-by-me-running-on-the-gpu-that-i-reserved-is-this-expected)
  - [How do I check how much space I'm using in either a project or home directory?](#how-do-i-check-how-much-space-im-using-in-either-a-project-or-home-directory)
  - [Can DSI Techstaff create and maintain a shared data resource (e.g., joint hosting of LLMs)?](#can-dsi-techstaff-create-and-maintain-a-shared-data-resource-eg-joint-hosting-of-llms)
  - [How to Get Additional Support?](#how-to-get-additional-support)

<!-- tocstop -->

### What are the requirements for getting access to this cluster?

For information on how to get access, visit the [Getting Access](/quickstart/accounts/) page.


### I want to install additional software.

Please use MiniConda or MicroMamba to create a custom software environment tailored to your needs.

### Is there a way to use the cluster via a GUI?

You may be able to set up X11 forwarding for using software that requires a GUI. This is not the intended use of the cluster and while users are allowed to access X11 forwarding, it is not supported behavior. You can find a brief "How-to" [here](https://github.com/dsi-rse/the-clinic/blob/main/tutorials/X11.md).

### Can I use a containerized workflow, such as Docker?

Docker is not available on the cluster because it requires root-equivalent privileges, which would compromise system security. Other container technologies, such as Apptainer (Singularity) and Podman, may be usable as rootless alternatives, but they are neither tested nor formally supported.


### What is the three-tier QoS system?

The cluster uses a three-tier QoS system (rolled out May 7, 2026) in place of the previous flat 8-concurrent-job limit:

- **`general`** — the default tier. Up to 24 concurrent jobs per user, 200 submitted, 12-hour wall time. Jobs are **preemptable** (with a 5-minute warning and automatic requeue) so that `interactive` sessions can start quickly.
- **`protected`** — non-preemptable, guaranteed to complete. Limited to 1 job at a time, 2-hour max, 4x fairshare cost. Good for learners and short critical jobs.
- **`interactive`** — highest priority, preempts `general` jobs so you get on a GPU fast. Limited to 1 session at a time, 4-hour max, 8x fairshare cost. For active development only.

Jobs submitted without a `--qos=` flag run on `general`. The wall-time limit is 12 hours, and `general` jobs are **preemptable**: when an `interactive` session needs your GPU, your job receives `SIGUSR1` 5 minutes before being killed and is automatically requeued when a GPU frees up. To avoid losing progress on long runs, implement the [checkpoint contract]({{ '/using-the-cluster/batch-jobs/#the-checkpoint-contract' | relative_url }}).

For the full specification see the [Job Scheduling Policy]({{ '/policies/scheduling/' | relative_url }}). For practical examples see the [batch jobs guide]({{ '/using-the-cluster/batch-jobs/' | relative_url }}) and the [interactive sessions guide]({{ '/using-the-cluster/interactive-sessions/' | relative_url }}).

### The current time limit on jobs is too low for what I want to do. Can I increase the job time limit?

`general`-tier jobs have a 12-hour wall time. When an `interactive` job needs your resources (or you hit the wall-time limit) your job receives a `SIGUSR1` signal 5 minutes before being killed, and is **automatically requeued** when GPUs free up. Checkpointing is the right pattern for long-running work, but the requeue is handled for you — you do not need to resubmit. See [Handling Preemption]({{ '/using-the-cluster/batch-jobs/#handling-preemption' | relative_url }}) for the checkpoint contract.

If you need a short guaranteed (non-preemptable) run, use `--qos=protected` (2-hour max, 1 job at a time).

### How many concurrent jobs can I run?

The `general` tier allows up to **24 concurrent jobs** per user and 200 submitted. `protected` and `interactive` each allow 1 at a time and will reject additional submissions immediately. If you have many small tasks, use a job array with a concurrency throttle — for example, `sbatch --array=0-99%20 ...` runs a 100-task array with at most 20 tasks running at once. See the [batch jobs guide]({{ '/using-the-cluster/batch-jobs/#job-arrays-for-hyperparameter-sweeps' | relative_url }}).

### I feel like the cluster isn't working for me. Can we set up any additional configurations to allow for my specific job?

The cluster is designed to efficiently process the most common use cases. We constantly monitor cluster performance and think through ways to increase _overall_ performance. Given our limited resources we do not have the bandwidth to accommodate, implement and monitor additional configurations. If you have a specific use case that is not being efficiently handled by the current configuration there are two options:

  1. Use an alternative compute resources (such as [AWS](https://aws.amazon.com/), [GCP](https://cloud.google.com/) or [UChicago's RCC](https://rcc.uchicago.edu/)).
  2. Purchase specific compute within the guidelines listed [here]({{ '/policies/purchasing/' | relative_url }}). For allocated resources we are able to implement alternative QOS policies.


### There are processes not owned by me running on the GPU that I reserved. Is this expected? 

Yes. In SLURM, this is called OverSubscribe. If a user is not utilizing a resource (GPU, CPU, or MEM) completely, then SLURM will assign jobs to the portion of your allocation that is unused.

### How do I check how much space I'm using in either a project or home directory?

Users are allocated storage quotas on home directories (50GB), project directories (default 500GB), and scratch space (50GB per location). To check your current usage, use the `dsiquota` command. See the detailed instructions in the [DSI Quota Tool]({{ '/using-the-cluster/checking-usage/' | relative_url }}) documentation.

For complete information on storage quotas, limits, and how to request additional space, see the [Storage Allocation Policy]({{ '/policies/general/#storage-allocation-policy' | relative_url }}).

### Can DSI Techstaff create and maintain a shared data resource (e.g., joint hosting of LLMs)?

No. For several reasons, DSI Techstaff cannot curate and maintain a shared dataset of this kind:

* It requires ongoing curation: deciding which assets are included, handling updates, pruning unused data.
* The cluster prioritizes data used in active research; it is not a long-term archival or public dataset host.
* If multiple teams need a common dataset, they should designate a maintainer who will both prune unused data and keep the dataset aligned with active research, and then request a project folder with appropriate permissions.
* Our current storage infrastructure uses NFS/ZFS, and simultaneous reads of the same files by multiple users can cause slowdowns.

For details, see the policy guidance: [Cluster Storage – FAQ on shared data resources]({{ '/resources/cluster-storage/#can-dsi-techstaff-put-together-a-shared-data-resource-such-as-joint-hosting-of-a-number-of-llms' | relative_url }}).

### How to Get Additional Support?

If you need help beyond the FAQs or documentation, feel free to reach out to the DSI support team. Visit our [Contact Us]({{ '/contact/' | relative_url }}) page for assistance.