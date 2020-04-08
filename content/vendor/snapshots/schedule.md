---
date: 2019-01-23
linktitle: "Schedules"
isAlpha: true
weight: 5
title: Schedules
---

Navigate to the `/app/<slug>/snapshots/schedule` page to configure automatic snapshots and to configure snapshot retention.

## Retention

The default retention period for snapshots is 1 month.
Setting the retention only affects snapshots created after the time of the change.
For example, if an existing snapshot had a retention of 1 year and is already 6 months old, and a user then uses the UI to change the retention to 1 month, the existing snapshot will still be around for another 6 months.
