---
date: 2019-11-01
linktitle: "Dashboard Graphs"
title: Adding Dashboard Graphs
weight: 20030
---

By default, when installing a Kots application into an embedded cluster, the Prometheus monitoring system will be included alongside your application. This will collect valuable metrics about the cluster as well as your application and expose graphs with key metrics on the dashboard of the Admin Console. When running in an existing cluster, it is possible to configure the address of your Prometheus service in the Admin Console.

By default, metrics graphs that are included monitor cluster disk usage, pod cpu usage, pod memory usage and pod health.

To add custom graphs, you can include the `graphs` property in your kots.io Application spec. A minimal graph includes only a title and a Prometheus query.

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
