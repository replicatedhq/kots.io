---
date: 2021-06-14
linktitle: "Updating the Admin Console"
title: Updating the Admin Console
weight: 10020
draft: false
---

This article refers to upgrading the Admin Console on an existing cluster. 
When running the Admin Console on an embedded cluster, refer to the [Updating an Embedded Cluster](/kotsadm/updating/updating-embedded-cluster/) documentation.

{{< warning title="Prerequisite" >}}
As a prerequisite for any admin console upgrade, you should start by upgrading your KOTS CLI to the desired version.
* For online installs, follow the instructions after running `kubectl kots version` to download the latest binary.
* For airgapped installs or to use a previous KOTS version, download the the latest KOTS binary from [Github](https://github.com/replicatedhq/kots/releases) or the customer download page in [vendor.replicated.com](https://vendor.replicated.com).
{{< /warning >}}

### Online Installations

A simple Kots command has been provided to update the Admin Console on an existing cluster.

```bash
kubectl kots admin-console upgrade -n <namespace>
```

Additional usage information can be found by running the `kubectl kots admin-console upgrade -h` command.

### Airgap Installations

Similar to the [initial installation](/kotsadm/installing/airgap-packages/#kots-install) into an existing cluster, images must be pushed to a private registry first:

```shell
kubectl kots admin-console push-images ./kotsadm.tar.gz private.registry.host/application-name \
  --registry-username rw-username \
  --registry-password rw-password
```

After images have been pushed, the upgrade command can be executed with registry read-only credentials:

```bash
kubectl kots admin-console upgrade \
  --kotsadm-registry private.registry.host/application-name \
  --registry-username ro-username \
  --registry-password ro-password \
  -n <namespace>
```
