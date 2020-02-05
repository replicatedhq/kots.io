---
date: 2019-10-09
linktitle: "Application"
title: Application
description: "The Application spec contains vendor-supplied metadata about the application."
weight: 1
aliases:
  - /vendor/additional-objects/application
---

The KOTS Application custom resource enables features such as branding, release notes, port forwarding, dashboard buttons, app status indicators, and custom graphs.

With ports specified, the kots CLI can establish port-forwarding, to simplify connections to the deployed application.  When [statusInformers](/vendor/dashboard/application-status/#kots-application-spec) are specified, the dashboard can provide timely feedback when the application deployment is complete and the application is ready for use. This CR is optional for KOTS applications.

There is some overlap between the [KOTS Application spec](/reference/v1beta1/application/) and the [Kubernetes SIG Application spec](https://github.com/kubernetes-sigs/application#application-objects).  In time, it's likely that the SIG Application spec will grow to include all the necessary metadata to support the full KOTS features.  In the meantime, enabling features (such as [dashboard buttons to the application](/vendor/dashboard/open-buttons/)) requires the use of both the KOTS Application spec and the SIG Application spec.

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
  kustomizeVersion: latest
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
This defaults to `latest`.
Set to `1.14.9`, `1.16.3` or a [blang semver range](https://github.com/blang/semver#ranges) (like  `>1.16.0 <1.17.0`) to use a specific version of kubectl to apply your app's yaml.
The latest version within the provided range will be used, falling back to the latest version if no version matches.
Currently `1.14.9` and `1.16.3` are supported, but patch versions may change and newer minor versions may be added in the future.

## kustomizeVersion
This defaults to `latest`.
Set to `2.0.3`, `3.5.4` or a [blang semver range](https://github.com/blang/semver#ranges) (like  `>3.0.0 <4.0.0`) to use a specific version of kustomize to apply your app's yaml.
The latest version without the provided range will be used, falling back to the latest version if no version matches.
Currently `2.0.3` and `3.5.4` are supported, but patch versions may change and newer versions may be added in the future.

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
