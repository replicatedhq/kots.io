---
date: 2020-3-31
linktitle: "Kurl Context"
title: kURL Context
weight: 90040
---

## kURL Context Functions

For KOTS apps installed on kURL clusters, we have a new feature that will allow use of template functions to show all options the cluster was installed with.
The creation of the Installer CRD will reflect both install script changes made by posting yaml to the kurl installer api and changes made with -s flags at runtime.
These functions are not available on the config page.

KurlBool, KurlInt, KurlString, and KurlOption all take a string yamlPath as a param.
This path is the path from the yaml spec, and is delineated between addon and subfield by a period ’.’.
For example, the kURL Kubernetes version can be accessed as `{{repl KurlString “Kubernetes.Version” }}`.

KurlBool, KurlInt, KurlString respectively return a bool, integer, and string value.
If used on a valid field but with the wrong type these will return the falsly value for their type, false, 0, and “string respectively. 
The `KurlOption` function will convert all bool, int, and string fields to string.
All functions will return falsy values if there is nothing at the yamlPath specified, or if these functions are run in a cluster with no installer CRD (i.e. Not a kURL cluster).

For a complete list of the Kurl Installer Spec with annotations see:

## KurlBool

```go
func KurlBool(yamlPath string) bool
```

Returns the value at the yamlPath if there is a valid boolean there, or false if there is not.

```yaml
‘{{repl KurlBool “Docker.NoCEonEE” }} 
```


## KurlInt

```go
func KurlInt(yamlPath string) int
```

Returns the value at the yamlPath if there is a valid integer there, or 0 if there is not.

```yaml
‘{{repl KurlInt “Rook.CephReplicaCount” }}'
```


## KurlString

```go
func KurlString(yamlPath string) string
```

Returns the value at the yamlPath if there is a valid string there, or "" if there is not.

```yaml
‘{{repl KurlString “Kubernetes.Version” }}'
```


## KurlOption

```go
func KurlOption(yamlPath string) string
```

Returns the value at the yamlPath if there is a valid string, int, or bool value there, or "" if there is not.
Int and Bool values will be converted to string values.

```yaml
‘{{repl KurlOption “Rook.CephReplicaCount” }}'
```


## KurlAll

```go
func KurlAll() string
```

Returns all values in the Installer CRD as key:value pairs, sorted by key.

```yaml
‘{{repl KurlAll }}'
```
