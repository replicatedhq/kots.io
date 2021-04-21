---
date: 2019-10-09
linktitle: "push-images"
title: kots admin-console push-images
weight: 90020
---

Pushes images from an airgap bundle to a private registry.
The airgap bundle can be either an Admin Console release or an application release.

### Usage
```bash
kubectl kots admin-console push-images [airgap-bundle] [private-registry] [flags]
```

This command supports all [global flags](/kots-cli/global-flags/) and also:

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `-h, --help`   |  |          help for admin-console |
| `--registry-username` | string |   username for the private registry |
| `--registry-password` | string |   password for the private registry |

### Examples
```bash
kubectl kots admin-console push-images ./kotsadm.tar.gz private.registry.host/app-name \
  --registry-username rw-username \
  --registry-password rw-password
```
