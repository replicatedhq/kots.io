---
date: "2019-11-27T00:00:00Z"
lastmod: "2019-11-27T00:00:00Z"
title: "Additional Kubernetes Objects"
aliases: [/vendor/additional-objects]
weight: "2"
hideFromList: true
---
Elements of a KOTS application lifecycle are controlled by various Custom Resource Definitions included as part of a KOTS application.

These additional Kubernetes objects serve as metadata for the KOTS application that can be used by the Admin Console, and by kubectl plug-ins on the client side.  There is no CRD controller code required, and thus no need for elevated permissions or installation of any additional components to the target cluster.