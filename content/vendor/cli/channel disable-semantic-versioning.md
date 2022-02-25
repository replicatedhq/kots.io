---
date: 2021-09-10
linktitle: "channel disable-semantic-versioning"
title: "channel disable-semantic-versioning"
weight: 90110
draft: false
---

Disable semantic versioning for a channel.

### Usage
```bash
replicated channel disable-semantic-versioning CHANNEL_ID
```

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `-h, --help`   |  |          Help for the command |
| `--app string` | |   The app slug or app id used in all calls (default uses `$REPLICATED_APP` env variable) |
| `--token string` | |  The API token used to access your app in the Vendor API (default uses `$REPLICATED_API_TOKEN` env variable) |

### Examples
```bash
replicated channel disable-semantic-versioning 1xyB2Mgbg9N7rExShfbdBYIuzeW
Semantic versioning successfully disabled for channel 1xyB2Mgbg9N7rExShfbdBYIuzeW
```