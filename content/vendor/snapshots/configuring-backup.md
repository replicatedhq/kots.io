---
date: 2019-01-23
linktitle: "Configuring Snapshots"
title: Configuring Snapshots
weight: 2
---

The KOTS Snapshots feature is a backup and restore option that lets you define how to execute a snapshot and how to restore a previous snapshot. Snapshots offers additional backup and restore management features through the admin console interface and the KOTS CLI. It also provides hooks to inject scripts. For more information, see Snapshots Overview.

This procedure explains how to install Snapshots on an existing cluster.

**Prerequisites:**

* KOTS licenses that have the Allow Snapshots feature enabled

1. Install Velero. KOTS Snapshots uses the Velero open source project on the backend for backup and restore. The full procedure for this step is in the [Velero documentation](https://velero.io/docs/v1.5/basic-install/).

    1. Requires access to a Kubernetes cluster, v1.12 or later, with DNS and container networking enabled.</li>
    1. `kubectl` must be installed locally.
    1. Install the correct version of Velero CLI for your operating system.
    1. Install and configure the server components.
    1. (Optional) Install and configure the command line auto-completion tool.

  **Note:** For information about supported versions of Velero, see [Snapshots Overview](https://kots.io/vendor/snapshots/overview/).


1. To enable snapshots:

  1. Add a backup resource to the application using the Velero manifest.

      Example:

      ```yaml
      apiVersion: velero.io/v1
      kind: Backup
      metadata:
        name: backup
      spec: {}

      ```

    This minimal example YAML enables Snapshots in all instances of the application.
    When a snapshot is executed in the admin console or by a schedule based on this example, Snapshots will include all annotated volumes as the archive (see the additional information on annotating volumes later in this task).

  1. (Optional) Configure the [optional resources](/vendor/packaging/include-resources/) annotation in the manifest so that it can be dynamically enabled based on a license field or a config option, if desired.

    **Note:** if you are using multiple applications, each application should have a [backup resource](/reference/v1beta1/backup/) in each application's manifest so that each application can be included in the [Full Snapshot](/kotsadm/snapshots/overview/#full-snapshots-recommended) backup.

1. Configure a backup for any volumes that require backup. By default, no volumes are included in the backup.  If any pods mount a volume that should be backed up, you must configure the backup with an annotation listing the specific volumes to include in the snapshot.

    The annotation name is `backup.velero.io/backup-volumes` and the value is a comma separated list of volumes to include in the backup.

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

1. (Optional) Configure manifest exclusions. By default, Velero also includes snapshots of all of the Kubernetes objects in the namespace.
To exclude any manifest, add a `velero.io/exclude-from-backup` label to the manifest to be excluded.

    Example:

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

1. (Optional) [Configure backup hooks](https://kots.io/vendor/snapshots/backup-hooks/).

1. [Create a snapshot in the admin console](https://kots.io/kotsadm/snapshots/overview/#full-snapshots-recommended) or using the [KOTS CLI 'backup` command](https://kots.io/kots-cli/backup/).

## Resources
  * [Velero API information](https://velero.io/docs/v1.5/api-types/)
  * [Including and excluding resources](https://kots.io/vendor/packaging/include-resources/)
  * [About backup resources](https://kots.io/reference/v1beta1/backup/)
