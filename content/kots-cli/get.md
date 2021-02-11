---
date: 2021-01-07
linktitle: "get"
title: kots get [resource]
weight: 90185
---

The `kots get` command shows information about one or more resources.

### Usage
```bash
kubectl kots get [resource] [flags]
```

This command supports all [global flags](/kots-cli/global-flags/) and also:

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `-o, --output` | |   output format, supported values: json |

### Resources

* `apps` lists installed applications. (Note: `--namespace` flag is required.)
* `backups` lists available full snapshots (instance).
* `restores` lists created full snapshot restores.

### Example
```bash
kubectl kots get apps -n default
kubectl kots get backups
kubectl kots get restores
```
