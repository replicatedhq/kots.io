---
date: 2021-02-19
linktitle: "config"
title: kots set config
weight: 90230
---

The `kots set config` allows setting values for application config items.

### Usage

```bash
kubectl kots set config [appSlug] [KEY_1=VAL_1 ... KEY_N=VAL_N] [flags]
```

- _Provide `[flags]` according to the table below_

| Flag              | Type   | Description                                                         |
| :---------------- | ------ | ------------------------------------------------------------------- |
| `--config-file`   | string | path to a manifest containing config values (must be `apiVersion: kots.io/v1beta1, kind: ConfigValues`) |
| `--merge`         |        | when set to true, only keys specified in config file will be updated. This flag can only be used when `--config-file` flag is used. |
|`--key`            | string | name of a single key to set. This flag requires `--value` or `--value-from-file` flags |
| `--value` string  |        | the value to set for the key specified in the `--key` flag. This flag cannot be used with `--value-from-file` flag. |
| `--value-from-file` | string | path to the file containing the value to set for the key specified in the `--key` flag. This flag cannot be used with `--value` flag. |
| `--deploy`        |        | when set, automatically deploy the latest version with the new configuration |
| `--skip-preflights` |      | set to true to skip preflight checks when deploying new version |

### Example

```bash
kubectl kots set config myapp --config-file /path/to/local/config.yaml
```

```bash
kubectl kots set config myapp --key config-item-name --value-from-file /path/to/config/file/value.txt
```

```bash
kubectl kots set config myapp config-item-name="config item value"
```

```bash
kubectl kots set config myapp --key config-item-name --value "config item value"
```
