---
date: 2019-11-05
linktitle: "Updating an Embedded Cluster"
title: Updating an Embedded Cluster
weight: 10030
draft: false
---

This article refers to upgrading the Admin Console on an embedded cluster. When running the Admin Console on an existing cluster, refer to the [Updating the Admin Console](/kotsadm/updating/updating-admin-console/) documentation.

### Online Installations

To update the Admin Console when deployed to an embedded cluster, simply re-run the installation script provided by the application developer.

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
