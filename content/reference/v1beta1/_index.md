---
date: "2020-01-02"
lastmod: "2020-01-02"
title: "KOTS Custom Resources"
aliases: [/v1beta1/]
weight: "1"
aliases: 
  - /pagelink/section-reference
---

A KOTS application can include several recommended, but optional, custom resources. These custom resources are packaged as part of the KOTS application, but are not deployed to the cluster. The custom resources defined here are included to control the KOTS application experience, but are consumed by KOTS, the Admin Console (kotsadm) or by other kubectl plugins.

| Group/Version | Kind | Description |
|---------------|------|-------------|
| kots.io/v1beta1 | [Config](/reference/v1beta1/config/)| Define a user-facing config screen |
| kots.io/v1beta1 | [Application](/reference/v1beta1/application) | Add additional metadata (branding, release notes and more) to an application |
| troubleshoot.replicated.com/v1beta1 | [Preflight](/reference/v1beta1/preflight) | Define custom Preflight Checks for an application version |
| troubleshoot.replicated.com/v1beta1 | [Analyzer](/reference/v1beta1/support-bundle#analyzer) | Define custom support bundle analyzers to run in the Admin Console |
| troubleshoot.replicated.com/v1beta1 | [Collector](/reference/v1beta1/support-bundle#collector) | Define custom data to include in a support bundle |
| app.k8s.io/v1beta1 | [Application](/reference/v1beta1/k8s-sig-application) | Define metadata about the application |
| kots.io/v1beta1 | [HelmChart](/reference/v1beta1/helmchart/) | Identifies an instantiation of a Helm Chart |
