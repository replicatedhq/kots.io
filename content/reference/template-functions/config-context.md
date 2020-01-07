---
date: 2019-12-06
linktitle: "Config Context"
title: Config Context
weight: 90040
aliases: 
  - /pagelink/config-context-template-function
---

## ConfigOption
```go
func ConfigOption(optionName string) string
```
Returns the value of the config option as a string.
The config screen and associated options are described [here](/docs/ship/config/overview).
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

## ConfigOptionIndex
