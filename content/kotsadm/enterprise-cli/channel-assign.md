---
date: 2020-04-29
linktitle: "channel assign"
title: "channel assign"
weight: 90113
draft: false
---

Assign a channel to a team

### Synopsis

Assign a channel to a vendor team.

### Usage
```bash
replicated enterprise channel assign [flags]
```


| Flag                  | Type   | Description |
|-----------------------|--------|-------------|
| `-h, --help` | | help for assign |
| `--channel-id` | string | The id of the channel to be assigned |
| `--team-id` | string | The id of the team to assign the channel to |

### Examples

```bash
replicated enterprise channel assign --channel-id "1aUcp52Hcvval50e1gyYaoW5oUO" --team-id "s4y3qclW7SHAioNWyKn2zZcHcgMHSDMK"

Channel successfully assigned
```
