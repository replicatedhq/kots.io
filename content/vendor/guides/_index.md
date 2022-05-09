---
date: "2020-02-20T00:00:00Z"
lastmod: "2020-02-20T00:00:00Z"
title: "Guides"
weight: "2"
---

End-to-end interactive guides to get you familiar with using the KOTS platform.

### Getting Started

These "Hello World" guides will help you get an end-to-end KOTS installation running quickly.

###### [Quickstart](/vendor/guides/quickstart)

The KOTS Quickstart is our simplest guide, we'll get running quickly with a simple Nginx application in Kubernetes using a single VM.

###### [Existing Cluster](/vendor/guides/existing-cluster)

The KOTS Existing Cluster Guide is another of our simplest guides. 
We'll get running quickly with a simple Nginx application on an existing cluster in GKE (or another cluster you have handy).

###### [CLI Quickstart](/vendor/guides/cli-quickstart)

Power users who already have their Kubernetes YAML handy and prefer to work from the CLI will want to start here. This guide will walk you through managing your YAML or Helm chart in KOTS with our CLI tools.

### Next Steps

These guides will assume you've completed one of the previous guides for either [Embedded Cluster](/vendor/guides/quickstart) or [Existing Cluster](/vendor/guides/existing-cluster) delivery.

###### [Integrate Persistent Datastores](/vendor/guides/persistent-datastores)

In this guide, we'll review best practices for integrating persistent stores like databases, queues, and caches. 
We'll explore ways to give your end user the option to either embed an instance with the application, or connect your application to an external cluster that they will manage. 
We'll use a PostgreSQL database as an example, configuring an example app to connect.

###### [Airgapped Cluster](/vendor/guides/airgapped-cluster)

The KOTS Airgapped Cluster is an advanced guide that shows how to install a KOTS app in an Airgapped environment.


###### [CI/CD Integration](/vendor/guides/ci-cd-integration)

The KOTS CI/CD Integration guide is an advanced workflow that allows you to use a similar workflow to KOTS Starter GitHub Actions, but for your own internal git repository and CI/CD systems like GitHub, GitLab, CircleCI, TravisCI, Jenkins, etc.


###### [Prepare for Production Deploys](/vendor/guides/production-ready)

This guide is intended as a lightweight checklist to ensure you have explored all the features of KOTS and integrated the ones that will help ensure your end users are most likely to be successful deploying and running your KOTS application


###### [Deploy an HA Cluster](/vendor/guides/ha-guide)

This guide is intented to guide the reader in setting up a basic 3 primary node Kubernetes cluster in an [Embedded Cluster](/vendor/guides/quickstart) scenario for learning and testing purposes.


<!-- Coming Soon!

###### [Package a Helm Chart](/vendor/guides/helm-chart)

In this guide, we'll explore how you can leverage the wealth of software bundled using Helm to integrate an off-the-shelf application into your KOTS app bundle. We'll use the Consul helm chart as an example in this case.

###### [Integrate Collectors and Analyzers for Troubleshooting](/vendor/guides/troubleshoot-support-bundle)

###### [Add custom Prometheus Graphs](/vendor/guides/prometheus-graphs)

###### [Manage Customer Entitlements](/vendor/guides/entitlements)

-->


## Advanced

#### [Airgapped Existing Cluster](/vendor/guides/airgapped-existing-cluster)

This guide covers an end-to-end example of deploying to an airgapped existing cluster with KOTS.
It walks through all the steps to create an airgapped environment in GCP, including the Kubernetes cluster setup, Docker Registry configuration, local workstation setup, and a jump box / bastion for accessing the airgapped instances.
Our goal will be to mimick an end-customers "existing cluster" setup.
Once set up, we walk through the steps for validating the environment is airgapped and the steps to perform a KOTS installation to the "existing cluster" environment.

<!-- Coming Soon!
###### [Runtime License Validation](/vendor/guides/runtime-license-validation)

#### [Advanced Embedded Cluster Usage](/vendor/guides/advanced-embedded-cluster)

- locking component versions
- staging Installer YAMLs through Unstable/Beta/Stable channels
- manging YAML in git repo
- preflight checks for embedded components when running in existing cluster (e.g. storageclass)


#### [Deploy an Operator to an airgapped cluster](/vendor/guides/operator-airgap)

#### [Deploy a Helm Chart to an airgapped cluster](/vendor/guides/helm-airgap)

#### [Deploy a Custom Preflight Check](/vendor/guides/custom-preflight)

#### [GitOps Workflow](/vendor/guides/gitops)

(Coming soon)

-->
