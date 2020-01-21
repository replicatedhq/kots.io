---
date: 2019-10-09
linktitle: "Getting Started"
title: Getting Started with Vendor CLI
description: "The Replicated Vendor CLI is a command-line interface that helps vendors package KOTS applications using a command-line interface, including the management of releases, channels, customers, and entitlements."
weight: 90100
draft: false
---

The Replicated Vendor CLI is a command-line interface that helps vendors package KOTS applications using a command-line interface, including the management of releases, channels, customers, and entitlements.

## Prerequisites

This guide already assumes that you have created a team and an initial KOTS application in [Vendor](/vendor/packaging). You'll also need: 

* [curl](https://curl.haxx.se/) (For running CLI directly on linux/mac)
* [docker](https://www.docker.com) (For running CLI through docker container)


## Configure Environment

You’ll need to set up two environment variables to interact with https://vendor.replicated.com: `REPLICATED_APP` and `REPLICATED_API_TOKEN`. 


* `REPLICATED_APP` should be set to the name of your KOTS application, as shown in the URL path at https://vendor.replicated.com/apps. _Note that this is **case sensitive**_
![Vendor Application Slug](/images/vendor-app-slug.png)

* `REPLICATED_API_TOKEN` should be set to a token created at https://vendor.replicated.com/team/tokens.
![Vendor API Token](/images/vendor-team-token.png)

Ensure the token has “Write” access or you’ll be unable create new releases. Once you have the values, set them in your environment. 

For Linux/Mac environments: 
```bash
export REPLICATED_APP=my_kots_app
export REPLICATED_API_TOKEN=d5cdf814bae01b211a8e891593dc12e1158238d27932d082a32b98706e576216
```

For Windows environments, use: 
```bash
set REPLICATED_APP=my_kots_app && setx REPLICATED_APP my_kots_app
set REPLICATED_API_TOKEN=d5cdf814bae01b211a8e891593dc12e1158238d27932d082a32b98706e576216
setx REPLICATED_API_TOKEN d5cdf814bae01b211a8e891593dc12e1158238d27932d082a32b98706e576216
```

## Installing The Vendor CLI

There are three options for installing the Vendor CLI. 

* Running the CLI directly on mac
* Running the CLI directly on linux
* Running the CLI through Docker (Useful for Windows, GitHub Actions, or computers without sufficient access). 

### MacOS

To install latest Vendor CLI on Mac with Brew:

```shell
brew install replicatedhq/replicated/cli
```

To install latest Vendor CLI on Mac without Brew: 
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

To install latest Vendor CLI on Linux:

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

To run latest Vendor CLI through a docker container:

```shell
docker run \
  -e $REPLICATED_APP \
  -e $REPLICATED_API_TOKEN \
  replicated/vendor-cli --help
```

For simplicity, we will represent usage assuming the CLI has been downloaded and installed to the desktop. 
