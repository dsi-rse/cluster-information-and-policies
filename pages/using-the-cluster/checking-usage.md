---
title: "Checking your Usage"
layout: single
nav_order: 2
parent: Using the Cluster
category: using-the-cluster
permalink: /using-the-cluster/checking-usage/
classes: [wide, left-aligned]
hide_hero: True
---

# Checking your Usage

The `dsiquota` command is a custom tool provided on the cluster to help you easily check your storage usage and quotas across the different filesystems. It provides a consolidated view of your usage in your home directory, project directories, and scratch space.

## How to Use `dsiquota`

To check your storage usage, simply log in to the cluster and run the following command in your terminal:

```bash
dsiquota
```

This command requires no arguments and will display the quota information for all the storage tiers you have access to.

## Example Output

When you run the `dsiquota` command, you will see output similar to the following. The output is organized by storage tier.

### Home Directory (`/home`)

This section shows the usage for your personal home directory.

```
+-----------------------------------------------------------------------------+
|                               /home/<cnetid>                                |
+-----------------------------------------------------------------------------+
| Filesystem |     Usage |     Quota | %Used |
|------------+-----------+-----------+-------|
| /dev/sda1  |    1.25GB |     20GB  |   6%  |
+-----------------------------------------------------------------------------+
```

*   **Usage**: The amount of disk space you are currently using.
*   **Quota**: The maximum amount of disk space you are allowed to use.
*   **%Used**: The percentage of your quota that is currently in use.

### Project Directories (`/project`)

This section lists the usage for all the project directories you are a member of.

```
+-----------------------------------------------------------------------------+
|                              /project/cool-lab                              |
+-----------------------------------------------------------------------------+
| Filesystem |     Usage |     Quota | %Used |
|------------+-----------+-----------+-------|
| /dev/sdb1  |   78.50TB |    100TB  |  78%  |
+-----------------------------------------------------------------------------+
```

### Scratch Space (`/scratch`)

The command also reports your usage on the `/scratch` filesystem.

```
+-----------------------------------------------------------------------------+
|                                  /scratch                                   |
+-----------------------------------------------------------------------------+
| Filesystem |     Usage |     Quota | %Used |
|------------+-----------+-----------+-------|
| /dev/sdc1  |   15.20TB |      inf  |   -   |
+-----------------------------------------------------------------------------+
```

Note that `/scratch` space typically does not have a hard quota (indicated by `inf` for infinite). However, it is subject to a strict purge policy where old files are automatically deleted. It is critical to move important data from `/scratch` to `/project` storage.

## What to do if you are over quota?

If you are approaching or have exceeded your quota in your `/home` or `/project` directory, you should:
1.  Identify and delete unnecessary files.
2.  Archive old data to a long-term storage solution if available.
3.  For project spaces, the Principal Investigator (PI) can request a quota increase by contacting support.

Regularly checking your usage with `dsiquota` helps prevent interruptions to your work and ensures fair use of shared resources.

## Additional CLI Examples

### Project directories

To check space usage assigned to a project, give its directory under `/net/spaces`:

```bash
dsiquota --project <PI_CNETID>/<PROJECT_NAME>
# e.g. a DSI clinic project
dsiquota --project clinic/<PROJECT_NAME>
```

### Scratch

```bash
dsiquota --scratch
```

This returns your current usage and limit on `/net/spaces/scratch`.

Note: `--scratch2` and `--monsoon` no longer exist; `/net/scratch2` is merged into `/net/spaces/scratch`.
