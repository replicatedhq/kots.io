---
date: 2019-12-16
linktitle: "upstream"
title: kots upstream
weight: 90180
---

The `kots upstream upgrade` fetches the latest version of the upstream application. It is functionality equivalent to clicking the Check For Updates in the Admin Console. 

### Usage
```bash
kubectl kots upstream upgrade [upstream-uri] [flags]
```
* _Replace `[upstream-uri]` with the URI for your KOTS application._ 
* _Provide `[flags]` according to the table below_ 

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `-h, --help` | |   help for upstream |
| `--kubeconfig`| string |    the kubeconfig to use _(default is $KUBECONFIG. If unset, then $HOME/.kube/config)_ |
| `-n, --namespace`| string |     the namespace to upload to _(default "default")_ |

### Example
```bash
kubectl kots upstream upgrade kots-sentry/unstable --namespace kots-sentry 
```