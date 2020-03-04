---
date: 2020-02-27
linktitle: "Additional Namespaces"
title: Additional Namespaces
weight: 104
---

Operators often need to be able to manage resource in multiple namespaces in the cluster. When deploying a KOTS application to an existing cluster, KOTS creates a Kubernetes Role and RoleBinding that are limited to only accessing the namespace that the application is being installed into. In addition to RBAC policies, clusters running in airgapped environments or clusters that are configured to use a local registry also need to ensure that image pull secrets exist in all namespaces that the operator will manage resource in.

## Creating additional namespaces

A KOTS application can identify additional namespaces to create during installation time. These are defined in the `additionalNamespaces` attribute of the [Application](/reference/v1beta1/application/) spec. When these are defined, `kots install` will create the namespaces and ensure that the Admin Console has full access to manage resources in these namespaces. This is accomplished by creating a Role abd RoleBinding per namespace, and setting the Subject to the Admin Console service account. If the current user account does not have access to create these additional namespaces, the installer will show an error and fail.

```yaml
apiVersion: kots.io/v1beta1
kind: Application
metadata:
  name: my-operator
spec:
  additionalNamespaces:
    - namespace1
    - namespace2
```

In addition to creating these namespaces, the Admin Console will ensure that a secret named `kotsadm-replicated-registry` exists in them, and that this secret has access to pull the application images (both images that are used and [additionalImages](/vendor/operators/additional-images/)). An operator can reliably depend on this secret existing in all installs (online and airgapped), and can use this secret name in any created podspec to pull private images.

## Dynamic namespaces

Some applications need access to dynamically created namespaces or even all namespaces. In this case, a KOTS application spec can list `"*"` as one of it's `addtionalNamespaces` in the [Application](/reference/v1beta1/application/) spec. When KOTS encounters the wildcard, it will not create any namespaces, but it will ensure that the `kotsadm-replicated-registry` secret is copied to all namespaces. The Admin Console will run an informer internally to watch namespaces in the cluster, and when a new namespace is created, the secret will automatically be copied to it.

```yaml
apiVersion: kots.io/v1beta1
kind: Application
metadata:
  name: my-operator
spec:
  additionalNamespaces:
    - "*"
```

When the wildcard (`"*"`) is listed in `additionalImages`, the KOTS installer will use a ClusterRole and ClusterRoleBinding for the Admin Console. This will ensure that the Admin Console will continue to have permissions to all newly created namespaces, even after the install has finished.


