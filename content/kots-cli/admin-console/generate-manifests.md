---
date: 2021-07-29
linktitle: "generate-manifests"
title: kots admin-console generate-manifests
weight: 90130
---

Running this command will create a directory on the workstation containing the KOTS Admin Console manifests. These assets can be used to deploy KOTS to a cluster through other workflows, such as kubectl, to provide additional customization of the Admin Console before deploying.

### Usage
```bash
kubectl kots admin-console generate-manifests [flags]
```

This command supports the following flags:

| Flag | Type | Description |
|:-----|------|-------------|
| `--rootdir` | string | root directory where the yaml will be written _(default `${HOME}` or `%USERPROFILE%`)_ |
| `--shared-password` | string | shared password to use when deploying the admin console |
| `--http-proxy` | string | sets HTTP_PROXY environment variable in all KOTS Admin Console components |
| `--https-proxy` | string | sets HTTPS_PROXY environment variable in all KOTS Admin Console components |
| `--no-proxy` | string | sets NO_PROXY environment variable in all KOTS Admin Console components |
| `--with-minio` | bool | set to true to include a local minio instance to be used for storage _(default true)_ |

### Examples
```bash
kubectl kots admin-console generate-manifests
kubectl kots admin-console generate-manifests --rootdir ./manifests
```
