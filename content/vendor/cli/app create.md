---
date: 2021-09-10
linktitle: "app create"
title: "app create"
weight: 90105
draft: false
---

Create a kots app.

### Usage
```bash
replicated app create NAME
```

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `-h, --help`   |  |          Help for the command |
| `--token string` | |  The API token used to access your app in the Vendor API (default uses `$REPLICATED_API_TOKEN` env variable) |

### Examples
```bash
replicated app create cli-app
ID                             NAME          SLUG               SCHEDULER
1xy9t8G9CO0PRGzTwSwWFkMUjZO    cli-app       cli-app            kots
```
