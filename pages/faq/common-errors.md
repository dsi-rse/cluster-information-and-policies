---
title: "Common Error Messages & Resolutions"
layout: single
nav_order: 2
parent: FAQ
category: faq
permalink: /faq/common-errors/
classes: [wide, left-aligned]
hide_hero: True
---

# Common Error Messages & Resolutions



This page lists common error messages you might encounter while using the cluster, along with their causes and solutions.

### Error: `sbatch: error: (QOSMaxJobsPerUserLimit)` or `(JobArrayLimit)`

**Cause:** You have submitted a job that would cause you to exceed the concurrent-job limit for your QoS tier. The limit depends on the tier — 24 on `general`, 1 on `protected`, 1 on `interactive`.

**Resolution:** Wait for some of your currently running jobs to finish. If you have many small, short tasks, use a job array with a concurrency throttle: `sbatch --array=1-100%10 ...` runs a 100-task array with only 10 tasks running at once. See the [batch jobs guide]({{ '/using-the-cluster/batch-jobs/#job-arrays-for-hyperparameter-sweeps' | relative_url }}).

---

### Error: `sbatch: error: (QOSMaxSubmitJobPerUserLimit)` on `--qos=protected` or `--qos=interactive`

**Cause:** The `protected` and `interactive` tiers are capped at **1 job/session per user**. When you submit a second job while the first is still running (or queued), Slurm rejects it immediately rather than queueing silently. This is the `DenyOnLimit` flag working as intended — not a bug.

**Resolution:**

- Wait for your existing `protected` or `interactive` job to finish before submitting another.
- Or submit to the `general` tier, which allows up to 24 concurrent jobs.
- If you forgot you already had a session running, `squeue -u $USER` will show it.

---

### Error: `sbatch: error: Batch job submission failed: Requested node configuration is not available`

**Cause:** Your job asks for a combination of resources that no node in the partition can provide. A common case is a **CPU-only job requesting too many cores**: GPU nodes reserve 2 cores per GPU for GPU jobs, so a CPU-only job on a GPU node can use at most `(node cores) − 2 × (node GPUs)`. A CPU-only request larger than that on every eligible node is rejected at submit.

**Resolution:**

- Lower `--cpus-per-task` / `--ntasks` so the request fits within the CPU-only max.
- Split the work into smaller jobs or a job array.
- Double-check `--mem`, `--gres`, and `--constraint` — an impossible combination of any of these produces the same error.

See [CPU-only Jobs on GPU Nodes]({{ '/policies/scheduling/#cpu-only-jobs-on-gpu-nodes' | relative_url }}).

---

### Error: `slurmstepd: error: *** JOB ... CANCELLED AT ... DUE TO TIME LIMIT ***`

**Cause:** Your job ran for longer than the maximum allowed time for its QoS tier or partition. Behavior depends on the QoS tier:

- `general` (12h default): Slurm sends `SIGUSR1` 5 minutes before the wall-time limit (if you submitted with `--signal=B:USR1@300 --requeue`) and the job is automatically requeued on exit code 99. See [Handling Preemption]({{ '/using-the-cluster/batch-jobs/#handling-preemption' | relative_url }}).
- `protected` (2h cap): hard cancellation at the wall-time limit, not requeued.
- `interactive` (4h cap): hard cancellation at the wall-time limit, not requeued.

**Resolution:** For long-running work, implement **checkpointing** using the wrapper pattern in the [batch jobs guide]({{ '/using-the-cluster/batch-jobs/#wrapper-script' | relative_url }}). A checkpointed job resumes from where it left off each time it is requeued.

---

### Job was preempted but did not requeue (stuck in `PREEMPTED` state)

**Cause:** `general`-tier jobs can be preempted by `interactive` jobs. A preempted job will only requeue automatically if both of these are true:

- The job was submitted with `#SBATCH --requeue`
- The job exits with code 99 (the convention for "requeue me")

Common failure modes:

- `--requeue` was not set on the submission — the job is killed and stays in `PREEMPTED`.
- The signal handler is missing, so the script exits with a non-99 code (typically 143 on `SIGTERM` or 130 on `SIGINT`) and Slurm does not requeue it.
- The training process did not catch `SIGUSR1` at all, so the grace-period `SIGKILL` leaves the wrapper with no chance to exit cleanly.

**Resolution:** Use the wrapper pattern from the [batch jobs guide]({{ '/using-the-cluster/batch-jobs/#wrapper-script' | relative_url }}). The two minimum SBATCH flags are:

```bash
#SBATCH --signal=B:USR1@300
#SBATCH --requeue
```

And your signal handler should end with `exit 99` once the checkpoint has been written.

---

### Error: `Disk quota exceeded`

**Cause:** You have used up all of your allocated storage space in your home (`/home/user`) or scratch (`/scratch/user`) directory.

**Resolution:** You need to free up space by deleting unnecessary files.

1.  Use the `dsiquota` command to check your current usage and see which filesystem is full. See the DSI Quota Tool documentation for more details.
2.  Navigate to the directory and use commands like `du -sh * | sort -h` to find large files and directories that can be removed.

If you require more storage for a project, please see the policy on purchasing resources.

---

### Error: `sbatch: error: Batch script contains DOS line endings`

**Cause:** You edited your submission script on a Windows machine, which uses different line endings (`\r\n`) than Linux/macOS (`\n`). The SLURM scheduler cannot parse scripts with Windows line endings.

**Resolution:** You need to convert the line endings to Unix format. You can do this on the cluster by running the `dos2unix` command on your script:

```bash
dos2unix your_script.sbatch
```

After running this command, you can resubmit your job.

---

### Error: `module: command not found`

**Cause:** You are trying to use the `module` command in a non-interactive shell script (e.g., a script run by `sbatch`) or in a context where the environment is not set up correctly. The `module` command is a function, not a binary, and may not be defined in all shell environments.

**Resolution:** To ensure the `module` command is available in your scripts, you should source the system-wide profile script at the beginning of your sbatch script. Add the following line before you try to use `module`:

```bash
#!/bin/bash
#SBATCH ...

# Source the environment setup script to make 'module' command available
source /etc/profile.d/modules.sh

# Now you can use module commands
module load anaconda
...
```

---

### Still Have Questions?

If your error isn't listed here or you need further assistance, please visit our [Contact Us]({{ '/contact/' | relative_url }}) page.
