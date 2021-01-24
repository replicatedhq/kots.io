---
date: 2020-12-08
linktitle: "enable-shared-password"
title: kots identity-service enable-shared-password
weight: 90240
---

Enable the shared password login option in the KOTS Admin Console.

### Usage

```bash
kubectl kots identity-service enable-shared-password [flags]
```

This command supports all [global flags](/kots-cli/global-flags/) and also:

| Flag              | Type   | Description                                      |
| :---------------- | ------ | ------------------------------------------------ |
| `-n, --namespace` | string | the namespace where the admin console is running |

NOTE: `--namespace` flag is required.

### Examples

```bash
kubectl kots identity-service enable-shared-password --namespace kots-sentry
```
