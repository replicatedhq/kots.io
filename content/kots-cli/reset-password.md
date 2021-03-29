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
* _Replace `[namespace]` with the namespace where the admin console and your KOTS application resides (required)._
* _Provide `[flags]` according to the table below_

This command supports all [global flags](/kots-cli/global-flags/) and also:


| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `-h, --help`   |          |  help for reset-password |
| `-n, --namespace`| string |     the namespace where the admin console is running |
| `--new-password`| string |     the new password to be used |

### Examples
```bash
kubectl kots reset-password sentry-namespace
```
