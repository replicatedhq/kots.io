---
date: 2019-10-09
linktitle: "Single App Workflows"
title: Pushing Updates to a GitOps Workflow in Single App Mode
weight: 1
draft: false
---

While the Admin Console is initially configured to receive updates, show the changes and deploy them to the cluster, this process can be changed and converted to use a GitOps workflow instead.
When using a GitOps workflow, changes from the Admin Console (config changes, upstream updates, license updates) will be pushed to a private Git repository, where an existing CI/CD process can execute to deliver the manifests to the cluster.

To begin migrating to a GitOps deployment workflow, click the GitOps link at the top of the Admin Console, then click on "Get started".

Continuing, choose the Git provider and hostname (if applicable), then click on "Continue to deployment action".

![GitOps Provider](/images/gitops-provider.png)

Enter the repo details, and optionally branch and path to commit to in the repo.

Finally, select the action to take when there's an update.
The Admin Console can create a new commit to the branch specified.

On this screen, there's an option to choose what type of asset to deliver to the git repo.
Selecting "Rendered YAML" will result in a single, multi-doc YAML file being committed to the repo.

![GitOps Action](/images/gitops-action.png)

Once GitOps is set up, a new "GitOps" tab will be available on the application.
This tab contains a public deploy key. The private key will be stored securely in the Admin Console.
Add the deploy key to the repo, and verify that the Admin Console can connect by clicking the "Try again" button.

![GitOps Action](/images/gitops-no-connection.png)

Once the Admin Console establishes a connection to the repo, the following screen will be displayed.

![GitOps Action](/images/gitops-connected.png)

## First commits

After converting to GitOps, the Admin Console will make a commit/pull request with the current version that's "Deployed".
Then, it will make separate commits (or a single pull request) with any pending updates that have not been deployed from the Admin Console.
