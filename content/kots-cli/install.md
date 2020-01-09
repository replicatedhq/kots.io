---
date: 2019-10-09
linktitle: "install"
title: kots install
weight: 90100
---

Installs the KOTS application and the admin console (kotsadm) directly to a cluster. The `kots install` command pulls Kubernetes manifests from the remote upstream, deploys the manifests to the specified cluster, installs the admin console, and sets up port forwarding to make the kotsadm admin console accessible.

### Usage
```bash
kubectl kots install [upstream uri] [flags]
```
* _Replace `[upstream-uri]` with the URI for your KOTS application (required)._
* _If the KOTS application has been packaged by Replicated Vendor, the `--license-file` flag must be provided._  
* _Provide `[flags]` according to the table below_ 

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `-h, --help`         |  |          help for install |
| `--hostname` | string |          the hostname to that the admin console will be exposed on _(default `"localhost:8800"`)_ |
| `--image-namespace` | string |   the namespace/org in the docker registry to push images to _(required when `--rewrite-images` is set)_|
| `--include-github`  | |          set up for github login |
| `--include-ship`    | |         include the shipinit/edit/update and watch components |
| `--kubeconfig` | string |        the kubeconfig to use _(default is `$KUBECONFIG`. If unset, then `$HOME/.kube/config`)_ |
| `--license-file` | string |      path to a license file _(required when `[upstream-uri]` points to a replicated app)_ |
| `--local-path` | string |        specify a local-path to test the behavior of rendering a replicated app locally _(only supported on replicated app types currently)_ |
| `--name` | string |              name of the application to use in the Admin Console |
| `-n, --namespace` | string |         the namespace to deploy to |
| `--node-port` | int32 |           the nodeport to assign to the service, when service-type is set to NodePort |
| `--registry-endpoint` | string | the endpoint of the local docker registry to use when pushing images _(required when `--rewrite-images` is set)_ |
| `--repo` | string |              repo uri to use when installing a helm chart |
| `--rewrite-images` |  |          set to true to force all container images to be rewritten and pushed to a local registry |
| `--service-type` | string |      the service type to create _(default `"ClusterIP"`)_ |
| `--set` | strings |                values to pass to helm when running helm template |
| `--shared-password` | string |   shared password to apply |


### Examples
```bash
kubectl kots install sentry/unstable --license-file ~/license.yaml
kubectl kots install kots-sentry/stable --shared-password IgqG5OBc9Gp --license-file ~/sentry-license.yaml --namespace sentry-namespace
kubectl kots install helm://stable/elasticsearch
```