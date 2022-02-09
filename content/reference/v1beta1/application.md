---
date: 2019-10-09
linktitle: "Application"
title: Application
description: "The Application spec contains vendor-supplied metadata about the application."
weight: 1
aliases:
  - /vendor/additional-objects/application
---

The KOTS Application custom resource enables features such as branding, release notes, port forwarding, dashboard buttons, app status indicators, and custom graphs.

With ports specified, the KOTS CLI can establish port-forwarding, to simplify connections to the deployed application.  
When [statusInformers](/vendor/config/application-status/#kots-application-spec) are specified, the dashboard can provide timely feedback when the application deployment is complete and the application is ready for use.
This CR is optional for KOTS applications.

There is some overlap between the [KOTS Application spec](/reference/v1beta1/application/) and the [Kubernetes SIG Application spec](https://github.com/kubernetes-sigs/application#application-objects). In time, it's likely that the SIG Application spec will grow to include all the necessary metadata to support the full KOTS features.

In the meantime, enabling features (such as [dashboard buttons to the application](/vendor/dashboard/open-buttons/)) requires the use of both the KOTS Application spec and the SIG Application spec.

The `Application` spec contains vendor-supplied metadata about the application.

```yaml
apiVersion: kots.io/v1beta1
kind: Application
metadata:
  name: my-application
spec:
  title: My Application
  icon: https://support.io/img/logo.png
  releaseNotes: These are our release notes
  allowRollback: false
  kubectlVersion: latest
  kustomizeVersion: latest
  targetKotsVersion: "1.60.0"
  minKotsVersion: "1.40.0"
  requireMinimalRBACPrivileges: false
  additionalImages:
    - jenkins/jenkins:lts
  additionalNamespaces:
    - "*"
  ports:
    - serviceName: web
      servicePort: 9000
      localPort: 9000
      applicationUrl: "http://web"
  statusInformers:
    - deployment/my-web-svc
    - deployment/my-worker
  graphs:
    - title: User Signups
      query: 'sum(user_signup_events_total)'
```

## title
The application title. This will be used on the license upload and in various places in the Admin Console.

## icon
The icon file for the application. This will be used on the license upload and in various places in the Admin Console.

## releaseNotes
The release notes for this version. These can also be set when promoting a release.

## allowRollback
This defaults to `false`. Enable to create a "Rollback" button on the end-customer Version History page.

## additionalNamespaces
An optional array of namespaces as strings.
In addition to creating these namespaces, the Admin Console will ensure that the application secret exists in them, and that this secret has access to pull the application images (both images that are used and [additionalImages](/vendor/operators/additional-images/)).
For access to dynamically created namespaces, `"*"` can be specified.
This pull secret will be automatically added to all application specs that use private images.
See the [Additional Namespaces](/vendor/operators/additional-namespaces/) documentation for more information.

## additionalImages
An optional array of strings that reference images to be included in airgap bundles and pushed to the local registry during installation.
While KOTS detects images from the PodSpecs in the application, some applications (Operators) may need to include additional images that will not be referenced until runtime.

## kubectlVersion
KOTS maintains up-to-date patch versions of all supported kubelet minor versions.
When unspecified, KOTS will use the newest version from the list of supported versions below.

- 1.22.x (added in [KOTS 1.59.3](https://kots.io/release-notes/1.59.3/))
- 1.21.x (added in [KOTS 1.48.0](https://kots.io/release-notes/1.48.0/))
- 1.20.x (added in [KOTS 1.48.0](https://kots.io/release-notes/1.48.0/))
- 1.19.x (added in [KOTS 1.22.0](https://kots.io/release-notes/1.22.0/))
- 1.18.x (added in [KOTS 1.22.0](https://kots.io/release-notes/1.22.0/))
- 1.17.x (added in [KOTS 1.22.0](https://kots.io/release-notes/1.22.0/))
- 1.16.x
- 1.14.x

An optional semver range can be specified, as defined in [blang semver range](https://github.com/blang/semver#ranges) (like `1.16.x` or `>=1.16.0 <1.17.0`).
The latest version within the provided range will be used.
If the specified version or range does not match any supported versions, the latest version from the above list will be used.

For backwards compatibility, exact versions are also supported.
When an exact version is specified, KOTS will choose the matching major and minor version.

## kustomizeVersion
KOTS maintains up-to-date minor and patch versions of all supported kustomize major versions.
When unspecified, KOTS will use the newest version from the list of supported versions below.

- 3.x.x

An optional semver range can be specified, as defined in [blang semver range](https://github.com/blang/semver#ranges) (like `3.x.x` or `>=3.0.0 <4.0.0`).
The latest version within the provided range will be used.
If the specified version or range does not match any supported versions, the latest version from the above list will be used.

For backwards compatibility, exact versions are also supported.
When an exact version is specified, KOTS will choose the matching major and minor version.

## requireMinimalRBACPrivileges
When set to true, this will instruct the KOTS installer to create a namespace-scoped Role and RoleBinding, instead of the default cluster-scoped ClusterRole and ClusterRoleBinding.
For more information, see the [RBAC](/vendor/packaging/rbac) documentation.

## ports
These are extra ports (additional to the :8800 admin console port) that should be port-forwarded when running the `kots admin-console` command.

### serviceName
The name of the service that has a `ClusterIP` type that should receive the traffic.

### servicePort
The `ClusterIP` port to forward traffic to.

### localPort
If set, the port to map on the local workstation.
If not set, this will be the same as `servicePort`.

### applicationUrl
This should match a service found in the `k8s.io` Application spec.

## statusInformers
Resources to watch and report application status back to the user.
In the format `[namespace/]type/name` where namespace is optional.
Entries support template functions.
For example, a specific status informer can be excluded based on an application config value like so:

```yaml
statusInformers:
    - deployment/my-web-svc
    - '{{repl if ConfigOptionEquals "option" "value"}}deployment/my-worker{{repl else}}{{repl end}}'
```

## graphs
Custom graphs to include on your Admin Console application dashboard.

### title
The graph title.

### query
The Prometheus query.

### legend
The legend to use for the query line.
Can be templated with each element returned from the Prometheus query.
Template escape sequence is `{{}}`.

### queries
A list of queries containing a query and legend.
- query: The Prometheus query
- legend: The legend to use for the query line.
Can be templated with each element returned from the Prometheus query.
Template escape sequence is `{{}}`.

### yAxisFormat
The format of the Y axis labels with support for all Grafana [units](https://grafana.com/docs/features/panels/graph/#left-y-right-y).

### yAxisTemplate
Y axis labels template. Use `{{ value }}`.

## targetKotsVersion
The KOTS version that is targeted by the release.

Specifying this in the application spec of the release will enforce compatibility checks for new installations and block the installation if the KOTS version being used is greater than the targeted KOTS version.

If the latest release in a channel specifies a target KOTS version, the install command for existing clusters is modified to install that specific version of KOTS. The install command for existing clusters can be found on the channel card in the [vendor portal](https://vendor.replicated.com). 

Specifying a target KOTS version will not prevent an end user from upgrading to a higher version of KOTS after the initial installation. Similarly, if a new version of the application specifies a higher target KOTS version than what is currently installed, the end user will not be prevented from deploying that version of the application. If an end-user's admin console is running a version of KOTS that is less than the target version specified in the new version of the application, a message will be displayed in the footer of the admin console to indicate that a newer supported version of KOTS is available.

For embedded cluster installs, it is important to keep the version of the [KOTS add-on](https://kurl.sh/docs/add-ons/kotsadm) in sync with the target KOTS version specified in the application spec. If the KOTS add-on version is greater than the target KOTS version, the initial installation will fail.

## minKotsVersion (Beta)
The minimum KOTS version that is required by the release.

Specifying this in the application spec of the release will enforce compatibility checks for both new installations and application updates, and will block if the currently deployed KOTS version is less than the specified minimum KOTS version.

### Limitations

`minKotsVersion` is not supported in the following cases:

* Channels that have [semantic versioning](/vendor/packaging/promoting-releases/#semantic-versioning) enabled.
* The minimum version increases and then decreases from one release to the next.

`minKotsVersion` is not supported in these cases because KOTS retrieves only the most recent release. This means that any intermediate releases are lost.

### Guidance for informing users of the need to update

After promoting a new release that specifies a minimum KOTS version that is later than what an end user currently has deployed, that release does not appear in the version history of the admin console after checking for updates. The admin console displays an error message temporarily, but it disappears after a few minutes. The admin console also displays this error when the user checks for updates with the [`kots upstream upgrade` command](/kots-cli/upstream/).

End users must update their admin console to the minimum KOTS version or later in order to fetch the update without error. KOTS cannot update itself automatically, and users cannot update KOTS from the admin console.

When promoting a new release that changes the minimum KOTS version to a later version, vendors can inform their end users of the need to update KOTS if they are concerned end users will not see the error message or will not know how to proceed.
