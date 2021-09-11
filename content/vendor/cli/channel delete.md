---
date: 2021-09-10
linktitle: "channel rm"
title: "channel rm"
weight: 90110
draft: false
---

Delete (archive) a channel.

### Usage
```bash
replicated channel rm CHANNEL_ID
```

Only the channel ID will work, not the name.

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `-h, --help`   |  |          help for the command |
| `--app string` | |   The app slug or app id to use in all calls (default uses `$REPLICATED_APP` env variable) |
| `--token string` | |  The API token to use to access your app in the Vendor API (default uses `$REPLICATED_API_TOKEN` env variable) |

### Examples
```bash
replicated channel rm 1xyB2Mgbg9N7rExShfbdBYIuzeW
Channel 1xyB2Mgbg9N7rExShfbdBYIuzeW successfully archived
```
