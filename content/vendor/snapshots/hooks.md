---
date: 2019-01-23
linktitle: "Hooks"
title: Hooks
isAlpha: true
weight: 4
---

The hooks will be copied from the release's Backup template to the Backup generated and applied by kotsadm.
It is possible to specify pre-exec hooks to run before the backup and post-exec hooks to run after the backup.
The recommended usage is to create db dumps to a directory with an empty volume in the pre-exec hook and to remove the dump from that directory in the post-exec hook.

It is not possible to run hooks during restore.
Use initContainers to load data from db dumps at restore time.
Note that you will have to place your db dumps in separate pods from your db for databases such as postgres that are required to be running to load a dump.

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

