---
date: 2019-10-09
linktitle: "download"
title: kots download
weight: 90120
---

Retrieves a copy of the application manifests from the cluster, and store them in a specific directory structure on your workstation. 
Requires a running KOTS application with the Admin Console.

### Usage
```bash
kubectl kots download [app-slug] [flags]
```

* _Replace `[app-slug]` with the "slug" of your KOTS application (required)._
* _Provide `[flags]` according to the table below_

This command supports all [global flags](/kots-cli/global-flags/) and also:


| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `--decrypt-password-values` | bool | decrypt password values to plaintext |
| `--dest` | string |        the directory to store the application in _(defaults to current working dir)_ |
| `-h, --help`   |  |           help for download |
| `-n, --namespace` | string |    the namespace to download from _(default `"default"`)_ |
| `--overwrite` |   |       overwrite any local files, if present |

### Example
```bash
kubectl kots download kots-sentry --namespace kots-sentry --dest ./manifests --overwrite
```
