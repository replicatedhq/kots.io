---
date: 2019-10-09
linktitle: "Application"
title: Application
description: "The Application spec contains vendor-supplied metadata about the application."
weight: 30010
---

The `Application` spec contains vendor-supplied metadata about the application.

```yaml
apiVersion: kots.io/v1beta1
kind: Application
metadata:
  name: my-application
spec:
  title: My Application
  icon: https://support.io/img/logo.png
  releaseNotes: These are our release notes
  allowRollback: false
  kubectlVersion: latest
  ports:
    - serviceName: web
      servicePort: 9000
      localPort: 9000
      applicationUrl: "http://web"
  statusInformers:
    - deployment/my-web-svc
    - deployment/my-worker
  graphs:
    - title: User Signups
      query: 'sum(user_signup_events_total)'
```

## title
The application title. This will be used on the license upload and in various places in the Admin Console.

## icon
The icon file for the application. This will be used on the license upload and in various places in the Admin Console.

## releaseNotes
The release notes for this version. These can also be set when promoting a release.

## allowRollback
This defaults to `false`. Enable to create a "Rollback" button on the end-customer Verison History page.

## kubectlVersion
This defaults to `latest`. Set to `1.14` or `1.16` to use a specific version of kubectl to apply your app's yaml.

## ports
These are extra ports (additional to the :8800 admin console port) that should be port-forwarded when running the `kots admin-console` command.

### serviceName
The name of the service that has a `ClusterIP` type that should receive the traffic.

### servicePort
The `ClusterIP` port to forward traffic to.

### localPort
If set, the port to map on the local workstation. If not set, this will be the same as `servicePort`.

### applicationUrl
This should match a service found in the `k8s.io` Application spec.

## statusInformers
Resources to watch and report application status back to the user. In the format `[namespace/]type/name` where namespace is optional.

## graphs
Custom graphs to include on your Admin Console application dashboard.

### title
The graph title.

### query
The Prometheus query.

### legend
The legend to use for the query line. Can be templated with each element returned from the Prometheus query. Template escape sequence is `{{}}`.

### queries
A list of queries containing a query and legend.
- query: The Prometheus query
- legend: The legend to use for the query line. Can be templated with each element returned from the Prometheus query. Template escape sequence is `{{}}`.

### yAxisFormat
The format of the Y axis labels with support for all Grafana [units](https://grafana.com/docs/features/panels/graph/#left-y-right-y). 

### yAxisTemplate
Y axis labels template. Use `{{ value }}`.
