---
date: 2019-10-09
linktitle: "release create"
title: "release create"
weight: 90120
---

Create a new release using a collection of YAML application manifests. 

### Usage
```bash
replicated release create --yaml-dir YAML_DIR [flags]
```

* _`YAML_DIR` corresponds to the root directory of the YAML application manifest files._
* _Additional flags returned by `--help` that are not supported in KOTS have been omitted from the list below_

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `--yaml-dir` | path | The directory containing multiple yamls for a Kots release. (**required**) |
| `--promote` | string |    Channel name to promote this release to (**case sensitive**)|
| `--ensure-channel` |  |    When used with --promote <channel>, will create the channel if it doesn't exist |
| `--release-notes` | string |  When used with --promote <channel>, sets the **markdown** release notes |
| `--required` |  |          When used with --promote <channel>, marks this release as required during upgrades. |
| `--version` string | When used with --promote <channel>, sets the version label for the release in this channel |
| `-h, --help`   |  |          help for admin-console |
| `--kubeconfig` | string |  the kubeconfig to use _(default is $KUBECONFIG. If unset, then $HOME/.kube/config)_ |
| `-n, --namespace` | string |   the namespace where the admin console is running _(default "default")_ |

### Examples
```bash
replicated release create --yaml-dir ./manifests --promote Unstable --ensure-channel --release-notes "CI Release" --version "1.2.3"
SEQUENCE: 28
Channel cA7ZWXUjq0BkH8KIoMHZubrHrZoCulWo successfully set to release 28
```

```bash
replicated release create --yaml-dir ./manifests
SEQUENCE: 29
```