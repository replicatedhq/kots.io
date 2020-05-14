---
date: 2020-04-29
linktitle: "policy assign"
title: "policy assign"
weight: 90133
draft: false
---

Assigns a policy to a channel

### Synopsis

Assigns a policy to a release channel.

### Usage
```bash
replicated enterprise policy assign [flags]
```


| Flag                  | Type   | Description |
|-----------------------|--------|-------------|
| `-h, --help` | | help for assign |
| `--policy-id` | string | The id of the policy to be assigned |
| `--channel-id` | string | The id of the channel to assign the policy to |

### Examples

```bash
replicated enterprise policy assign --policy-id "1bHLBmmybKi4uASV1TDHldZCB6H" --channel-id "1aUcp52Hcvval50e1gyYaoW5oUO"

Policy successfully assigned
```
