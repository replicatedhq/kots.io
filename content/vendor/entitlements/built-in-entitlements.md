---
date: 2019-12-06
linktitle: "Built-In Entitlements"
title: Built-In Entitlements
weight: 20060
---

All KOTS applications that are packaged and delivered through the Replicated [Vendor Portal](https://vendor.replicated.com) include built-in entitlement fields. The application vendor can specify the values for these fields, but these are reserved field names and are available in every license.

## List of Built-In Fields

### License Expiration Date

**Field Name**: `expires_at`

**Description**: If a license has an expiration date, the expiration date will be included in the license here. The date is encoded in ISO 8601 format (e.g., `2026-01-23T00:00:00Z`). If a license does not expire, this field will be missing.

### Airgap Support

**Field Name**: `isAirgapSupported`

**Description**: If a license supports airgap installations, this field will be present and set to `true`. If airgap is not supported, this field will be missing.

### License Type

**Field Name**: `license_type`

**Description**: This field contains a string value that describes the type of the license. This is currently one of `paid`, `trial`, `dev` or `community`.

### GitOps Support

**Field Name**: `isGitOpsSupported`

**Description**: If a license supports gitops-enablement in the Admin Console, this field will be present and set to `true`. If gitops is not supported, this field is either `false` or missing.

## Using Built-In Fields

Built-In entitlement fields are referenced the nearly the same as you would for [Custom Entitlement Fields](/vendor/entitlements/custom-entitlements).  

* **Within application manifests**: Use the [LicenseFieldValue](/reference/template-functions/license-context) function to return the value of the corresponding built-in field (e.g., `value: '{{repl LicenseFieldValue "expires_at" }}'`
* **Within the application**: Execute an http request to `http://kotsadm:3000/license/v1/license` to return the license API response, then parse the response to find the return the corresponding license field value.