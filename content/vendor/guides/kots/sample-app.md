# Deploy a sample app

Deploying and managing applications at all stages of the lifecycle is what Replicated is all about. Deploying an app is a fundamental step.

Releases are a set of Kubernetes and Replicated manifests.

Releases are assigned to a Channel.

# In this Tutorial

Learn how to create a release, assign it to a channel, and then deploy the application. We will use the CLI to create our release and deploy the application.

We will use the example [Sentry application](https://github.com/replicatedhq/kots-sentry).

# Prerequisites

* Create an account in the vendor portal
* Create an app in vendor portal (if not already created)
* Create an admin/read/write service account
* Install `replicated` CLI and configure it to your service account token
* Have a test environment (existing k8s or embedded with kURL)
* git CLI is installed

# Instructions

## Clone the application files from GitHub

1. Open your terminal

1. Navigate to the folder you wish to work in

1. Clone the Sentry application repo

    ```sh
    git clone https://github.com/replicatedhq/kots-sentry.git
    ```

1. Navigate to the `kots-sentry` folder

    ```sh
    cd kots-sentry
    ```

## Create a release with the CLI

There are two ways to create a release in Replicated - the CLI and the Vendor Portal. For this tutorial, we will use the CLI.

1. List your applications

    ```sh
    replicated app ls
    ```

1. Copy the slug name of the application you created for our sample app. In my sample below, my slug name is `sentry-bream`

    ```
    ID                             NAME              SLUG                      SCHEDULER
    1qtlHLerzLCBWyAUc9FDdbeQqaY    Sentry            sentry-bream              kots
    ```

1. Create a release

    ```sh
    replicated release create --app [your app slug name] --yaml-dir manifests
    ```

    This will create a release from the `manifests` directory

1. Copy the sequence number for the release. This will be used when promoting the release to a channel. In this example, my sequence is `4`.

    ```
    • Reading manifests from manifests ✓
    • Creating Release ✓
    • SEQUENCE: 4
    ```

## Assign the release to a channel

1. List the channels to get the Channel ID. In this case, I want to use the `unstable` channel

    ```sh
    replicated channel ls --app [app slug name]
    ```

1. Copy the ID of the channel you wish to promote the release to. In this example, I want to promote to the `unstable` channel, so I would copy the ID `1qtlHFmKe0DO8j73yRNuSlrEho0`

    ```
    ID                             NAME        RELEASE    VERSION
    1qtlHGb9G0QjduoyP1pba9buf5z    Stable      1          0.0.1
    1qtlHIDRUNYCVtkergEnkTmzWxR    Beta        1          0.0.1
    1qtlHFmKe0DO8j73yRNuSlrEho0    Unstable    1          0.0.1
    ```

1. Promote the release to the channel. The sequence is typically your application version. The version number is the application version number (for example `1.4.2`).

    ```sh
    replicated release promote [sequence number] [channel ID] --app [app slug name] --version [app version number]
    ```

## Copy the command used to install the application

1. Navigate to the vendor portal in the browser

1. Select the application from the top left drop down

1. Navigate to "Channels"

1. Identify the channel the release was promoted to. In this example, the `Unstable` channel is used

1. Choose whether you want to deploy to an `Existing` or `Embedded` cluster. To understand the difference, [see this doc](todo)

1. Copy the CLI command. In this example, we will copy

    ```
    curl https://kots.io/install | bash
    kubectl kots install sentry-bream/unstable
    ```

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

1. Supply appropriate configuration for the application

1. Click "Continue"

### Preflights

Preflights check your environment to make sure is meets all requirements of the application

1. Review the preflights. Correct any failed preflights and rerun the preflights

1. Click "Continue"

### Application Deployment

At this point, the application will be deployed. The deployment status will be visible on the left of the screen.
