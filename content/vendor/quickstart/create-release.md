---
date: "2018-01-30T04:02:20Z"
title: "Create a KOTS Release"
description: ""
weight: "11001"
categories: [ "Kubernetes Guide" ]
index: "guides/kubernetes"
type: "chapter"
gradient: "kubernetes"
icon: "replicatedKubernetes"
---

# Create and Promote a Release

When starting and learning the Replicated platform, the [Vendor Portal](https://vendor.replicated.com) will be the place you spend most of your time. This guide is designed to help you get familiar with the concepts and ideas that are important to successfully deploy your application with Replicated. If you get stuck or need help, head to our [community](https://help.replicated.com/community/).

This guide will deploy a basic application using Replicated and show you how to deliver an update to that application. The guide isn't going to teach Kubernetes, but will start with a working Kubernetes application.

### Create a New Application

To start, log in (or create a new team) on [vendor.replicated.com](https://vendor.replicated.com) and create a new application. After signing up and activating your account, you will be prompted to create a new application. Give it a name like "Starter kots application" and click the Create application button.

![Create Application](/images/guides/kots/create-application.png)

### Releases

You'll should be at the channels page now. This is a list of your release channels, which are logical stacks for you to stage and promote releases to your customers. We'll explore this in more detail later. For now, click on the Releases item on the left menu and then click the "Create a release" button.

![Create Release](/images/guides/kots/create-release.png)

### Create a Release

You should now see a YAML editor where you can define how you application will work and the integration with Replicated functionality. Once you are familiar with these concepts, you'll probably use our CLI and API to automate this rather than manually edit YAML on this page. The default YAML documents above the white line contain information for Replicated, preflight checks, customer configuration screen options, and support bundle analyzers for troubleshooting installs. You can learn about those elsewhere but for now let's click the "Save release" button in the bottom right.

![Default YAML](/images/guides/kots/default-yaml.png)

### Save and Promote Release

Once the release is saved, we should promote it to the Unstable channel to make this release available for installation. To do this, click the "Releases" link in the top left and then click the Promote button on the row we just created. In this popup, choose the Unstable channel and click the Promote button.

![Create Application](/images/guides/kots/promote-release.png)

That's all there is to creating and promoting a release in Replicated using Kubernetes.

In the [next guide](../install), we'll walk through creating a license and installing Kubernetes and the guestbook application on a test server.
