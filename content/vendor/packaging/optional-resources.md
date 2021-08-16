---
date: 2020-01-06
linktitle: "Optional Resources"
title: Optional Kubernetes Resources
weight: 20220
---

Often Vendors need a way to optionally install resources depending on customer choices. A common example is giving the customer the choice of installing a new database, or use their own managed external database. 

In this scenario, when a customer chooses to bring their own database, it is not desireable to deploy the optional database resources (StatefulSet, Service, etc.). This means that the customer-supplied configuration input values may result in optional Kubernetes manifests that should not be installed.

To provide optional resource installation, KOTS uses [annotations](https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/) and [template functions](/reference/template-functions/) to conditionally include or exclude resources.


## KOTS Annotations

### Placeholder Annotation

`kots.io/placeholder '<bool>' '<string>'`

KOTS uses placeholder annotations as a way to provide an annotation that may not appear in the final rendered YAML.

Use case: providing custom Ingress annotations for a customer-provided Ingress controller.

When the placeholder evaluates to `true`, it will be replaced with the value of the desired annotation in the final rendered YAML.

When the placeholder evaluates to `false`, the annotation will not appear at all in the final rendered YAML.

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: example-annotation
  annotations:
    kots.io/placeholder: repl{{ printf "'true'" }}repl{{ printf "'my.custom/annotation.class: somevalue'" | nindent 4 }}
```

will result in the final rendered YAML:

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: example-annotation
  annotations:
    my.custom/annotation.class: somevalue
```

Similarly:

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: example-annotation
  annotations:
    kots.io/placeholder: repl{{ printf "'false'" }}repl{{ printf "'my.custom/annotation.class: somevalue'" | nindent 4 }}
```

will result in no annotations appearing in the final rendered YAML:

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: example-annotation
  annotations:
```

### Exclude A Resource

`kots.io/exclude: '<bool>'`

When this annotation is present on a resource and evaluates to `'true'`, the resource will not be included in the `kustomization.yaml` file and will not be written to disk.

**NOTE**: Kubernetes annotations cannot be booleans and must be strings, so make sure to quote this!

#### Example Exclude

The following example WILL NOT include the postgres StatefulSet when the user has not selected the `install_postgres` checkbox.

```yaml
apiVersion: apps/v1
kind: Statefulset
metadata:
  name: postgresql
  annotations:
    "kots.io/exclude": '{{repl ConfigOptionEquals "install_postgres" "0" }}'
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

### Include A Resource
`kots.io/when: '<bool>'`

When this annotation is present on a resource and evaluates to `'false'`, the resource will not be included in the kustomization.yaml file and will not be written to disk.

**NOTE**: Kubernetes annotations cannot be booleans and must be strings, so make sure to quote this!

#### Example Include

The following example WILL include the postgres StatefulSet when the user has selected the `install_postgres` checkbox.

```yaml
apiVersion: apps/v1
kind: Statefulset
metadata:
  name: postgresql
  annotations:
    "kots.io/include": '{{repl ConfigOptionEquals "install_postgres" "1" }}'
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

### Please Note 

By default, if neither `kots.io/exclude` nor `kots.io/include` annotations are present on a resource, the resource will be included.

Only one of the following annotations can be present on a resource. If both are present, the `kots.io/exclude` annotation will be applied, and the `kots.io/when` annotation will be ignored.



