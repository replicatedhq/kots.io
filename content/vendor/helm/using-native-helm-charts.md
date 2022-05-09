---
date: 2021-10-06
linktitle: "Installing with Native Helm"
title: Installing with Native Helm
weight: 20560
---

With the native Helm installation, you can exercise more control over chart deployment via Helm hooks and weights. The native Helm chart support currently has the following limitations:
* Only available for Helm V3.
* Only supported for new installations.
* Not supported on existing charts deployed on existing applications.
* The test hook is not supported.
* Hook weights below -9999. All hook weights must be set to a value above -9999 to ensure the Replicated image pull secret is deployed before any resources are pulled.
* Not supported with the [GitOps Workflow](/kotsadm/gitops/).

> Currently, migrating existing applications to the native Helm implementation is not supported. Vendors who are interested in delivering applications using the native Helm workflow can promote releases to a new channel for new customer installations.

## Enabling and using native Helm chart support

To leverage this option, set `useHelmInstall: true` in the `HelmChart` CRD. Then promote these changes to a channel and install new instances of the application with the native Helm installation. For any existing installations of the application, you can update these via the Admin Console or KOTS CLI. Once updated, any new helm charts added to the application will be deployed with the native Helm installation.

For more information on adding charts to KOTS applications, see our documentation on [optional charts](/vendor/helm/optional-charts) and the [Helm docs](https://helm.sh/docs/topics/charts/).

![Use Helm Install Flag](/images/vendor-use-helm-install-flag.png)

> This is a chart level flag that is only supported for new charts. Modifying existing charts in existing applications is not supported.

## Helm hooks and weights

Native Helm hooks and weights enable more control over when resources are deployed. This is useful if you want to bundle actions as part of a release. For example, you can build in a database backup as part of the upgrade process while ensuring that the backup occurs prior to upgrading the rest of the resources. Helm weights provide even more control by governing the order of operations within each hook.

The following hooks are currently supported:
* pre-install - executes after resources are rendered but before any resources are installed.
* post-install - executes after resources are installed.
* pre-upgrade - executes after resources are rendered but before any resources are upgraded.
* post-upgrade - executes after resources are upgraded.

The following hooks may be used but no actions will be taken by Replicated:
* pre-rollback - executes after resources are rendered but before any resources are rolled back.
* post-rollback - executes after resources are rolled back.
* pre-delete - executes before any resources are deleted.
* post-delete - executes after resources are deleted.

See the [Helm docs](https://helm.sh/docs/topics/charts_hooks/) for more information on Helm hooks and weights.
