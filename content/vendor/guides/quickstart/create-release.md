---
date: "2018-01-30T04:02:20Z"
title: "Create a KOTS Release"
weight: "11001"
categories: [ "Kubernetes Guide" ]
index: "guides/kubernetes"
type: "chapter"
---

When getting started with the Replicated platform, the [Vendor Portal](https://vendor.replicated.com) will be the place you spend a lot of time. This guide is designed to help you get familiar with the concepts and ideas that are important to successfully deploy your application with KOTS. If you get stuck or need help, head to our [community](https://help.replicated.com/community/).

This guide will deploy a basic application using KOTS and show you how to deliver an update to that application. The guide isn't going to teach Kubernetes, rather it will start with a minimal Kubernetes application that deploys a single replica of [nginx](https://www.nginx.com).

### Create a New Application

To start, log in (or create a new team) on [vendor.replicated.com](https://vendor.replicated.com) and create a new application. After signing up and activating your account, you will be prompted to create a new application. Give it a name like "Starter KOTS application" or "Nginx Example" and click the Create Application button.

![Create Application](/images/guides/kots/create-application.png)

### Releases

You'll should be at the channels page now. This is a list of your release channels, which are logical stacks for you to stage and promote releases to your customers. We'll explore this in more detail later. For now, click on the Releases item on the left menu and then click the "Create a release" button.

![Create Release](/images/guides/kots/create-release.png)

### Create a Release

You should now see a YAML editor where you can define how you application will work and the integration with KOTS functionality. Once you are familiar with these concepts, you'll probably use our [CLI and API](/vendor/cli) to automate this rather than manually edit YAML on this page (Although if you're itching to hit the command line, rather than editing YAML in the browser, you can always run through the [CLI setup chapter](/vendor/guides/quickstart/automate) before coming back to complete this guide). 

![Default YAML](/images/guides/kots/default-yaml.png)

The default YAML documents above the white line contain information for Replicated, preflight checks, customer configuration screen options, and support bundle analyzers for troubleshooting installs. You can learn about those [in the reference docs](/reference/v1beta1) but for now let's click the "Save release" button in the bottom right.

### Save and Promote Release

Once the release is saved, go ahead and promote it to the Unstable channel to make this release available for installation. To do this, click the "Releases" link in the top left and then click the Promote button on the row we just created. In this popup, choose the Unstable channel and click the Promote button.

![Create Application](/images/guides/kots/promote-release.png)

Now that we've got a release promoted, we can head to the [next guide](../install), in which we'll walk through creating a license and installing our basic nginx application on a test server.
