# Support Bundle Collectors

Collectors extend the information captured in a support bundle. This information is useful for triage, especially where access to the environment is limited.

# In this Tutorial

Learn how to implement custom collectors. We will use the `exec` custom collector, which runs a command inside an application container. Running `service nginx status` prints out the current state of Nginx running in a container. We expect the following string: `nginx is running.` We will capture this output and collect it as part of the support bundle.

> The stock Nginx app comes with some collectors already defined. We will be adding to this list

# Prerequisites

* Create and Deploy the stock [Nginx app](../deploy-basic-app.md)

# See Also

  * [Collectors overview](https://troubleshoot.sh/docs/collect/collectors/)

  * [`exec` collector documentation](https://troubleshoot.sh/docs/collect/exec/)

# Instructions

## Add the custom collector

1. Navigate to your app in the vendor portal

1. Click the "Releases" tab in the top left

1. Click the "Create Release" button in the top right

1. Select the file `support-bundle.yaml`

1. Add the following lines at the bottom of the file.

    ```yaml
        - exec:
        name: nginx-status
        selector:
            - app=example
            - component=nginx
        command: ["/bin/sh"]
        args: ["-c", "service nginx status"]
    ```

    The file should look like this when done

    ```yaml
    apiVersion: troubleshoot.replicated.com/v1beta1
    kind: Collector
    metadata:
      name: collector-sample
    spec:
      collectors:
        - clusterInfo: {}
        - clusterResources: {}
        - logs:
            selector:
              - app=example
              - component=nginx
            namespace: '{{repl Namespace }}'
            limits:
              maxAge: 30d
              maxLines: 10000
        - exec:
            name: nginx-status
            selector:
                - app=example
                - component=nginx
            command: ["/bin/sh"]
            args: ["-c", "service nginx status"]
    ```

1. Click the "Save Release" button in the bottom right

## Promote the release to a channel and update the deployed app

[Follow these instructions for promoting a release to a channel](../deploy-basic-app.md#assign-the-release-to-a-channel)

[Follow these instructions for updating a deployed app](todo)

## Collect a support bundle

[Follow these instructions to collect a support bundle](todo)

## View the support bundle contents

1. Navigate to the admin console, if not already there.

1. Click the "Troubleshoot" tab at the top

1. Click on the latest support bundle

1. Click on the "File Inspector" tab

1. Open the following file: `nginx-status/[your app slug name]/[pod name]/stdout.txt`

    This file should read:

    ```
    nginx is running.
    ```

    This is the output from the `service nginx status` command
