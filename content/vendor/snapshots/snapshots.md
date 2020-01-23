---
date: 2019-01-23
linktitle: "Snapshots"
title: Snapshots
aliases:
  - /vendor/snapshots/
---

## Requirements

### Application YAML

Enable snapshots in your app with the field `spec.allowSnapshots: true` in your kots.io/v1beta1 Application yaml.

```
apiVersion: kots.io/v1beta1
kind: Application
metadata:
  name: app-slug
spec:
  title: App Name
  allowSnapshots: true
```

### Customer License

For each customer license there is an Allow Snapshots checkbox. Both the application yaml and the customer license must have snapshots enabled in order for the Admin Console to display snapshot UI to the user.

### Velero

The customer must have [velero](https://velero.io/) installed in the `velero` namespace.

## Release YAML Configuration

### Backup

Kotsadm requires exactly one [velero.io/v1/Backup](https://velero.io/docs/v1.2.0/api-types/backup/) resource be included in the release yaml.
Templates are supported in this file.
All fields in the metadata are irrelevant.
This yaml is never applied directly to Kubernetes - it is saved until a backup is triggered.
After rendering it, kotsadm will pick the `includedNamespaces` and `hooks` fields from the spec.
No other fields are added to the Backup kotsadm generates and applies.

`spec.includedNamesapces` tells Velero which namespaces should be included in the snapshot.
If this is not set in the Backup from the release yaml, kotsadm will add its own namespace.
If the application has hardcocded namespaces into its yaml configs, all those namespaces should be specified in this list.

All objects in included namespaces will be snapshotted, including objects that are not found in release yaml and are dynamically created by other components or manually created by users with kubectl.
There are two exceptions:

1. If the Backup includes the same namespace that kotsadm is running in, then kotsadm will add a label selector to the Backup spec to exclude all kotsadm objects.
2. Invidual resources in your release can be excluded from the backup by adding the `velero.io/exclude-from-backup=true` label to them.

#### Example

Below is an example of a Backup that hardcodes a namespace and adds some pre-exec hooks.

```yaml
apiVersion: velero.io/v1
kind: Backup
metadata:
  name: backup
spec:
  includedNamespaces:
  - test
  hooks:
    resources:
    - name: echo-hook
      includedNamespaces:
      - '*'
      includedResources:
      - 'pods'
      labelSelector:
        matchLabels:
          app: example
          component: nginx
      pre:
      - exec:
          command: ["/bin/bash", "-c", "echo hello"]
      - exec:
          command: ["/bin/bash", "-c", "echo $(date) > /scratch/timestamp"]
      - exec:
          command: ["/bin/bash", "-c", "head -c 1G </dev/urandom >/scratch/data"]
          timeout: 3m
          onError: Continue
```
