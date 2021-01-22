---
date: 2020-08-12
linktitle: "Kubernetes RBAC"
title: Kubernetes RBAC
weight: 20240
---

When a KOTS application is installed, [Kubernetes RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) resources are created to allow the Admin Console to manage the application.
By default, the Admin Console will create a ClusterRole and ClusterRoleBinding with permissions to all namespaces.
This behavior can be controlled by editing the [application](/reference/v1beta1/application/) manifest.

## Cluster-scoped access

For compatibilty with earlier versions of KOTS, the default behavior of a KOTS application is to create a ClusterRole and ClusterRoleBinding with permissions to all namespaces.

Applications that need access to cluster-wide resources should continue to use cluster-scoped access installers.

#### Reference Objects

The following ClusterRole is created for cluster-scoped applications:

```yaml
apiVersion: "rbac.authorization.k8s.io/v1"
kind: "ClusterRole"
metadata:
  name: "kotsadm-role"
rules:
  - apiGroups: ["*"]
    resources: ["*"]
    verb: "*"
```

## Namespace-scoped access

An application developer can limit the RBAC grants for the Admin Console to be limited to a single namespace by specifying the `requireMinimalRBACPrivileges` flag in the [application](/reference/v1beta1/application/) manifest.
When this is set, the KOTS installer will create a Role and RoleBinding, granting the Admin Console access to select resources in the namespace, but not outside of the cluster.
Without access to cluster-scoped resources, some Preflight Checks and Support Bundle collectors will not be able to read the resources.
These tools will continue to function, but will return less data.
In this situation, the Admin Console will present an option for the user to either proceeed with limited data or a command to execute the Preflight Checks or Support Bnudle remotely, using the user's RBAC authorizations.
Additionally, the namespace-scoped permission does not currently grant access to the `velero` namespace.
Applications that are using the [snapshots](/vendor/snapshots/overview) functionality should continue to use cluster-scoped access at this time.

Please note that airgapped installs honor the `requireMinimalRBACPrivileges` flag in [headless mode only](/kotsadm/installing/automating/#airgap-install).
Without access to the internet or the app's `.airgap` package as provided in a headless install, kots does not have the information required to determine whether minimal RBAC is appropriate and so defaults to the more permissive RBAC policy.

### Operators and multiple namespaces

It is possible to use namespace-scoped access for Operators and multi-namespace applications.
During the installation, if there are `additionalNamespaces` specified in the application manifest, Roles and RoleBindings will be created to give the Admin Console access to all namespaces specified.

To enable namespace-scoped access for an application:

```yaml
apiVersion: kots.io/v1beta1
kind: Application
metadata:
  name: my-application
spec:
  title: My Application
  icon: https://support.io/img/logo.png
  requireMinimalRBACPrivileges: true
```

#### Reference Objects

The following Role is created for namespace-scoped applications:

```yaml
apiVersion: "rbac.authorization.k8s.io/v1"
kind: "Role"
metadata:
  name: "kotsadm-role"
rules:
  - apiGroups: [""]
    resources: ["configmaps"]
    verb: "*"
  - apiGroups: [""]
    resources: ["secrets"]
    verb: "*"
```

(Note that the kotsadm-operator component receives an authorization for all verb, all resources and all apiGroups in the namespace).

## Converting

At this time, the RBAC permissions are set during the initial installation.
The Admin Console is running using the assumed identity and cannot change it's own authorization.
Changing the RBAC scope from cluster to namespace or from namespace to cluster will only affect new installations of the application; existing installations will continue to run with their current authorization.
For applications that need to elevate their permission from namespace to cluster, we recommmend including a Prefight Check to ensure the permission is available.
