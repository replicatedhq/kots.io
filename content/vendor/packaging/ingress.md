---
date: 2020-02-19
linktitle: "Ingress"
title: Ingress
weight: 20225
---

When delivering a configurable KOTS application, ingress can be challenging as it is very cluster specific. Below is an example of a flexible Ingress resource spec designed to work in most Kubernetes clusters including existing and embedded Kurl clusters.

## Example

The following example includes an Ingress resource with a single host based routing rule. The resource will work in both existing and embedded Kurl clusters.

A config option `enable_ingress` has been provided to allow the end-user to choose whether or not to enable the Ingress resource. In some clusters a custom Ingress resource may be desired, or no ingress controller may not be available and instead there may be another means preferred to expose a service.

An `annotations` textarea has been made available for the end-user to add additional annotations to the ingress. Here, cluster specific annotations can be added to support a variety of ingress controllers. For example, when using the [ALB ingress controller](https://docs.aws.amazon.com/eks/latest/userguide/alb-ingress.html) in AWS, it is necessary to include the `kubernetes.io/ingress.class: alb` annotation on your Ingress resource.

### Config

```yaml
apiVersion: kots.io/v1beta1
kind: Config
metadata:
  name: example-application
spec:
  groups:
    - name: ingress
      title: Ingress
      items:
        - name: enable_ingress
          type: bool
          title: Enable Kuberentes Ingress?
          description: |
            Uncheck this box to disable the Kubernetes Ingress resource.
          default: "1"
        - name: hostname
          type: text
          title: Hostname
          description: |
            Use this field to provide a hostname for your Example Application installation.
          required: true
          when: repl{{ ConfigOptionEquals "enable_ingress" "1" }}
        - name: allow_http
          type: bool
          title: Allow HTTP?
          description: |
            Uncheck this box to disable HTTP traffic between the client and the load balancer.
          when: repl{{ ConfigOptionEquals "enable_ingress" "1" }}
        - name: annotations
          type: textarea
          title: Annotations
          description: |
            Use this textarea to provide annotations specific to your ingress controller.
            For example, `kubernetes.io/ingress.class: alb` when using the ALB ingress controller.
          when: repl{{ ConfigOptionEquals "enable_ingress" "1" }}
```

### Ingress

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: example-application-ingress
  annotations:
    "kots.io/exclude": 'repl{{ or (ConfigOptionEquals "enable_ingress" "1" | not) IsKurl }}'
    "kubernetes.io/ingress.allow-http": 'repl{{ if ConfigOptionEquals "allow_http" "1" }}truerepl{{ else }}falserepl{{ end }}'repl{{ ConfigOption "annotations" | nindent 4 }}
spec:
  rules:
    - host: repl{{ or (ConfigOption "hostname") "~" }}
      http:
        paths:
          - path: /
            backend:
              serviceName: nginx
              servicePort: 80
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: example-application-ingress
  annotations:
    "kots.io/exclude": 'repl{{ or (ConfigOptionEquals "enable_ingress" "1" | not) (not IsKurl) }}'
    "kubernetes.io/ingress.allow-http": 'repl{{ if ConfigOptionEquals "allow_http" "1" }}truerepl{{ else }}falserepl{{ end }}'repl{{ ConfigOption "annotations" | nindent 4 }}
spec:
  tls:
    - hosts:
        - repl{{ ConfigOption "hostname" }}
      secretName: kotsadm-tls
  rules:
    - host: repl{{ ConfigOption "hostname" }}
      http:
        paths:
          - path: /
            backend:
              serviceName: nginx
              servicePort: 80
```
