---
date: 2019-11-18
linktitle: "Installing an Embedded Cluster"
title: Installing an Embedded Cluster
weight: 10040
draft: false
---

This article refers to installing the Admin Console on an embedded cluster. When running the Admin Console on an existing cluster, refer to the [Installing the Admin Console](/kotsadm/installing/installing-a-kots-app/) documentation.

Refer to the [reference documentation](/reference/kubernetes-installers/kurl/) for a full list of options that may be passed to the install script.

### Online Installations

To install the Admin Console with an embedded cluster, simply run the installation script provided by the application developer.

```bash
curl -sSL https://kurl.sh/supergoodtool | sudo bash
```

### Airgapped Installations

To install an airgapped embedded cluster, download the airgap bundle, untar it, and run the install.sh script.

```bash
curl -SL -o supergoodtool.tar.gz https://kurl.sh/bundle/supergoodtool.tar.gz
tar xzvf supergoodtool.tar.gz
cat install.sh | sudo bash -s airgap
```

Note that the airgapped installer is not the same as an [airgapped application package](/kotsadm/installing/airgap-packages/).
A KOTS application may be installed in airgap mode on clusters installed online and vice versa.

### HA Installations

Both online and airgapped installations can be installed configured in high-availability mode.

```bash
curl -sSL https://kurl.sh/supergoodtool | sudo bash -s ha
```

or

```bash
cat install.sh | sudo bash -s airgap ha
```

## Operating Systems

* Ubuntu 18.04
* Ubuntu 16.04
* Red Hat Enterprise Linux 7.4 - 7.6
* CentOS 7.4 - 7.6

## Minimum System Requirements

* 2 CPUs per machine for master nodes, 1 CPU for worker nodes
* 4 GB or more of RAM per machine
* TCP ports 6443, 6783, and 8800 open
* UDP ports 6783 and 6784 open

## Joining Nodes

Visit the `/cluster/manage` page in the Kotsadm web console to generate scripts for joining additional worker and master nodes.

For airgapped installations, the airgap bundle must also be downloaded and extracted on the remote node prior to running the join script.
