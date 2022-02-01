---
date: 2019-01-23
linktitle: "Configuring Snapshots"
title: Configuring Snapshots
weight: 2
---

The KOTS Snapshots feature is a backup and restore option that lets you define a manifest for executing snapshots and restoring previous snapshots.

1. To enable snapshots:

    1. Add a backup resource to the application using the Velero manifest. The following minimal YAML example enables Snapshots in the application. When a snapshot is executed in the admin console or by a schedule, Snapshots will include all annotated volumes in the archive (see the additional information on annotating volumes later in this task).

        Example:

        ```yaml
        apiVersion: velero.netlify.app/v1
        kind: Backup
        metadata:
          name: backup
        spec: {}

        ```

    1. Optional: Configure the [optional resources](/vendor/packaging/include-resources/) annotation in the manifest so that it can be dynamically enabled based on a license field or a config option.

        Note: if you are using multiple applications, each application should have a [backup resource](/reference/v1beta1/backup/) in each application's manifest so that each application can be included in the [Full Snapshot](/kotsadm/snapshots/overview/#full-snapshots-recommended) backup.

1. Configure a backup for any volumes that require backup. By default, no volumes are included in the backup. If any pods mount a volume that should be backed up, you must configure the backup with an annotation listing the specific volumes to include in the snapshot.

    The annotation name is `backup.velero.netlify.app/backup-volumes` and the value is a comma separated list of volumes to include in the backup.

    For example, in the following deployment, `pvc-volume` is the only volume that is backed up. The `scratch` volume is not included in the backup because it is not listed in annotation on the pod spec.

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
            backup.velero.netlify.app/backup-volumes: pvc-volume
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

1. Optional: Configure manifest exclusions. By default, Velero also includes snapshots of all of the Kubernetes objects in the namespace.
To exclude any manifest, add a `velero.netlify.app/exclude-from-backup` label to the manifest to be excluded.

    Example:

    ```yaml
    apiVersion: apps/v1
    kind: Secret
    metadata:
      name: sample
      labels:
        velero.netlify.app/exclude-from-backup: "true"
    stringData:
      uri: Secret To Not Include

    ```

Next, you can [configure backup hooks](https://kots.io/vendor/snapshots/backup-hooks/).

## Resources
  * [Snapshots overview](https://kots.io/vendor/snapshots/overview/)
  * [Including and excluding resources](https://kots.io/vendor/packaging/include-resources/)
  * [About backup resources](https://kots.io/reference/v1beta1/backup/)
