---
date: 2021-10-06
linktitle: "Using Native Helm Charts"
title: Using Native Helm Charts
weight: 70001
isBeta: true
---

Replicated KOTS already supports delivering [Helm charts](/vendor/helm/using-helm-charts/) as components of an application but, until now, it did not support certain Helm features. With the new Helm workflow, you can now exercise more control over chart deployment via Helm hooks and weights. Currently, this is a beta feature and is not recommended for production.

The native Helm chart support currently has the following limitations:
* Only available for Helm V3.
* Only supported for new charts added to an existing application or a new application with new charts.
* Not yet supported on existing charts deployed on existing applications.
* The pre-rollback, post-rollback, and test hooks are not supported.
* Hook weights below -9999. All hook weights must be set to a value above -9999 to ensure the Replicated image pull secret is deployed before any resources are pulled.
* Not supported with [GitOps Workflow](/kotsadm/gitops/).

## Enabling and using native Helm chart support

To access this feature, you must first contact Replicated so the feature can be enable for your team. Once enabled, you can leverage this workflow by setting `useHelmInstall: true` in the `HelmChart` CRD. You can then promote these changes to a channel and install new instances of the application with this new Helm workflow. For any existing installations of the application, you can update these via the Admin Console or KOTS CLI. Once updated, any new helm charts added to the application will be deployed with this feature.

![Use Helm Install Flag](/images/vendor-use-helm-install-flag.png)

> This is a chart level flag that is only supported for new charts. Modifying existing charts in existing applications is not supported.

## Helm hooks and weights

Native Helm hooks and weights enable more control over when resources are deployed. This is useful if you want to bundle actions as part of a release. For example, you can build in a database backup as part of the upgrade process while ensuring that the backup occurs prior to upgrading the rest of the resources. Helm weights provide even more control by governing the order of operations within each hook.

The following hooks are currently supported:
* pre-install - executes after resources are rendered but before any resources are installed.
* post-install - executes after resources are installed.
* pre-upgrade - executes after resources are rendered but before any resources are upgraded.
* post-upgrade - executes after resources are upgraded.
* pre-delete - executes before any resources are deleted.
* post-delete - executes after resources are deleted.

See the [Helm docs](https://helm.sh/docs/topics/charts_hooks/) for more information on Helm hooks and weights.
