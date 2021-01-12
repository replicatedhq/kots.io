---
date: 2021-01-04
linktitle: "Disaster Recovery"
title: Disaster Recovery
isBeta: true
weight: 12
---

In order to setup disaster recovery snapshots, backups should be configured to use an S3 store that exists outside of the cluster.
This is especially true for [embedded kURL installs](/kotsadm/installing/installing-embedded-cluster/).

## Existing Cluster Restore

Restore must begin with installing a version of Velero compatible with the one that was used to make the snapshot.
Please refer to Velero documnetion for [installing](https://velero.io/docs/v1.5/basic-install/) and [configuring](https://velero.io/plugins/) the plugins.
Note that Restic is required and `--restic` flag must be used with `velero install` command.

Once Velero is installed, KOTS CLI can be used to [list backups](/kots-cli/backup-ls/) and [create restores](/kots-cli/restore/)

## Online Embedded Cluster Restore

Once the [cluster is setup](/kotsadm/installing/installing-embedded-cluster/#online-installations), the procedure for restoring an online embedded cluster is the same as the one for restoring an existing cluster.

Prior to configuring Velero with the `velero install` command in this case, the `velero` namespace should be deleted:

```bash
kubectl delete ns velero
```

## Airgapped Embedded Cluster Restore

An airgapped embedded kURL cluster can be restored only if the S3 backend used for backups is accessible from the new cluster.
kURL installer must also be able to assign the same IP address to the embedded private image registry in the new cluster.
kURL installer must be provided with the correct registry IP address:

```bash
cat install.sh | sudo bash -s airgap kurl-registry-ip=<ip>
```

Please note that the registry from the old cluster does not need to be (and should not be) accessible.

Prior to configuring Velero with the `velero install` command in this case, the `velero` namespace should be deleted:

```bash
kubectl delete ns velero
```

For more details refer to the [document about installing an airgapped kURL cluster](/kotsadm/installing/installing-embedded-cluster/#airgapped-installations)
