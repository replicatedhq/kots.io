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

NOTE: `--namespace` flag is required.

`apps` is the only supported resource at this time.

### Example
```bash
kubectl kots get apps -n default
```
