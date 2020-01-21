---
date: 2019-10-09
linktitle: "channel ls"
title: "channel ls"
weight: 90110
draft: false
---

List all channels in your app

### Usage
```bash
replicated channel ls
```

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `-h, --help`   |  |          help for the command |
| `--app string` | |   The app slug or app id to use in all calls (default uses `$REPLICATED_APP` env variable) |
| `--token string` | |  The API token to use to access your app in the Vendor API (default uses `$REPLICATED_API_TOKEN` env variable) |

### Examples
```bash
replicated channel ls
ID                                  NAME        RELEASE    VERSION
QE1niv46O6RyHyzYp185mt5on1SOoVhn    Stable                     0.0.1
Ng4EzwjhEdR_XzjOi032qjDKjI4cz3qs    Beta                       0.0.1
BHrujJ-qAJiKQ2jIe8EP_GNukpegEF1o    Unstable                   0.1.2
```