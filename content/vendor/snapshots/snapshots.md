---
date: 2019-01-23
linktitle: "Snapshots"
title: Snapshots
is_alpha: true
aliases:
  - /vendor/snapshots/
---

## Requirements

### Application YAML

Enable snapshots in your app with the field `spec.allowSnapshots: true` in your kots.io/v1beta1 Application yaml.

```
apiVersion: kots.io/v1beta1
kind: Application
metadata:
  name: app-slug
spec:
  title: App Name
  allowSnapshots: true
```

### Customer License

For each customer license there is an Allow Snapshots checkbox. Both the application yaml and the customer license must have snapshots enabled in order for the Admin Console to display snapshot UI to the user.

### Velero

The customer must have [velero](https://velero.io/) installed in the `velero` namespace along with [Restic](https://velero.io/docs/v1.2.0/restic/).

#### Modify default install (this is going away)

The following patches must be applied to the default velero install with the CLI.

Load all provider plugins as initContainers. (Velero deployment only, not applicable to restic daemonset).

[velero deployment](https://github.com/replicatedhq/kURL/blob/master/addons/velero/1.2.0/deployment.yaml#L20-L34)

Mount secrets volumes in subdirectory

[velero deployment](https://github.com/replicatedhq/kURL/blob/master/addons/velero/1.2.0/deployment.yaml#L48-L53)

[restic daemonset](https://github.com/replicatedhq/kURL/blob/master/addons/velero/1.2.0/restic-daemonset.yaml#L20-L31)

Use env vars to point to provider-specific credentials

[velero deployment](https://github.com/replicatedhq/kURL/blob/master/addons/velero/1.2.0/deployment.yaml#L64-L69)

[restic daemonset](https://github.com/replicatedhq/kURL/blob/master/addons/velero/1.2.0/restic-daemonset.yaml#L46-L51)

Use provider-specific secret volumes

[velero deployment](https://github.com/replicatedhq/kURL/blob/master/addons/velero/1.2.0/deployment.yaml#L71-L82)

[restic daemonset](https://github.com/replicatedhq/kURL/blob/master/addons/velero/1.2.0/restic-daemonset.yaml#L68-L73)


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

### Data

By default snapshots only include API objects, not any data.
Include a Pod volume in backups by adding the `backup.velero.io/backup-volumes` annotation to the Pod spec.
This example shows a Pod with a PVC volume and an emptyDir volume snapshotted.

```
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
        backup.velero.io/backup-volumes: pvc-volume,scratch
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
