---
date: 2019-10-09
linktitle: "System Requirements"
title: "System Requirements for KOTS Apps"
weight: 10012
draft: false
---

This page describes the system requirements for executing and installing KOTS applications.
The requirements for command-line-interfaces (such as the [Vendor CLI](/vendor/cli/getting-started) and the [KOTS CLI](/kots-cli/getting-started)) are described separately, and are not subject to these requirements.

## Kubernetes Version Compatibility

Each release of KOTS maintains compatability with the current Kubernetes version, and the 2 most recent versions.
This includes support against all patch releases of the corrersponding Kubernetes version.

| KOTS Version | Release Date | Kubernetes Compatibility |
|------|------------|-------------|
| 1.11 | 2020-01-22 | 1.17, 1.16, and 1.15 |
| 1.12 | 2020-02-04 | 1.17, 1.16, and 1.15 |
| 1.13 | 2020-02-27 | 1.17, 1.16, and 1.15 |
| 1.14 | 2020-03-31 | 1.17, 1.16, and 1.15 |
| 1.15 | 2020-05-01 | 1.18, 1.17, and 1.16 |
| 1.16 | 2020-06-01 | 1.18, 1.17, and 1.16 |
| 1.17 | 2020-07-14 | 1.18, 1.17, and 1.16 |

## Existing Cluster Installation Requirements

Existing cluster compatibility is primarily determined through the version of kubernetes running on the cluster. 
Unless otherwise noted on this page, cluster infrastructure having compatibility for a supported version of Kubernetes will be compatible with KOTS.
This excludes any specific and additional requirements imposed by software vendor.

In additional to a valid Kubernetes version, KOTS requires an existing [storage class](https://kubernetes.io/docs/concepts/storage/storage-classes/) to be available in the cluster.

Root access on nodes or workstations is *not* required for installations to existing clusters. To perform an install, the user executing `kubectl kots install` will need either

- Existing namespace, and an RBAC binding that allows the `kubectl`-ing user to create workloads, ClusterRoles, and ClusterRoleBindings
- cluster-admin permissions to create namespaces and assign RBAC roles across the cluster

## Embedded Cluster Installation Requirements

KOTS leverages [kURL](https://kurl.sh/) to embed a new Kubernetes cluster into existing machines.
As such, any embedded installation includes additional system requirements (see [kURL System Requirements](https://kurl.sh/docs/install-with-kurl/system-requirements)).

Root access *is* required when for embedded cluster installations. 
