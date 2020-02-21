---
date: "2018-01-30T04:02:20Z"
title: "Installing and Testing a Kubernetes Release"
description: "A guide to installing and testing a Kubernetes appliance and release in Replicated"
weight: "11002"
categories: [ "Kubernetes Guide" ]
index: "guides/kubernetes"
type: "chapter"
gradient: "kubernetes"
icon: "replicatedKubernetes"
---

# Installing and Testing a Kubernetes Release

This guide will give you first-hand experience installing a Replicated Kubernetes appliance. If you haven't yet created a release, head back to the [Create and Promote as Release](../create-release) guide and complete that first.

Now that we've created a release and promoted it to the Unstable channel, the next step is to create a customer license and use this this license to install the application on a test server.

### Create License

A customer license (downloadable as a `.yaml` file) is required to install any Replicated application. To create a customer license, log in to the [Vendor Portal](https://vendor.replicated.com) and select the Customers link on the left. You will see a screen that says you haven't created any customers. Click the "Create a customer" button to continue.

![Customers](/images/guides/kots/customers.png)

On the Create a new customer page, fill in your name for the Customer name field, select the Unstable channel on the right hand side, and click Create customer. The defaults in all other fields will be fine.

![Create Customer](/images/guides/kots/create-customer.png)

After creating the customer, click the "Download license" link in the upper right corner. This will download file file with your customer name and a `.yaml` extension. This is the license file your customer will need to install your application. When a customer is installing your software you need to send them two things: the KOTS install script and the license file.

### Create Test Server and Install Replicated

KOTS can be installed either into an existing Kubernetes cluster or as an embedded cluster. You can see the installation options at the bottom of each channel on the Channels page.

![Installation Methods](/images/guides/kots/installation-methods.png)

We're going to use the embedded cluster option for this guide. First we will need a server. We use Google Cloud a lot but any cloud provider or local virtual machine will suffice. For this guide, let's create a server with:

- Ubuntu 18.04
- at least 8 GB of RAM
- 4 CPU cores
- at least 50GB of disk space

Next, ssh into the server we just created, and run the install script:

```shell
curl -sSL https://kurl.sh/<your-app-name-and-channel> | sudo bash
```

This script will install Docker, Kubernetes, and the Replicated management containers.

Installation should take about 10-15 minutes.

Once the installation script is completed, it will show the URL you can connect to in order to continue the installation.

Once the installer is completed, you'll see:

```shell
The UIs of Prometheus, Grafana and Alertmanager have been exposed on NodePorts 30900, 30902 and 30903 respectively.

To access Grafana use the generated user:password of admin:[password] .




Kotsadm: http://[ip-address]:8800
Login with password (will not be shown again): [password]



To access the cluster with kubectl, reload your shell:

    bash -l


To add worker nodes to this installation, run the following script on your other nodes
    curl -sSL https://kurl.sh/starter-kots-demo-unstable/join.sh | sudo bash -s kubernetes-master-address=[ip-address]:6443 kubeadm-token=[token] kubeadm-token-ca-hash=sha256:[sha] kubernetes-version=1.16.4 docker-registry-ip=[ip-address]

```

Following the instructions on the screen, you can reload the shell and `kubectl` will now work:

```bash
dmichaels@david-kots-guide:~$ kubectl get pods
NAME                                  READY   STATUS      RESTARTS   AGE
kotsadm-585579b884-v4s8m              1/1     Running     0          4m47s
kotsadm-api-659db65fcd-b7kds          1/1     Running     2          4m47s
kotsadm-migrations                    0/1     Completed   2          4m47s
kotsadm-operator-fd9d5d5d7-8rrqg      1/1     Running     0          4m47s
kotsadm-postgres-0                    1/1     Running     0          4m47s
kurl-proxy-kotsadm-77c59cddc5-qs5bm   1/1     Running     0          4m46s
dmichaels@david-kots-guide:~$
```

### Install License

At this point, Replicated and Kubernetes are running, but the application isn't yet. This is also what your customer would be experiencing when installing your application. To complete the installation, visit the URL that the installation script displayed when completed. Replicated automatically provisions a self-signed certificate on every installation and detects what browser is being used in order to show users how to bypass this.

On the next screen, you have the option of uploading a trusted cert and key. For customer installations we recommend using a trusted cert. For this demo let's continue with the Replicated-generated self-signed cert. Click the "skip this step" button.

![Console TLS](/images/guides/kots/admin-console-tls.png)

Now the installation needs a license file to continue. Until this point, this server is just running docker, kubernetes, and the Replicated containers. Once we put a license file on it the server will install our application. Click the Upload button and select your `.yaml` file to continue.

![Upload License](/images/guides/kots/upload-license.png)

The settings page is here with default configuration items. These can be specified in the `config.yaml` file.

![Settings Page](/images/guides/kots/configuration.png)

Preflight checks are designed to ensure this server has the minimum system and software requirements to run the application. By default we include some preflight checks that are expected to fail so that we can see what that will look like for a customer. If you click continue it will warn you but you can still continue no matter what the preflight checks say.

![Preflight Checks](/images/guides/kots/preflight.png)

Click the Application link on the top to see the application running. If you are still connected to this server over ssh, `kubectl get pods` will now show the example nginx service we just deployed.

![Dashboard](/images/guides/kots/dashboard.png)

On the nav bar, there's a link to the application page. Clicking that will show you the Kubernetes services that we just deployed.

![Cluster](/images/guides/kots/application.png)

In the [next guide](../iterate), we'll walk through creating and delivering an update to the application we just installed.
