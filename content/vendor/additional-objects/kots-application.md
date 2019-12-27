---
date: 2019-11-27
linktitle: "KOTS Application"
title: KOTS Application
weight: 20030
---

The [KOTS Application spec](/v1beta1/application/) enables features such as branding, release notes, port forwarding, dashboard buttons, app status indicators, and custom graphs.

With ports specified, the kots CLI can establish port-forwarding, to simplify connections to the deployed application.  When [statusInformers](/vendor/dashboard/application-status/#kots-application-spec) are specified, the dashboard can provide timely feedback when the application deployment is complete and the application is ready for use.

There is some overlap between the [KOTS Application spec](/v1beta1/application/) and the [Kubernetes SIG Application spec](https://github.com/kubernetes-sigs/application#application-objects).  In time, it's likely that the SIG Application spec will grow to include all the necessary metadata to support the full KOTS features.  In the meantime, enabling features (such as [dashboard buttons to the application](/vendor/dashboard/open-buttons/)) requires the use of both the KOTS Application spec and the SIG Application spec.

The full reference for this kind is in the [reference section](/v1beta1/application).
