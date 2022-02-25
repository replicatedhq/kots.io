---
date: 2019-12-16
linktitle: "upgrade"
title: kots upstream upgrade
weight: 90181
---

The `kots upstream upgrade` command fetches the latest version of the upstream application.
It is functionally equivalent to clicking "Check for update" in the admin console.

### Usage
```bash
kubectl kots upstream upgrade [app-slug] [flags]
```
* _Replace `[app-slug]` with the app slug for your KOTS application (required)._
* _Provide `[flags]` according to the table below._

| Flag                              | Type   | Description                                                                                      |
|:----------------------------------|--------|--------------------------------------------------------------------------------------------------|
| `-h, --help`                      |        | Help for `upstream upgrade`.                                                                        |
| `--kubeconfig`                    | string | The kubeconfig to use **Default**:`$KUBECONFIG`. If unset, then `$HOME/.kube/config`.          |
| `-n, --namespace`                 | string | (Required) The namespace where the admin console is running.                                    |
| `--deploy`                        | bool   | Ensures the latest available release is deployed.                                                 |
| `--deploy-version-label`          | string | Ensures the release with the provided version label is deployed.                                  |
| `--skip-preflights`               | bool   | Set to `true` to skip preflight checks.                                                             |
| `--airgap-bundle`                 | string | Path to the application airgap bundle where application images and metadata will be loaded.  |
| `--skip-compatibility-check`      | bool   | Set to `true` to skip compatibility checks between the current kots version and new app versions. |
| `--kotsadm-namespace`             | string | The registry namespace to use for application images.                                             |
| `--kotsadm-registry`              | string | The registry endpoint where application images will be pushed.                                  |
| `--registry-password`             | string | The password to use to authenticate with the registry.                                            |
| `--registry-username`             | string | The username to use to authenticate with the registry.                                            |
| `--disable-image-push`            | bool   | Set to true to disable images from being pushed to private registry.                              |
| `--wait`                          | bool   | Set to false to download the updates in the background. **Default**: `true`.                        |
| `-o, --output`                    | string | Output forma. **Supported format**: json. **Default**: Plain text.                  |

### Example
```bash
kubectl kots upstream upgrade kots-sentry --namespace kots-sentry
```
