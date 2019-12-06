---
date: 2019-12-06
linktitle: "Template Functions"
title: Template Functions
weight: 20210
---

KOTS applications have access to a rich set of template functions that can be used to render the Kubernetes manifests in the customer's environment.

All template functions are documented in the [template function reference](../../../reference/template-functions) section of these docs.

## Using Template Functions

To use a template function, include it as a string in the application. A simple example is using a boolean [custom entitlement field](../entitlements/custom-entitlements/) to deliver a value for Max Concurrent Users. This value should be available as an environment variable in a pod.

Given the custom license field named `max_concurrent_users`, this value can be supplied to the pod environment variable like this:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api
spec:
  selector:
    matchLabels:
      app: api
  template:
    spec:
      containers:
      - image: myapp/api:v1.0.1
        name: api
        env:
          - name: MAX_CONCURRENT_USERS
            value: 'repl{{ LicenseFieldValue "max_concurrent_users" }}'
```

### A note on `{{repl` vs `repl{{`

The template function syntax supports delimiters of either `{{repl ...}}` or `repl{{ ... }}`. These are functionally equivalent and both are supported by the KOTS runtime.

However, `{{` is not a valid string beginning in YAML, so to use `{{repl` as the only part of a value, it's required that the YAML attribute be surrounded by quotes. For example:

```yaml
env:
  - name: MAX_CONCURRENT_USERS
    value: '{{repl LicenseFieldValue "max_concurrent_users"}}'
```

This solution is readable and works well for string values. The surrounding `'` characters allow this to be parsed and will render as:

```yaml
env:
  - name: MAX_CONCURRENT_USERS
    value: '100'
```

But some Kubernetes API fields require integer values, not strings. For example, replica count. **The following YAML is not valid**:

```yaml
replicas: '{{repl ConfigOption "replicas"}}`
```

This is invalid because it will render as:

```yaml
replicas: '5'
```

And the Kubernetes API will reject a string value in this position.

To solve this, reverse the delimiter in the template function and remove the surrounding quotes:

```yaml
replicas: repl{{ ConfigOption "replicas" }}
```

Because this doesn't have surrounding quotes and is valid YAML, this will render as:

```yaml
replicas: 5
```

And Kubernetes will be able to handle this.
