---
date: 2021-08-09
linktitle: "Tokens"
title: Using Vendor API Tokens
weight: 90105
draft: false
---

Using the Vendor CLI requires tokens for authentication.  Replicated supports two types of tokens: Team tokens and User tokens.

Tokens may only access a portion of the Vendor API. Administrative tasks such as adding a new user, creating a new token, or changing a password cannot be performed with token authentication.  These administrative tasks must be performed by a logged-in user.

Tokens are primarily used with vendor CLI commands for automated customer, channel, and release management.

## Team Tokens

Team tokens are available to all members of the vendor team. These tokens can be created, retrieved, and revoked by any user with the proper RBAC policy.

Team tokens can be set to Read Only or Read / Write.

## User Tokens

User tokens are private to each user. User tokens assume the user's account when used, including RBAC permissions.

User token names must be unique per user.

User tokens will only be displayed to the user once during creation. The token may never be retrieved again after initial creation. It is highly recommended to store the token in a safe place such as a password vault.

User tokens may be revoked by the user at any time. Revoked tokens are immediately invalidated.

Updates to a user's RBAC role are applied to the user's tokens. A removed user's tokens are immediately invalidated.