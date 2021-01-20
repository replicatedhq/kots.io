---
date: 2021-01-19
linktitle: "Snapshot Types"
isBeta: true
weight: 9
title: Snapshot Types
---

Currently, there are two types of snapshots:
  * Instance Snapshots
  * Application Snapshots

## Instance Snapshots (Recommended)

Instance snapshots back up the Admin Console and all application data.
They can be used for full Disaster Recovery; by restoring over the same instance, or into a new cluster.

There are two ways to create an instance snapshot. First, make sure that your license has the snapshots feature enabled, then:

1. Using the KOTS CLI [backup](/kots-cli/backup/) command.
2. Using the Admin Console (check screenshot below).

![Instance Backup UI](/images/snapshot-instance-backup.png)

## Application Snapshots

Application snapshots only back up applications volumes and application manifests; they do not back up the Admin Console or the metadata about an application.
They are great for capturing information before deploying a new release, in case you need to roll back, but they are not suitable for full disaster recovery.
For backups that give you the ability to do full Disaster Recovery, use [Instance Snapshots](/kotsadm/snapshots/snapshot-types/#instance-snapshots-recommended).

Application snapshots can only be created via the Admin Console (check screenshot below).

![Application Backup UI](/images/snapshot-application-backup.png)