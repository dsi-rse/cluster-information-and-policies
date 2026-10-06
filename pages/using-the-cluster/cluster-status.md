---
title: "Cluster Status"
layout: single
nav_order: 2
parent: Using the Cluster
category: using-the-cluster
permalink: /using-the-cluster/cluster-status/
classes: [cluster-status-page]
hide_hero: True
---

Stay on top of the cluster’s health with two complementary dashboards.

## Dashboards at a glance

* **Simple status (`cluster-status-page.ds.uchicago.edu`)**: a lightweight view that tells you if the scheduler, login nodes, and key services are up.
* **Grafana metrics (`graf.ds.uchicago.edu`)**: detailed node-by-node charts for GPU/CPU usage, job pressure, historical utilization, and more.

Both update in real time and are public, so you can keep them open on any device while a job runs.

<div class="dashboard-links">
  <a class="btn btn--primary" href="https://cluster-status-page.ds.uchicago.edu/status/uchicago-dsi-cluster" target="_blank" rel="noopener">Simple status dashboard</a>
  <a class="btn btn--light-outline" href="https://graf.ds.uchicago.edu" target="_blank" rel="noopener">Grafana metrics</a>
</div>

## When to use each

- **Quick yes/no**: Check the simple status page before filing a ticket or starting a workshop. It answers “is the cluster generally healthy?” in a single glance.
- **Deep dive**: Use Grafana while debugging hung jobs, capacity questions, or GPU contention. You can drill into specific nodes, partitions, or GRES devices to see if what you’re experiencing matches cluster-wide activity.

## Tips for incident triage

1. Keep the simple status page open in a browser tab; it auto-refreshes and is mobile-friendly.
2. Bookmark favorite Grafana dashboards (GPU saturation, node availability, Lustre/NFS throughput) so you can jump straight to the metrics that matter for your workflow.
3. When reporting an issue, include timestamps and screenshots/links from either dashboard—this helps staff correlate your report with backend logs.
