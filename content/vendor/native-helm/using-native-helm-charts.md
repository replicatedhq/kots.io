---
date: 2021-10-06
linktitle: "Using Native Helm Charts"
title: Using Native Helm Charts
weight: 70001
isBeta: true
---

todo: intro paragraph about new helm features
todo: This is a Beta feature and should not be used in production.

The native Helm chart support currently has the following limitations:
* Only available for Helm V3.
* Only supported for new charts added to an existing application or a new application with new charts.
* Not yet supported on existing charts deployed on existing applications.
* The pre-rollback and post-rollback hooks are Not yet supported.
* Hook weights must be set to a value above -9999 to ensure the Replicated image pull secret is deployed before any resources are pulled.
* Not yet supported with GitOps. 

## Enabling and using native Helm chart support

todo: add info on enabling the feature (contacting replicated, etc) and info on adding it to a new Helm chart (link the image)

todo: Once this is done, installing...

## Helm hooks and weights

This feature supports native Helm hooks and weights, which enables more control over when resources are deployed. This is useful if you want to bundle actions as part of a release. For example, you can build in the ability to back up a database as part of the upgrade process while ensuring that the backup occurs prior to upgrading the rest of the resources. Helm weights provide even more control by governing the order of operations within each hook.

The following hooks are currently supported:
* pre-install - executes after resources are rendered but before any resources are installed.
* post-install - executes after resources are installed.
* pre-upgrade - executes after resources are rendered but before any resources are upgraded.
* post-upgrade - executes after resources are upgraded.
* pre-delete - executes before any resources are deleted.
* post-delete - executes after resources are deleted.

See the [Helm docs](https://helm.sh/docs/topics/charts_hooks/) for more information on Helm hooks and weights.
