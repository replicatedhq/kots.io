---
date: 2021-08-09
linktitle: "Tokens"
title: Using Vendor API Tokens
weight: 90105
draft: false
---

Using the [Vendor CLI](https://help.replicated.com/api/replicated-vendor-cli/) and [REST API](https://help.replicated.com/api/vendor-api/) requires a token for authorization.  Replicated supports two types of tokens: Service Accounts and User Tokens.

Tokens may only access a portion of the Vendor API. The following administrative tasks cannot be performed with token authentication and can only be executed by a logged-in user: 
- Managing team members
- Managing tokens
- Managing notifications
- Managing integrations
- Managing user tokens

Tokens are primarily used with [Vendor CLI](https://help.replicated.com/api/replicated-vendor-cli/) or [REST API](https://help.replicated.com/api/vendor-api/) commands for automated customer, channel, and release management.

## Service Accounts

Service accounts are assigned a token and associated with an RBAC policy. Any user with the proper permissions can create, retrieve or revoke them.

Service account names must be unique within a given team. Admin users can assign any RBAC policy to a service account. Non-admin users can only assign their own RBAC policy.

Service account tokens are only displayed once when created. The token may never be retrieved again after initial creation. It is highly recommended to store the token in a safe place, such as a password vault.

Updates to a service account's RBAC policy are automatically applied to its associated token. When a service account is removed, it's token is also invalidated.

## User Tokens

User tokens are private to the user creating the token. User tokens assume the user's account when used, including RBAC permissions.

User token names must be unique per user.

User tokens are only displayed once when created. The token may never be retrieved again after initial creation. It is highly recommended to store the token in a safe place, such as a password vault.

Revoking a user token will immediately invalidate that token.

Updates to a user's RBAC role are applied to the user's tokens. A removed user's tokens are immediately invalidated.
