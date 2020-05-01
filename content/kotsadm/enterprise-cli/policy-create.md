---
date: 2020-04-29
linktitle: "policy create"
title: "policy create"
weight: 90130
draft: false
---

Create a new policy

### Synopsis

Create a new policy that can later be assigned to a channel.

### Usage
```bash
replicated enterprise policy create [flags]
```


| Flag                  | Type   | Description |
|-----------------------|--------|-------------|
| `-h, --help` | | help for create |
| `--name` | string | The name of this policy |
| `--description` | string | A longer description of this policy |
| `--policy-file` | string | The filename containing an OPA policy |

### Examples

```bash
replicated enterprise policy create --name "CRDs" --description "CRDs are not encouraged" --policy-file crd.rego
ID                             NAME
1bHLBmmybKi4uASV1TDHldZCB6H    CRDs
```
