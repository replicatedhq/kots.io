---
date: 2019-01-23
linktitle: "Snapshots"
title: Snapshots
aliases:
  - /vendor/snapshots/
---

## Application YAML

Enable snapshots in your app with the field `spec.allowSnapshots: true` in your kots.io/v1beta1 Application yaml.

```
piVersion: kots.io/v1beta1
kind: Application
metadata:
  name: app-slug
spec:
  title: App Name
  allowSnapshots: true
```
