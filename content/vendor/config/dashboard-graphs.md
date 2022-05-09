---
date: 2019-11-01
linktitle: "Dashboard Graphs"
title: Dashboard Graphs
weight: 20030
aliases: 
  - /vendor/dashboard/graphs
---

By default, when installing a Kots application into an embedded cluster, the Prometheus monitoring system will be included alongside the Kots application. 
This will collect valuable metrics about the cluster as well as the application and expose graphs with key metrics on the dashboard of the Admin Console. 
When running in an existing cluster, it is possible to configure the address of the Prometheus service in the Admin Console.

![Graphs](/images/kotsadm-dashboard-graph.png)

By default, metrics graphs that are included monitor cluster disk usage, pod cpu usage, pod memory usage and pod health.

### Kots Application Spec

To add custom graphs, use the `graphs` property of the kots.io Application spec. Adding custom graphs will replace the default graphs. 
A minimal graph includes only a title and a Prometheus query:

```yaml
apiVersion: kots.io/v1beta1
kind: Application
metadata:
  name: my-application
spec:
  graphs:
    - title: User Signups
      query: 'sum(user_signup_events_total)'
```

See below for a more robust example:

```yaml
apiVersion: kots.io/v1beta1
kind: Application
metadata:
  name: my-application
spec:
  graphs:
    - title: Disk Usage
      queries:
        - query: 'sum((node_filesystem_size_bytes{job="node-exporter",fstype!="",instance!=""} - node_filesystem_avail_bytes{job="node-exporter", fstype!=""})) by (instance)'
          legend: 'Used: {{ instance }}'
        - query: 'sum((node_filesystem_avail_bytes{job="node-exporter",fstype!="",instance!=""})) by (instance)'
          legend: 'Available: {{ instance }}'
      yAxisFormat: bytes
```

### Prometheus Query

A valid PromQL prometheus query is required in the `query` property. 
By default an [Embedded Cluster](/vendor/embedded-kubernetes/embedded-kubernetes/) exposes the Prometheus [Expression Browser](https://prometheus.io/docs/visualization/browser/) at NodePort 30900. 
This can be used to aid in constructing queries.

More information on querying Prometheus with PromQL can be found [here](https://prometheus.io/docs/prometheus/latest/querying/basics/).
