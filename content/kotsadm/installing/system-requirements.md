---
date: 2019-10-09
linktitle: "System Requirements"
title: "System Requirements for KOTS Apps"
weight: 10012
draft: false
---

This page describes the system requirements for executing and installing KOTS applications. The requirements for command-line-interfaces (such as the [Vendor CLI](/vendor/cli/getting-started) and the [KOTS CLI](/kots-cli/getting-started)) are described separately, and are not subject to these requirements. 

## Supported Operating Systems
* Ubuntu 16.04 (Kernel version >= 4.15)
* Ubuntu 18.04 (Recommended)
* CentOS 7.4, 7.5, 7.6
* RHEL 7.4, 7.5, 7.6

## Minimum System Requirements 
* 2 CPUs or more 
* 4 GB or more of RAM per machine
* TCP ports 6443 and 6783 open
* UDP ports 6783 and 6784 open

## Kubernetes Version Compatibility

Each release of KOTS maintains compatability with the current Kubernetes version, and the 2 most recent versions. This includes support against all patch releases of the corrersponding Kubernetes version. 

| KOTS Version                 | Release Date | Kubernetes Compatibility |
|:----------------------|------|-------------|
| 1.11 | 01-22-20 | 1.17, 1.16, and 1.15 | 
