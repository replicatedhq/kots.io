---
date: 2019-11-18
linktitle: "Installing an Embedded Cluster"
title: Installing an Embedded Cluster
weight: 10040
draft: false
aliases: 
  - /pagelink/installing-kots-embedded-cluster
---

This article refers to installing the Admin Console on an embedded cluster. When running the Admin Console on an existing cluster, refer to the [Installing the Admin Console](/kotsadm/installing/installing-a-kots-app/) documentation.

### Powered by kURL
Replicated KOTS leverages a [deep integration](https://blog.replicated.com/kurl-with-replicated-kots/) with the [Replicated kURL project](https://github.com/replicatedhq/kurl) in order to provide native embedded Kubernetes cluster support. More documentation on installing with kURL (including [advanced install options](https://kurl.sh/docs/install-with-kurl/advanced-options)) is available at [kurl.sh/docs](https://kurl.sh/docs).

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

Both online and airgapped installations can be configured in high-availability mode.
When installing a highly available cluster, the script will prompt for a load balancer address.
The load balancer can be preconfigured by passing in the load-balancer-address=<host:port> flag.
This load balancer should be configured to distribute traffic to all healthy control plane nodes in its target list.
This should be a TCP forwarding load balancer.
The health check for an apiserver is a TCP check on the port the kube-apiserver listens on (default value :6443).
For more information on the kube-apiserver load balancer see https://kubernetes.io/docs/setup/independent/high-availability/#create-load-balancer-for-kube-apiserver.
In the absence of a load balancer, all traffic will be routed to the first master.

```bash
curl -sSL https://kurl.sh/supergoodtool | sudo bash -s ha
```

or

```bash
cat install.sh | sudo bash -s airgap ha
```

## System Requirements

Supported operating systems and minimum system requirements are [specified by Replicated kURL](https://kurl.sh/docs/install-with-kurl/system-requirements).

## Joining Nodes

Visit the `/cluster/manage` page in the Kotsadm web console to generate scripts for joining additional worker and master nodes.

For airgapped installations, the airgap bundle must also be downloaded and extracted on the remote node prior to running the join script.
