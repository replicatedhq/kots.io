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

| Flag                | Type   | Description                                                                      |
|:--------------------|--------|----------------------------------------------------------------------------------|
| `-h, --help`        |        | help for admin-console                                                           |
| `-n, --namespace`   | string | the namespace where the admin console is running _(required)_                    |
| `--ignore-rollback` | string | force images garbage collection even if rollback is enabled for the application  |

### Examples
```bash
kubectl kots admin-console garbage-collect-images -n default
```
