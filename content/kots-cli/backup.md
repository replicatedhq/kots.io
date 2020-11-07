---
date: 2019-10-30
linktitle: "backup"
title: kots backup
weight: 90200
---

The `kots backup` creates a new instance snapshot for disaster recovery. 

### Usage
```bash
kubectl kots backup [flags]
```
* _Provide `[flags]` according to the table below_

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `-h, --help` | |   help for upstream |
| `-n, --namespace`| string |     the namespace where the admin console is running _(default `"default"`)_ |

### Example
```bash
kubectl kots backup --namespace kots-sentry
```
