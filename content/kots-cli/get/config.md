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
| `--appslug`       | string | The app slug of the application from which to retrieve the configValues file. Required if more than one app is deployed. |
| `--sequence`      | int    | The app sequence from which to retrieve the configValues file. Default: latest.                       |
| `--decrypt`       | bool   | Decrypt password items within the config.                                                          |
| `-h, --help`      |        | Help for get config.                                                                               |
| `-n, --namespace` | string | (Required) The namespace where the admin console is running.                                 |

### Example

```bash
kubectl kots get config -n default --sequence 5 --appslug myapp
```
