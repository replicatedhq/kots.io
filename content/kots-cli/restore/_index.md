---
date: 2019-10-30
linktitle: "restore"
title: kots restore
weight: 90220
---

The `kots restore` restores an instance snapshot for disaster recovery.

### Usage

```bash
kubectl kots restore [flags]
```

- _Provide `[flags]` according to the table below_

| Flag            | Type   | Description                                         |
| :-------------- | ------ | --------------------------------------------------- |
| `-h, --help`    |        | help for restore                                    |
| `--from-backup` | string | the name of the backup to restore from _(required)_ |

### Example

```bash
kubectl kots restore --from-backup sentry-942kf
```
