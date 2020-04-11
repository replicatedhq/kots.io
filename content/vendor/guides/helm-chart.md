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

## Adding a Chart to your Application


### Getting a Chart Archive

##### With helm fetch

helm repo add ...
helm fetch stable/consul

##### With helm package

lorem ipsum

### Adding the Archive to your manifests

- copy into manifests dir
- add HelmChart kots kind

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
- We'll use the `replicas` field for consul (yaml)

### Create config fields

- open config.yaml
- Add a section for consul with values (yaml)
- make a release, test in kotsadm UI (screenshot)
- but we haven't mapped these into the helm chart yet

### Map to Helm Values

- open consul.yaml (HelmChart kots kind)
- add mappings to config options (yaml)
- make release and test in kotsadm
- kubectl get pods should show new replicas(terminal output)

* * *

## Next Steps

You did it! Next steps are:

- Managing CRDs if you want to use a chart that needs them (advanced guide)
- Airgap / Helm (advanced guide)
- Explore Operators (advanced guide)

