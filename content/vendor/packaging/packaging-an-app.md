---
date: 2019-10-23
linktitle: "Overview"
title: Packaging An Application
weight: 20010
---

A KOTS application is a Kubernetes application with a few additional Custom Resource Definitions to support application lifecycle management via the `kubectl kots` plugin and the `kotsadm` admin console.  When an application vendor adds Pre-flight, Application, Config, Entitlement, and Support-Bundle specs to their application,  users will have a much-improved experience with installation and day-2 operations.

# Installation
When installing a KOTS application, customizable Pre-flight checks test a cluster for necessary resources or other dependencies, and show warnings or errors if the target cluster fails any of these checks.  The Application specs provide branding metadata (icon, title, descriptions, etc...), readiness checks, and end-user links, enabling the `kotsadm` console to show application health and readiness and launch the app once it's ready.

KOTS licenses are created and managed through the Replicated [Vendor Portal](https://vendor.replicated.com), which supports product assortment and differentiation with configurable entitlements, expiration dates, and license types.

The Config spec controls a dynamic configuration page which collects site-specific values from the operator and exposes generated values such as internal access tokens and connection strings.

# Day-2 Operations

The Support Bundle spec controls the collection and analysis of diagnostic info to simplify troubleshooting application issues.

Application updates can be reviewed and audited before they're applied.  Release Notes from the Application spec provide a high-level description of each release, and the `kotsadm` dashboard provides a version history and diff-viewer to highlight any changes in deployment specs from one version to the next.

# Getting Started

To get started creating a KOTS application, bring a Kubernetes application to the [Vendor Portal](https://vendor.replicated.com) and create a new application.  Each of the Custom Resources described above is optional, making it simple to iterate and refine the application specs incrementally.
