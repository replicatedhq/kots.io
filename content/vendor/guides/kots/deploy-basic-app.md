# Deploy the stock Nginx app

Deploying and managing applications at all stages of the lifecycle is what Replicated is all about. Deploying an app is a fundamental step.

Releases are a set of Kubernetes and Replicated manifests.

Releases are assigned to a Channel.

# In this Tutorial

Learn how to create a release, assign it to a channel, create a customer license and then deploy the application to an environment.

We will use the stock Nginx app.

# Prerequisites

* Create an account in the vendor portal
* Create an app in vendor portal (if not already created)
* Have a test environment (existing k8s or embedded with kURL)

# Instructions

## Create an App in the vendor portal

Creating your first app is the first thing done when creating an account in the vendor portal. You may have already performed this step.

If you want to create another app, see [Creating an App](todo).

## Create a release

1. Click the "Releases" tab in the top left

1. Click the "Create Release" button in the top right

> Note we will not edit the manifests for this tutorial

1. Click "Save Release" button in the bottom right

## Assign the release to a channel

1. Click the "Releases" tab in the top left

1. Click the "Promote" button next to the `Sequence` you just created

1. Click the checkboxes for the channels you wish to promote to. If this is your first time, use the `Unstable` channel.

> Note: Version Label is typically the version of the application being packaged in the release

> Note: you may add release notes if you wish

1. Click "Promote"

## Create a customer and download a license file

1. Click the "Customers" tab in the top left

1. Click the "+ Create Customer" button in the top right

1. Enter a Customer name - typically the company name of the customer. Feel free to make one up.

1. Assign a Channel - choose the channel we just promoted our release to

1. Click the "Save Changes" button in the lower right

1. Click the "Download Licenses" link in the top right

    Your browser will now save the license file. You will use this file as part of the installation.

## Copy the command used to install the application

1. Click the "Channels" tab in the top left

1. Identify the channel the release was promoted to. In this example, the `Unstable` channel is used

1. Choose whether you want to deploy to an `Existing` or `Embedded` cluster. To understand the difference, [see this doc](todo)

1. Copy the CLI command.

## Deploy the app

### Install and Access the Admin Console

1. Open a terminal and navigate to the environment where you wish to install the application. This may involve using `ssh` or configuring `kubectl`. See [this doc](todo) for more details

1. Paste in the command copied from the Vendor Portal

1. Supply or copy the password for the Admin Console

1. Copy the url provided for accessing the Admin Console

1. Navigate to the Admin Console in your browser

1. Click on 'Continue to Setup'

1. Accept and continue past any browser warnings

1. Click 'Skip & Continue' on the screen for uploading a certificate

    This is used to provide the admin console a cert to secure the connection. This is not required for this tutorial

1. Enter the password provided from the terminal output at the login screen

### Configure the application

> The stock Nginx does not require any configuration to be set.

1. Click "Continue"

### Preflights

Preflights check your environment to make sure is meets all requirements of the application

1. Review the preflights. Correct any failed preflights and rerun the preflights

1. Click "Continue"

### Application Deployment

At this point, the application will be deployed. The deployment status will be visible on the left of the screen.
