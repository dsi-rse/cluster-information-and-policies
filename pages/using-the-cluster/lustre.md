---
title: "Using Lustre"
layout: single
nav_order: 3
parent: Using the Cluster
category: using-the-cluster
permalink: /using-the-cluster/lustre/
classes: [wide, left-aligned]
hide_hero: True
---

# Using Lustre

The cluster's shared storage is moving to **Lustre**, a parallel filesystem mounted on every node at `/net/spaces`. Unlike a traditional NFS server, Lustre splits the filesystem across many servers: a metadata server tracks file names and permissions, while your file *contents* are spread ("striped") across object storage servers. Because a single file can live on many disks and servers at once, Lustre can move data far faster than the storage it replaces — but it also behaves differently in a few ways worth understanding.

**Migration in progress:** shared storage is being migrated in phases, starting with scratch, followed by project spaces and home directories. The old `/net/scratch`, `/net/scratch2`, `/net/projects`, and `/net/projects2` paths still work today, but they are being retired — once the migration completes, `/net/spaces` will be the only path. Each phase will be announced before it happens; write new workflows against `/net/spaces` paths.
{: .notice--info}

## Layout of `/net/spaces`

The new filesystem uses one consistent naming scheme:

| Data | Path |
| :--- | :--- |
| Scratch | `/net/spaces/scratch/<your directory>` |
| Research group / project data | `/net/spaces/<pi_cnetid>/<project>` |
| DSI Clinic, practicum, and course data | `/net/spaces/clinic/<project>` |

The two old scratch filesystems merge into the single `/net/spaces/scratch`: directories from `/net/scratch` keep their names, and directories from `/net/scratch2` gain a `-scratch2` suffix so nothing collides.

Scratch on Lustre keeps the same rules as before: it is **not backed up**, and files unused for 60 days are purged. See the [Shared Storage Overview]({{ '/using-the-cluster/storage-overview/' | relative_url }}) for how the tiers fit together.

## Quotas

Quotas on `/net/spaces` are enforced per user, per group, and per project directory. Check yours with `lfs quota`:

```bash
$ lfs quota -h -u $USER /net/spaces
Disk quotas for usr jdoe (uid 24894):
     Filesystem    used  bquota  blimit  bgrace   files  iquota  ilimit  igrace
    /net/spaces  412.3G    500G    550G       -   81225       0       0       -
```

`used` is your current usage, `bquota` is your soft limit, and `blimit` is the hard limit. Use `-g <group>` instead of `-u` to see a group quota. Allocation sizes are set by the [Storage Allocation Policy]({{ '/policies/general/#storage-allocation-policy' | relative_url }}).

Two behaviors differ from the old storage:

*   **Usage is measured after compression.** The filesystem transparently compresses everything it stores, and quota counts the compressed size. Most datasets will use noticeably less quota than their apparent size — text-heavy data often compresses 2–3&times;.
*   **Enforcement is slightly elastic.** Lustre pre-grants space to the servers holding your files, so you may briefly overshoot a limit by a few percent (shown with a `*` in `lfs quota` output) before writes start failing with `Disk quota exceeded`.

## Striping: how your files are spread across the filesystem

Every file on Lustre has a *stripe layout*: how many object storage servers it is split across, and in what chunk size. Striping is what makes large-file I/O fast — a file striped across 7 storage targets can be read and written at roughly 7 disks' worth of speed at once.

By default, files on `/net/spaces` are striped across **4 storage targets in 4&nbsp;MiB chunks**. Scratch is smarter: it uses a *progressive layout* that picks striping automatically from the file's size — small files (&lt;&nbsp;64&nbsp;MiB) stay on a single target, medium files use 4, and files over 1&nbsp;GiB spread across 7. **Most users never need to set striping by hand.**

To see a file's layout:

```bash
$ lfs getstripe myresults.h5
myresults.h5
lmm_stripe_count:  4
lmm_stripe_size:   4194304
lmm_pattern:       raid0
...
```

If you are writing very large files (tens of GB or more) outside scratch and want maximum throughput, create a directory that stripes across all storage targets *before* writing into it:

```bash
$ mkdir /net/spaces/mylab/checkpoints
$ lfs setstripe -c -1 /net/spaces/mylab/checkpoints   # -1 = stripe across all targets
```

New files inherit the directory's layout. A file's striping is **fixed when it is created** — changing a directory's layout does not restripe existing files. To restripe an existing file, re-copy it into the new directory or use `lfs migrate -c <count> <file>`.

## Getting good performance

*   **Parallelism pays.** A single write stream runs at roughly 1–2&nbsp;GB/s depending on striping; a handful of parallel streams (multiple processes, tasks, or `rsync` jobs) reaches ~5&nbsp;GB/s aggregate, and reads of wide-striped files hit ~5&nbsp;GB/s even single-stream. If a transfer or job is I/O-bound, split it into several concurrent streams rather than one big one.
*   **Prefer a few large files over many small ones.** Every file open touches the metadata server, so workloads that create or scan millions of tiny files are Lustre's weak spot. Package small files into archives, HDF5/Zarr containers, or WebDataset-style shards where you can.
*   **Keep small-file-heavy work off the shared filesystem.** Conda environments, pip caches, compilation, and similar metadata-churn workloads belong on node-local NVMe — request the `local` GRES described in the [Node-local Storage guide]({{ '/using-the-cluster/node-local-storage/' | relative_url }}) and stage results back when the job finishes.

> **Warning:** Avoid running `find`, `du`, or recursive `ls -R` sweeps over huge directory trees on `/net/spaces`. Walking millions of files hammers the metadata server for every user on the cluster.

## See also

- [Shared Storage Overview]({{ '/using-the-cluster/storage-overview/' | relative_url }})
- [Node-local Storage]({{ '/using-the-cluster/node-local-storage/' | relative_url }})
- [Checking Your Usage]({{ '/using-the-cluster/checking-usage/' | relative_url }})
- [Storage Allocation Policy]({{ '/policies/general/#storage-allocation-policy' | relative_url }})
