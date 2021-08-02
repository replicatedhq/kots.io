---
date: 2021-01-07
linktitle: "apps"
title: kots get apps
weight: 90200
---

The `kots get apps` command lists installed applications.

### Usage

```bash
kubectl kots get apps [flags]
```

- _Provide `[flags]` according to the table below_

| Flag              | Type   | Description                                                         |
| :---------------- | ------ | ------------------------------------------------------------------- |
| `-h, --help`      |        | help for get apps                                                   |
| `-n, --namespace` | string | the namespace where the admin console is running _(required)_       |

### Example

```bash
kubectl kots get apps -n default
```
