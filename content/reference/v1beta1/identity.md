---
date: 2020-12-23
linktitle: "Identity"
title: Identity
description: "The Identity spec contains vendor-supplied configuration for the KOTS Identity Service."
weight: 8
aliases:
  - /vendor/additional-objects/identity
---

The KOTS Identity custom resource enables and configures the KOTS Identity Service for the vendor application.

The `Identity` spec contains vendor-supplied configuration for the KOTS Identity Service.

```yaml
apiVersion: kots.io/v1beta1
kind: Identity
metadata:
  name: my-application
spec:
    identityIssuerURL: https://{{repl ConfigOption "ingress_hostname"}}/dex
    oidcRedirectUris:
      - https://{{repl ConfigOption "ingress_hostname"}}/oidc/login/callback
    supportedProviders: [ oidc ]
    requireIdentityProvider: true
    roles:
      - id: member
        name: Member
        description: Can see every member and non-secret team in the organization.
      - id: owner
        name: Owner
        description: Has full administrative access to the entire organization.
    oauth2AlwaysShowLoginScreen: false
    signingKeysExpiration: 6h
    idTokensExpiration: 24h
```

## identityIssuerURL
**(required)** This is the canonical URL that all clients MUST use to refer to the OIDC identity service.
If a path is provided, the HTTP service will listen at a non-root URL.

## oidcRedirectUris
**(required)** A registered set of redirect URIs.
When redirecting from the KOTS identity OIDC server to the client, the URI requested to redirect to MUST match one of these values.

## supportedProviders
A list of supported identity providers.
If unspecified, all providers will be available.

## requireIdentityProvider
If true, require the identity provider configuration to be set by the customer before the app can be deployed.

## roles
**(`id` required)** A list of roles to be mapped to identity provider groups by the customer on the KOTS Admin Console Identity Service configuration page.

## oauth2AlwaysShowLoginScreen
If true, show the identity provider selection screen even if there's only one configured.
Default `false`.

## signingKeysExpiration
Defines the duration of time after which the SigningKeys will be rotated.
Default `6h`.

## idTokensExpiration
Defines the duration of time for which the IdTokens will be valid.
Default `24h`.
