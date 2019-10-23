---
date: 2019-10-09
linktitle: "Kurl"
title: Kubernetes Installers
weight: 90020
draft: false
---

The Kubernetes Installers (kurl) reference documentation.

## Add Ons

Each addon is listed with all supported keys, and the default for the key, if not present.

### kubernetes

```yaml
spec:
  kubernetes:
    version: "1.15.3"
    serviceCIDR: "10.0.0.0/8"
```

### Docker

```yaml
spec:
  docker:
    version: "18.09"
    bypassStorageDriverWarnings: false
    hardFailOnLoopback: false
    noCEOnEE: false
```

### Registry

```yaml
spec:
  registry:
    version: ""
     
```

### Weave

```yaml
spec:
  weave:
    version: ""
    IPAllocRange: false
    encryptNetwork: true
```

### Rook

```yaml
spec:
  rook:
    version: ""
    storageClass: "default"
    cephPoolReplicas: 3
```

### Contour

```yaml
spec:
  contour:
    version: ""
```

### Prometheus

```yaml
spec:
  prometheus:
    version: ""
```

### Kotsadm

```yaml
spec:
  kotsadm:
    version: "0.9.12"
    applicationSlug: ""
    uiBindPort: 8800
```


