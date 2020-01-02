---
date: 2020-01-02
linktitle: "HelmChart"
title: HelmChart
weight: 20070
---
A [KOTS HelmChart custom resource](/v1beta1/helmchart) enables KOTS to process and deploy Helm charts as part of a Vendor distributed application. HelmChart CRs are required for KOTS to deploy Helm charts (but not necessary if only raw K8s manifests are being deployed). This spec references a required `.tgz` export of the Helm chart resources and provides the necessary instructions for processing and preparing the chart for deployment.

### Deploying multiple instance of the same chart
Vendors must provide additional HelmChart CR for each instance of the chart that is to be deployed as part of the application. However, only one `.tgz` of the chart needs to be included in the release.
