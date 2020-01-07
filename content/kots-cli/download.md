---
date: 2019-10-09
linktitle: "download"
title: kots download
weight: 90120
---

Retrieves a copy of the application manifests from the cluster, and store them in a specific directory structure on your workstation. Requires a running KOTS application with the Admin Console (kotsadm).

### Usage
```bash
kubectl kots download [app-slug] [flags]
```
* _Replace `[app-slug]` with the "slug" of your KOTS application_ 
* _Provide `[flags]` according to the table below_ 

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `--dest` | string |        the directory to store the application in (default "/Users/austin") |
| `-h, --help`   |  |           help for download |
| `--kubeconfig` | string |  the kubeconfig to use (default "/Users/austin/.kube/config") |
| `-n, --namespace` | string |    the namespace to download from (default "default") |
| `--overwrite` |   |       overwrite any local files, if present |

### Example
```bash
kubectl kots download kots-sentry --namespace kots-sentry --dest ./manifests --overwrite
```