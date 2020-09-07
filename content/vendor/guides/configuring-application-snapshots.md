Enabling Snaphots in your KOTS applicaiton is requires a few simple steps.

## Steps

### Add Backup Resource File

This enables backup in your application

### Configure Volumes to be part of snaphot

Volumes by default are not part of the snapshot. You configure which volumes to be part of the snaphot by adding a label annotation.

### Optional/Advanced Use Case: Backup Hooks

If you want to perform an action before a snapshot is peformed (i.e., run pg_dump), back up hooks allow you to do this.

## Configuring the Cluster

The cluster has to have Velero

### KURL Cluster

For those applications running on a KURL cluster, add the Velero add-on to the Kubernetes KURL installer.

### Other Clusters

You on your own, sucka!

