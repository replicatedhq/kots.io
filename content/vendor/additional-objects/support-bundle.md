---
date: 2019-11-27
linktitle: "Support Bundle"
title: Support Bundle
weight: 20050
---

The Support Bundle diagnostic tool collects, redacts, and analyzes troubleshooting data from a cluster, to help diagnose problems with application deployments. The tool depends on two Custom Resources.

A [Collector Spec](https://troubleshoot.sh/reference/collectors/overview/) defines the data to be collected and included in the .tar.gz bundle.

An [Analyzer Spec](https://troubleshoot.sh/reference/analyzers/overview/) defines how to interpret and present the collected data to the application operator.

Both support-bundle CRs are optional for KOTS applications.

Support Bundle collection and analysis are integrated into the [Admin Console Dashboard](/kotsadm/troubleshooting/support-bundle/), and are also available through a [standalone kubectl plugin](https://troubleshoot.sh/docs/support-bundle/collecting/).
