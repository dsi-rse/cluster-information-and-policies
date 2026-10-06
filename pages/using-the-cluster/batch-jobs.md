---
title: "Submitting Batch Jobs (Slurm Examples)"
layout: single
nav_order: 2
parent: Using the Cluster
category: using-the-cluster
permalink: /using-the-cluster/batch-jobs/
classes: [wide, left-aligned]
hide_hero: True
---

# Submitting Batch Jobs (Slurm Examples)

For any task that is computationally intensive or expected to run for more than a few minutes, you should not run it directly on a login node. Instead, submit it to the Slurm workload manager as a **batch job**.

## Basic Batch Job

Create a script (e.g., `train.sh`):

```bash
#!/bin/bash
#SBATCH --job-name=my_training
#SBATCH --partition=general
#SBATCH --gres=gpu:1
#SBATCH --time=12:00:00
#SBATCH --output=logs/%x_%j.out
#SBATCH --error=logs/%x_%j.err

# Activate your environment
source activate myenv

# Run your code
python train.py
```

Submit it:

```bash
sbatch train.sh
```

## Choosing a QoS Tier

Every job runs under a QoS tier that determines its scheduling priority, preemption behavior, and fairshare cost. Add `--qos=<tier>` to your submission.

| Tier | Flag | Fairshare Cost | Preemptable? | Max Jobs | Best For |
|------|------|----------------|--------------|----------|----------|
| `general` | `--qos=general` (or omit) | 1x | Yes | 24 | Sweeps, training, batch work |
| `protected` | `--qos=protected` | 4x | No | 1 | Learning, short guaranteed jobs |
| `interactive` | `--qos=interactive` | 8x | No | 1 (max 2 GPUs / 32 CPUs / 256 GB) | Live development, debugging |

If you do not specify `--qos`, your job defaults to `general`. For the full tier specification — including fairshare math, wall-time limits, and the preemption hierarchy — see the [Job Scheduling Policy]({{ '/policies/scheduling/' | relative_url }}).

**DenyOnLimit on `protected` and `interactive`:** If you try to submit a second `protected` job (or a second `interactive` session) while one is already running, the request is **immediately rejected** with an error — it will not queue silently. The rejection is normal behavior, not a bug. Wait for the first job to finish, or submit to `general` instead.

### Examples

```bash
# Standard batch job (general tier, preemptable, cheap)
sbatch --qos=general train.sh

# Non-preemptable job (protected tier, 4x fairshare cost, 2h max)
sbatch --qos=protected --time=02:00:00 train.sh
```

You can also set the QoS inside the script:

```bash
#SBATCH --qos=protected
```

## Job Arrays for Hyperparameter Sweeps

If you are running many similar jobs (parameter sweeps, cross-validation folds, etc.), use a **job array** instead of submitting individual jobs. Arrays are more efficient for Slurm to manage and respect the per-user job limits cleanly.

```bash
#!/bin/bash
#SBATCH --job-name=sweep
#SBATCH --partition=general
#SBATCH --qos=general
#SBATCH --gres=gpu:1
#SBATCH --time=04:00:00
#SBATCH --array=0-99%20
#SBATCH --output=logs/sweep_%A_%a.out

# Each task gets a unique SLURM_ARRAY_TASK_ID (0-99)
python train.py --config configs/sweep_${SLURM_ARRAY_TASK_ID}.yaml
```

Submit:

```bash
sbatch sweep.sh
```

The `%20` in `--array=0-99%20` limits the sweep to **20 concurrent tasks**. This is important — without a throttle, a 100-task array could consume your entire job limit and flood the queue.

**Recommended throttle values:**
- Small, short jobs: `%20` to `%30`
- GPU-intensive jobs: `%5` to `%10`
- Start conservatively and increase if the cluster has capacity

## Handling Preemption

Jobs on the `general` tier can be **preempted** — interrupted and requeued — when an `interactive` job needs the resources. This will only happen to jobs that have already been running for at least 5 minutes. Preemption works as follows:

1. Slurm sends your job a **SIGUSR1** signal
2. Your job has **5 minutes** (grace period) to save a checkpoint
3. After the grace period, the job is killed and **automatically requeued**
4. When resources become available, the job restarts from the beginning of the script

### The Checkpoint Contract

To handle preemption gracefully, submit your job with these flags:

```bash
#SBATCH --signal=B:USR1@300
#SBATCH --requeue
```

- `--signal=B:USR1@300` — sends SIGUSR1 to the batch script 300 seconds (5 minutes) before the wall-time limit (and also at preemption time)
- `--requeue` — allows the job to be requeued after preemption

`--signal=B:USR1@N` is a per-job SBATCH directive and is **QoS-agnostic**: it fires N seconds before the wall-time limit on every QoS (`general`, `protected`, `interactive`) when you request it. Preemption signaling — also delivered via SIGUSR1 — is separate and only applies on `general`, since `protected` and `interactive` are not preemptable. If you want a checkpoint warning before the wall clock runs out on a `protected` job, add `--signal=B:USR1@N` to your submission; it will work even though `protected` is never preempted.

### Wrapper Script

Here is a complete wrapper script that handles preemption with checkpoint/restart:

```bash
#!/bin/bash
#SBATCH --job-name=preemptable_train
#SBATCH --partition=general
#SBATCH --qos=general
#SBATCH --gres=gpu:1
#SBATCH --time=12:00:00
#SBATCH --signal=B:USR1@300
#SBATCH --requeue
#SBATCH --output=logs/%x_%j.out

set -euo pipefail

child_pid=""

CKPT_DIR="/project/${SLURM_JOB_ACCOUNT}/checkpoints/${USER}/${SLURM_JOB_NAME}/${SLURM_JOB_ID}"

mkdir -p "$CKPT_DIR"

# Send a signal to the training process group, falling back to the direct child PID.
# This relies on launching Python with `setsid` below: the child PID is then also
# the process-group ID, so `-$child_pid` targets the whole training process group.
# This matters for parallelized workflows using things like multiprocessing, joblib, etc.
signal_child_group() {
    local signal="$1"

    if [[ -n "${child_pid:-}" ]] && kill -0 "$child_pid" 2>/dev/null; then
        echo "[$(date)] Sending ${signal} to training process group..."

        kill -s "${signal}" -- "-$child_pid" 2>/dev/null || \
            kill -s "${signal}" "$child_pid" 2>/dev/null || \
            true
    fi
}

# Handle Slurm's preemption warning signal.
# Python should checkpoint and exit. In the usual Slurm path, this wrapper exits 99
# to request requeue; if Python exits 99 directly, the defensive path below propagates it.
on_preempt() {
    echo "[$(date)] USR1 received: checkpointing before preemption or time-limit warning."

    signal_child_group USR1

    # Let Python checkpoint and exit. Slurm's grace window is the hard deadline.
    wait "$child_pid" || true

    # DSI cluster policy: exit code 99 tells Slurm to requeue this job.
    echo "[$(date)] Exiting with code 99 for Slurm requeue policy."
    exit 99
}

# Handle TERM if it reaches the batch shell, e.g. scancel --batch or scheduler cleanup.
# Plain `scancel <jobid>` may terminate the Python process directly instead and will then
# be handled by the `wait "$child_pid"` below.
on_term() {
    echo "[$(date)] TERM received: terminating without intentional requeue."

    signal_child_group TERM
    wait "$child_pid" || true

    echo "[$(date)] Exiting due to TERM."
    exit 143
}

trap on_preempt USR1
trap on_term TERM

# Check for existing checkpoint
latest_ckpt=$(ls -1t "$CKPT_DIR"/*.pt 2>/dev/null | head -n1 || true)

if [[ -n "${latest_ckpt:-}" ]]; then
    echo "[$(date)] Resuming from checkpoint: $latest_ckpt (restart #${SLURM_RESTART_COUNT:-0})"
    setsid python train.py --resume "$latest_ckpt" --ckpt-dir "$CKPT_DIR" &
else
    echo "[$(date)] Starting fresh training run"
    setsid python train.py --ckpt-dir "$CKPT_DIR" &
fi

child_pid=$!

return_code=0
wait "$child_pid" || return_code=$?

echo "[$(date)] Training exited with code ${return_code}"

# Defensive path: if Python itself exits 99 after checkpointing, propagate that
# as the Slurm requeue request. The normal Slurm path is still the USR1 trap above.
if [[ "$return_code" -eq 99 ]]; then
    echo "[$(date)] Training requested requeue via exit code 99."
    exit 99
fi

exit "$return_code"
```

> **About the checkpoint directory.** The wrapper writes to `/project/$SLURM_JOB_ACCOUNT/checkpoints/$USER/$SLURM_JOB_NAME/$SLURM_JOB_ID`. The `$SLURM_JOB_ID` segment prevents two same-named jobs from clobbering or cross-resuming each other's checkpoints; `$SLURM_JOB_ID` is preserved across requeues, so resume after preemption still works. Confirm the parent path `/project/$SLURM_JOB_ACCOUNT/checkpoints/$USER/` is writable by your account before relying on it — if `/project/<your-account>/` does not exist or is read-only for you, the `mkdir -p` will fail and the job will abort immediately. Substitute a path under `/project/` or `/scratch/` that you own. Running `touch /project/$SLURM_JOB_ACCOUNT/checkpoints/test && rm /project/$SLURM_JOB_ACCOUNT/checkpoints/test` on a login node is a quick way to check.

### PyTorch Checkpoint Example

In your training script, handle SIGUSR1 to save a checkpoint:

```python
import os
import signal
import sys
from pathlib import Path

import torch

should_checkpoint = False


def handle_preempt(signum, frame):
    global should_checkpoint
    print(f"Received signal {signum}; will checkpoint at next opportunity...", flush=True)
    should_checkpoint = True


def atomic_torch_save(state, path):
    path = Path(path)
    path.parent.mkdir(parents=True, exist_ok=True)

    tmp_path = path.with_suffix(path.suffix + ".tmp")
    torch.save(state, tmp_path)
    os.replace(tmp_path, path)


signal.signal(signal.SIGUSR1, handle_preempt)

for epoch in range(start_epoch, max_epochs):
    for step, batch in enumerate(dataloader):
        loss = train_step(model, batch)

        if should_checkpoint:
            ckpt_path = Path(ckpt_dir) / f"checkpoint_epoch{epoch}_step{step}.pt"

            atomic_torch_save(
                {
                    "epoch": epoch,
                    "step": step,
                    "model_state_dict": model.state_dict(),
                    "optimizer_state_dict": optimizer.state_dict(),
                    "loss": float(loss.detach().cpu()) if torch.is_tensor(loss) else float(loss),
                },
                ckpt_path,
            )

            print("Checkpoint saved. Exiting with code 99 for requeue.", flush=True)
            sys.exit(99)
```

To resume from a checkpoint:

```python
if args.resume:
    checkpoint = torch.load(args.resume)
    model.load_state_dict(checkpoint['model_state_dict'])
    optimizer.load_state_dict(checkpoint['optimizer_state_dict'])
    start_epoch = checkpoint['epoch']
```

### What Preemption Looks Like for an Overnight Job

The 5-minute warning is a signal to your job, not to you — you do not need to be awake for it. Here is what actually happens if you submit a 12-hour training run at 6pm and go home:

1. **6:00pm** — `sbatch train.sh` submitted. Job ID 12345 starts on a GPU node.
2. **11:47pm** — an `interactive` job requests a GPU; the scheduler picks your job for preemption.
3. **11:47pm** — Slurm sends `SIGUSR1` to your batch script. Your trap handler (see wrapper script above) forwards it to the training process. PyTorch catches it, sets `should_checkpoint = True`, finishes the current step, writes a checkpoint to `$CKPT_DIR`, and exits with code 99.
4. **11:52pm** — 5 minutes elapse. If the job has already exited cleanly it is requeued immediately. If it is still running, Slurm sends `SIGKILL` and requeues it.
5. **Sometime overnight** — a GPU frees up. Slurm restarts job 12345 (same job ID, `SLURM_RESTART_COUNT` increments). The wrapper script finds the checkpoint in `$CKPT_DIR` and resumes from it.
6. **Morning** — you check `sacct -j 12345` and see the restart history in `logs/my_training_12345.out`.

**The 5-minute warning is a deadline for your code, not for you.** As long as your job implements the checkpoint contract, preemption is automatic and requires no human intervention. A job that does not implement checkpointing will simply restart from epoch 0 — correct, but wasteful.

**Key recommendations for overnight and long-running jobs:**

- **Always** set `--signal=B:USR1@300` and `--requeue`. Without both, a preempted job has no warning and will not be requeued.
- **Checkpoint at a cadence your code can actually meet in 5 minutes.** If a single training step takes 4 minutes, you need to checkpoint per-step. For typical step times (seconds), checkpointing every N steps or at each `should_checkpoint` flag check is sufficient.
- **Write checkpoints atomically** — save to `checkpoint.pt.tmp` and `mv` into place — so a `SIGKILL` mid-write cannot corrupt the previous checkpoint.
- **Log to `logs/%x_%j.out`** (the `%j` is the job ID, which is preserved across requeues), so a single log file captures all restart attempts.
- **If preemption would be catastrophic** (e.g., a rare reproducibility run, or you need guaranteed wall-clock completion), use `--qos=protected` instead. The 2-hour limit and 4x fairshare cost are the tradeoff.
- **Test your signal handler during the day** before relying on it overnight. You can simulate preemption with `scancel --signal=USR1 --batch <jobid>`, which sends SIGUSR1 to the batch step without killing the job. Confirm that a checkpoint appears in `$CKPT_DIR` and that the job exits with code 99.

### Parallel Python: multiprocessing & joblib

If your job uses `multiprocessing.Pool`, `joblib.Parallel`, or anything else that calls `fork()` to spawn workers, the parent process's signal handler does **not** propagate to the workers automatically. The wrapper above signals the entire process group via `setsid`, but the Python side still needs to (1) make workers ignore SIGUSR1 so only the parent coordinates checkpointing, (2) record completed work between batches, and (3) write a single atomic checkpoint from the parent before exiting with code 99.

The two reference scripts below implement this pattern. Both use `--ckpt-dir` and `--resume` arguments matching the wrapper's invocation.

#### multiprocessing example

```python
import argparse
import os
import pickle
import signal
import sys
from pathlib import Path
from multiprocessing import Pool

should_checkpoint = False


def handle_preempt(signum, frame):
    """Record that Slurm requested checkpointing; keep the signal handler minimal."""
    global should_checkpoint
    print(f"Received signal {signum}; will checkpoint soon.", flush=True)
    should_checkpoint = True


def ignore_preempt_in_worker():
    """Make workers ignore SIGUSR1 so only the parent coordinates checkpointing."""
    signal.signal(signal.SIGUSR1, signal.SIG_IGN)


def atomic_pickle_save(state, path):
    """Write a checkpoint safely by saving to a temp file, then renaming atomically."""
    path = Path(path)
    path.parent.mkdir(parents=True, exist_ok=True)
    tmp_path = path.with_suffix(path.suffix + ".tmp")

    with open(tmp_path, "wb") as f:
        pickle.dump(state, f)

    os.replace(tmp_path, path)


def load_checkpoint(path):
    """Load a previously saved checkpoint so the job can resume after requeue."""
    with open(path, "rb") as f:
        return pickle.load(f)


def work(x):
    """Run one unit of parallel work; replace this with your actual computation."""
    return x, x * x


def main():
    parser = argparse.ArgumentParser()
    parser.add_argument("--ckpt-dir", required=True)
    parser.add_argument("--resume", default=None)
    args = parser.parse_args()

    signal.signal(signal.SIGUSR1, handle_preempt)

    ckpt_dir = Path(args.ckpt_dir)
    ckpt_path = ckpt_dir / "mp_checkpoint.pkl"

    items = list(range(1000))
    results = []
    start_index = 0

    if args.resume:
        state = load_checkpoint(args.resume)
        start_index = state["next_item_index"]
        results = state["results"]
        print(f"Resuming from item index {start_index}", flush=True)

    remaining_items = items[start_index:]

    with Pool(processes=4, initializer=ignore_preempt_in_worker) as pool:
        for absolute_index, result in enumerate(pool.imap(work, remaining_items), start=start_index):
            results.append(result)
            next_item_index = absolute_index + 1

            if should_checkpoint:
                # This checkpoint records only completed/yielded results.
                # Any in-flight worker tasks may be discarded on exit and recomputed after resume.
                atomic_pickle_save(
                    {
                        "next_item_index": next_item_index,
                        "results": results,
                    },
                    ckpt_path,
                )

                print(f"Checkpoint saved to {ckpt_path}. Exiting.", flush=True)
                sys.exit(99)

    atomic_pickle_save(
        {
            "next_item_index": len(items),
            "results": results,
            "complete": True,
        },
        ckpt_path,
    )

if __name__ == "__main__":
    main()
```

#### joblib example

```python
import argparse
import os
import pickle
import signal
import sys
from pathlib import Path

from joblib import Parallel, delayed

should_checkpoint = False


def handle_preempt(signum, frame):
    """Record that Slurm requested checkpointing; joblib checks between batches."""
    global should_checkpoint
    print(f"Received signal {signum}; will checkpoint after current batch.", flush=True)
    should_checkpoint = True


def atomic_pickle_save(state, path):
    """Write a checkpoint safely by saving to a temp file, then renaming atomically."""
    path = Path(path)
    path.parent.mkdir(parents=True, exist_ok=True)
    tmp_path = path.with_suffix(path.suffix + ".tmp")

    with open(tmp_path, "wb") as f:
        pickle.dump(state, f)

    os.replace(tmp_path, path)


def load_checkpoint(path):
    """Load a previously saved checkpoint so the job can resume after requeue."""
    with open(path, "rb") as f:
        return pickle.load(f)


def work(x):
    """Run one unit of parallel work; replace this with your actual computation."""
    return x, x * x


def batched(items, batch_size):
    """Yield small batches so checkpointing can happen between joblib Parallel calls."""
    for start in range(0, len(items), batch_size):
        yield start, items[start : start + batch_size]


def main():
    parser = argparse.ArgumentParser()
    parser.add_argument("--ckpt-dir", required=True)
    parser.add_argument("--resume", default=None)
    args = parser.parse_args()

    signal.signal(signal.SIGUSR1, handle_preempt)

    ckpt_dir = Path(args.ckpt_dir)
    ckpt_path = ckpt_dir / "joblib_checkpoint.pkl"

    items = list(range(1000))
    results = []
    start_index = 0
    batch_size = 16

    if args.resume:
        state = load_checkpoint(args.resume)
        start_index = state["next_item_index"]
        results = state["results"]
        print(f"Resuming from item index {start_index}", flush=True)

    for batch_offset, batch in batched(items[start_index:], batch_size):
        absolute_start = start_index + batch_offset

        batch_results = Parallel(n_jobs=4)(
            delayed(work)(x) for x in batch
        )

        results.extend(batch_results)
        next_item_index = absolute_start + len(batch)

        # Rolling checkpoint after each completed batch, so resume only re-runs at most
        # the batch that was in progress when preemption was requested.
        atomic_pickle_save(
            {
                "next_item_index": next_item_index,
                "results": results,
            },
            ckpt_path,
        )

        if should_checkpoint:
            print(f"Checkpoint saved to {ckpt_path}. Exiting.", flush=True)
            sys.exit(99)

    atomic_pickle_save(
        {
            "next_item_index": len(items),
            "results": results,
            "complete": True,
        },
        ckpt_path,
    )

if __name__ == "__main__":
    main()
```

### What If I Don't Implement Checkpointing?

Jobs without checkpointing will simply **restart from the beginning** when requeued. This is fine for short jobs (< 1 hour), but for multi-hour or overnight training runs you will lose all progress each time the job is preempted. Implementing checkpointing is strongly recommended for any job that runs for more than an hour or is submitted to run overnight.

## Useful Commands

```bash
# Check your job status
squeue -u $USER

# Check job status with QoS and priority info
squeue -u $USER -O jobid,name,partition,qos,state,timelimit,priority

# Cancel a job
scancel <jobid>

# Cancel all your jobs
scancel -u $USER

# Check your fairshare standing
sshare -u $USER

# View detailed job info
scontrol show job <jobid>

# View past job accounting
sacct -j <jobid> --format=JobID,QOS,State,Elapsed,MaxRSS
```

## Quick Reference

```bash
# Standard batch job (cheapest, preemptable)
sbatch --qos=general --partition=general --gres=gpu:1 --time=12:00:00 train.sh

# Non-preemptable job (4x cost, guaranteed completion, 2h max)
sbatch --qos=protected --partition=general --gres=gpu:1 --time=02:00:00 train.sh

# Sweep with throttle
sbatch --qos=general --array=0-99%20 sweep.sh

# Preemption-safe job
sbatch --qos=general --signal=B:USR1@300 --requeue train_with_ckpt.sh
```
