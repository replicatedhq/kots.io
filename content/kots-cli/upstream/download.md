---
date: 2022-02-24
linktitle: "download"
title: kots upstream download
weight: 90182
---

The `kots upstream download` command retries downloading a failed update of the upstream application.

> Introduced in KOTS v1.65.0

### Usage
```bash
kubectl kots upstream download [app-slug] [flags]
```
* _Replace `[app-slug]` with the app slug for your KOTS application (required)._
* _Provide `[flags]` according to the table below._

| Flag                              | Type   | Description                                                                                      |
|:----------------------------------|--------|--------------------------------------------------------------------------------------------------|
| `-h, --help`                      |        | help for upstream download                                                                       |
| `--kubeconfig`                    | string | the kubeconfig to use _(default is `$KUBECONFIG`. If unset, then `$HOME/.kube/config`)_          |
| `-n, --namespace`                 | string | the namespace where the admin console is running _(required)_                                    |
| `--sequence`                      | int    | The local app sequence for the version to retry downloading _(required)_                         |
| `--skip-preflights`               | bool   | set to true to skip preflight checks                                                             |
| `--skip-compatibility-check`      | bool   | set to true to skip compatibility checks between the current kots version and the update         |
| `--wait`                          | bool   | set to false to download the update in the background (defaults to true)                         |
| `-o, --output`                    | string | output format (currently supported: json) _(defaults to plain text if not set)_                  |

### Example
```bash
kubectl kots upstream download kots-sentry --namespace kots-sentry --sequence 8
```
