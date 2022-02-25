---
date: 2019-10-09
linktitle: "Creating Entitlements"
title: Using and Creating Entitlements
description: "All applications created in Replicated Vendor require a valid license file to install. This license file contains some standard, pre-built entitlements, but can also include any custom entitlement fields required by the application. Kots will securely deliver these entitlement values and make them available when building the Kubernetes manifests or at runtime using the titled API"
weight: 20020
aliases: 
  - /vendor/entitlements/entitlements
  - /vendor/entitlements/custom-entitlements
  - /vendor/entitlements/custom-entitlments
---

KOTS applications that are packaged and delivered through the Replicated [Vendor Portal](https://vendor.replicated.com) use per-customer licenses to install. 
Each of these licenses uniquely identifies the customer, defines their release channel, and defines _entitlement information_ about the customer.  

KOTS securely delivers these entitlement values to the application, and makes them available in the Kubernetes manifests or at runtime using the Admin Console license API.

## Built-In Entitlement Fields
See [Built-In Entitlements](/vendor/entitlements/built-in-entitlements), for a list of the entitlement fields that are included by default for every license.  

## Creating Entitlement License Fields
To create new entitlement fields, first click the "License Fields" section in the Vendor UI with your application selected. 

![License Fields](/images/license-fields.png)

On this page, you can see a list of the existing custom entitlement fields, edit existing fields, or create new fields. 

![License Fields](/images/license-fields-create.png)

To create a new field, click the "Create custom field" link and provide the following information: 

* **Field** The name of the field. 
This is the name used to reference the field, and as such, is generally not changeable. 
* **Title** The display name of the field. 
This is how the field will appear in the Vendor UI and the Admin Console (if visible). Easily changed through Vendor UI. 
* **Type** The type of the field. 
We presently support integer, string, text (multi-line string), and boolean values. Cannot be changed.
* **Default** The default value to set for the field on existing customers, or when a new customer is created. 
It is generally considered good practice to provide a default, where possible. 
* **Required** If set, this will prevent the creation of customers unless this field is explicitly set with a value. 
* **Hidden** If set, this hide the field from being visible in the Admin Console. 
It will remain visible in Vendor UI. 
Useful for fields like internal IDs, that are not meaningful to the end-user. 

After the license field has been created, it will be shown for all customers created in the Vendor UI. 
If the field is not hidden, it will additionally shown under the "Licenses" tab for customers within the Admin Console. 
 ![License Fields](/images/license-fields-customer.png)

### Updating Entitlement License Fields
To update a custom license field:
1. Log in to the vendor portal and select the application.
1. On the **License Fields** page, click **Edit Field** icon on the right side of the target row.

The following attributes of the custom license fields can be updated.
   * **Title** This can be changed without any special behavior.
   * **Default** Updating the default will update each customer record that hasn't overridden the default value.
   * **Is this field is required?** When updated to on, this field will become required on all existing licenses. If you do not set a default, you'll need to update each license manually.
   * **Is this field hidden?** This can be changed without any special behavior.

## Deleting Entitlement License Fields
To delete a custom license field:
1. Log in to the vendor portal and select the application.
1. On the **License Fields** page, click **Edit Field** icon on the right side of the target row.
1. Select delete on the bottom left of the modal and follow the instructions on the following screen to confirm.

License fields can be deleted, but this should be carefully considered. OUtages can occur when existing deployments where your application or the app manager config screen are expecting a license to provide a required value. As such, we have limited the RBAC policy to administrators and added several levels of warnings on deletion.

Deleted license fields and their values do not show up in the customer's license in any location (such as your view in the vendor portal, the downloaded YAML version of the license or the app manager license screen).

By default, deleting a license field also deletes all of the values associated with that field in each customer record. 

When the Preserve License Values option is selected, the values for the field not set by the default are orphaned in each customer record (this is not visible to you or your customer). If you recreate a field with this exact name, the orphaned values are reinstated. To bring default values back to licenses that didn't override the value, use the same default value during recreation. 

### Writing Entitlements to Manifests
The [LicenseFieldValue](/reference/template-functions/license-context) template function exists to access entitlements when installing or updating the application. 
To illustrate how to use this, let's create a custom entitlement to limit the number of users that a license is permitted to use.

This entitlement will look like:

| Name | Key | Type | Description | Default Value |
|------|-----|------|-------------|---------------|
| Seat Count | seat_count | Integer | The maximum number of users permitted | 50 |

For this example, we will write this value to an environment variable in a pod that is part of an API deployment:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api
spec:
  selector:
    matchLabels:
      app: api
  template:
    spec:
      containers:
      - image: myapp/api:v1.0.1
        name: api
        env:
          - name: SEAT_COUNT
            value: '{{repl LicenseFieldValue "seat_count" }}
```

### Querying Entitlements at Runtime
The admin console runs on the customer's cluster, and provides real-time entitlement information through the following endpoint: 
>`/license/v1/license`

To query entitlements at runtime, execute an http request to the following location: 
>`http://kotsadm:3000/license/v1/license`

The response will be returned in YAML format. 
For example: 
```javascript
{"license_id":"WicPRaoCv1pJ57ZMf-iYRxTj25eZalw3",
"installation_id":"a4r1s31mj48qw03b5vwbxvm5x0fqtdl6",
"assignee":"FirstCustomer",
"release_channel":"Unstable",
"license_type":"trial",
"expiration_time":"2026-01-23T00:00:00Z",
"fields":[
  {"field":"Customer ID","title":"Customer ID (Internal)","type":"Integer","value":121,"hide_from_customer":true},
  {"field":"Modules","title":"Enabled Modules","type":"String","value":"Analytics, Integration"}]}
```

To return the entitlement value of interest, parse the above response using the name of the built-in or custom entitlement. 
The following is an example in Javascript to demonstrate: 

```javascript
import * as rp from "request-promise";

rp({
  uri: "http://kotsadm:3000/license/v1/license",
  json: true
}).then(license => {
  const seatCount = license.fields.find((field) => {
    return field.field === "seat_count";
  });
  console.log(seatCount.value);
}).catch(err => {
  // Handle error response from `kotsadm`
});
```
