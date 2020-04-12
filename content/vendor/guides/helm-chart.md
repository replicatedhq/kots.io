---
date: "2020-02-20T00:00:00Z"
lastmod: "2020-02-20T00:00:00Z"
title: "Package a Helm Chart"
weight: "1005"
draft: true
---

In this guide, we'll explore how you can leverage software bundled using Helm to integrate an off-the-shelf application into your KOTS app bundle. We'll use the Consul helm chart as an example in this case.

This guide will assume you've already completed one of the [Getting Started Guides](/vendor/guides/#getting-started) and set up a local git repo to manage your application. It is divided into four sections

- [Adding a Chart to your Application](#adding-a-chart-to-your-application)
- [Releasing and Testing](#releasing-and-testing)
- [Mapping User Input to Helm Values](#mapping-user-input-to-helm-values)

### Prerequisites

- You should have completed one of the [Getting Started Guides](../#getting-started), this guide assumes you have a running instance of `kotsadm` to iterate against in either an existing or embedded cluster, and a local git checkout of your KOTS app manifests.

### Accompanying Code Examples

A full example of the code for this guide can be found in the [kotsapps repository](https://github.com/replicatedhq/kotsapps/tree/master/helm-grafana).

* * *

## Adding a Chart to your Application

### Getting a Chart Archive

##### With helm fetch

- helm repo add ...
- VALIDATE: helm repo ls 
- helm fetch stable/grafana
- VALIDATE: tar xOf grafana-5.0.11.tgz grafana/Chart.yaml

##### With helm package

- git checkout https://github.com/helm/charts
- cd charts/stable/grafana
- you can use the latest, but we'll be working off of this commit: `git checkout f2fcadaa5` (just to be sure)
- helm package .
- VALIDATE: tar xOf grafana-5.0.11.tgz grafana/Chart.yaml, you should see

```yaml
apiVersion: v1
appVersion: 6.7.1
description: The leading tool for querying and visualizing time series and metrics.
home: https://grafana.net
icon: https://raw.githubusercontent.com/grafana/grafana/master/public/img/logo_transparent_400x.png
kubeVersion: ^1.8.0-0
maintainers:
- email: zanhsieh@gmail.com
  name: zanhsieh
- email: rluckie@cisco.com
  name: rtluckie
- email: maor.friedman@redhat.com
  name: maorfr
name: grafana
sources:
- https://github.com/grafana/grafana
version: 5.0.11
```

### Adding the Archive to your manifests

- copy into manifests dir
- add HelmChart kots kind
- add adminUser and adminPassword defaults
- VALIDATE: next step we'll make a release and publish it

* * *

## Releasing and Testing

- make release
- check for updates
- kubectl get pods
- exec into a pod, make sure its working or something (does consul come with a UI we can hit, or do we have to use curl?)

* * *

## Mapping User Input to Helm Values


### Choosing Values

- go read the chart's values.yaml
- pick some simple ones to customize
- We'll use the `adminUser` and `adminPassword` fields for Grafana (yaml)

### Create config fields

- open config.yaml
- Add a section for grafana with values (yaml)
- make a release, test in kotsadm UI (screenshot)
- but we haven't mapped these into the helm chart yet

### Map to Helm Values

- open consul.yaml (HelmChart kots kind)
- add mappings to config options (yaml)
- make release and install in kotsadm
- update config fields
- deploy config change
- navigate to grafana UI, log in with new credentials

* * *

## Next Steps

You did it! Next steps are:

- If you're using this chart as the primary artifact in your KOTS application, you might want to take an `icon` from the `Chart.yaml` and add it to your kots `Application` CRD.
- Managing CRDs if you want to use a chart that needs them (advanced guide)
- Airgap / Helm (advanced guide)
- Explore Operators (advanced guide)

