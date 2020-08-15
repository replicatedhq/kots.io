---
date: 2019-10-09
linktitle: "Existing Prometheus Installations"
title: Existing Prometheus Installations
weight: 10020
---

Kots needs to be aware of the address of the Prometheus instance within the cluster in which it is installed. The address can be configured on the Admin Console dashboard page, by clicking the "Configure Prometheus Address" link and entering the address in the text box and clicking "Save". Graphs should appear on the dashboard shortly after saving the address. A [support bundle](/kotsadm/troubleshooting/support-bundle/) may include more information when troubleshooting configuration of the Prometheus address. If Prometheus is not already installed, see the [Prometheus documentation](/kotsadm/monitoring/prometheus/) for more information on installing Prometheus in a cluster.

![Configuring Prometheus](/images/kotsadm-dashboard-configureprometheus.png)

By default, Kots displays cluster disk usage, pod cpu usage, pod memory usage, and pod health graphs on the dashboard page of the Admin Console. Additionally, application developers can choose to expose business and application level metrics and alerts. In all cases, the metrics built into Kots will be exposed automatically to Prometheus, and any existing Web UI or Visualization tool such as [Grafana](https://prometheus.io/docs/visualization/grafana/).
