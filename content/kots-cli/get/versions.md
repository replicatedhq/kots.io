---
date: 2022-01-24
linktitle: "versions"
title: kots get versions
weight: 90203
---

The `kots get versions` command lists all versions of an application.

### Usage

```bash
kubectl kots get versions [flags]
```

- _Provide `[flags]` according to the table below_

| Flag              | Type   | Description                                                         |
| :---------------- | ------ | ------------------------------------------------------------------- |
| `-h, --help`      |        | help for get apps                                                   |
| `-n, --namespace` | string | the namespace where the admin console is running _(required)_       |

### Example

```bash
kubectl kots get versions -n default
```
