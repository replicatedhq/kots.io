---
date: 2019-10-09
linktitle: "upgrade"
title: kots admin-console upgrade
weight: 90010
---

Upgrades Admin Console to match the version of KOTS CLI


### Usage
```bash
kubectl kots admin-console upgrade [flags]
```

This command supports all [global flags](/kots-cli/global-flags/) and also:

| Flag              | Type   | Description                                                                    |
|:------------------|--------|--------------------------------------------------------------------------------|
| `-h, --help`      |        | help for admin-console                                                         |
| `-n, --namespace` | string | the namespace where the admin console is running _(default "default")_         |
| `--wait-duration  | string | timeout out to be used while waiting for individual components to be ready.  must be in [Go duration](https://pkg.go.dev/time#ParseDuration) format (eg: 10s, 2m). |
| `--with-minio`    | bool   | when set, kots will deploy a local minio instance for storage _(default true)_ |
### Examples
```bash
kubectl kots admin-console upgrade --namespace kots-sentry
```
