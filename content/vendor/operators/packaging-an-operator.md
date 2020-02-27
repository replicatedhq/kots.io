---
date: 2020-02-27
linktitle: "Packaging An Operator"
title: Packaging An Operator
weight: 100
---

Kubernetes Operators can be packaged and delivered as a KOTS application using the same methods as other applications. Operators are generally defined using one or more `CustomResourceDefinition` manifests, and the controller is often a `StatefulSet` and other additional objects. These can be included in a KOTS application by adding the manifests to a release and promoting the release to a channel.

Unlike other applications, Operators interact with the Kubernetes API and create new objects (`Deployments`, `Statefulsets`, etc) at runtime, to fulfill requests from deployed Custom Resources. When an Operator creates a `PodSpec` at runtime, it should use locally-available images in order to remain compatible with airgapped environments and customers who have configured a local registry to push all images to.

A KOTS application supports Operators by exposing a way for the developer to list the additional iamges that are required for the application, and by exposing the local registry details to the application so that they can be referenced when creating a `PodSpec` at runtime.


