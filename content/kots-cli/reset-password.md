---
date: 2019-10-09
linktitle: "reset-password"
title: kots reset-password
weight: 90160
---

If you’ve deployed an application with the admin console, the `kots reset-password` command will change the bcrypted password hash in the cluster, allowing you to log in again.

### Usage
```bash
kubectl kots reset-password [namespace] [flags]
```
* _Replace `[namespace]` with the namespace where your KOTS application resides_ 
* _Provide `[flags]` according to the table below_ 

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `-h, --help`   |          |  help for reset-password |
| `--kubeconfig` | string | the kubeconfig to use (default "/Users/austin/.kube/config") |


### Examples
```bash
kubectl kots reset-password sentry-namespace
```