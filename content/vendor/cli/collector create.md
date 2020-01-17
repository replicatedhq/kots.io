---
date: 2019-10-09
linktitle: "Collector Create"
title: "Create a Collector"
weight: 90122
draft: false
---

Create a new collector by providing a name and YAML configuration

### Usage
```bash
replicated collector create [flags]
```
* _Either the `yaml` or the `yaml-file` flag must be specified. Both cannot be specified._

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `--name` | string |          The name for this collector |
| `--yaml` | string | The YAML config for this collector. Use '-' to read from stdin.  Cannot be used with the yaml-file flag. |
| `--yaml-file` | file-name | The file name with YAML config for this collector.  Cannot be used with the yaml flag. |
| `-h, --help`   |  |          help for the command |
| `--app string` | |   The app slug or app id to use in all calls (default uses `$REPLICATED_APP` env variable) |
| `--token string` | |  The API token to use to access your app in the Vendor API (default uses `$REPLICATED_API_TOKEN` env variable) |

### Examples
```bash
replicated channel create --name Beta --description 'New features subject to change'
```