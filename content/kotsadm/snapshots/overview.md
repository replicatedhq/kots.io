---
date: 2021-01-19
linktitle: "Overview"
weight: 8
title: Overview
---

An important part of the lifecycle of an application is backup and restore. The Admin Console can be used to create and manage your storage destination and schedule, and to perform and monitor the backup and restore process. This feature is only available for licenses that have the Allow Snapshots feature enabled.

Currently, there are two types of snapshots:
  * Full Snapshots (Instance)
  * Partial Snapshots (Application)
  
Snapshots are useful for rollback and disaster recovery scenarios. They are not intended to be used for application migration scenarios. 

## Full Snapshots (Recommended)

Full snapshots back up the Admin Console and all application data.
They can be used for full Disaster Recovery; by restoring over the same instance, or into a new cluster.

There are two ways to create a full snapshot. First, make sure that your license has the snapshots feature enabled, then:

1. Using the KOTS CLI [backup](/kots-cli/backup/) command.
2. Using the Admin Console (check screenshot below).

![Instance Backup UI](/images/snapshot-instance-backup.png)

After a full snapshot is created you can restore it by clicking restore icon on a full snapshot row (check screenshot below).
There are two available options for restore. You can do a full restore of the application, admin console, and databases or you can do a partial restore of just your application and its data.

![Instance Restore UI](/images/snapshot-instance-restore.png)

## Partial Snapshots

Partial snapshots only back up applications volumes and application manifests; they do not back up the Admin Console or the metadata about an application.
They are great for capturing information before deploying a new release, in case you need to roll back, but they are not suitable for full disaster recovery.
For backups that give you the ability to do full Disaster Recovery, use [Full Snapshots](/kotsadm/snapshots/overview/#full-snapshots-recommended).

Partial snapshots can only be created via the Admin Console (check screenshot below).

![Application Backup UI](/images/snapshot-application-backup.png)
