---
date: 2020-12-08
linktitle: "Securing the Console"
title: Securing access to the KOTS Admin Console
weight: 1
draft: false
isBeta: true
---

Upon initial installation, the KOTS Admin Console is secured with a single shared password for all users.
It is possible to further configure the KOTS Admin Console to authenticate users with your organization's user management system.
This feature is only available for licenses that have the Identity Service feature enabled.

KOTS leverages the open-source project [Dex](https://dexidp.io/) as an intermediary to control access to the console.
Dex implements an array of protocols for querying other user-management systems, known as [connectors](https://dexidp.io/docs/connectors/).

## Configuration

To begin, click the Access link at the top of the Admin Console.
Here you can configure access to the KOTS Admin Console, integrating with one of the supported identity providers.

![Configure Identity Provider](/images/access-identity.png)


## Role Based Access Control

It is also possible to regulate access to the Admin Console resources based on the roles of individual users within your organization.

To begin, click on "Add a group" under the "Role Based Access Control" secion.
Then, enter a group name that matches one of the group names already established with your identity provider. 
Next, choose one of the pre-defined Admin Console roles to be assigned to that group, and click "Add group".

![Role Based Access Control](/images/identity-service-kotsadm-rbac.png)

## Supported Providers

**[OpenID Connect](https://openid.net/connect/)**

## Resetting Authentication

When enabling identity provider access to the Admin Console, shared password authentication will be disabled.
In case authentication needs to be reset, the [`kubectl kots identity-service enable-shared-password --namespace [namespace]`](/kots-cli/identity-service-enable-shared-password/) command can be run to re-enable shared password authentication.
