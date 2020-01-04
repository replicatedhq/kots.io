---
date: 2019-10-23
linktitle: "Overview"
title: Packaging KOTS with Vendor
weight: 10
---

Vendor is the UI for the packaging and management of Kubernetes-off-the-shelf (KOTS) applications. 

* **Packaging**: Tools to define, link, patch, validate, and maintain the manifests that define the application and how it works. 
* **Management**: Tools to manage customers, licenses/entitlements, Admin Console UI, releases/release channels, and troubleshooting. 

Importantly, Vendor does all of this by leveraging open source tools like [Kurl.sh](https://kurl.sh), [troubleshoot.sh](https://troubleshoot.sh) (pre-flights and support bundling), Kustomize, Helm, and [KOTS](https://kots.io). 

This document starts with an overview on how to use Vendor package and manage KOTS applications, then goes into greater detail within each section on this page. Please refer to our documentation of the [KOTS CLI](https://kots.io/kots-cli/getting-started/) to manage KOTS via the command line, or our [Reference Documentation](/reference/v1beta1/) for in-depth documentation of all capabilities. 

# Getting Started with Vendor

It's easy to get started with Vendor. Create your Vendor account at our [Vendor Portal](https://vendor.replicated.com) and follow the promts to create a new application. It is highly recommended to follow the steps from one of our example applications (such as the [Quickstart for KOTS Sentry](https://github.com/replicatedhq/kots-sentry) to gain familiarity with how to package and maintain a KOTS application with Vendor.  

# Installation Overview
When installing a packaged KOTS application, customizable Pre-flight checks test a cluster for necessary resources or other dependencies, and show warnings or errors if the target cluster fails any of these checks.  The Application specs provide branding metadata (icon, title, descriptions, etc...), readiness checks, and end-user links, enabling the `kotsadm` console to show application health and readiness and launch the app once it's ready.

KOTS licenses are created and managed through the Replicated [Vendor Portal](https://vendor.replicated.com), which supports product assortment and differentiation with configurable entitlements, expiration dates, and license types.

The Config spec controls a dynamic configuration page which collects site-specific values from the operator and exposes generated values such as internal access tokens and connection strings.

# Day-2 Operations

The Support Bundle spec from [troubleshoot.sh](https://troubleshoot.sh) controls the collection and analysis of diagnostic info to simplify troubleshooting application issues.

Application updates can be reviewed and audited before they're applied.  Release Notes from the Application spec provide a high-level description of each release, and the `kotsadm` dashboard (See [Admin Console](/kotsadm/installing/installing-a-kots-app/))provides a version history and diff-viewer to highlight any changes in deployment specs from one version to the next.


