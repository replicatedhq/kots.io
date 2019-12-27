---
date: 2019-12-27
linktitle: "Optional Charts"
title: Optional Charts
weight: 20530
---

Charts can be optionally included in a Replicated KOTS application. By default, an instance of a Helm chart is created for every `apiVersion: kots.io/v1beta` and `kind: HelmChart` that's found in the upstream application manifests.

To make a chart optional, add a [template-parsable](/reference/template-functions/contexts/) `exclude` attribute to the `kind: HelmChart` document. When downloading, KOTS will render this field and exclude the entire chart if the output of this field can be parsed as a boolean.
