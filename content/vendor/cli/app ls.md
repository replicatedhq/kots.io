---
date: 2021-09-10
linktitle: "app ls"
title: "app ls"
weight: 90105
draft: false
---

List all created kots apps, or specify the kots app you want to list.

### Usage
```bash
replicated app ls
```

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `-h, --help`   |  |          Help for the command |
| `--token string` | |  The API token used to access your app in the Vendor API (default uses `$REPLICATED_API_TOKEN` env variable) |

### Examples

List all created kots apps:

```bash
replicated app ls
ID                                  NAME                       SLUG                      SCHEDULER
cqzmRRIdJtZ_E82Cl36nkBDSmGrG2uoe    sentry-enterprise          sentry-enterprise-1       kots
1xy9t8G9CO0PRGzTwSwWFkMUjZO         cli-app                    cli-app                   kots
XlkTz6qxXepG_HjK_vUFlLUY14p05wO1    default-kots               default-kots              kots
```
List a specific kots app:

```bash
replicated app ls default-kots
ID                                  NAME            SLUG            SCHEDULER
XlkTz6qxXepG_HjK_vUFlLUY14p05wO1    default-kots    default-kots    kots
```
