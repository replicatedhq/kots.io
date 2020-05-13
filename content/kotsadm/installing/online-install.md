---
date: 2019-10-09
linktitle: "Online Install"
title: Online (Internet-Connected) Installation
weight: 3
---

The most direct and simple way to install a KOTS application to a Kubernetes cluster is to deploy to an existing cluster that contains nodes that can access the Internet. In this scenario, the container images will be pulled from the upstream registries directly.

## Kots install
To start, first [install the Kots CLI kubectl plugin](/kots-cli/getting-started/) & then run the command that was provided by the application vendor.

```shell
kubectl kots install application-name
```

The kubectl plugin will walk you through the necessary steps to install the application:

```shell
$ kubectl kots install application-name
Enter the namespace to deploy to: application-name
  • Deploying Admin Console
    • Creating namespace ✓
    • Waiting for datastore to be ready ✓
Enter a new password to be used for the Admin Console: ••••••••
  • Waiting for Admin Console to be ready ✓

  • Press Ctrl+C to exit
  • Go to http://localhost:8800 to access the Admin Console

```

Once this has completed, the kots plugin will create a port-forward to the Admin Console interface. The Admin Console API and Web server are exposed over a ClusterIP service in the namespace provided. The port-forward will be active as long as the CLI is running. Pressing Ctrl+C will end the port forward.

Once this has completed, click the link, or visit `http://localhost:8800` to complete the setup using the Admin Console web-based UI.

## Web Based Setup

At this point, visit `http://localhost:8800` to complete the setup of the application.

### Unlock the Admin Console
![Secure Console](/images/secure-console.png)

Enter the password provided during the setup, and you'll be redirected to the "Upload License" screen.

### Provide a License File
At this point, the Admin Console is still just an admin console without an application. Providing a license file will include the entitlements necessary to pull the manifest and images and start the application.

![Upload License](/images/upload-license.png)

Once the license file is installed, if airgap installations are enabled, an option will be presented to proceed with an airgapped setup. For instructions on performing an airgapped setup, [read the airgap doc](/kotsadm/installing/airgap-packages). For now, this walk through will continue with an online installation.

### Config Screen
Most KOTS applications include some required and some optional configuration. This is used to build the final, deployable Kubernetes manifests for the application. The config screen of the setup will prompt for initial values to use in the application. These can be changed later, but must be completed to continue.

![Initial Config](/images/initial-config.png)

### Preflight Checks
Finally, Preflight checks (conformance tests) are executed against the target namespace and cluster to ensure that the environment meets the minimum requirements to support the application.

![Preflight Checks](/images/preflight-checks.png)
