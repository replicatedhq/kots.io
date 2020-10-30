---
date: 2019-10-30
linktitle: "restore create"
title: kots restore create
weight: 90220
---

The `kots restore create` creates a new instance restore request from a specified backup for disaster recovery. 

### Usage
```bash
kubectl kots restore create [flags]
```
* _Provide `[flags]` according to the table below_

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `-h, --help` | |   help for upstream |
| `--from-backup`| string |     the name of the backup to restore from |

### Example
```bash
kubectl kots restore create --from-backup sentry-942kf
```
