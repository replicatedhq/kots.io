---
date: 2019-10-09
linktitle: "Installing a KOTS application"
title: Installing a KOTS application
weight: 10010
draft: false
---

KOTS applications can be installed into various environments and Kubernetes clusters, from airgapped, bare metal Kubernetes installs to managed offering such as EKS, GKE and AKS, and many options in between.

To start installing a KOTS application, you'll need to have:

- A Kubernetes cluster
- `kubectl` installed and configured to access the cluster
- The `kots` plugin.

## Kots Plugin

Install the `kots` plugin for `kubectl` using the [instructions on the Getting Started page](/kots-cli/getting-started/).

## Choose Installation Method

Before installing a KOTS application, answer a few questions:

### Namespace(s)
During installation, KOTS will prompt for the target namespace to install to. This is the namespace that the Admin Console will be written to, and also where the application will be deployed, unless any manifests have hard-coded namespace names or are overriden using Kustomize. The `kubectl` access used must have read and write access to the namespace. By default, KOTS does not require any access outside of the target namespace, and will not install CRDs or cluster-wide RBAC roles.

### Internet Access
If the target cluster does not have outbound Internet access, the application can be delivered through a `.airgap` package. When chosing this option, the cluster must have access to an image registry. All images will be retagged and pushed to the target registry. All application manifests will be patched (using Kustomize) to pull the images from the provided image registry.

### Images and Registries
During install, KOTS can re-tag and push images to a local image registry. This is useful to enable CVE scans, image policy validation and other pre-deployment rules. To learn how to use a local image registry, visit the [image registries](/kotsadm/registries/) section of the docs.



