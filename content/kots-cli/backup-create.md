---
date: 2019-10-30
linktitle: "backup create"
title: kots backup create
weight: 90200
---

The `kots backup create` creates a new instance backup request for disaster recovery. 

### Usage
```bash
kubectl kots backup create [flags]
```
* _Provide `[flags]` according to the table below_

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `-h, --help` | |   help for upstream |
| `-n, --namespace`| string |     the namespace where the admin console is running |

### Example
```bash
kubectl kots backup create --namespace kots-sentry
```
