---
date: 2019-10-09
linktitle: "Updating the Admin Console"
title: Updating the Admin Console
weight: 10020
draft: false
---

This article refers to upgrading the Admin Console on an existing cluster. When running the Admin Console on an embedded cluster, refer to the [Updating an Embedded Cluster](/kotsadm/updating/updating-embedded-cluster/) documentation.

### Online Installations

A simple Kots command has been provided to update the Admin Console on an existing cluster.

```bash
kubectl kots admin-console upgrade -n <namespace>
```

Additional usage information and examples can be found by running the `kubectl kots admin-console upgrade -h` command.
