---
date: 2019-01-23
linktitle: "Backend Stores"
title: Backend Stores
---

Velero uploads backup data to an object store.
Customers should navigate to the `/apps/<slug>/snapshots/settings` page of the admin console to configure an object store.
There are four options.
All options require a bucket name and permit an optional prefix in the bucket.

* AWS S3
* S3 compatible
* Azure
* GCS

By default on embedded clusters the in-cluster Ceph RGW object store is configured as the backend.
