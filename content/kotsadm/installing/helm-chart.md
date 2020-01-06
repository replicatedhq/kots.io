---
date: 2019-10-09
linktitle: "Installing a Helm Chart"
title: Installing a Helm Chart
weight: 10030
draft: false
---
**Alpha Feature:** *Installing and managing Helm charts via Kotsadm is still an Alpha feature, please open [issues](https://github.com/replicatedhq/kots/issues) with any problems you run into.*

Software Vendors interested in delivering a Helm chart to customers should consider [delivering their Helm chart as a KOTS application](/vendor/helm/using-helm-charts/).

This guide is intended for Cluster Operators interested in using Kotsadm to manage their Helm chart deployments (either directly or through an automated GitOps pipeline). The most direct and simple way to install a KOTS application to a Kubernetes cluster is to deploy to an existing cluster that contains nodes that can access the Internet. In this scenario, the container images will be pulled from the upstream registries directly.

**Note** Currently the only supported source for helm charts are public helm repositories support by helm out-of-the-box.

## Kots install
To start, first [install the Kots CLI kubectl plugin](/kots-cli/getting-started/) & then run a `kots install` command to pull the chart and prepare it for deployment. In the example, we'll use the `elasticsearch` chart from the `stable` helm repository.

```shell
kubectl kots install helm://stable/elasticsearch
```

The kubectl plugin will walk you through the necessary steps to install the application:

```shell
$ kubectl kots install helm://stable/elasticsearch
Enter the namespace to deploy to: elasticsearch

  • Pulling upstream ✓
  • Creating base ✓
  • Creating midstream ✓
  • Creating downstream "this-cluster" ✓
  • Deploying Admin Console
    • Creating namespace ✓
    • Waiting for datastore to be ready ✓
Enter a new password to be used for the Admin Console: ••••••••
  • Waiting for Admin Console to be ready ✓
Application name: elasticsearch
  • Uploading local application to Admin Console ✓

  • Press Ctrl+C to exit
  • Go to http://localhost:8800 to access the Admin Console
```

Once this has completed, the kots plugin will create a port-forward to the Admin Console interface. The Admin Console API and Web server are exposed over a ClusterIP service in the namespace provided. The port-forward will be active as long as the CLI is running. Pressing Ctrl+C will end the port forward.

Once this has completed, click the link, or visit http://localhost:8800 to complete the setup using the Admin Console web-based UI.

## Web Based Setup

At this point, visit http://localhost:8800 to complete the setup of the application.

### Unlock the Admin Console
![Secure Console](/images/secure-console.png)

Enter the password provided during the setup, and you'll be redirect to the "Upload License" screen.

### Config Screen
Most KOTS applications include some required and some optional configuration. This is used to build the final, deployable Kubernetes manifests for the application. The config screen of the setup will prompt for initial values to use in the application. These can be changed later, but must be completed to continue. Note that this section is optional, and depends whether the Helm chart maintainer has included the `kots.io/v1beta` `Config` custom resource. In either case, you will still be able to [edit the values.yaml](#viewing-files-and-configuring-chart-values) before deploying the application.

![Initial Config](/images/initial-config.png)

### Preflight Checks
Finally, Preflight checks (conformance tests) are executed against the target namespace and cluster to ensure that the environment meets the minimum requirements to support the application. **Note** as with the `Config` custom resource, not all community Helm charts will include a `kots.io/v1beta` `Preflight` specification, in which case this page will be skipped.

![Preflight Checks](/images/preflight-checks.png)

### Deploying the Application

At this point, the application is ready, but has not been deployed yet. You will land on the main "Dashboard" page, where you can review the overall state and [set your Prometheus endpoint to display graphs on the dashboard](../../monitoring/existing-prometheus).


![Dashboard](/images/kotsadm-helm-main-dashboard.png)

From here, navigate to `Version History`, where you will see a single release staged for deployment.

![Version History](/images/kotsadm-helm-version-history.png)

Click "Deploy" to deploy the application to the namespace alongside the `kotsadm` console.

![Deployed](/images/kotsadm-helm-version-deployed.png)

### Viewing Files and Configuring Chart Values

To review the YAML that was deployed, head to the `View Files` tab of the console:


![View Files](/images/kotsadm-helm-view-files.png)

To modify the Chart Values or default [Kustomize](https://kustomize.io) overlays, follow the instructions to upload/download the application for editing on your local workstation:


![Download Upload](/images/kotsadm-helm-download-upload.png)

To modify specifically the values file for this `elasticsearch` chart, we'll run the following:

```shell
kubectl kots download elasticsearch --namespace elasticsearch --dest ~/elasticsearch
cd ~/elasticsearch
vim ./upstream/values.yaml # edit in your shell or favorite IDE
kubectl kots rebuild # re-template chart with new values.yaml
kubectl kots upload --namespace elasticsearch --slug elasticsearch ~/elasticsearch
```

From here, you can head over to the "Version History" page, where a new version will be created for review and deployment.


![New Version](/images/kotsadm-helm-upload-changes.png)

You can click the `+1 -1` to review the diff of changes:


![Upload Diff](/images/kotsadm-helm-upload-diff.png)

When you are ready, click "Deploy" to ship the changes.


### Next Steps: GitOps and Registries

By default, `kotsadm` is set up for a simple click-to-deploy workflow, to enable quick bootstrapping and testing. Once you've iterated on the values file and you're happy with your yaml, you can [configure gitops](../../gitops) for the chart, so that upstream chart updates will cause new pull requests to be made into a VCS your choice.

Similarly, you can [configure a private registry](../../registries) so that when a new version of the chart requires a new Docker image, kotsadm can

- Pull that image from the public internet
- Tag and push it to your internal registry
- Rewrite the new chart YAML with a [Kustomize](https://kustomize.io) patch to pull the image from your registry instead of the public internet
