---
date: 2021-09-10
linktitle: "channel inspect"
title: "channel inspect"
weight: 90110
draft: false
---

Show the full details for a channel.

### Usage
```bash
replicated channel inspect CHANNEL_ID
```

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `-h, --help`   |  |          help for the command |
| `--app string` | |   The app slug or app id to use in all calls (default uses `$REPLICATED_APP` env variable) |
| `--token string` | |  The API token to use to access your app in the Vendor API (default uses `$REPLICATED_API_TOKEN` env variable) |

### Examples
```bash
replicated channel inspect cli-created
ID:             1xyB2Mgbg9N7rExShfbdBYIuzeW
NAME:           cli-created
DESCRIPTION:
RELEASE:        1
VERSION:        0.0.1
EXISTING:

    curl -fsSL https://kots.io/install | bash
    kubectl kots install laverya-cli-app/cli-created

EMBEDDED:

    curl -fsSL https://k8s.kurl.sh/laverya-cli-app-cli-created | sudo bash

AIRGAP:

    curl -fSL -o laverya-cli-app-cli-created.tar.gz https://k8s.kurl.sh/bundle/laverya-cli-app-cli-created.tar.gz
    # ... scp or sneakernet laverya-cli-app-cli-created.tar.gz to airgapped machine, then
    tar xvf laverya-cli-app-cli-created.tar.gz
    sudo bash ./install.sh airgap

```

```bash
replicated channel inspect 1xyB2Mgbg9N7rExShfbdBYIuzeW
ID:             1xyB2Mgbg9N7rExShfbdBYIuzeW
NAME:           cli-created
DESCRIPTION:    this is a description for a channel
RELEASE:        1
VERSION:        0.0.1
```
