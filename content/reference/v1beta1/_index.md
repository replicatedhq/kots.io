---
date: "2020-01-02"
lastmod: "2020-01-02"
title: "KOTS Custom Resources"
weight: "1"
aliases: 
  - /v1beta1
---

A KOTS application can include several recommended, but optional, custom resources. These custom resources are packaged as part of the KOTS application, but are not deployed to the cluster. The custom resources defined here are included to control the KOTS application experience, but are consumed by KOTS, the Admin Console (kotsadm) or by other kubectl plugins.

| Group/Version | Kind | Description |
|---------------|------|-------------|
| kots.io/v1beta1 | [Config](/reference/v1beta1/config/)| Define a user-facing config screen |
| kots.io/v1beta1 | [Application](/reference/v1beta1/application) | Add additional metadata (branding, release notes and more) to an application |
| troubleshoot.replicated.com/v1beta1 | [Preflight](/reference/v1beta1/preflight) | Define custom Preflight Checks for an application version |
| troubleshoot.replicated.com/v1beta1 | [Analyzer](https://troubleshoot.sh/reference/analyzers/overview/) | Define custom support bundle analyzers to run in the Admin Console |
| troubleshoot.replicated.com/v1beta1 | [Collector](https://troubleshoot.sh/reference/collectors/overview/) | Define custom data to include in a support bundle |
| troubleshoot.replicated.com/v1beta1 | [Redactor](https://troubleshoot.sh/reference/redactors/overview/) | Define custom redactors that apply to support bundle contents. Only configurable via the admin console |
| app.k8s.io/v1beta1 | [Application](/reference/v1beta1/sig-application) | Define metadata about the application |
| kots.io/v1beta1 | [HelmChart](/reference/v1beta1/helmchart/) | Identifies an instantiation of a Helm Chart |
| velero.io/v1 | [Backup](https://velero.io/docs/v1.3.2/api-types/backup/) | A Velero backup request, triggered when the user initiates a [snapshot](https://kots.io/vendor/snapshots/overview/) |
