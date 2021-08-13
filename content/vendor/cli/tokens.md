---
date: 2021-08-09
linktitle: "Tokens"
title: Using Vendor API Tokens
weight: 90105
draft: false
---

Using the Vendor CLI and REST API requires a token for authorization.  Replicated supports two types of tokens: Team Tokens and User Tokens.

Tokens may only access a portion of the Vendor API. The following administrative tasks cannot be performed with token authentication and can only be executed by a logged-in user: 
- Managing team members
- Managing tokens
- Managing notifications
- Managing integrations
- Managing user tokens

Tokens are primarily used with vendor CLI commands for automated customer, channel, and release management.

## Team Tokens

Team tokens should be used for CLI authorization.

Team tokens are available to all members of the vendor team. These tokens can be created, retrieved, and revoked by any user with the proper RBAC policy.

Team tokens can be set to Read Only or Read / Write.

## User Tokens

User tokens should be used for REST API authorization.

User tokens are private to the user creating the token. User tokens assume the user's account when used, including RBAC permissions.

User token names must be unique per user.

User tokens are only displayed once when created. The token may never be retrieved again after initial creation. It is highly recommended to store the token in a safe place, such as a password vault.

Revoking a user token will immediately invalidate that token.

Updates to a user's RBAC role are applied to the user's tokens. A removed user's tokens are immediately invalidated.
