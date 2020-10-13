---
date: "2020-09-24T00:00:00Z"
lastmod: "2020-09-24T00:00:00Z"
title: "Using GCR for Private Images"
weight: "1005"
---

KOTS supports working with private images stored in Google's Container Registry (GCR).

## Objective

The purpose of this guide is to walk you through a hello-world example on how to configure Replicated to pull images from a private registry in Google's Container Registry (ECR).

## Prerequisites & Assumptions

This power-user's guide assumes you have completed the [standard quickstart](https://kots.io/vendor/guides/quickstart/) or the [CLI quickstart](https://kots.io/vendor/guides/cli-quickstart/) guides as this guide is a continuation of those guides.
As with the previous guides, we will also need a VM to install the application with the following minimum requirements:

* Ubuntu 18.04
* At least 8 GB of RAM
* 4 CPU cores
* At least 100GB of disk space

In this guide we are going to focus on the difference between using a public image versus a private image in Replicated.
To do this, we'll pull the public ngnix container and then push it to a private repository in GCR.

This means we'll need:

* A GCR Repository
* A Google Account to use with Docker to pull and push the public nginx image to the GCR repository
* Docker
* The gcloud CLI

Later in the guide we'll configure Replicated to pull from the GCR repository using a read-only account.
To do this we'll need to make sure the above AWS account can also create this user.

## Overview

The guide is divided into the following steps:

 1. [Set Up Testing Environment](#1-set-up-testing-environment) 
 
 2. [Configure Private Registries in Replicated](#2-configure-private-registries-in-replicated)

 3. [Update Definition Files](#3-update-definition-files)

 4. [Install the New Version](#4-install-the-new-version)

## 1. Set Up Testing Environment

For this guide, we are simply going to use the default nginx deployment to create our application and then update it to pull the same container from a private repository in GCR and note the differences.

### Create Sample Application and deploy the first release

In this section, we cover at a high level the steps to create a new application and install it on a VM. As mentioned earlier, it's assumed you have completed the [standard quickstart](https://kots.io/vendor/guides/quickstart/) or the [CLI quickstart](https://kots.io/vendor/guides/cli-quickstart/) guide, which cover these steps in detail.

To create our sample application follow these steps:

* Create a new application in Replicated and call it 'MySampleGCRApp'. 
* Create the first release using the default definition files and promote it to the *unstable* channel.
* Create a customer, assign it to the *Unstable* channel and download the license file after creating the customer.
* Install the application to a Virtual Machine

Once you have installed the first release of the sample application you should arrive at this screen in the Admin Console:

![kots-admin-v1](/images/guides/kots/priv-reg-ecr-after-deploy.png)

To inspect what was deployed let's look at the files under **View Files** from the Admin Console. 
In the Upstream files (files from the Release created in the Replicated Vendor Portal) show that we are pulling the public image.

![admin-console-view-files-upstream-release1](/images/guides/kots/priv-reg-ecr-ups-files-rel1.png)

We can further validate this if we switch back to the terminal window on the VM where we installed the application.
If we run `kubectl describe pod <pod-name>` on the nginx pod, we can confirm that it was in fact pulled from the public repository.

![admin-console-kubectl-describe-release2](/images/guides/kots/priv-reg-ecr-kubctl-describe-rel1.png)

Now that we have the basic application installed, we are now going to pull the same image but from an GCR repository.

### Pull Public Image and Push to GCR

To keep the changes to a minimum and only focus on using a private registry, we are going to pull the public nginx container (as specified in the `deployment.yaml` file) to our local environment, and then push it to a repository in GCR.
To use `docker login` with GCR, we will need to configure the gcloud CLI to authenticate and set it to the right project in Google Cloud Platform (GCP).

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


## Part 3 - Configure Private Registries in Replicated

To configure a Private Registry in Replicated, you need to provide the same information you would provide the ```docker login``` command:

- **Endpoint**
- **Username**
- **Password**

### Determine the endpoint

The endpoint for GCR is usually ```grc.io```, but you may also have

```shell
gcr.io/<gcp-project-id>

```

To determine the endpoint for GCR, login to the GCP console and seach for 'GCR', which should bring up the Container Registry as an option as shown below.

![search-4-ecr](/images/guides/kots/priv-reg-gcr-search.png)

Select 'Container Registry' from the options in the dropdown to get to the list of repositories.

![ecr-repos](/images/guides/kots/priv-reg-gcr-repos.png)

As you can see from the screenshot above, you can see the endpoints for each repository. To determine the endpoint to enter replicated, use the url without the repository name.

### Setting up the Service Account User

Replicated only needs access to pull images from the private registry. As a best practice, let's create a new user in 

### Pull Public Image and Push to GCR

To keep the changes to a minimum and only focus on using a private registry, we are going to pull the public ```nginx``` container (as specified in the ```deployment.yaml``` file) to our local environment, and then push it to a repository in ECR.
To do this, we will need to have Docker on our local environment, have the AWS CLI installed as well as an account in 

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
