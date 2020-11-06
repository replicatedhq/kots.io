---
date: 2019-10-30
linktitle: "backup ls"
title: kots backup ls
weight: 90210
---

The `kots backup ls` shows a list of all the available instance snapshots for disaster recovery. 
A namespace can be provided to filter backups based on the namespace the admin console is/was installed in.

### Usage
```bash
kubectl kots backup ls [flags]
```
* _Provide `[flags]` according to the table below_

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `-h, --help` | |   help for upstream |
| `-n, --namespace`| string |     filter by the namespace in which the admin console is/was installed |

### Example
```bash
kubectl kots backup ls --namespace kots-sentry
```
