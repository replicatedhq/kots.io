---
date: 2020-04-29
linktitle: "policy update"
title: "policy update"
weight: 90132
draft: false
---

update an existing policy

### Synopsis

Update an existing policy.

### Usage
```bash
replicated enterprise policy update [flags]
```


| Flag                  | Type   | Description |
|-----------------------|--------|-------------|
| `-h, --help` | | help for update |
| `--id` | string | The id of the policy to be updated |
| `--name` | string | The new name for this policy |
| `--description` | string | The new description of this policy |
| `--policy-file` | string | The filename containing an OPA policy |

### Examples

```bash
replicated enterprise policy update --id "1bHLBmmybKi4uASV1TDHldZCB6H" --name "CRDs" --description "CRDs are not encouraged" --policy-file crd.rego
ID                             NAME
1bHLBmmybKi4uASV1TDHldZCB6H    CRDs
```
