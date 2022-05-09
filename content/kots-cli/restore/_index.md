---
date: 2019-10-30
linktitle: "restore"
title: kots restore
weight: 90220
---

The `kots restore` restores a full snapshot for disaster recovery.

### Usage

```bash
kubectl kots restore [flags]
```

- _Provide `[flags]` according to the table below_

| Flag                        | Type   | Description                                                                                   |
| :-------------------------- | ------ | --------------------------------------------------------------------------------------------- |
| `-h, --help`                |        | help for restore                                                                              |
| `--from-backup`             | string | the name of the backup to restore from _(required)_                                           |
| `--exclude-admin-console`   | bool   | exclude restoring the admin console and only restore the application(s) _(defaults to false)_ |
| `--exclude-apps`            | bool   | exclude restoring the application(s) and only restore the admin console _(defaults to false)_ |
| `--wait-for-apps`           | bool   | wait for all applications to be restored _(defaults to true)_                                 |
| `--velero-namespace`        | string | the namespace in which Velero is installed _(only required for minimal RBAC installations)_   |
| `-o, --output`              | string | output format (currently supported: json) _(defaults to plain text if not set)_               |

### Example

```bash
kubectl kots restore --from-backup instance-942kf
```
