---
date: 2019-10-09
linktitle: "pull"
title: kots pull
weight: 90110
---

If you’d rather use kubectl or another workflow to deploy to your cluster, you can run `kots pull` to create a directory on your workstation with the Kots application and the Kubernetes manifests. This workflow is necessary when managing a KOTS application without the use of the admin console (kotsadm).

### Usage
```bash
kubectl kots pull [upstream uri] [flags]
```
* _Replace `[upstream-uri]` with the URI for your KOTS application (required)._
* _If the KOTS application has been packaged by Replicated Vendor, the `--license-file` flag must be provided._
* _Provide `[flags]` according to the table below_

This command supports all [global flags](/kots-cli/global-flags/) and also:


| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `--downstream` |  strings |  the list of any downstreams to create/update |
| `--exclude-admin-console` |  bool  |  set to true to exclude the admin console _(only valid when `[upstream-uri]` points to a replicated app)_ |
| `--exclude-kots-kinds` |  bool  | set to true to exclude rendering kots custom objects to the base directory _(default `true`)_ |
| `-h, --help`  |          |  help for pull |
| `--helm-version` | string | the Helm version with which to render the Helm Chart _(default `"v2"`)_. This is a beta feature |
| `--image-namespace` |  string  |  the namespace/org in the docker registry to push images to _(required when `--rewrite-images` is set)_ |
| `--license-file` |  string |   path to a license file _(required when `[upstream-uri]` points to a replicated app)_ |
| `--local-path` |  string   |   specify a local-path to pull a locally available replicated app _(only valid when `[upstream-uri]` points to a replicated app)_ |
| `-n, --namespace` |  string      |   namespace to render the upstream to in the base _(default `"default"`)_ |
| `--registry-endpoint` |  string  |   the endpoint of the local docker registry to use when pushing images _(required when `--rewrite-images` is set)_|
| `--repo`  | string  |   repo uri to use when downloading a helm chart |
| `--rewrite-images` |  bool   |  set to true to force all container images to be rewritten and pushed to a local registry |
| `--rootdir` |  string  |  root directory that will be used to write the yaml to _(default `"/path/"`)_ |
| `--set`  | strings  |  values to pass to helm when running helm template |
| `--shared-password` | string  | shared password to use when deploying the admin console |

### Example
```bash
kubectl kots pull sentry/unstable --license-file ~/license.yaml
kubectl kots pull helm://stable/elasticsearch --helm-version v3
```
