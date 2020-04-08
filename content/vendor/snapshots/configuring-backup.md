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
apiVersion: kots.io/v1beta1
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


## Release YAML Configuration

### Backup

Kotsadm requires exactly one [velero.io/v1/Backup](https://velero.io/docs/v1.2.0/api-types/backup/) resource be included in the release yaml.
Templates are supported in this file.
All fields in the metadata are irrelevant.
This yaml is never applied directly to Kubernetes - it is saved until a backup is triggered.
After rendering it, kotsadm will pick the `includedNamespaces` and `hooks` fields from the spec.
No other fields are added to the Backup kotsadm generates and applies.

`spec.includedNamesapces` tells Velero which namespaces should be included in the snapshot.
If this is not set in the Backup from the release yaml, kotsadm will add its own namespace.
If the application has hardcocded namespaces into its yaml configs, all those namespaces should be specified in this list.

All objects in included namespaces will be snapshotted, including objects that are not found in release yaml and are dynamically created by other components or manually created by users with kubectl.
There are two exceptions:

1. If the Backup includes the same namespace that kotsadm is running in, then kotsadm will add a label selector to the Backup spec to exclude all kotsadm objects.
2. Invidual resources in your release can be excluded from the backup by adding the `velero.io/exclude-from-backup=true` label to them.

#### Example

Below is an example of a Backup that hardcodes a namespace and adds some pre-exec hooks.

```yaml
apiVersion: velero.io/v1
kind: Backup
metadata:
  name: backup
spec:
  includedNamespaces:
  - test
  hooks:
    resources:
    - name: echo-hook
      includedNamespaces:
      - '*'
      includedResources:
      - 'pods'
      labelSelector:
        matchLabels:
          app: example
          component: nginx
      pre:
      - exec:
          command: ["/bin/bash", "-c", "echo hello"]
      - exec:
          command: ["/bin/bash", "-c", "echo $(date) > /scratch/timestamp"]
      - exec:
          command: ["/bin/bash", "-c", "head -c 1G </dev/urandom >/scratch/data"]
          timeout: 3m
          onError: Continue
```

#### Hooks

The hooks will be copied from the release's Backup template to the Backup generated and applied by kotsadm.
It is possible to specify pre-exec hooks to run before the backup and post-exec hooks to run after the backup.
The recommended usage is to create db dumps to a directory with an empty volume in the pre-exec hook and to remove the dump from that directory in the post-exec hook.

It is not possible to run hooks during restore.
Use initContainers to load data from db dumps at restore time.
Note that you will have to place your db dumps in separate pods from your db for databases such as postgres that are required to be running to load a dump.
