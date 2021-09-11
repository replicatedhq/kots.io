---
date: 2021-09-10
linktitle: "installer ls"
title: "installer ls"
weight: 90115
---

List an app's https://kurl.sh Kubernetes Installers

### Usage
```bash
replicated installer ls
```

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `-h, --help`   |  |          help for admin-console |
| `--app string` | |   The app slug or app id to use in all calls (default uses `$REPLICATED_APP` env variable) |
| `--token string` | |  The API token to use to access your app in the Vendor API (default uses `$REPLICATED_API_TOKEN` env variable) |

### Examples


```bash
replicated installer ls
SEQUENCE    CREATED                 ACTIVE_CHANNELS
2           2021-09-11T00:06:45Z
1           2021-09-10T23:31:13Z
```
