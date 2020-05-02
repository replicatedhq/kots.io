---
date: 2020-04-29
linktitle: "auth init"
title: "auth init"
weight: 90101
draft: false
---

Initialize authentication

### Synopsis

Create a keypair to begin authentication

### Usage
```bash
replicated enterprise auth init [flags]
```


| Flag                  | Type   | Description |
|-----------------------|--------|-------------|
| `-h, --help` | | help for init |
| `--create-org` | string | If this flag is provided, a new organization will be created with the specified name. If not, the auth request will have to be approved by Replicated or your already authenticated organization |

### Examples

### With "--create-org" flag
```bash
replicated enterprise auth init --create-org "Some Big Bank"

Organization has been created successfully with the following ID: 1bGmd2sGVVpbsTKcnT0gLLeS6QH
```

### Without "--create-org" flag
```bash
replicated enterprise auth init

Your authentication request has been submitted. Please contact your organization or Replicated at support@replicated.com to complete this request with the following code: SHA256:SzK7n2nrC4igO+wGo+ncaYRICpH0AEoCRPuFUe1mUKI
```