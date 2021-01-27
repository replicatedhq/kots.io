---
date: 2019-10-09
linktitle: "install"
title: kots install
weight: 90100
---

Installs the KOTS application and the Admin Console directly to a cluster.
The `kots install` command pulls Kubernetes manifests from the remote upstream, deploys the manifests to the specified cluster, installs the admin console, and sets up port forwarding to make the Admin Console accessible.

### Usage

```bash
kubectl kots install [upstream uri] [flags]
```

- _Replace `[upstream-uri]` with the URI for your KOTS application (required)._
- _If the KOTS application has been packaged by Replicated Vendor, the `--license-file` flag must be provided._
- _Provide `[flags]` according to the table below_

This command supports all [global flags](/kots-cli/global-flags/) and also:

| Flag                | Type   | Description                                                                                                                          |
| :------------------ | ------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| `-h, --help`        |        | help for install                                                                                                                     |
| `--license-file`    | string | path to a license file _(required when `[upstream-uri]` points to a replicated app)_                                                 |
| `--local-path`      | string | specify a local-path to test the behavior of rendering a replicated app locally _(only supported on replicated app types currently)_ |
| `--name`            | string | name of the application to use in the Admin Console                                                                                  |
| `-n, --namespace`   | string | the namespace to deploy to                                                                                                           |
| `--shared-password` | string | shared password to apply                                                                                                             |
| `--config-values`   | string | path to a manifest containing config values (must be apiVersion: kots.io/v1beta1, kind: ConfigValues                                 |
| `--http-proxy`      | string | sets HTTP_PROXY environment variable in all KOTS Admin Console components                                                            |
| `--https-proxy`     | string | sets HTTPS_PROXY environment variable in all KOTS Admin Console components                                                           |
| `--no-proxy`        | string | sets NO_PROXY environment variable in all KOTS Admin Console components                                                              |
| `--copy-proxy-env`  | bool   | copy proxy environment variables from current environment into all KOTS Admin Console components                                     |
| `--skip-preflights` | bool   | set to true to skip preflight checks                                                                                                 |

<!-- Helm Options coming soon -->
<!-- | `--repo`            | string  | repo uri to use when installing a helm chart                                                                                         | -->
<!-- | `--set` | strings | values to pass to helm when running helm template | -->

### Examples

```bash
kubectl kots install sentry/unstable --license-file ~/license.yaml
kubectl kots install kots-sentry/stable --shared-password IgqG5OBc9Gp --license-file ~/sentry-license.yaml --namespace sentry-namespace --config-values ~/config-values.yaml
```
