---
date: 2020-3-31
linktitle: "Kurl Context"
title: Kurl Context
weight: 90040
---

## Kurl Context Functions

For kots apps installed on kURL clusters, we have a new feature that will allow
use of template functions to show all options the cluster was installed with.
The creation of the Installer CRD will reflect both install script changes made
by posting yaml to the kurl installer api and changes made with -s flags at
runtime. These functions are available after the config page.

KurlBool, KurlInt, KurlString, and KurlOption all take a string yamlPath as a
param. This path is the path from the yaml spec, and is delineated between addon
and subfield by a period’.’ Example is “Kubernetes.Version” 

KurlBool, KurlInt, KurlString respectively return a bool, integer and string
value. If used on a valid field but with the wrong type these will return the
falsly value for their type, false, 0, and “string” respectively. The KurlOption
function will convert all bool, int, and string fields to string. All functions
will return falsy values if there is nothing at the yamlPath specified, or if 
these functions are run in a cluster with no installer CRD(i.e. Not a kurl cluster)

For a complete list of the Kurl Installer Spec with annotations see:

## KurlBool

```go
func KurlBool(yamlPath string) bool
```

Returns the value at the yamlPath if there is a valid boolean there, or false if
there is not not

```yaml
‘{{ repl KurlBool “Docker.NoCEonEE” }} 
```


## KurlInt

```go
func KurlInt(yamlPath string) int
```

Returns the value at the yamlPath if there is a valid integer there, or 0 if
there is not not

```yaml
‘{{ repl KurlInt “Rook.CephReplicaCount” }} 
```


## KurlString

```go
func KurlString(yamlPath string) string
```

Returns the value at the yamlPath if there is a valid string there, or "" if
there is not not.

```yaml
‘{{ repl KurlString “Kubernetes.Version” }} 
```


## KurlOption

```go
func KurlOption(yamlPath string) string
```

Returns the value at the yamlPath if there is a valid string, int, or bool value
there, or "" if there is not not. Int and Bool values will get converted to
string values.

```yaml
‘{{ repl KurlOption “Rook.CephReplicaCount” }} 
```


## KurlAll

```go
func KurlAll() string
```

This function will return all values in the Installer CRD as key:value pairs,
sorted by key.

```yaml
‘{{ repl KurlAll }} 
```
