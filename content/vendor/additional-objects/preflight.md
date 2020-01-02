---
date: 2019-11-27
linktitle: "Preflight"
title: Preflight
weight: 20030
---
A [Preflight spec](https://troubleshoot.sh/reference/preflights/) makes an application's cluster dependencies explicit, enabling KOTS and the Admin Console to provide Cluster Operators with clear feedback on any missing requirements or incompatibilities in the target environment before an application is deployed. This CR is optional for KOTS applications.

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
