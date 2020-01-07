---
date: 2020-01-03
linktitle: "Helm Processing"
title: Helm Processing
weight: 20505
aliases: 
  - /pagelink/helm-processing
---

The KOTS Helm integration is compatible with both Helm V2 and Helm V3 as it does not rely on Tiller to deploy the chart into the cluster. Instead, KOTS treats a Helm Chart as the packaging spec rather than the deployment tool. KOTS using Helm to create deployable YAML by leveraging the `helm template` command.

Except in the case of [Helm as KOTS application airgap installations](../helm-airgap-builder), the processing of the chart is managed in the end customer environment (via KOTS cli or KOTS as part of Kotsadm). This means that the customer supplied values, license values and existing values can be used to create the deployable manifests. 
