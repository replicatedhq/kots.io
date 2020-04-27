---
date: 2020-04-07
linktitle: "Backup"
title: Backup
isBeta: true
description: "The Backup resource defines the steps to create and restore snapshots in the application"
weight: 7
---

A Backup resource in an application causes the Admin Console to enable [snapshots](/vendor/snapshots/overview/) for the application. This resource is [fully documented](https://velero.io/docs/v1.3.2/api-types/backup/) on velero.io.

This resource supports the [KOTS optional resources](/vendor/packaging/optional-resources/) annotations.


```yaml
apiVersion: velero.io/v1
kind: Backup
metadata:
  name: backup
  annotations:
    "kots.io/when": '{{repl ConfigOptionEquals "postgres_type" "embedded_postgres" }}'
spec: {}
```

Refer to the Velero documentation for all options in this resource.
