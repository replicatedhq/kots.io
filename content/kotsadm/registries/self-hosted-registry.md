---
date: 2019-10-09
linktitle: "Using self-hosted image registry"
title: Self-Hosted Image Registry
description: "Kots can be used to download and prepare an application to be installed onto a secured, airgapped Kubernetes cluster. When doing this, there are a few additional steps and configuration needed."
weight: 10010
---

Kots can be used to download and prepare an application to be installed onto a secured, airgapped Kubernetes cluster. When doing this, there are a few additional steps and configuration needed.

## Docker Registry
To install an application into an airgapped network, you must have a Docker image registry that’s available inside the network.
Kots will manage rewriting the application image names in all application manifests to read from the on-prem registry, and it will retag and push the images to the on-prem registry.
When authenticating to the registry, credentials with `push` permissions are required.

A single Kots application expects to use a single “namespace” in the Docker image registry.
The namespace name can be any valid URL-safe string, supplied at installation time.
Keep in mind that registries typically expect namespace to exist before any images can be pushed into it.
lso, ECR does not use namespaces.

Kots has been tested for compatibility with the following registries:

- Docker Hub
- Quay
- Amazon Elastic Container Registry (ECR)
- Google Container Registry (GCR)
- Harbor

