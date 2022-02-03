---
date: 2021-12-01
linktitle: "config"
title: kots get config
weight: 90202
---

The `kots get config` command returns the **configValues** file for an application.

### Usage

```bash
kubectl kots get config [flags]
```

- _Provide `[flags]` according to the table below_

| Flag              | Type   | Description                                                                                       |
| :---------------- | ------ |---------------------------------------------------------------------------------------------------|
| `--appslug`       | string | The app slug for which the config will be retrieved _(required if more than one app is deployed)_ |
| `--sequence`      | int    | The app sequence for which the config will be retrieved _(default: latest)_                       |
| `--decrypt`       | bool   | Decrypt password items within the config                                                          |
| `-h, --help`      |        | help for get config                                                                               |
| `-n, --namespace` | string | the namespace where the admin console is running _(required)_                                     |

### Example

```bash
kubectl kots get config -n default --sequence 5 --appslug myapp
```
