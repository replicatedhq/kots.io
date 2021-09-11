---
date: 2021-09-10
linktitle: "app ls"
title: "app ls"
weight: 90105
draft: false
---

List all created kots apps, or one in particular.

### Usage
```bash
replicated app ls
```

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `-h, --help`   |  |          help for the command |
| `--token string` | |  The API token to use to access your app in the Vendor API (default uses `$REPLICATED_API_TOKEN` env variable) |

### Examples
```bash
replicated app ls
ID                                  NAME                                  SLUG                              SCHEDULER
cqzmRRIdJtZ_E82Cl36nkBDSmGrG2uoe    sentry-enterprise                     sentry-enterprise-1               kots
1xy9t8G9CO0PRGzTwSwWFkMUjZO         laverya-cli-app                       laverya-cli-app                   kots
XlkTz6qxXepG_HjK_vUFlLUY14p05wO1    laverya-default-kots                  laverya-default-kots              kots
```

```bash
replicated app ls laverya-default-kots
ID                                  NAME                    SLUG                    SCHEDULER
XlkTz6qxXepG_HjK_vUFlLUY14p05wO1    laverya-default-kots    laverya-default-kots    kots
```