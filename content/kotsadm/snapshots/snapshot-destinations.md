---
date: 2021-01-11
linktitle: "Snapshot Destinations"
isBeta: true
weight: 5
title: Compatible Backend Stores
---

A KOTS snapshot supports any compatible [Velero storage provider](https://velero.io/docs/main/supported-providers/). 
The Admin Console has built-in support for configuring AWS, GCP, Azure and S3-Compatible object stores as destinations.

For embedded clusters, the Admin Console is pre-configured to store backups in the locally-provisioned object store. 
This is sufficient for rollbacks and downgrades, but not a suitable configuration for disaster recovery. 
Embedded cluster installations should visit the "Snapshots" page in the Admin Console and configure a snapshot destination that is external to the cluster.

## AWS

When configuring the Admin Console to store snapshots on AWS, the following fields are available:

| Name | Description |
|------|-------------|
| Region | The AWS region that the S3 bucket is available in |
| Bucket | The name of the S3 bucket to use |
| Path (optional) | The path in the bucket to store all snapshots in |
| Access Key ID (optional) | The AWS IAM Access Key ID that can read from and write to the bucket |
| Secret Access Key (optional) | The AWS IAM Secret Access Key that is associated with the Access Key ID |
| Use Instance Role | When enabled, instead of providing an Access Key ID and Secret Access Key, Velero will use an instance IAM role |

## GCP

When configuring the Admin Console to store snapshots on GCP, the following fields are available:

| Name | Description |
|------|-------------|
| Bucket | The name of the GCP storage bucket to use |
| Path (optional) | The path in the bucket to store all snapshots in |
| Service Account | The GCP IAM Service Account JSON file that has permissions to read from and write to the storage location |

## Azure

When configuring the Admin Console to store snapshots on a Azure, the following fields are available:

| Name | Description |
|------|-------------|
| Bucket | The name of the Azure storable bucket to use |
| Path (optional) | The path in the bucket to store all snapshots in |
| Resource Group | |
| Storage Account | |
| Subscription ID | |
| Resource Group | |
| Tenant ID | |
| Client ID | |
| Client Secret | |
| Cloud Name (optional) | |

## S3 Compatible

When configuring the Admin Console to store snapshots on an S3-Compatible storage, the following fields are available:

| Name | Description |
|------|-------------|
| Region | The AWS region that the S3 bucket is available in |
| Endpoint | The endpoint to use to connect to the bucket |
| Bucket | The name of the S3 bucket to use |
| Path (optional) | The path in the bucket to store all snapshots in |
| Access Key ID (optional) | The AWS IAM Access Key ID that can read from and write to the bucket |
| Secret Access Key (optional) | The AWS IAM Secret Access Key that is associated with the Access Key ID |
| Use Instance Role | When enabled, instead of providing an Access Key ID and Secret Access Key, Velero will use an instance IAM role |
