---
date: "2019-11-27T00:00:00Z"
lastmod: "2019-11-27T00:00:00Z"
title: "Additional Kubernetes Objects"
aliases: [/vendor/additional-objects]
weight: "2"
hideFromList: true
---

A KOTS application can include several recommended, but optional, custom resources. These custom resources are packages as part of the KOTS application, but are not deployed to the cluster. The custom resources defined here are includfed to control the KOTS application experience, but are consumed by KOTS, the Admin Console (kotsadm) or by other kubectl plugins.

| Group/Version | Kind | Description |
|---------------|------|-------------|
| kots.io/v1beta1 | [Config](./config) | Define a user-facing config screen |
| kots.io/v1beta1 | [Application](./kots-application) | Add additional metadata (branding, release notes and more) to an application |
| troubleshoot.replicated.com/v1beta1 | [Preflight](./preflight) | Define custom Preflight Checks for an application version |
| troubleshoot.replicated.com/v1beta1 | Analyzer | Define custom support bundle analyzers to run in the Admin Console |
| troubleshoot.replicated.com/v1beta1 | Collector | Define custom data to include in a support bundle |
| app.k8s.io/v1beta1 | [Application](./k8s-sig-application) | Define metadata about the application |
