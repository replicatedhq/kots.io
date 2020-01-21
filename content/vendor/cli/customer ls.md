---
date: 2019-10-09
linktitle: "customer ls"
title: "customer ls"
weight: 90117
draft: false
---

List all customers in your app

### Usage
```bash
replicated customers ls
```

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `-h, --help`   |  |          help for the command |
| `--app string` | |   The app slug or app id to use in all calls (default uses `$REPLICATED_APP` env variable) |
| `--token string` | |  The API token to use to access your app in the Vendor API (default uses `$REPLICATED_API_TOKEN` env variable) |

### Examples
```bash
replicated customer ls
ID                                  NAME                            CHANNELS         EXPIRES    TYPE
iEgJuVDHy2pi-AqOjLXbZCTX9bqlV6YH    John Smith                      Unstable         Never      
YAg7ripYbK0tM5MVn_81nMy0YrhBsHrm    Megacorp                        Megacorp_Beta    Never      
```