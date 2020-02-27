---
date: 2020-02-27
linktitle: "Referencing Images"
title: Referencing Images
weight: 102
---

While KOTS handles delivering application images (and additional images) to the customer's private, internal registry, the Operator Manager should uses the local image reference when creating a PodSpec.

There are several template functions available to help with this. The workflow to get and use these values is:

1. Add a new environment variables to the Manager Pod so that the Manager knows the location of the private registry, if one is set.
2. Add a new environment variable to the Manager Pod so that the Manager also knows the `imagePullSecret` that's needed to pull the local image.

### Adding a reference to the local registry

The manager of an operator is normally a Statefulset, but could be a Deployment or another object. Regardless of where the spec is defined, the location of the private images can be read using the [Replicated template functions](https://kots.io/vendor/packaging/template-functions/).

If an operator only requires one additional image, the easiest way to determine this location is to use the `LocalImageName` function. This will always return the image name to use, whether the customer's environment is configured to use a local registry or not.

```yaml
env:
  - name: IMAGE_NAME_ONE
    value: 'repl{{ LocalImageName "elasticsearch:7.6.0" }}'
```

For online installations (no local registry), this will be written with no changes -- the variable will contain `elasticsearch:7.6.0`. For installations that are airgapped or have a locally-configured registry, this will be rewritten as the locally referencable image name (i.e. `registry.somebigbank.com/my-app/elasticsearch:7.6.0`).

```yaml
env:
  - name: IMAGE_NAME_TWO
    value: 'repl{{ LocalImageName "quay.io/orgname/private-image:v1.2.3" }}'
```

In the above example, this is a private image, and will always be rewritten. For online installations, this will return `proxy.replicated.com/app-name/private-image:v1.2.3` and for installations with a locally-configured registry it will return `registry.somebigbank.com/my-app-private-image:v.1.2.3`.

**Multiple Images**

For applications that have multiple images or dynamically construct the image name at runtime, the Replicated Template functions can also return the components that make up the local registry, and let the application developer construct the locally-referencable image name.

```yaml
env:
  - name: REGISTRY_HOST
    value: 'repl{{ LocalRegistryHost }}'
  - name: REGISTRY_NAMESPACE
    value: 'repl{{ LocalRegistryNamespace }}'
```

### Determining the imagePullSecret

Private, local images will need to reference an image pull secret to be pulled. The value of the secret's dockerconfigjson is provided in a template function, and the application can write this pull secret as a new secret to the namespace. If the application is deploying the pod to the same namespace as the operator, the pull secert will already exist.

This template function returns the base64-encoded, docker auth that can be written directly to a secret, and referenced in the imagePullSecrets attribute of the PodSpec.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: myregistrykey
  namespace: awesomeapps
data:
  .dockerconfigjson: '{{repl LocalRegistryImagePullSecret }}'
type: kubernetes.io/dockerconfigjson
```

## Using the local registry at runtime

The developer of the Operator should use these environment variables to change the imageName in any deployed PodSpec to ensure that it will work in airgapped environments.
