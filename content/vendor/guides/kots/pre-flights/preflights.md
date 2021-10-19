# Preflights

Preflights are a way to ensure that the environment meets the requirements for the app. Custom checks can be created at the application level to allow a vendor to build their own checks before their application releases are deployed.

# Prerequisites
1. [Creating an App](https://github.com/replicatedhq/kots.io/blob/tutorials/content/vendor/guides/kots/todo)
1. [Deploying a Sample App](https://github.com/replicatedhq/kots.io/blob/tutorials/content/vendor/guides/kots/deploy-basic-app.md)

# Types of Preflights:

## Default Preflights include the following:

- clusterVersion
	- Verifies that the cluster meets minimum version requirements to support the application
- containerRuntime
	- Verifies which runtime environment is configured
- distribution
	- Verifies that the host runs a distribution that can support the application
- nodeResources
	- Verifies that the host has sufficient hardware resources to support the application

## Additional preflights can include any of the following types:

- configMap
- cephStatus
- clusterVersion
- containerRuntime
- customResourceDefinition
- deploymentStatus
- distribution
- imagePullSecret
- ingress
- longhorn
- mysql
- nodeResources
- postgres
- redis
- registryImages
- secret
- statefulsetStatus
- storageClass
- textAnalyze

# Instructions

## Create a release for your existing sample app

In order to add a preflight, you will first need to create a new release. This time, don’t publish it after you save.

1. In your vendor portal, click on “Releases” on the lefthand side
1. Click the “Create Release” button in the upper right corner
1. Click “Save Release” button in the lower right corner
1. Click “Promote” button and select a channel, then click “promote”

1. In the admin console for your app, go to “Dashboard”
	1. If you have more than one application, choose the correct application on the lefthand side
1. Click “Application” at the top
1. Under Version in the center, click the “version history” link
1. Click the “Check for update” button
1.  Under “Upstream Update” click the “view preflights” and then “Re-run”
1. Click “Deploy” and confirm your choice
1. Review the successful preflights using “view preflights” link

## View Existing and Add a New Preflight Check

1. In your vendor portal, click on “Releases” on the lefthand side
1. Click the “Create Release” button in the upper right corner
1. Click “Save Release” button in the lower right corner. Make a note of the new release name.
1. Click on “Releases” on the lefthand side in order to return to your list of releases
1. Notice that your new release has an “Edit yaml” button. Click this.
1. Select the “preflight.yaml” file for modifying. Notice that all the preflights we’ve already seen are configured here.
1. Add a preflight to the bottom of the file to check for secrets:

    ```yaml
       - secret:
            checkName: Postgres URI Secret
            secretName: my-app-postgres
            namespace: default
            key: uri
            outcomes:
              - fail:
                  message: The `my-app-postgres` Secret was not found or the `uri` key was not detected.
              - pass:
                  message: The Postgres URI was found in a Secret in the cluster.
    ```

    Notice that the editor will highlight syntax errors as you type. Correct any errors and warnings before moving on.

1. Click “Save Release” button in the lower right corner
1. Click “Promote” button and select a channel, then click “Promote”

1. In the admin console for your app, go to “Dashboard”
	1. If you have more than one application, choose the correct application on the lefthand side
1. Click “Application” at the top
1. Under Version in the center, click the “version history” link
1. Click the “Check for update” button
1.  Under “Upstream Update” notice that it displays “Checks failed”
1. Review the failed preflights using “view preflights” link
1. See that the preflight analyzer itself failed, rather than the preflight check:

    > **Analyzer Failed**
    >  file secrets/default/my-app-postgres/uri.json was not collected

## Correct the Configuration Errors in Preflight yaml File

1. In your vendor portal, click on “Releases” on the lefthand side
1. Click the “Create Release” button in the upper right corner
1. Click “Save Release” button in the lower right corner
1. Click on “Releases” on the lefthand side in order to return to your list of releases
1. Notice that your new release has an “Edit yaml” button. Click this.
1. Click the preflight.yaml file to edit it.
1. Notice that we have no collectors configured, so the preflights are not collecting enough data to analyze.
1. Add the following just above `analyzers`:

    ```yaml
        collectors:
        - secret:
            name: my-app-postgres
            namespace: default
            key: uri
            includeValue: false # this is the default, specified here for clarity
    ```

1. Click “Save Release” button in the lower right corner
1. Click “Promote” button, select a channel, and write any notes about your release
1. Click “Promote” button and select a channel, then click “promote”

1. In the admin console for your app, go to “Dashboard”
	1. If you have more than one application, choose the correct application on the lefthand side
1. Click the “Version history” link
1. Click the “check for update” button
1. Watch it check for updates, and automatically run preflights.
1. Review the failed preflights using “view preflights” link
1. See that the check was able to run, but failed:

> The `my-app-postgres Secret `was not found or the `uri` key was not detected.

## Correct the missing file so the check succeeds

1. Save the following file as `postgres-secret.yaml`

    ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
      name: my-app-postgres
    data:
      uri: YmFyCg==
    ```
1. Create the object by running the following command

    ```sh
    kubectl apply -n <namespace> -f postgres-secret.yaml
    ```

    `namespace` is the namespace name where the app is deployed. For embedded clusters, this is `default`

## Rerun Preflight Checks

1. Select the failed preflights using “view preflights” link for the failed release
1. Click “Re-run” in the lower right corner
1. See the check succeed:

> The Postgres URI was found in a Secret in the cluster.

## Deploy the New Release

1. In the admin console for your app, go to “Dashboard”
	1. If you have more than one application, choose the correct application on the lefthand side
1. Click “Application” at the top
1. Under Version in the center, click the “version history” link
1. (Optional: Deploy a release that failed preflight checks and see what happens)
1. Click the “Deploy” button for the new release
