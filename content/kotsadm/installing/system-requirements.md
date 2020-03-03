---
date: 2019-10-09
linktitle: "System Requirements"
title: "System Requirements for KOTS Apps"
weight: 10012
draft: false
---

This page describes the system requirements for executing and installing KOTS applications. The requirements for command-line-interfaces (such as the [Vendor CLI](/vendor/cli/getting-started) and the [KOTS CLI](/kots-cli/getting-started)) are described separately, and are not subject to these requirements.

## Kubernetes Version Compatibility

Each release of KOTS maintains compatability with the current Kubernetes version, and the 2 most recent versions. This includes support against all patch releases of the corrersponding Kubernetes version.

| KOTS Version                 | Release Date | Kubernetes Compatibility |
|:----------------------|------|-------------|
| 1.11 | 01-22-20 | 1.17, 1.16, and 1.15 |
| 1.12 | 02-04-20 | 1.17, 1.16, and 1.15 |
| 1.13 | 02-04-20 | 1.17, 1.16, and 1.15 | 

## Existing Cluster Installation Requirements

Existing cluster compatibility is primarily determined through the version of kubernetes running on the cluster. Unless otherwise noted as an exception below, cluster infrastructure having compatibility for a supported version of Kubernetes will be compatible with Replicated. This excludes any specific and additional requirements imposed by software vendor. 

* _No exceptions noted._

## Embedded Cluster Installation Requirements

KOTS leverages [kURL](https://kurl.sh/) to embed a new Kubernetes cluster into existing machines. As such, any embedded installation includes additional system requirements (see [kURL System Requirements](https://kurl.sh/docs/install-with-kurl/system-requirements)).
