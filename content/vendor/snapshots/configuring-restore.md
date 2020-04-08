---
date: 2019-01-23
linktitle: "Configuring Restore"
title: Configuring Restore
isAlpha: true
weight: 3
---

Click Restore on a snapshot on the `/apps/<slug>/snapshots` page to click a restore.

First kotsadm will undeploy the application version by running `kubectl delete --wait` on the release yaml.
Then it will create the Restore resource in the velero namespace to trigger the restore.


Restored pods will have an extra initContainer and an extra directory named `.velero`.
[How backups and restore work](https://velero.io/docs/v1.2.0/restic/#how-backup-and-restore-work-with-restic).
