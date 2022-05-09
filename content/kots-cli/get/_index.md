---
date: 2021-01-07
linktitle: "get"
title: kots get [resource]
weight: 90185
---

The `kots get` command shows information about one or more resources.

> Introduced in KOTS v1.28.0

### Usage
```bash
kubectl kots get [resource] [flags]
```

This command supports all [global flags](/kots-cli/global-flags/) and also:

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `-o, --output` | |   output format, supported values: json |

### Resources

* `apps` lists installed applications.
* `backups` lists available full snapshots (instance).
* `config` lists the **configValues** for an application.
* `restores` lists created full snapshot restores.
* `versions` lists the versions available for a given `app-slug`.
