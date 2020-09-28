---
date: "2020-09-24T00:00:00Z"
lastmod: "2020-09-24T00:00:00Z"
title: "Using ECR for Private Images"
weight: "1005"
---

## About This Guide

This guide is a tutorial on how to configure Replicated to pull images from a private registry in Amazon's Elastic Container Registry (ECR).

## Prerquisites/Assumptions

This guide assumes a level of familiarity with Replicated and will not cover in detail the process of creating a release, promoting it to a channel and running an install. 
These topics are covered in the [Quickstart Guide](https://kots.io/vendor/guides/quickstart/), and should be reviewed prior to this guide.

The guide also assumes the following:

- You have an account for https://vendor.replicated.com
- You have an account in AWS to pull & push to ECR, as well as the ability to create an account with pull only permissions.
- You know how to create and update releases in Replicated using the Command Line Interface (CLI) tools.

## Overview

The guide is divided into the following parts:

- [Part 1 - Getting Started](#part-1---getting-started) - Covers creating an account in Replicated, creating a new application and its first release. 
- [Part 2 - Install the Application](#part-2---install-the-application) - Covers at a high level the process of installing the first release of the application.
- [Part 3 - Configuring Private Registries in Replicated](#part-3---configure-private-registries-in-replicated) - Covers the steps to link Replicated with ECR.
- [Part 4 - Update Definition Files](#part-4---update-definition-files) - Covers updating the files with the private registry address and port.
- [Part 5 - Install the New Version ](part-5---install-the-new-version) - Covers updating the installed application to the latest version to verify that we were able to pull the image from the private registry set in the previous steps.


## Part 1 - Getting Started

To get started with this guide, we are going to create a new application in Replicted and create a release using the default definition files.

### Create a New Application in Replicated

To create a new application, login to vendor.replicated.com and select *Create new app...* from the *Application* menu.

![new-app](/images/guides/kots/priv-reg-ecr-new-app.png)

For the purpose of this guide we will name this application **MySampleECRApplication**, but feel free to use a different name.

![new-app-name](/images/guides/kots/priv-reg-ecr-new-app-name.png)

Once the application is created, click on the *Releases* tab.

![new-release](/images/guides/kots/priv-reg-ecr-new-release.png)

As you see in the screenshot above, the application does not have any releases.

### Create the First Release

Replicated provides several ways to create a release, but the most common way is to automate it as described in our [quickstart guide](https://kots.io/vendor/guides/quickstart/#automating-your-workflow).
The guide will assume you have created a starter repository in GitHub based on the [Kots Starter Template](https://github.com/replicatedhq/replicated-starter-kots/), and are either using the provided GitHub Action workflow file or CLI tools to create releases and promote to the *Unstable* channel.

As we see can below, the default deployment definition file will deploy the public NGINX container.

![release-2](/images/guides/kots/priv-reg-ecr-default-def-yaml.png)

### Create a Customer License

We will need a license to install the application, so click on **Create a Customer** under the *Customers* tab.

![create-customer](/images/guides/kots/priv-reg-ecr-create-customer.png)

Create new customer, and assign it to the *Unstable* channel. Once the customer is saved, a link to download the license becomes available:

![new-customer](/images/guides/kots/priv-reg-ecr-new-customer.png)

## Part 2 - Install the Application

Now we are ready to install the application!

As mentioned earlier in this guide, the following steps and screenshots are for doing an embedded install on a plain VM. However, doing an existing install to a Kubernetes cluster also works.

For this guide, let’s create a server with Ubuntu 18.04 and at least:
 
- 8 GB of RAM
- 4 CPU cores
- 100GB of disk space

SSH into the VM and paste the command from the *Unstable* channel:

![replicated-channels](/images/guides/kots/priv-reg-ecr-channels.png)

Once the install is complete, browse to the Admin Console, and follow the prompts to enter the password, upload the license file, etc... to get the application deployed. Once the application is deployed, the **Dashboard** on the KOTS Admin Console should look similar to this:

![admin-console](/images/guides/kots/priv-reg-ecr-admin-console.png)

To inspect what was deployed let's look at the files under **View Files** from the Admin Console. 
In the Upstream files (files from the Release created in the Replicated Vendor Portal) show that we are pulling the public image.

![admin-console-view-files-upstream-release1](/images/guides/kots/priv-reg-ecr-ups-files-rel1.png)

If we switch back to the terminal window and run `kubectl describe pod` on the nginx pod, we can confirm that it was in fact pulled from the public repository.


![admin-console-kubectl-describe-release2](/images/guides/kots/priv-reg-ecr-kubctl-describe-rel1.png)

Now that we have the basic application installed, we are now going to pull the same image but from an ECR repository.


## Part 3 - Configure Private Registries in Replicated

To configure a Private Registry in Replicated, you need to provide the same information you would provide the ```docker login``` command:

- **Endpoint**
- **Username**
- **Password**

### Determine the endpoint

To determine the endpoint for ECR, login to the AWS console and seach for 'ECR', which should bring up Elastic Container Registry as an option as shown below.

![search-4-ecr](/images/guides/kots/priv-reg-ecr-search-4-ecr.png)

Select 'Elastic Container Registry' from the options in the dropdown to get to the list of repositories.

![ecr-repos](/images/guides/kots/priv-reg-ecr-repos.png)

As you can see from the screenshot above, you can see the endpoints for each repository. To determine the endpoint to enter replicated, use the url without the repository name.

### Setting up the Service Account User

Replicated only needs access to pull images from the private registry. As a best practice, let's create a new user in AWS:

![aws-new-user](/images/guides/kots/priv-reg-ecr-new-user.png)

As far as permissions go, there are a couple of options, depending on scope of access.
If exposing all images to Replicated is an acceptable solution, the Amazon-provided [AmazonEC2ContainerRegistryReadOnly](https://docs.aws.amazon.com/AmazonECR/latest/userguide/ecr_managed_policies.html#AmazonEC2ContainerRegistryReadOnly) policy will work:

```shell
{
	"Version": "2012-10-17",
	"Statement": [{
		"Effect": "Allow",
		"Action": [
			"ecr:GetAuthorizationToken",
			"ecr:BatchCheckLayerAvailability",
			"ecr:GetDownloadUrlForLayer",
			"ecr:GetRepositoryPolicy",
			"ecr:DescribeRepositories",
			"ecr:ListImages",
			"ecr:DescribeImages",
			"ecr:BatchGetImage"
		],
		"Resource": "*"
	}]
}
```
If you wish to limit Replicated to only certain images, this policy should be used instead:

```shell
{
    "Version": "2012-10-17",
    "Statement": [{
        "Effect": "Allow",
        "Action": [
		    "ecr:BatchCheckLayerAvailability",
		    "ecr:GetDownloadUrlForLayer",
		    "ecr:GetRepositoryPolicy",
		    "ecr:DescribeRepositories",
		    "ecr:ListImages",
		    "ecr:DescribeImages",
		    "ecr:BatchGetImage"
        ],
        "Resource": [
            "arn:aws:ecr:us-east-1:<account-id>:repository/<repo1>",
            "arn:aws:ecr:us-east-1:<account-id>:repository/<repo2>"
        ]
    }]
}{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ecr:GetAuthorizationToken"
            ],
            "Resource": "*"
        },
    ]
}
```

We will need the ```AWS Access Key ID``` and ```AWS Secret Key``` from this user later in this guide as these will map the *Username* and *Password* fields. You can obtain these as you create the user or after the user has been created.

### Pull Public Image and Push to ECR

To keep the changes to a minimum and only focus on using a private registry, we are going to pull the public ```nginx``` container (as specified in the ```deployment.yaml``` file) to our local environment, and then push it to a repository in ECR.
To do this, we will need to have Docker on our local environment, have the AWS CLI installed as well as an account in AWS with with the ability to push the container to the repository.
To use ```docker login``` with ECR, we will need to configure AWS CLI with the ```AWS Access Key ID``` and ```AWS Secret Key`` for this user.

Let's start by pulling the public image

```shell
$ docker pull nginx
```

You should have an output similar to this:

```shell
Using default tag: latest
latest: Pulling from library/nginx
d121f8d1c412: Pull complete 
ebd81fc8c071: Pull complete 
655316c160af: Pull complete 
d15953c0e0f8: Pull complete 
2ee525c5c3cc: Pull complete 
Digest: sha256:c628b67d21744fce822d22fdcc0389f6bd763daac23a6b77147d0712ea7102d0
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest
```

Next we need to login to ECR and push this container. 
If you don't have it already, you will need to [install the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html) and  [configure it](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html). 
As part of configuring the AWS CLI, we will need to provide the ```AWS Access Key ID``` and ```AWS Secret Key``` for a user that has permissions to create and push to a repository. 
**This is not the same Service Account user we created above as that user can only push, but not pull images.**
 If you are unsure about the roles needed for this user, please refer to the [AWS ECR Documentation](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecr_managed_policies.html)
Please refer to [this guide](https://docs.aws.amazon.com/AmazonECR/latest/userguide/getting-started-cli.html) if you are not familiar using the AWS CLI to work with containers and ECR.
As noted in the guide, you will need to create a repository before you can push an image.

To login to ECR we run a command similar to:

```shell

$ aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin 4999999999999.dkr.ecr.us-east-2.amazonaws.com
```

A succesful login will display a ```Login Succeeded``` message. Next we need to tag the image, and for the new tag we are prepend the tag we used to pull the image with the ECR repository URL:

```shell
$ docker tag nginx 4999999999999.dkr.ecr.us-east-2.amazonaws.com/demo-apps/nginx
```

Assuming a successfu' tag, we now push the container to our ECR repository

```shell
$ docker push 4999999999999.dkr.ecr.us-east-2.amazonaws.com/demo-apps/nginx      
The push refers to repository [4999999999999.dkr.ecr.us-east-2.amazonaws.com/demo-apps/nginx]
908cf8238301: Pushed 
eabfa4cd2d12: Pushed 
60c688e8765e: Pushed 
f431d0917d41: Pushed 
07cab4339852: Pushed 
latest: digest: sha256:794275d96b4ab96eeb954728a7bf11156570e8372ecd5ed0cbc7280313a27d19 size: 1362

```
Now we are ready to update Replicated!

### Enter Registry Information in Replicated

First, we must link Replicated with the registry. To do this, click on **Add External Registry** from the *Images* tab.

![vendor-web-new-registry](/images/guides/kots/priv-reg-ecr-link-new.png)

The values for the fields are:

**Endpoint:** 
Enter the same URL used to login to ECR.
For example, to link to the same registry as the one in the section, we would enter *4999999999999.dkr.ecr.us-east-2.amazonaws.com*.

**Username:**
Enter the ```AWS Access Key ID``` for the user created in the [Setting Up the Service Account User](#setting-up-the-service-account-user) section.

**Password:**
Enter the ```AWS Secret Key``` for the user created in the [Setting Up the Service Account User](#setting-up-the-service-account-user) section.

## Part 4 - Update Definition Files

Last step is to update our defintion manifest to pull the image from the ECR repository.
To do this, we'll update the ```deployment.yaml``` file by adding the ECR registry URL to the ```image``` value. 
Below is an example using the registry URL used in this guide.

![release-2](/images/guides/kots/priv-reg-ecr-mod-dep-yaml.png)

Save your changes and create the new release and promote it to the *Unstable* channel.

## Part 5 - Install the New Version

To deploy the new version of the application, go back to the Admin Console and select the *Version History* tab.
Click on **Check for Updates** and then **Deploy** when the new version is listed.
To confirm that the new version was in fact installed, it should look like the screenshot below.

![version-history](/images/guides/kots/priv-reg-ecr-version-history.png)

Now, we can inspect to see the changes in the definition files.
Looking at the ```deployment.yaml``` upstream file, we see the image path as we set it in the [Update Definition Files](#part-4---update-definition-files) section.

![admin-console-view-files-upstream-release2](/images/guides/kots/priv-reg-ecr-upstream-file-rel2.png)

Since KOTS is able to detect that it can't pull this image anonymously, it then tries to proxy the private registries configured. Looking at the ```kustomization.yaml``` downstream file we can see that the image path is changed to use the Replicated proxy.

![admin-console-view-files-downstream-release2](/images/guides/kots/priv-reg-ecr-downstream-file-rel2.png)

The install of the new version should have created a new pod. If we run ```kubectl describe pod``` on the new nginx pod, we can confirm that the image was in fact pulled from the ECR repository.

![admin-console-kubectl-describe-release2](/images/guides/kots/priv-reg-ecr-kubectl-describe-rel2.png)

## Further Reading

- [Kots.io Documenmtation on using Private Images](https://kots.io/vendor/packaging/private-images/)

- [Replicated Community Thread on AWS Roles and Permissions](https://help.replicated.com/community/t/what-are-the-minimal-aws-iam-permissions-needed-to-proxy-images-from-elastic-container-registry-ecr/267)
