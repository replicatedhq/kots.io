---
date: 2019-11-27
linktitle: "Preflight"
title: Preflight
weight: 20040
---
A [Preflight spec](https://troubleshoot.sh/preflight/) makes an application's cluster dependencies explicit, enabling kots and kotsadm to provide operators with clear feedback on any missing requirements or incompatibilities in the target environment before an application is deployed.

Preflight checks can even be run independently of kots and kotsadm, via the [Preflight kubectl plugin](https://troubleshoot.sh/docs/preflight/overview/).
