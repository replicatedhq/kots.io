---
date: 2020-04-29
linktitle: "installer update"
title: "installer update"
weight: 90122
draft: false
---

Update an existing installer

### Synopsis

Update an existing installer.

### Usage
```bash
replicated enterprise installer update [flags]
```


| Flag                  | Type   | Description |
|-----------------------|--------|-------------|
| `-h, --help` | | help for update |
| `--id` | string | The id of the installer to be updated |
| `--yaml-file` | string | The filename containing the installer yaml |

### Examples

```bash
replicated enterprise installer update --id "1bHJ3o4vEL7Mbwhm3bcug2HpkeY" --yaml-file custom-installer.yaml
ID
1bHJ3o4vEL7Mbwhm3bcug2HpkeY
```
