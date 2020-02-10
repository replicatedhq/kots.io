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
---
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
