---
date: 2020-04-07
linktitle: "Backup"
title: Backup
isAlpha: true
description: "The Backup resource defines the steps to create and restore snapshots in the application"
weight: 7
---

The Backup resource enables ...


```yaml
apiVersion: velero.io/v1
kind: Backup
metadata:
  name: backup
spec:
  includedNamespaces:
    - my-other-namespace
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

## includedNamespaces

A list of any additional namespaces to search for and include in snapshots. By default, KOTS will include the application namespaces, but some applications can deploy resources to multiple namespaces. If there are additional namespaces to back up, enumerate them here.

## hooks

An optional set of ooks to run at various stages of the backup lifecycle.

### resources

The resources here are an array of hooks that can be executed.
