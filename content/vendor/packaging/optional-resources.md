---
date: 2020-01-06
linktitle: "Optional Resources"
title: Optional Kubernetes Resources
weight: 20220
---

When delivering a configurable KOTS application, the customer-supplied config values may result in optional Kubernetes manifests that are not needed for one environment. A common example of this is when a KOTS application includes a Postgres Statefulset, but provides a config option for the user to choose if they want to run that included Postgres, or bring their own managed or external Postgres instance.

In this scenario, when a customer chooses to bring their own component, you should not deploy the Postgres Statefulset. This can be can be accomplished using the Replicated [template functions](../template-functions/) and Kubernetes annotations in the related components.

## Annotations

By default, if neither of the annotations are present on a resource, the resource will be included. Only one of the following annotations can be present on a resource. If both are present, the `kots.io/exclude` annotation will be applied, and the `kots.io/when` annotation will be ignored.

#### Optionally Exclude A Resource
`kots.io/exclude: '<bool>'`: When this is present and evaluates to `'true'`, the resource will not be included in the kustomization.yaml file and will not be written to disk.
Kubernetes annotations cannot be booleans and must be strings, so make sure to quote this!

#### Optionally Include A Resource
`kots.io/when: '<bool>'`: When this is present and evaluates to `'false'`, the resource will not be included in the kustomization.yaml file and will not be written to disk.
Kubernetes annotations cannot be booleans and must be strings, so make sure to quote this!


## Example

The following example includes a config option to select "embedded postgres" or "external postgres", and contains part of a statefulset definition for Postgres that optionally renders only when the "embedded_postgres" option is selected.

### Config

```yaml
apiVersion: kots.io/v1beta1
kind: Config
metadata:
  name: example-application
spec:
  groups:
    - name: database
      title: Database
      description: Database Options
      items:
        - name: postgres_type
          type: select_one
          title: Postgres
          default: embedded_postgres
          items:
            - name: embedded_postgres
              title: Embedded Postgres
            - name: external_postgres
              title: External Postgres
```

### Statefulset

```yaml
apiVersion: apps/v1
kind: Statefulset
metadata:
  name: postgresql
  annotations:
    "kots.io/exclude": '{{repl ConfigOptionEquals "postgres_type" "embedded_postgres" }}'
  labels:
    app: postgresql
spec:
  selector:
    matchLabels:
      app: postgresql
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: postgresql
    spec:
      containers:
      - name: postgresql
        image: "postgres:9.6"
        imagePullPolicy: ""
...
```
