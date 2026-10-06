---
title: "General Policies"
layout: single
nav_order: 2
parent: Policies
category: policies
permalink: /policies/general/
classes: [wide, left-aligned]
hide_hero: True
---

Welcome to the University of Chicago's DSI Cluster, where you gain access to a diverse range of clusters, computer systems, networks, applications, and technological assets. To ensure a positive and responsible experience, please familiarize yourself with the Terms of Use outlined below. By using the DSI's resources, you agree to abide by these rules, ensuring ethical and lawful use.[^citation]

### General Usage

Abiding by the following principles is mandatory for utilizing DSI resources responsibly:

* **Responsibility:** Users are accountable for proper tool utilization and maintaining confidentiality. In addition, they must maintain an environment in which all DSI users can fairly access DSI clusters to run jobs or store data.
* **Legality and Ethics:** DSI resources must not be used for illegal, malicious, or unethical purposes.
* **Respect:** Users should refrain from unauthorized use, academic dishonesty, or violation of software licenses.
* **Confidentiality:** Respect the privacy of individuals and report any security breach promptly.
* **Security:** Users must not attempt to circumvent system security or resource allocation mechanisms.
* **Monitoring:** Be aware that all activities on DSI systems are subject to monitoring.

Failure to follow these principles may result in account termination.

DSI resources are dedicated to supporting legitimate research and teaching activities at the University of Chicago, and users must align with their account's stated purpose.

#### User Accounts and Passwords

Account credentials are personal and should not be shared. If compromised, report immediately to DSI support staff. Sharing account or password information is strictly prohibited.

#### Data Management

DSI maintains an open research network but assumes no responsibility for data stored on the general-purpose HPC systems it operates. In these collaborative environments, researchers are responsible for overseeing the protection and sharing of their own data. Access to user data by DSI system administrators is limited to what is necessary for user support, technical troubleshooting, or maintaining system security, in accordance with UChicago Information Technology Policies.

#### Restricted Research Data

General-purpose DSI systems should not be used to store, process, transmit, or analyze restricted research data. Please contact us regarding your project as we offer a separate service, Secure Data Enclave, with resources dedicated to research on restricted data.

#### Data Integrity and Retention

The DSI prioritizes robust filesystems and archival storage with performance and reliability. Backup services are limited. The purpose of the DSI Cluster is _not_ for long-term data storage and the expectation should be that data should be moved off of the cluster once a research project is complete. The exception to this is when storage is purchased, in which case, the storage will fall under the purchase policy guidelines.

#### Storage Allocation Policy

The cluster provides different storage quotas for each storage tier:

* **Home Directories**: Each user receives **50GB** of storage in their home directory (`/home/<cnetid>`). This quota is inflexible and cannot be increased.

* **Project Directories**: The default allocation for project directories (`/project/<group>`) is **500GB**. Principal Investigators may request a different initial allocation when the project is created based on their research needs.
  * Maximum allocation without investment: **10TB per project**
  * Faculty members may request up to **3 project directories**
  * Allocations beyond 10TB require investment in additional storage. Contact DSI Techstaff to discuss options and pricing.

* **Scratch Space**: Each user starts with **50GB** on `/net/scratch` and **50GB** on `/net/scratch2`. Users may request additional scratch space as needed for active computational work.

For information on checking your current storage usage, see the [Checking Your Usage]({{ '/using-the-cluster/checking-usage/' | relative_url }}) page.

Please adhere to these guidelines to ensure a positive and secure DSI experience. 

### Oversight

The DSI Cluster is overseen by DSI Techstaff and the DSI’s cluster oversight faculty committee.

### Unsupported Workloads

As described in the [cluster mission statement]({{ "/mission/" | relative_url }}), the DSI Cluster is designed for high-performance computational operations. The cluster does **NOT** support:

* Real-time operations
* Significant data collection operations (such as web scraping)
* Web servers
* High-uptime systems
* High-privacy data storage (HIPAA/SOC2/etc.)

These workloads are not appropriate for the cluster and may result in job termination or account review.

### Login Node Usage and Limits

The login (frontend) nodes are passthroughs for reaching the cluster — submitting, monitoring, and managing jobs, transferring data, and light file editing. They are **not** for running computation.

As of **June 8, 2026**, the following apply to the login nodes:

* Access is through the load balancer at `login.ds.uchicago.edu`. Direct SSH to individual login nodes (`fe01`, `fe02`, `fe03`) is no longer available.
* Each user is limited to **1 CPU**, **8&nbsp;GB RAM**, and a **12-hour** wall-time limit per process. The 12-hour limit does not apply to the `tmux`/`screen` *server* itself, so those persist between logins; processes running inside them are still subject to it.

Computationally intensive work, IDE remote/backend servers that scan shared storage, code agents that spawn many processes, and other long-running processes must run on the compute nodes, not the login nodes. Long-lived `tmux` and code-agent sessions (Claude Code, Codex, herdr) can instead run on the **session node** `g010` — reached with `ssh session` from a login node — which is CPU-only, limited to **2 cores / 16&nbsp;GB RAM per user**, and has no uptime guarantee. Compute work started from the session node must still be submitted with `sbatch`. For how to update your SSH config and move work to the compute nodes, see [Login Nodes &amp; the Load Balancer]({{ "/using-the-cluster/login-nodes/" | relative_url }}).

### Acceptable Use and Enforcement

Usage of the DSI Cluster is managed by the University’s [Acceptable Use Policy](https://intranet.uchicago.edu/policies/information-technology-policies/acceptable-use-policy/).

The DSI Cluster cannot be used for commercial purposes. All usage must be constrained to educational and research activities.

[^citation]: This document was modified from the [RCC's policy guidelines](https://docs.rcc.uchicago.edu/101/policies/)