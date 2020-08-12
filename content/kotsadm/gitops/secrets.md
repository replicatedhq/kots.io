---
date: 2020-08-12
linktitle: "Secrets"
title: Managing Secrets with GitOps
weight: 10040
isAlpha: true
---

When enabling GitOps, the KOTS Admin Console will push the rendered application manifests to the configured git repository.
Application manifests often contain secrets and sensitive information that should not be commited to git.

KOTS 1.19 and introduces an integration with [SealedSecrets](https://github.com/bitnami-labs/sealed-secrets) to encrypt secrets before commiting. This integration is currently alpha and subject to change in future releases of KOTS.

To enable this integration, a Secret with specific labels must be deployed to the same namespace as the Admin Console. This secret must contain the SealedSecrets public key and will be used be KOTS to replace all Secret objects created by the application and by the Admin Console.

This secret should be manually deployed to the same namespace as the KOTS Admin Console.
There is not currently functionality to automate or use the Admin Console UI to configure this functionality.
The secret can be named anything unique that does not conflict with application secrets. The labels in this example YAML are important and must remain.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: kots-sealed-secret
  namespace: <same as kots>
  labels:
    kots.io/buildphase: secret
    kots.io/secrettype: sealedsecrets
data:
  cert.pem: <sealed secret public key, base64 encoded>
```
