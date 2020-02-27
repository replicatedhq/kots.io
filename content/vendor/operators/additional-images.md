---
date: 2020-02-27
linktitle: "Defining Additional Images"
title: Defining Additional Images
weight: 101
---

To ensure that images will be available locally, KOTS finds all images defined in the application manifests and includes them in airgap bundles and pushes these to local registries. If there are additional images needed, images that are not defined in the Operator or CustomResourceDefinition, these should be listed in the `additionalImages` attribute of the [Application](/reference/v1beta1/application/) spec.

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

KOTS supports listing public images, images push to the Replicated Registry, or images pushed to another [private, linked registry](https://kots.io/vendor/packaging/private-images/). When packaging the airgap bundle or performing an online install, KOTS will ensure that private images are rewritten to use the Replicated Proxy and the license will be used as authentication to pull images from the Replicated Registry.
