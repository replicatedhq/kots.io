---
date: 2019-12-06
linktitle: "Built-In Entitlements"
title: Built-In Entitlements
weight: 20020
---

All KOTS applications that are packaged and delivered the the Replicated [Vendor Portal](https://vendor.replicated.com) include some built-in entitlements. The application vendor can specify the values for these fields, but these are reserved field names and are available in every license.

### License Expiration Date

**Field Name**: `expires_at`
**Description**: If a license has an expiration date, the expiration date will be included in the license here. The date is encoded in ISO 8601 format. If a license does not expire, this field will be missing.

### Airgap Support

**Field Name**: `isAirgapSupported`
**Description**: If a license supports airgap installations, this field will be present and set to `true`. If airgap is not supported, this field is either `false` or missing.

### License Type

**Field Name**: `type`
**Description**: This field contains a string value that describes the type of the license. This is currently one of `paid`, `trial`, `dev` or `community`.

### GitOps Support

**Field Name**: `isGitOpsSupported`
**Description**: If a license supports gitops-enablement in the Admin Console, this field will be present and set to `true`. If gitops is not supported, this field is either `false` or missing.

