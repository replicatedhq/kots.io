---
date: 2021-07-13
linktitle: "Repeatable"
title: Repeatable Config
description: "Repeatable config generates copies of kubernetes manifest items."
weight: 9
---

## Repeatable Items

A repeatable config item copies a YAML array entry or YAML document for as many values as are provided. Any number of values can be added to a repeatable item to generate additional copies.

**Repeatable Items presently only work for text and textarea types**

To make an item repeatable, set `repeatable` to true
```yaml
    - name: ports
      items:
      - name: service_port
        title: Service Port
        type: text
        repeatable: true
```

Repeatable items do not use the `default` or `value` fields, but instead a `valuesByGroup` field.
`valuesByGroup` should have an entry for the parent Config Group name, with all default `key:value` pairs nested in the group.  1 default entry is required for the repeatable item.
```yaml
    valuesByGroup:
      ports:
        port-default-1: "80"
```

## Template Targets

Repeatable items require at least 1 `template` to be provided.  The `template` defines a YAML target in the manifest to duplicate for each repeatable item.

Required fields for a template target are `apiVersion`, `kind`, and `name`.

The entire YAML node at the target will be duplicated, including nested fields.

The `yamlPath` field of the `template` must denote index position for arrays using square brackets.  For example, `spec.ports[0]` to select the first port entry for duplication.  All duplicate YAML will be appended to the final array in the `yamlPath`.

`yamlPath` **must** end with an array.
```yaml
    template:
    - apiVersion: v1
      kind: Service
      name: my-service
      namespace: my-app
      yamlPath: 'spec.ports[0]'
```

If the `yamlPath` field is blank, the entire YAML document matching the `template` will be replaced with a copy for each of the repeatable item entries.  The `metadata.name` field of the new doc will reflect the repeatable item `key`.

## Templating

The repeat items are called with the delimeters `repl[[ .itemName ]]` or `[[repl .itemName ]]`.  These delimiters can be placed anywhere inside of the `yamlPath` target node.
```yaml
    - port: repl{{ ConfigOption "[[repl .service_port ]]" | ParseInt }}
      name: '[[repl .service_port ]]'
```
This repeatable templating is not compatible with sprig templating functions.  It is designed for inserting repeatable `keys` into the manifest. Repeatable templating **can** be placed inside of Replicated config templating.

## Ordering

Repeatable templates are processed before config template rendering.

Repeatable items are processed in order of the template targets in the Config Spec file.  Effectively, this ordering is from the top of the Config Spec, by Config Group, by Config Item, and then by template target.

```yaml
    - name: ports
      items:
      - name: service_port
        title: Service Port
        type: text
        repeatable: true
        template:
        - apiVersion: v1 #processed first
          kind: Service
          name: my-service
          namespace: my-app
          yamlPath: 'spec.ports[0]'
        - apiVersion: v1 #processed second
          kind: Service
          name: my-service
          namespace: my-app
          yamlPath:
        {other item properties ...}
      - name: other_ports
        title: Other Service Port
        type: text
        repeatable: true
        template:
        - apiVersion: v1 #processed third
          kind: Service
          name: my-other-service
          namespace: my-app
        {other item properties ...}
    - name: deployments
      items:
      - name: deployment-name
        title: Deployment Names
        type: text
        repeatable: true
        template:
        - apiVersion: apps/v1 #processed fourth
          kind: Deployment
          name: my-deployment
          namespace: my-app
          yamlPath:
        {other item properties ...}
```

# Examples

In this example, the default service port of "80" is included with the release. Port 443 is added as an additional port on the KOTSADM `Config` page, which is stored in the ConfigValues file.
## Repeatable Item Example for a YamlPath
**Config spec**
```yaml
    - name: ports
      items:
      - name: service_port
        title: Service Port
        type: text
        repeatable: true
        template:
        - apiVersion: v1
          kind: Service
          name: my-service
          namespace: my-app
          yamlPath: spec.ports[0]
        valuesByGroup:
          ports:
            port-default-1: "80"
```

**Config values**
```yaml
apiVersion: kots.io/v1beta1
kind: ConfigValues
metadata:
  name: example_app
spec:
  values:
    port-default-1:
      repeatableItem: service_port
      value: "80"
    service_port-8jdn2bgd:
      repeatableItem: service_port
      value: "443"
```

**Template manifest**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
  namespace: my-app
spec:
  type: NodePort
  ports:
  - port: repl{{ ConfigOption "[[repl .service_port ]]" | ParseInt }}
    name: '[[repl .service_port ]]'
  selector:
    app: repeat_example
    component: my-deployment
```

**After repeatable config processing**

*Note: this phase is internal to KOTS configuration rendering. This example is only provided to further explain the templating process.*
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
  namespace: my-app
spec:
  type: NodePort
  ports:
  - port: repl{{ ConfigOption "port-default-1" | ParseInt }}
    name: 'port-default-1'
  - port: repl{{ ConfigOption "service_port-8jdn2bgd" | ParseInt }}
    name: 'service_port-8jdn2bgd'
  selector:
    app: repeat_example
    component: my-deployment
```

**Resulting manifest**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
  namespace: my-app
spec:
  type: NodePort
  ports:
  - port: 80
    name: port-default-1
  - port: 443
    name: service_port-8jdn2bgd
  selector:
    app: repeat_example
    component: my-deployment
```

## Repeatable Item Example for an Entire Document
**Config spec**
```yaml
    - name: ports
      items:
      - name: service_port
        title: Service Port
        type: text
        repeatable: true
        template:
        - apiVersion: v1
          kind: Service
          name: my-service
          namespace: my-app
          yamlPath: ""
        valuesByGroup:
          ports:
            port-default-1: "80"
            service_port-8jdn2bgd: "443"
```
**Template manifest**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
  namespace: my-app
spec:
  type: NodePort
  ports:
  - port: repl{{ ConfigOption "[[repl .service_port ]]" | ParseInt }}
  selector:
    app: repeat_example
    component: repl[[ .service_port ]]
```

**After repeatable config processing**

*Note: this phase is internal to KOTS configuration rendering. This example is only provided to further explain the templating process.*
```yaml
apiVersion: v1
kind: Service
metadata:
  name: port-default-1
  namespace: my-app
spec:
  type: NodePort
  ports:
  - port: repl{{ ConfigOption "port-default-1" | ParseInt }}
  selector:
    app: repeat_example
    component: port-default-1
---
apiVersion: v1
kind: Service
metadata:
  name: service_port-8jdn2bgd
  namespace: my-app
spec:
  type: NodePort
  ports:
  - port: repl{{repl ConfigOption "service_port-8jdn2bgd" | ParseInt }}
  selector:
    app: repeat_example
    component: service_port-8jdn2bgd
```

**Resulting manifest**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: port-default-1
  namespace: my-app
spec:
  type: NodePort
  ports:
  - port: 80
  selector:
    app: repeat_example
    component: port-default-1
---
apiVersion: v1
kind: Service
metadata:
  name: service_port-8jdn2bgd
  namespace: my-app
spec:
  type: NodePort
  ports:
  - port: 443
  selector:
    app: repeat_example
    component: service_port-8jdn2bgd
```
