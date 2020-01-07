---
date: 2019-10-09
linktitle: "upload"
title: kots upload
weight: 90140
---

Upload Kubernetes manifests from the local filesystem to a cluster, creating a new version of the application that can be deployed. When you have a copy of an application that was created with `kots pull` or `kots download`, you can upload it back to the Admin Console using the `kots upload` command. 

### Usage
```bash
kubectl kots upload [source] [flags]
```
* _Replace `[source]` with a directory containing the manifests of your KOTS application_ 
* _Provide `[flags]` according to the table below_ 

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `-h, --help`  |       |       help for upload |
| `--kubeconfig`| string |    the kubeconfig to use (default "/Users/austin/.kube/config") |
| `--name`| string |          the name of the kotsadm application to create |
| `-n, --namespace`| string |     the namespace to upload to (default "default") |
| `--slug`| string |          the application slug to use. if not present, a new one will be created |
| `--upstream-uri`| string |  the upstream uri that can be used to check for updates |


### Examples
```bash
kubectl kots upload ./manifests --name kots-sentry --namespace kots-sentry --slug kots-sentry --upstream-uri kots-sentry/unstable
```