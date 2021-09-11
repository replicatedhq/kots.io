---
date: 2021-09-10
linktitle: "app delete"
title: "app delete"
weight: 90105
draft: false
---

Delete a kots app.

### Usage
```bash
replicated app delete NAME
```

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `-h, --help`   |  |          help for the command |
| `-f, --force`  |  | Skip confirmation prompt. There is no undo for this action.
| `--token string` | |  The API token to use to access your app in the Vendor API (default uses `$REPLICATED_API_TOKEN` env variable) |

### Examples
```bash
replicated app delete laverya-deletion-example
  • Fetching App ✓
ID                             NAME                        SLUG                        SCHEDULER
1xyAIzrmbvqHzzcKPbTmmrTxhl4    laverya-deletion-example    laverya-deletion-example    kots
Delete the above listed application? There is no undo: yes█
  • Deleting App ✓
```