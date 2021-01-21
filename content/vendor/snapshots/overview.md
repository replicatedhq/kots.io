---
date: 2019-01-23
linktitle: "Overview"
title: Snapshots
isBeta: true
weight: 1
aliases:
  - /vendor/snapshots/
---

An important part of the lifecycle of an application is backup and restore. 
A KOTS application can optionally include manifest that define how to execute a snapshot and how to restore a previous snapshot.

To enable Snapshots, KOTS uses the [Velero open source project](https://velero.io/). Velero is a mature, fully-featured application that can back up Kubernetes manifests and persistent volumes.

In addition to the default functionality that Velero provides, KOTS provides a detailed interface in the [Admin Console](/kotsadm/snapshots/snapshot-destinations) that can be used to manage the snapshot destination and schedule, and to perform and monitor the backup and restore process. This feature is only available for licenses that have the Allow Snapshots feature enabled.

Finally, KOTS exposes hooks that can be used to inject scripts to execute both [before and after a backup](/vendor/snapshots/configuring-backup) and [before and after a restore](/kotsadm/snapshots/restore).

## Velero version compatibility

| KOTS Version(s) | Velero Version |
|------|-------------|
| 1.15 to 1.20.2 | 1.2.0 |
| 1.20.3+ | 1.5.1 |
