---
date: 2019-01-23
linktitle: "Storage Destinations"
weight: 9
title: Compatible Backend Stores
---

A KOTS snapshot supports any compatible [Velero storage provider](https://velero.io/docs/main/supported-providers/). 
The Admin Console has built-in support for configuring AWS, GCP, Azure, S3-Compatible object store, NFS Server, or local host path as destinations.

For embedded clusters, the Admin Console is pre-configured to store backups in the locally-provisioned object store. 
This is sufficient for rollbacks and downgrades, but not a suitable configuration for disaster recovery. 
Embedded cluster installations should visit the "Snapshots" page in the Admin Console and configure a storage destination that is external to the cluster.

If the Admin Console is running with [minimal role-based-access-control (RBAC) privileges](/vendor/packaging/rbac/#namespace-scoped-access), the [`kots velero ensure-permissions` command](/kots-cli/velero/ensure-permissions/) will need to be leveraged, as the Admin Console requires access to the namespace in which Velero is installed. 

## AWS

When configuring the Admin Console to store snapshots on AWS, the following fields are available:

| Name                         | Description                                                                                                     |
|------------------------------|-----------------------------------------------------------------------------------------------------------------|
| Region                       | The AWS region that the S3 bucket is available in                                                               |
| Bucket                       | The name of the S3 bucket to use                                                                                |
| Path (optional)              | The path in the bucket to store all snapshots in                                                                |
| Access Key ID (optional)     | The AWS IAM Access Key ID that can read from and write to the bucket                                            |
| Secret Access Key (optional) | The AWS IAM Secret Access Key that is associated with the Access Key ID                                         |
| Use Instance Role            | When enabled, instead of providing an Access Key ID and Secret Access Key, Velero will use an instance IAM role |

## GCP

When configuring the Admin Console to store snapshots on GCP, the following fields are available:

| Name            | Description                                                                                               |
|-----------------|-----------------------------------------------------------------------------------------------------------|
| Bucket          | The name of the GCP storage bucket to use                                                                 |
| Path (optional) | The path in the bucket to store all snapshots in                                                          |
| Service Account | The GCP IAM Service Account JSON file that has permissions to read from and write to the storage location |

## Azure

When configuring the Admin Console to store snapshots on a Azure, the following fields are available:

| Name                       | Description                                                                                                                                |
|----------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Bucket                     | The name of the Azure Blob Storage Container to use                                                                                        |
| Path (optional)            | The path in the Blob Storage Container to store all snapshots in                                                                           |
| Resource Group             | The Resource Group name of the target Blob Storage Container                                                                               |
| Storage Account            | The Storage Account Name of the target Blob Storage Container                                                                              |
| Subscription ID            | The Subscription ID associated with the target Blob Storage Container (required only for access via Service Principle or AAD Pod Identity) |
| Tenant ID                  | The Tenant ID associated with the Azure account of the target Blob Storage container (required only for access via Service Principle)      |
| Client ID                  | The Client ID of a Service Principle with access to the target Container (required only for access via Service Principle)                  |
| Client Secret              | The Client Secret of a Service Principle with access to the target Container (required only for access via Service Principle)              |
| Cloud Name                 | The Azure cloud for the target storage (options: AzurePublicCloud, AzureUSGovernmentCloud, AzureChinaCloud, AzureGermanCloud)              |

Only connections with Service Principles are supported at this time. 
Please see the [Velero Plugin for Microsoft Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure) documentation for more detail on authentication methods and setting up Azure.

## S3 Compatible

When configuring the Admin Console to store snapshots on an S3-Compatible storage, the following fields are available:

| Name                         | Description                                                                                                     |
|------------------------------|-----------------------------------------------------------------------------------------------------------------|
| Region                       | The AWS region that the S3 bucket is available in                                                               |
| Endpoint                     | The endpoint to use to connect to the bucket                                                                    |
| Bucket                       | The name of the S3 bucket to use                                                                                |
| Path (optional)              | The path in the bucket to store all snapshots in                                                                |
| Access Key ID (optional)     | The AWS IAM Access Key ID that can read from and write to the bucket                                            |
| Secret Access Key (optional) | The AWS IAM Secret Access Key that is associated with the Access Key ID                                         |
| Use Instance Role            | When enabled, instead of providing an Access Key ID and Secret Access Key, Velero will use an instance IAM role |

## Network File System (NFS)

> Introduced in KOTS v1.33.0

When configuring the Admin Console to store snapshots on an NFS server, the following fields are available:

| Name   | Description                                  |
|--------|----------------------------------------------|
| Server | The hostname or IP address of the NFS server |
| Path   | The path that is exported by the NFS server  |

An overview for configuring NFS is available [here](/kotsadm/snapshots/configuring-nfs/).

## Host Path

> Introduced in KOTS v1.33.0

When configuring the Admin Console to store snapshots on a local host path, the following fields are available:

| Name      | Description                   |
|-----------|-------------------------------|
| Host Path | A local host path on the node |

An overview for configuring a Host Path is available [here](/kotsadm/snapshots/configuring-hostpath/).
