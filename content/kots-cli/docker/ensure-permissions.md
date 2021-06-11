---
date: 2021-06-10
linktitle: "ensure-secret"
title: kots docker ensure-secret
weight: 90261
---

Creates an image pull secret that the Admin Console can utilize in case of [rate limiting](/kots-cli/docker/_index.md).
Will validate the credentials before creating the image pull secret.

### Usage

```bash
kubectl kots docker ensure-secret [flags]
```

- _Provide `[flags]` according to the table below_

| Flag              | Type   | Description                                                         |
| ----------------- | ------ | ------------------------------------------------------------------- |
| `-h, --help`      |        | help for ensure-secret |
| `--dockerhub-username` | string | DockerHub username to be used _(required)_ |
| `--dockerhub-password` | string | DockerHub password to be used _(required)_ |
| `-n, --namespace`      | string | the namespace where the admin console is running _(required)_ |

### Example

```bash
kubectl kots docker ensure-secret --dockerhub-username sentrypro --dockerhub-password password --namespace sentry-pro
```
