---
date: 2020-02-27
linktitle: "Defining Additional Images"
title: Defining Additional Images
weight: 101
---

To ensure that images will be available locally, KOTS finds all images defined in the application manifests and includes them in airgap bundles. 
During the install or update workflow, KOTS will collect these images from the airgap bundle (if airgapped) or from the internet (if online), retag and push all of the images to customer-defined registry.

If there are required images that are not defined in any of the Kubernetes manifests, these should be listed in the `additionalImages` attribute of the [Application](/reference/v1beta1/application/) spec.

```yaml
apiVersion: kots.io/v1beta1
kind: Application
metadata:
  name: my-operator
spec:
  additionalImages:
    - elasticsearch:7.6.0
    - registry.replicated.com/my-operator/my-private-image:abd123f
    - quay.io/orgname/private-image:v1.2.3
```

KOTS supports additional images that are:

- public images: referenced by the docker pullable image name
- images pushed to the Replicated Registry: referenced by the `registry.replicated.com` name
- images pushed to another [private, linked registry](https://kots.io/vendor/packaging/private-images/): referenced by the docker pullable name

## Authentication

When creating the airgap bundle or performing an online install, KOTS will ensure that private images are available, without sharing registry credentials with the installation. 
Airgapped packages include the image layers in the bundle, and online installs will rewrite externally hosted private images to be pulled from `proxy.replicated.com`. 
When the installation sends credentials to `proxy.replicated.com` or `registry.replicated.com`, the credentials are based on the customer license file, and the credentials stop working when the license expires.
