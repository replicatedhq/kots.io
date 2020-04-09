---
date: 2019-01-23
linktitle: "Configuring Backup"
title: Configuring Backup
isAlpha: true
weight: 2
---

There are 2 required steps to enable snapshots in a KOTS application:
1. Add the [Backup](/reference/v1beta1/backup) resource to your KOTS application
1. Identify the required volumes to include in the backup

## Add A Backup Resource

To enable snapshots, add a Backup resource to the application. The minimal example of this is:

```yaml
apiVersion: velero.io/v1
kind: Backup
metadata:
  name: backup
spec: {}
```

The above YAML will enable snapshots in all instances of the application. When a snapshot is executed in the Admin Console or by a schedule, it will simply include all annotated volumes as the archive (more on annotating volumes below). This manifest supports the [optional resources](/vendor/packaging/optional-resources/) annotation so that it can be dynamically enabled based on a license field or a config option, if desired.

## Identify Volumes

By default, no volumes will be included in the backup. Any pod that mounts a volume that should be backed up must also include an annotation listing which volumes to include in the snapshot. The annotation name is `backup.velero.io/backup-volumes` and the value is a comma separated list of volumes to include in the backup.

For example, in the following Deployment, only one of the volumes will be backed up (pvc-volume). The volume named `scratch` is not included in the backup because its not listed in annotation on the pod spec.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: foo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: foo
  template:
    metadata:
      labels:
        app: foo
      annotations:
        backup.velero.io/backup-volumes: pvc-volume
    spec:
      containers:
      - image: k8s.gcr.io/test-webserver
        name: test-webserver
        volumeMounts:
        - name: pvc-volume
          mountPath: /volume-1
        - name: scratch
          mountPath: /volume-2
      volumes:
      - name: pvc-volume
        persistentVolumeClaim:
          claimName: test-volume-claim
      - name: scratch
        emptyDir: {}
```

## Application Manifests

In addition to volume data, Velero will also snapshot all of the Kubernetes objects in the namespace. Any manifest that should not be included in the snapshot should include a `velero.io/exclude-from-backup` label, for example:

```yaml
apiVersion: apps/v1
kind: Secret
metadata:
  name: sample
  labels:
    velero.io/exclude-from-backup: "true"
stringData:
  uri: Secret To Not Include
```

By default, the KOTS Admin Console will exclude itself from snapshots so that the Admin Console manifests are not included. This is to allow the Admin Console to control the restore process without being interrupted and replaced with a different version.

