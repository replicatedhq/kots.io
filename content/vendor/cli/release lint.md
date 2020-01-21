---
date: 2019-10-09
linktitle: "release lint"
title: "release lint"
weight: 90121
---

Lint a directory of YAML application manifests

### Usage
```bash
replicated release lint --yaml-dir YAML_DIR [flags]
```

* _`YAML_DIR` corresponds to the root directory of the YAML application manifest files._
* _Returns exit code 1 when an error in linting is discovered. Otherwise, returns exit codse 0 (such as for info and warning messages)._

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `--yaml-dir` | path | The directory containing yaml application manifests for a Kots release. (**required**) |
| `-h, --help`   |  |          help for admin-console |
| `--kubeconfig` | string |  the kubeconfig to use _(default is $KUBECONFIG. If unset, then $HOME/.kube/config)_ |
| `-n, --namespace` | string |   the namespace where the admin console is running _(default "default")_ |

### Examples
```bash
replicated release lint --yaml-dir ./manifests
RULE                           TYPE    LINE    MESSAGE    
config-spec                    warn            Missing config spec
replicas-1                     info    10      Found Replicas 1
container-resource-requests    info    27      Missing resource requests
```