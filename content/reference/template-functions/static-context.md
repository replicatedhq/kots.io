---
date: 2019-12-06
linktitle: "Static Context"
title: Static Context
weight: 90030
---

## Mastermind Sprig

Many of the utility functions provided come from Sprig, a third-party library of Go template functions.
The Sprig documentation can be found [here](https://masterminds.github.io/sprig/).


## Namespace
```go
func Namespace() string
```
Namespace returns the Kubernetes namespace that the KOTS application belongs to. 
```yaml
'{{repl Namespace}}'
```

## KubeSeal
```go
func KubeSeal(certData string, namespace string, name string, value string) string
```

## HumanSize
```go
func HumanSize(size interface{}) string
```
HumanSize returns a human-readable approximation of a size in bytes capped at 4 valid numbers (eg. "2.746 MB", "796 KB"). The size must be a integer or floating point number.
```yaml
'{{repl ConfigOption "min_size_bytes" | HumanSize }}'
```

## Now
```go
func Now() string
```
Returns the current timestamp as an RFC3339 formatted string.
```yaml
'{{repl Now }}'
```

## NowFmt
```go
func NowFmt(format string) string
```
Returns the current timestamp as a formatted string. See Go's time formatting guidelines [here](https://golang.org/pkg/time/#pkg-constants).
```yaml
'{{repl Now "20060102" }}'
```

## ToLower
```go
func ToLower(stringToAlter string) string
```
Returns the string, in lowercase.
```yaml
'{{repl ConfigOption "company_name" | ToLower }}'
```

## ToUpper
```go
func ToUpper(stringToAlter string) string
```
Returns the string, in uppercase.
```yaml
'{{repl ConfigOption "company_name" | ToUpper }}'
```

## TrimSpace
```go
func TrimSpace(s string) string
```
Trim returns a string with all leading and trailing spaces removed.
```yaml
'{{repl ConfigOption "str_value" | TrimSpace }}'
```

## Trim
```go
func Trim(s string, args ...string) string
```
Trim returns a string with all leading and trailing string contained in the optional args removed (default space).
```yaml
'{{repl ConfigOption "str_value" | Trim " " "." }}'
```

## UrlEncode
```go
func UrlEncode(stringToEncode string) string
```
Returns the string, url encoded.
Equivalent to the [`QueryEscape`](https://godoc.org/net/url#QueryEscape) function within the golang `net/url` library.
```yaml
'{{repl ConfigOption "smtp_email" | UrlEncode }}:{{repl ConfigOption "smtp_password" | UrlEncode }}@smtp.example.com:587'
```

## UrlPathEscape
```go
func UrlPathEscape(stringToEncode string) string
```
Returns the string, url *path* encoded.
Equivalent to the [`PathEscape`](https://godoc.org/net/url#PathEscape) function within the golang `net/url` library.
```yaml
'{{repl ConfigOption "smtp_email" | UrlPathEscape }}:{{repl ConfigOption "smtp_password" | UrlPathEscape }}@smtp.example.com:587'
```

## Base64Encode
```go
func Base64Encode(stringToEncode string) string
```
Returns a Base64 encoded string.
```yaml
'{{repl ConfigOption "name" | Base64Encode }}'
```

## Base64Decode
```go
func Base64Decode(stringToDecode string) string
```
Returns decoded string from a Base64 stored value.
```yaml
'{{repl ConfigOption "base_64_encoded_name" | Base64Decode }}'
```

## Split
```go
func Split(s string, sep string) []string
```
Split slices s into all substrings separated by sep and returns an array of the substrings between those separators.
```yaml
'{{repl Split "A,B,C" "," }}'
```

Combining `Split` and `index`:
Assuming the `github_url` param is set to `https://github.mycorp.internal:3131`, the following would set
`GITHUB_HOSTNAME` to `github.mycorp.internal`.
```yaml
'{{repl index (Split (index (Split (ConfigOption "github_url") "/") 2) ":") 0}}'
```

## RandomString
```go
func RandomString(length uint64, providedCharset ...string) string
```
Returns a random string with the desired length and charset.
Provided charsets must be Perl formatted and match individual characters.
If no charset is provided, `[_A-Za-z0-9]` will be used.

Each time that this function is called, it will return a different value.
```yaml
'{{repl RandomString 64}}'
```
Or for a total of 64 `a`s and `b`s:
```yaml
'{{repl RandomString 64 "[ab]" }}'
```

## Add
```go
func Add(x interface{}, y interface{}) interface{}
```
Adds x and y.

If at least one of the operands is a floating point number, the result will be a floating point number.

If both operands are integers, the result will be an integer.
```yaml
'{{repl Add (ConfigOption "maximum_users") 1}}'
```

## Sub
```go
func Sub(x interface{}, y interface{}) interface{}
```
Subtracts y from x.

If at least one of the operands is a floating point number, the result will be a floating point number.

If both operands are integers, the result will be an integer.
```yaml
'{{repl Sub (ConfigOption "maximum_users") 1}}'
```

## Mult
```go
func Mult(x interface{}, y interface{}) interface{}
```
Multiplies x and y.

If at least one of the operands is a floating point number, the result will be a floating point number.

If both operands are integers, the result will be an integer.
```yaml
'{{repl Mult (NodePrivateIPAddressAll "DB" "redis" | len) 2}}'
```

## Div
```go
func Div(x interface{}, y interface{}) interface{}
```
Divides x by y.

If at least one of the operands is a floating point number, the result will be a floating point number.

If both operands are integers, the result will be an integer and will be rounded down.
```yaml
'{{repl Div (ConfigOption "maximum_users") 2.0}}'
```

## ParseBool
```go
func ParseBool(str string) bool
```
ParseBool returns the boolean value represented by the string.
```yaml
'{{repl ConfigOption "str_value" | ParseBool }}'
```

## ParseFloat
```go
func ParseFloat(str string) float64
```
ParseFloat returns the float value represented by the string.
```yaml
'{{repl ConfigOption "str_value" | ParseFloat }}'
```

## ParseInt
```go
func ParseInt(str string, args ...int) int64
```
ParseInt returns the integer value represented by the string with optional base (default 10).
```yaml
'{{repl ConfigOption "str_value" | ParseInt }}'
```

## ParseUint
```go
func ParseUint(str string, args ...int) uint64
```
ParseUint returns the unsigned integer value represented by the string with optional base (default 10).
```yaml
'{{repl ConfigOption "str_value" | ParseUint }}'
```
