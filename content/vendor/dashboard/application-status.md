---
date: 2019-10-09
linktitle: "Application Status"
title: Determining Application Status
weight: 20020
---

Once your application has been installed in your customer's cluster, it's helpful to report status back to the end user. This status will be displayed on the dashboard page of the Admin Console.

With minimal additional YAML you can instruct kotsadm on what resources to watch in the cluster. We suggest that you add at least one resource. Resources that are currently supported are Deployments, StatefulSets, Services, Ingresses and PersistentVolumeClaims.

To add an informer, simply include the `statusInformers` property in your kots.io Application spec. Status informers are in the format `[namespace/]type/name` where namespace is optional.

```yaml
apiVersion: kots.io/v1beta1
kind: Application
metadata:
  name: my-application
spec:
  statusInformers:
    - deployment/my-web-svc
    - deployment/my-worker
```
