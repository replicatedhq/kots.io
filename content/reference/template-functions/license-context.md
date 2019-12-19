---
date: 2019-12-06
linktitle: "License Context"
title: License Context
weight: 90050
---

## LicenseFieldValue
```go
func LicenseFieldValue(name string) string
```
LicenseFieldValue returns the value of the entitlement with the provided name.

```yaml
'{{repl LicenseFieldValue "numSeats" }}'
```

## LicenseDockerCfg
```go
func LicenseDockerCfg() string
```
LicenseDockerCfg returns a value that can be written to a secret if needed to deploy manually.
KOTS will create and inject this secret automatically in normal conditions, but some deployments (with static, additional namespaces) may need to include this.

```yaml
apiVersion: v1
kind: Secret
type: kubernetes.io/dockerconfigjson
metadata:
  name: kotsadm-replicated-registry
  namespace: my-other-namespace
data:
  .dockerconfigjson: repl{{ LicenseDockerCfg }}
```
