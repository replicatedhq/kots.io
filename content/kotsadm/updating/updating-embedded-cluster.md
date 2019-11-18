---
date: 2019-11-05
linktitle: "Updating an Embedded Cluster"
title: Updating an Embedded Cluster
weight: 10030
draft: false
---

This article refers to upgrading the Admin Console on an embedded cluster. When running the Admin Console on an existing cluster, refer to the [Updating the Admin Console](/kotsadm/updating/updating-admin-console/) documentation.

### Online Installations

To update the Admin Console when deployed to an embedded cluster, re-run the installation script on the first master node where the install was initialized.
All flags passed to the script for the initial install must be passed again.

```bash
curl -sSL https://kurl.sh/supergoodtool | sudo bash
```

### Airgapped Installations

To update an airgapped installation, download the new airgap bundle, untar it, and run the install.sh script.

```bash
curl -SL -o supergoodtool.tar.gz https://kurl.sh/bundle/supergoodtool.tar.gz
tar xzvf supergoodtool.tar.gz
cat install.sh | sudo bash -s airgap
```

### Updating Kubernetes

If the application vendor has updated the version of Kubernetes in the installer since the last time the script was run, it will begin an upgrade of Kubernetes.

The script will first print a prompt to continue with an upgrade of the local master node.

```bash
    Drain local node and apply upgrade?
```

If confirmed, the local master node will be drained and the upgrade to the control plane will be applied.

Then the script will upgrade any remote nodes one at a time, starting with all masters and then continuing to all workers.
For each remote node detected, it will drain the node and wait for the drain to complete.
Then it will print a command that must be run on that node to upgrade the control plane.

```bash
    curl -sSL https://kurl.sh/supergoodtool/upgrade.sh | sudo bash -s hostname-check=master-node-2 kubernetes-version=v1.15.3
```

The script will poll the status of the remote node until it detects the upgrade has completed.
Then it will uncordon that node and proceed to drain the next node.
The script will ensure that at most one node is cordoned at a time.
The Kubernetes scheduler will automatically reschedule Pods to other nodes during maintenance.
Any Deployments or StatefulSets with a single replica will experience downtime while being rescheduled.

### Updating Addons

If the application vendor has updated any add-ons in the installer since the last time the script was run, the upgrade will proceed automatically after any required Kubernetes upgrade.
