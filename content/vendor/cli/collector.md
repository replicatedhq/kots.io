---
date: 2019-10-09
linktitle: "Collector"
title: "Collector"
weight: 90120
draft: false
---

The collector command allows vendors to create, display, modify entitlement values for end customer licensing.

### Usage
```bash
replicated collector create      #Create a new collector
replicated collector inspect     #Print the YAML config for a collector
replicated collector cls         # List all of an app's collectors
replicated collector promote     #Set the collector for a channel
replicated collector update      #Update a collector's name or yaml config
```

* _If `upgrade` is not specified, this command opens a proxy to the admin console. Otherwise, it upgrades the admin console to the latest version._

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `-h, --help`   |  |          help for admin-console |
| `--kubeconfig` | string |  the kubeconfig to use _(default is $KUBECONFIG. If unset, then $HOME/.kube/config)_ |
| `-n, --namespace` | string |   the namespace where the admin console is running _(default "default")_ |

### Examples
```bash
kots admin-console --namespace kots-sentry
kots admin-console upgrade --namespace kots-sentry
```