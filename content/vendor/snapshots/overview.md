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

In addition to the default functionality that Velero provides, KOTS provides a detailed interface in the Admin Console that can be used to manage the snapshot destination and schedule, and to perform and monitor the backup and restore process.

Finally, KOTS exposes hooks that can be used to inject scripts to execute both [before and after a backup](/vendor/snapshots/configuring-backup) and [before and after a restore](/vendor/snapshots/configuring-restore).

## Velero version compatibility

| KOTS Version | Velero Version |
|------|-------------|
| 1.15 | 1.2.0 |
| 1.16 | 1.2.0 |
| 1.17 | 1.2.0 |
