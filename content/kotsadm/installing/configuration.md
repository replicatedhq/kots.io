---
date: 2020-05-13
linktitle: "Customization and Configuration"
title: Customization and Configuration
weight: 7
isAlpha: true
---

While the Admin Console includes all necessary dependencies to run, it might be desirable to use externally provided instances instead of the built in services.

## Storage

By default, the Admin Console requires an S3-Compatible Object Store to operate. This is used to store archives of every application version and the support bundles that are generated. Without any additional parameters provided, KOTS will provide this as a Minio deployment or a Rook provided object store.

Beginning with KOTS 1.16.0, it's possible to use a container registry that supports [OCI Artifacts](https://github.com/opencontainers/artifacts) instead of S3. Currently, this includes:

- [docker/distribution](https://github.com/deislabs/oras/blob/master/implementors.md#docker-distribution)
- [Azure Container Registry](https://github.com/deislabs/oras/blob/master/implementors.md#azure-container-registry-acr)

When installing the Admin Console, KOTS can also provide the docker/distribution service locally, instead of Minio.

To enable this alpha feature with the bundled registry:

```shell
kubectl kots install \
  --storage-backend=oci
  <application>
```

The enable this alpha feature with an external registry:

```shell
kubectl kots install \
  --storage-backend=oci \
  --registry-endpoint=docker://azurecr.io/my-app \
  --registry-secret=already-deployment-secret
  <application>
```

