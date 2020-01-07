---
date: 2019-10-09
linktitle: "Airgap Packages"
title: Installing from an Airgap Package
weight: 10030
draft: false
aliases: 
  - /pagelink/installing-airgap
---

When installing an application from an airgap package, the container images and application manifests are provided by the application vendor in an archive that can be used to deliver the artifacts into the cluster. This feature is only available for licenses that have the airgap feature enabled.

![Airgap Bundle](/images/airgap-install.png)

### Upload Airgap Bundle
The software vendor should have delivered a `.airgap` bundle to be used on this screen. The bundle contains the container images and manifests. Choose the bundle and click continue to start processing.

![Airgap Uploading](/images/airgap-uploading.png)

### Processing Images
Once the bundle has been completely uploaded, the Admin Console will start to process the images and manifests. Images will be loaded, re-tagged and pushed to the registry provided.

![Processing Images](/images/processing-images.gif)
