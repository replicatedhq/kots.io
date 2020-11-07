---
date: 2019-10-30
linktitle: "restore ls"
title: kots restore ls
weight: 90230
---

The `kots restore ls` shows a list of all the available instance restores for disaster recovery. 
A namespace can be provided to filter restores based on the namespace the admin console is/was installed in.

### Usage
```bash
kubectl kots restore ls [flags]
```
* _Provide `[flags]` according to the table below_

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `-h, --help` | |   help for upstream |
| `-n, --namespace`| string |     filter by the namespace in which the admin console is/was installed |

### Example
```bash
kubectl kots restore ls --namespace kots-sentry
```
