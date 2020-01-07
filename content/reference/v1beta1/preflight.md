---
date: 2019-11-27
linktitle: "Preflight"
title: Preflight
weight: 40
aliases: 
  - /vendor/additional-objects/preflight
---
The [Replicated Troubleshoot project](https://github.com/replicatedhq/troubleshoot) provides a [Preflight spec](https://troubleshoot.sh/reference/preflights/) that KOTS integrates with to make an application's cluster dependencies explicit. This enables KOTS and the Admin Console to provide Cluster Operators with clear feedback on any missing requirements or incompatibilities in the target environment before an application is deployed. This custom resource is optional for KOTS applications.

Preflight checks can even be run independently, via the [Preflight kubectl plugin](https://troubleshoot.sh/docs/preflight/overview/).

A Preflight kind is a [Collectors](https://troubleshoot.sh/reference/collectors/overview/) and an [Analyzers](https://troubleshoot.sh/reference/analyzers/overview/) spec in one document.

```yaml
apiVersion: troubleshoot.replicated.com/v1beta1
kind: Preflight
metadata:
  name: sample
spec:
  collectors: []
  analyzers: []
```
