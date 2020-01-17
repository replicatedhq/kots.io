---
date: 2019-10-09
linktitle: "Channel Create"
title: "Create a Channel"
weight: 90112
draft: false
---

Create a new channel in your app and print the full set of channels in the app on success.

### Usage
```bash
replicated channel create [Flags]
```

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `-name` | string |          The name of this channel |
| `--description` | string |  A longer description of this channel |
| `-h, --help`   |  |          help for the command |
| `--app string` | |   The app slug or app id to use in all calls (default uses `$REPLICATED_APP` env variable) |
| `--token string` | |  The API token to use to access your app in the Vendor API (default uses `$REPLICATED_API_TOKEN` env variable) |

### Examples
```bash
replicated channel create --name Beta --description 'New features subject to change'
```