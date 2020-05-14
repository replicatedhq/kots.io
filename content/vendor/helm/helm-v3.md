---
date: 2020-05-14
linktitle: "Helm V3"
title: Helm V3
isBeta: true
weight: 20507
---

By default, KOTS will use Helm V2 to process all Helm Charts to create deployable YAML. Optionally, in the KOTS [`HelmChart`](https://kots.io/reference/v1beta1/helmchart/) resource an API version may be specified. When the `apiVersion` property is set to `"v3"`, KOTS will use Helm V3 to process the Helm Chart.

Helm V3 integration is currently a beta feature. In the future, KOTS may be updated to use Helm V3 by default.
