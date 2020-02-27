---
date: 2020-02-27
linktitle: "Operator Example"
title: Operator Example
weight: 104
draft: true
---

For a more complete example, we can look at the [SchemaHero](http://github.com/schemahero/schemahero) code. This is a Go based, Kubernetes Operator that was bootstrapped using Kubebuilder. While it's an open source operator, there's an Enterprise version available as a KOTS application that uses private images, and fully supports airgapped environments.

### additionalImages
The SchemaHero KOTS application spec [references the private images](https://github.com/schemahero/schemahero/tree/master/kots) in the application YAML. These additional images are not built from the public repo, but they are part of a private repo that adds the enterprise functionality.

### Operator environment variables
The SchemaHero manager defines the additional environment variables to reference the local images in the [Manager code](https://github.com/schemahero/schemahero/blob/1786b1486b154dfb10b3887b471be71624087e3a/pkg/installer/manager.go#L190-L199).

