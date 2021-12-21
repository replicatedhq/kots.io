---
date: 2020-08-14
linktitle: Stateful Components
title: Managing Admin Console State
weight: 10065
---

The KOTS Admin Console requires persistent storage for state.
When installing KOTS to an existing Kubernetes cluster, the installer will provision the required stateful components using the default StorageClass in the cluster.
The only requirement is that a default StorageClass be present.

# Postgresql

KOTS uses a Postgresql StatefulSet to store the version history, application metadata and other small amounts of data needed to manage the application(s).
This Postgresql component is deployed with KOTS and secured with a randomly generated password, and only exposed as a ClusterIP on the overlay network.

# S3-Compatible Object Store

By default, KOTS also requires an S3-compatible object store for application archives and support bundles. KOTS can be deployed and upgraded to remove this requirement, as described below. 

## Embedded Clusters

By default, embedded cluster installers must include an add-on that satisfies the S3-compatible object store.
This could be the Rook or Minio add-ons.
Without one of these add-ons, the installer will fail with an error.

Embedded Clusters can have optional, indirect dependencies on the S3-compatible object store. 
1. The Registry add-on, used for air-gapped, embedded installs, will use object storage if available. Otherwise it will use the default Storage Class.
1. The Velero add-on, used for snapshots, will default to saving snapshots in the object store (i.e. the **Internal** option in the console).

To deploy an embedded cluster without object storage, set the `disableS3` flag to `true` in the KOTS add-on.
This will deploy KOTS without object storage, as well as allow the supporting add-ons to use Persistent Volumes (PVs) instead of object storage.
The behavior of this flag is described in the [KOTS add-on](https://kurl.sh/docs/add-ons/kotsadm) documentation.

See [Removing Minio](https://kurl.sh/docs/install-with-kurl/removing-minio) for documentation on migrating a cluster away from object storage.

## Existing Clusters

By default, a KOTS installation to an existing cluster will deploy Minio to satisfy the object storage requirement, and nothing further is required during installation.
When deploying, Minio is configured with a randomly generated `AccessKeyID` and `SecretAccessKey`, and only exposed as a `ClusterIP` on the overlay network.

When the [install command](https://kots.io/kots-cli/install/) flag `--with-minio=false` is used, the installer will not deploy Minio.
KOTS will deployed as a Statefulset with attached PV instead of a Deployment.

When the [admin-console upgrade command](https://kots.io/kots-cli/upgrade/) flag `--with-minio=false` is used, an existing KOTS admin console will be upgraded to the latest version, the running Deployment will be replaced with a Statefulset and Minio will be removed after a data migration. 
This will result in temporary downtime for the KOTS admin console, but deployed applications will be unaffected.
