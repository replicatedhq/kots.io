---
date: 2021-09-10
linktitle: "channel create"
title: "channel create"
weight: 90110
draft: false
---

Create a new channel in your app, and print the channel on success.

### Usage
```bash
replicated channel create --name Beta --description 'New features subject to change'
```

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `--description` | string | A longer description of this channel |
| `--name` | string | The name of this channel (**required**) |
| `-h, --help`   |  |          help for the command |
| `--app string` | |   The app slug or app id to use in all calls (default uses `$REPLICATED_APP` env variable) |
| `--token string` | |  The API token to use to access your app in the Vendor API (default uses `$REPLICATED_API_TOKEN` env variable) |

### Examples
```bash
replicated channel create --name cli-created --description "this is a description for a channel"
ID                             NAME           RELEASE    VERSION
1xyB2Mgbg9N7rExShfbdBYIuzeW    cli-created    1          0.0.1
```