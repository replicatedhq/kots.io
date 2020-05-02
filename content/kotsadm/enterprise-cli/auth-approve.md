---
date: 2020-04-29
linktitle: "auth approve"
title: "auth approve"
weight: 90102
draft: false
---

Approve an auth key request

### Synopsis

Approve an auth key request given a fingerprint

### Usage
```bash
replicated enterprise auth approve [flags]
```


| Flag                  | Type   | Description |
|-----------------------|--------|-------------|
| `-h, --help` | | help for approve |
| `--fingerprint` | string | The fingerprint provided on auth init |

### Examples

```bash
replicated enterprise auth approve --fingerprint "SHA256:SzK7n2nrC4igO+wGo+ncaYRICpH0AEoCRPuFUe1mUKI"

Authentication request approved successfully
```
