---
date: 2020-04-29
linktitle: "Getting Started"
title: Getting Started with Replicated Enterprise CLI
description: "The Replicated Enterprise CLI is a command-line interface that helps enterprises manage their channels, policies and installers."
weight: 90100
draft: false
---

The Replicated Enterprise CLI is a command-line interface that helps enterprises manage their channels, policies and installers.

## Prerequisites

* [curl](https://curl.haxx.se/) (For running CLI directly on linux/mac)
* [docker](https://www.docker.com) (For running CLI through docker container)


## Installing The Replicated CLI

There are three options for installing the Replicated CLI. 

* Running the CLI directly on mac
* Running the CLI directly on linux
* Running the CLI through Docker (Useful for Windows, GitHub Actions, or computers without sufficient access). 

### MacOS

To install latest Replicated CLI on Mac with Brew:

```shell
brew install replicatedhq/replicated/cli
```

To install latest Replicated CLI on Mac without Brew: 
```shell
curl -s https://api.github.com/repos/replicatedhq/replicated/releases/latest \
| grep "browser_download_url.*darwin_amd64.tar.gz" \
| cut -d : -f 2,3 \
| tr -d \" \
| wget -O replicated.tar.gz -qi -
tar xf replicated.tar.gz replicated && rm replicated.tar.gz
mv replicated /usr/local/bin/replicated
```

### Linux

To install latest Replicated CLI on Linux:

```shell
curl -s https://api.github.com/repos/replicatedhq/replicated/releases/latest \
| grep "browser_download_url.*linux_amd64.tar.gz" \
| cut -d : -f 2,3 \
| tr -d \" \
| wget -O replicated.tar.gz -qi -
tar xf replicated.tar.gz replicated && rm replicated.tar.gz
mv replicated /usr/local/bin/replicated
```

### Docker / Windows

To run latest Replicated CLI through a docker container:

```shell
docker run replicated/vendor-cli --help
```

For simplicity, we will represent usage assuming the CLI has been downloaded and installed to the desktop. 
