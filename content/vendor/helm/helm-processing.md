---
date: 2020-01-03
linktitle: "Helm Processing"
title: Helm Processing
weight: 20505
---

The Replicated Helm installation is compatible with both Helm V2 and Helm V3 as it does not rely on Tiller to deploy the chart into the cluster. 
Instead, KOTS treats a Helm Chart as the packaging spec rather than the deployment tool. 
The Replicated Helm installation creates deployable YAML by leveraging the same functionality that the `helm template` command uses (with some extended functionality for [specific Helm hooks](/vendor/packaging/cleaning-up-jobs/#helm-charts)).

Except in the case of [Helm as KOTS application airgap installations](/vendor/helm/helm-airgap-builder), the processing of the chart is managed in the end customer environment (via Kots CLI or as part of the Admin Console). 
This means that the customer supplied values, license values and existing values can be used to create the deployable manifests. In either scenario the resulting deployment is comprised of raw Kubernetes manifests. 
By doing this, cluster operator's are always able to view the exact diff between what is currently deployed and what the update will deploy. 
This level of change management provides the necessary transparency to provide the level of assurance that cluster operators require.

## Helm V3

KOTS will check the apiVersion supplied in the Chart.yaml file of the Helm Chart to check if Helm V3 is necessary. By default (if Chart.yaml is not supplied or apiVersion is not present), KOTS will use Helm V2 to process all Helm Charts to create deployable YAML.
Optionally, in the KOTS [`HelmChart`](https://kots.io/reference/v1beta1/helmchart/) resource an API version may be specified. When the `helmVersion` property is set to `"v3"`, KOTS will use Helm V3 to process the Helm Chart.

Helm V3 integration is currently a beta feature. 
In the future, KOTS may be updated to use Helm V3 by default.
