---
date: 2021-12-01
linktitle: "config"
title: kots get config
weight: 90202
---

The `kots get config` command returns the configValues file for an application.

### Usage

```bash
kubectl kots get config [flags]
```

- _Provide `[flags]` according to the table below_

| Flag              | Type   | Description                                                         |
| :---------------- | ------ | ------------------------------------------------------------------- |
| `--appslug`       | string | app slug to retrieve config for _(required)_                        |
| `--sequence`      | int    | app sequence to retrieve config for _(required)_                    |
| `-h, --help`      |        | help for get apps                                                   |
| `-n, --namespace` | string | the namespace where the admin console is running _(required)_       |

### Example

```bash
kubectl kots get config -n default --sequence 5 --appslug myapp
```
