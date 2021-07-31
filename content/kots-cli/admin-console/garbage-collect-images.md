---
date: 2019-10-09
linktitle: "garbage-collect-images"
title: kots admin-console garbage-collect-images
weight: 90030
---

Starts image garbage collection.
Admin Console must be running and an application must be installed in order to use this command.

### Usage
```bash
kubectl kots admin-console garbage-collect-images -n <namespace>
```

This command supports all [global flags](/kots-cli/global-flags/).

### Examples
```bash
kubectl kots admin-console garbage-collect-images -n default
```
