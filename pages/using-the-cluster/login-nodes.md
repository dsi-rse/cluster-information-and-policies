---
title: "Login Nodes & the Load Balancer"
layout: single
nav_order: 1
parent: Using the Cluster
category: using-the-cluster
permalink: /using-the-cluster/login-nodes/
classes: [wide, left-aligned]
hide_hero: True
---
# Login Nodes & the Load Balancer

**In effect as of June 8, 2026.** The changes described on this page are now live.
{: .notice--info}

As of **June 8, 2026**, two changes are in effect on the login (frontend) nodes to improve stability and quality of life:

1. **A load balancer** sits in front of the login nodes. You connect to a single hostname, `login.ds.uchicago.edu`, and are routed automatically to the least-loaded node. Direct SSH to the individual nodes (`fe01`, `fe02`, `fe03`) has been retired.
2. **Per-user resource limits** on the login nodes: **1 CPU**, **8&nbsp;GB RAM**, and a **12-hour** wall-time limit per process.

These changes address the "noisy neighbor" problem, where heavy activity by one user destabilizes the login node for everyone else. The login nodes are passthroughs for *reaching and submitting work* to the compute nodes — not for running computation themselves.

## Connecting through the load balancer

Previously, you may have had to log in and out of individual nodes by hand to find one that isn't saturated. The load balancer does this for you: it directs each connection to the login node with the most available resources.

Connect to:

```bash
ssh <cnetid>@login.ds.uchicago.edu
```

Direct connections to `fe01.ds.uchicago.edu`, `fe02.ds.uchicago.edu`, and `fe03.ds.uchicago.edu` are no longer available.

## Action required: update your SSH config

If you have a `Host` entry in your `~/.ssh/config` that points at an individual login node, update its `HostName` to `login.ds.uchicago.edu`. Most users have something like this:

```sshconfig
# Before
Host fe.ds
  HostName fe01.ds.uchicago.edu
  IdentityFile ~/.ssh/id_ecdsa
  ForwardAgent yes
  User <cnetid>
```

Change it to:

```sshconfig
# After
Host login.ds
  HostName login.ds.uchicago.edu
  IdentityFile ~/.ssh/id_ecdsa
  ForwardAgent yes
  User <cnetid>
```

The only line that *must* change is `HostName`. The `Host` line is just a local nickname you type after `ssh` (e.g. `ssh login.ds`) — you can keep it as `fe.ds` or rename it to whatever you like. Replace `<cnetid>` with your CNetID and `IdentityFile` with the path to your key.

New to SSH config? See [Using the Cluster (ssh)]({{ "/quickstart/ssh/" | relative_url }}).

## tmux / screen: make your session portable

The load balancer routes each connection to the **least-loaded** node — it does **not** pin you to a particular node, so the node you land on can change between logins. A `tmux` or `screen` server runs only on the machine where you started it, and you cannot reattach to it from a different login node (the session is local to that host). So if you simply `tmux attach` after reconnecting, your session may appear to be "missing" — it's really still on the other node.

Because your **home directory is shared across all login nodes** (it's the same NFS storage everywhere), you can make your layout follow you with [`tmux-resurrect`](https://github.com/tmux-plugins/tmux-resurrect) and [`tmux-continuum`](https://github.com/tmux-plugins/tmux-continuum). They periodically save your window/pane layout and working directories to `~/.tmux/resurrect` — which, being on shared storage, is visible from whichever node you land on — and restore them automatically when you start `tmux`.

```tmux
# ~/.tmux.conf
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-resurrect'
set -g @plugin 'tmux-plugins/tmux-continuum'
set -g @continuum-restore 'on'   # auto-restore on tmux start

run '~/.tmux/plugins/tpm/tpm'
```

```bash
# one-time install of the plugin manager, then press prefix + I inside tmux
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
```

**What this does and doesn't do.** It rebuilds your *workspace skeleton* — window/pane layout, working directories, and (for whitelisted programs) editors like `vim`/`nvim` — on whatever node you land on. It does **not** preserve the live state of running programs: a process is relaunched from scratch, not resumed mid-run. Combined with the 12-hour process limit below, the right mental model is "rebuild my editing workspace anywhere," not "resume a running job." For anything long-running, use a [batch job]({{ "/using-the-cluster/batch-jobs/" | relative_url }}) or an [interactive session]({{ "/using-the-cluster/interactive-sessions/" | relative_url }}) on a compute node.

## Session node (g010)

For long-lived terminal sessions — `tmux`, and code agents such as Claude Code, Codex, or herdr — there is a dedicated **session node**, `g010`. It gives you a stable host to keep a multiplexer or agent session on, without the login nodes' 1-CPU limit or the load balancer moving you between hosts.

Reach it from any login node:

```bash
ssh <cnetid>@login.ds.uchicago.edu   # land on a login node
ssh session                          # hop to the session node (g010)
```

| Property | Value |
| --- | --- |
| Hardware | CPU only (no GPUs) |
| Per-user limit | 2 cores, 16&nbsp;GB RAM |
| Intended for | `tmux`, Claude Code, Codex, herdr, and similar agent/terminal sessions |
| Uptime | **No uptime guarantee** — the node may be rebooted or restarted |

The session node is a place to *drive* your work, not to run it. **Compute work must still be submitted with `sbatch`** (or run in an [interactive session]({{ "/using-the-cluster/interactive-sessions/" | relative_url }})) — for example, have your agent submit batch jobs rather than training models on `g010` itself. Because there is no uptime guarantee, don't keep anything there you can't afford to lose; the `tmux-resurrect` setup above works here too since your home directory is shared.

## Login node resource limits

Each user is limited on a login node to:

| Resource | Limit |
| --- | --- |
| CPU | 1 core |
| Memory | 8&nbsp;GB RAM |
| Process wall time | 12 hours |

The 12-hour limit applies to processes, not to the `tmux` or `screen` *server* itself — the multiplexer keeps running so you can reconnect to it (see [tmux / screen: make your session portable](#tmux--screen-make-your-session-portable) above). Anything you actually run, including processes you launch *inside* `tmux`, is subject to the 12-hour limit.

This is sufficient for what the login nodes are meant for:

* **Fine on the login nodes:** file operations and transfers, light file editing, and requesting, submitting, and monitoring jobs.
* **Not appropriate on the login nodes:** computationally intensive work, IDE remote/backend servers that scan or index all of shared storage, code agents (such as Claude or Codex) that spawn many processes, and any long-running process. These belong on the compute nodes. See also the [login node usage policy]({{ "/policies/general/#login-node-usage-and-limits" | relative_url }}).

## What to do instead: move work to the compute nodes

These limits do not stop you from running heavy or agentic workflows — you just run them on the compute nodes rather than on a login node:

* **Long or compute-intensive work** → submit a [batch job]({{ "/using-the-cluster/batch-jobs/" | relative_url }}).
* **Running a code agent or keeping a long-lived `tmux` session** → use the [session node](#session-node-g010) (`ssh session` from a login node), and have it submit compute work with `sbatch`.
* **Interactive development or notebooks** → start an [interactive session]({{ "/using-the-cluster/interactive-sessions/" | relative_url }}) on a compute node.
* **Moving data** → `scp`/`rsync` through the same hostname; see [Transferring Data with rsync and scp]({{ "/advanced-topics/rsync-scp/" | relative_url }}).

## If a login node is full

Based on historical peak usage, the resource limits should leave plenty of headroom to log in. In the rare case that every login node is saturated, you'll receive a message at login asking you to try again shortly. We don't expect this to happen with any regularity.

## Questions

If you hit a workflow these changes disrupt, [reach out]({{ "/contact/" | relative_url }}) — we'd rather hear about it than have you blocked.
