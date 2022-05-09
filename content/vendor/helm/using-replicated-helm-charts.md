---
date: 2019-12-27
linktitle: "Installing with Replicated KOTS"
title: Installing with Replicated KOTS
aliases:
  - /vendor/helm/
weight: 20580
---

By default, Replicated KOTS renders Helm chart templates and deploys them as standard Kubernetes manifests. To include a Helm chart, start by retrieving the chart `.tgz` package. For Helm charts that you do not control, these can be quickly downloaded using the `helm` CLI.

```shell
helm repo update
helm fetch stable/postgresql
```

After running these commands, the latest copy of the Helm chart will be in the current directory, named `chartname-<version>.tgz`. For example, `postgresql-8.1.2.tgz`.

#### Using a local copy

If you have your helm chart sources available locally, you can create a a package by `cd`-ing to the chart root (where the `Chart.yaml` file lives) and running

```shell
helm package .
```

Note that if you use subcharts, you may also need to run `helm dependecy update` before running `helm package`.

## Adding a Helm Chart to a Replicated application

To add this chart to a Replicated application, create a new release on the [Vendor portal](https://vendor.replicated.com). 
Once you are editing the release, drop the Helm chart into the file tree. 
The chart will be added to a new section near the top of the file tree, and the `values.yaml`, `Chart.yaml` and a new <chart-name>`.yaml` file will be created.

![Postgres Helm Chart](/images/postgres-helm-chart.png)

### &lt;chart-name&gt;.yaml
This file is required for chart to be installed. 
When using the Vendor portal generated automatically when adding a new Chart, and allows you to configure the chart with your application. 
The reference for this kind is in the [references section](/reference/v1beta1/helmchart).

### Chart.yaml
This file is readonly, and extracted from the Helm chart (as metadata).

### values.yaml
This file is readonly, and extracted from the Helm chart (as metadata).
