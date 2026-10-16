---
title: "Current Cluster Information"
layout: single
permalink: /faq/cluster-information/
parent: Troubleshooting & FAQ
nav_order: 2
classes: [wide, left-aligned]
---

# Current Cluster Information

As of October 2026, the following table summarizes the DSI cluster resources. Some resources are reserved for specific initiatives or grants.

## Total Cluster Resources

| Resource Type   | Number of Units |
|:----------------|----------------:|
| Number of Nodes | 32              |
| Total Cores     | 3,296           |
| Total Storage   | 2.1 PB          |
| GPUs            | 32 NVIDIA A40 48GB <br/> 60 NVIDIA A100 80GB <br/> 12 NVIDIA L40S 48GB <br/> 8 NVIDIA H100 80GB <br/> 8 NVIDIA H100 NVL 94GB <br/> 44 NVIDIA H200 141GB <br/> Total: 164 |



## Cluster Networking and Topology

The cluster is organized into a series of nodes and storage servers. Storage servers have a mix of different drives and types. 

<!-- Nodes are broken into series (denoted by their name):
- The `g` series has a 200GB SSD locally
- The `h` series has a 200GB SSD locally
- The `j` series has a 400GB SSD locally
- The `k` series has a 1TB SSD locally
- The `i` series has 400GB SSD locally
- The `m` series has 500GB SSD locally
- The `n` series has a 10TB SSD locally -->

Users connect to the cluster through the load balancer at **login.ds.uchicago.edu**, which routes each connection to one of three login (frontend) nodes (`fe01`, `fe02`, `fe03`). This provides redundant, balanced access without users having to pick a node by hand. See [Login Nodes & the Load Balancer]({{ "/using-the-cluster/login-nodes/" | relative_url }}).

Note that not all nodes are available on all SLURM partitions and queues.

The cluster has a 10 Gbps line to the internet.

The network between nodes and storage servers runs at 100 Gbps. This internal network is not routable from the campus network or internet.

## Space Availability and Server Room

The DSI cluster is housed in the Hinds data center, a UChicago-managed facility. Available rack space and power capacity are managed by the Hinds data center operations team, and our allocation is contingent on the university's overall use of that space. The server room is power-constrained, which limits the density of GPU nodes per rack and is the primary limiting factor for cluster growth.


