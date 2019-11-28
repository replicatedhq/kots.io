---
date: 2019-11-27
linktitle: "KOTS Application"
title: KOTS Application
weight: 20010
---

The [KOTS Application spec](/v1beta1/application/) allows for the customization of the initial experience (title, icon, release notes) and various operational and diagnostic features (port forwarding, statusInformers, and custom graphs).

With ports specified, the kots CLI can establish port-forwarding, to simplify connections to the deployed application.  When [statusInformers](/vendor/dashboard/application-status/#kots-application-spec) are specified, the dashboard can provide timely feedback when the application deployment is complete and the application is ready for use.

There is some overlap between the [KOTS Application spec](/v1beta1/application/) and the [Kubernetes SIG Application spec](https://github.com/kubernetes-sigs/application#application-objects).  In time, it's likely that the SIG Application spec will grow to include all the necessary metadata to support the full KOTS featureset.  In the meantime, best practice will be to include a complete KOTS Application spec along with a complete SIG Application spec in the release YAML of any KOTS application.
