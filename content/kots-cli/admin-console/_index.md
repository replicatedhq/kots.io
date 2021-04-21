---
date: 2019-10-09
linktitle: "admin-console"
title: kots admin-console
weight: 90000
---

Opens a port forward to Admin Console so it can be accessed from localhost.

### Usage
```bash
kubectl kots admin-console [flags]
```

This command supports all [global flags](/kots-cli/global-flags/) and also:

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `-h, --help`   |  |          help for admin-console |
| `-n, --namespace` | string |   the namespace where the admin console is running _(default "default")_ |

### Examples
```bash
kubectl kots admin-console --namespace kots-sentry
```
