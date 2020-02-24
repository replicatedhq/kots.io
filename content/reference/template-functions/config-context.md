---
date: 2019-12-06
linktitle: "Config Context"
title: Config Context
weight: 90040
---

## ConfigOption

```go
func ConfigOption(optionName string) string
```

Returns the value of the config option as a string.
The config screen and associated options are described [here](/reference/v1beta1/config).

```yaml
'{{repl ConfigOption "hostname" }}'
```

## ConfigOptionData

```go
func ConfigOptionData(fileName string) string
```

Returns the base64 decoded value of a config option.

```yaml
'{{repl ConfigOptionData "ssl_key"}}'
```

This is often used to provide files as part of a secret or configmap, like this:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-tls-secret
type: kubernetes.io/tls
data:
  tls.crt: '{{repl ConfigOptionData "tls_certificate_file" | Base64Encode }}'
  tls.key: '{{repl ConfigOptionData "tls_private_key_file" | Base64Encode }}'
```

## ConfigOptionEquals

```go
func ConfigOptionEquals(optionName string, expectedValue string) bool
```

Returns true if the configuration option value is equal to the supplied value.

```yaml
'{{repl ConfigOptionEquals "http_enabled" "1" }}'
```

## ConfigOptionNotEquals

```go
func ConfigOptionNotEquals(optionName string, expectedValue string) bool
```

Returns true if the configuration option value is not equal to the supplied value.

```yaml
'{{repl ConfigOptionNotEquals "http_enabled" "1" }}'
```

## LocalRegistryAddress

```go
func LocalRegistryAddress() string
```

Returns the local registry host or host/namespace that's configured. This will always return everything before the image name and tag.

## LocalImageName

```go
func LocalImageName(remoteImageName string) string
```

This is a wrapper around other functions. Given a `remoteImageName` rewrite it so that it references the local registry. If no local registry is set, the `remoteImageName` is returned.

## LocalRegistryImagePullSecret

```go
func LocalRegistryImagePullSecret() string
```

Returns the base64 encoded local registry image pull secret value. This is often needed when an operator is deploying images to a namespace that is not managed by KOTS. Image pull secrets must be present in the namespace of the pod.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-image-pull-secret
  namespace: my-namespace
type: kubernetes.io/dockerconfigjson
data:
  .dockerconfigjson: '{{repl LocalRegistryImagePullSecret }}'
---
apiVersion: v1
kind: Pod
metadata:
  name: dynamic-pod
  namespace: my-namespace
spec:
  containers:
    - image: '{{repl LocalImageName "registry.replicated.com/my-app/my-image:abcdef" }}'
      name: my-container
  imagePullSecrets:
    - name: my-image-pull-secret
```

## HasLocalRegistry

```go
func HasLocalRegisrtry() bool
```

Returns true if the environment is configured to rewrite images to a local registry. This will be true for airgap installs, and optionally true for online installs.
