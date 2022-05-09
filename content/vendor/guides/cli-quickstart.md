---
date: "2019-02-20T00:00:00Z"
lastmod: "2019-02-20T00:00:00Z"
title: "CLI Quickstart"
weight: "1003"
---

This power-user's guide will help you get set up with a CLI-based workflow for quickly iterating on your Kubernetes application with KOTS.
If you'd prefer a more conceptual overview before digging into the CLI tools, you can start with the [standard quickstart](/vendor/guides/quickstart).

In this guide we'll walk through the following steps:

- [Installing the Replicated CLI](#1-install-cli)
- [Setting a Service Account Token](#2-setting-a-service-account-token)
- [Getting some YAML ready](#3-getting-some-yaml)
- [Creating a Release](#4-creating-our-first-release)
- [Creating a Customer License](#5-creating-a-customer-license)
- [Getting an Install Command](#6-getting-an-install-command)
- [Installing KOTS](#7-installing-kots)
- [Installing the Application](#8-install-the-application)
- [Iterating](#9-iterating)

### 1. Install CLI

To start, you'll want to install the `replicated` CLI.
You can install with [homebrew](https://brew.sh) or grab the latest Linux or macOS version from [the replicatedhq/replicated releases page](https://github.com/replicatedhq/replicated/releases).

```shell script
brew install replicatedhq/replicated/cli
```

You can verify it's installed with `replicated version`:

```text
$ replicated version
```
```json
{
  "version": "0.31.0",
  "git": "c67210a",
  "buildTime": "2020-09-03T18:31:11Z",
  "go": {
      "version": "go1.14.7",
      "compiler": "gc",
      "os": "darwin",
      "arch": "amd64"
  }
}
```

* * *

#### 2. Setting a Service Account Token

Next we'll set up two environment variables. You can export these right in your shell or add them to your favorite dotfiles.

If you haven't already, you'll want to head over to https://vendor.replicated.com and create an Application.
You can name the app whatever you want, we'll use "CLI Quickstart" in this case.
From there you can head to the settings page, grab the Application Slug, and export it:

![app-slug](/images/guides/kots/cli-setup-quickstart-settings.png)

In this case, we'd run

```shell script
export REPLICATED_APP=cli-quickstart
```

Then you can create a Read/Write capable Service Account token and export it as well:

![service-account-token](/images/guides/kots/cli-setup-service-account-token.png)

```shell script
export REPLICATED_API_TOKEN=1366be611e3bf...
```

We can verify these are set correctly with

```shell script
replicated release ls
```

You'll likely see an empty list of releases, this is okay, we'll create a release next.

```text
SEQUENCE    CREATED    EDITED    ACTIVE_CHANNELS
```

* * *

### 3. Getting some YAML

Next, we'll need some YAML files from which to create a release, we'll start from the repo at https://github.com/replicatedhq/replicated-starter-kots.
When you've finshed this guide, you can explore [Creating a release from an existing Helm Chart](/vendor/guides/helm-chart) for steps on how to add the necessary kots YAMLs to your helm chart.

First, let's create a folder for our project:

```shell script
mkdir replicated-cli-tutorial
cd replicated-cli-tutorial
```

Next we'll make a folder for manifests and download the starter YAML to it:

```shell script
mkdir ./manifests
curl -fSsL https://github.com/replicatedhq/kots-default-yaml/archive/v2020-09-03.tar.gz | \
  tar xzv --strip-components=1 -C ./manifests \
  --exclude README.md --exclude LICENSE --exclude .gitignore
```

#### Verifying manifests

You should now have a few YAML files in `manifests`:


```text
$ ls -la manifests
.rw-r--r--  114 dex 16 Aug  7:59 config-map.yaml
.rw-r--r--  906 dex 16 Aug  7:59 config.yaml
.rw-r--r--  608 dex 16 Aug  7:59 deployment.yaml
.rw-r--r--  296 dex 16 Aug  7:59 ingress.yaml
.rw-r--r-- 2.5k dex 16 Aug  7:59 preflight.yaml
.rw-r--r--  399 dex 16 Aug  7:59 replicated-app.yaml
.rw-r--r--  205 dex 16 Aug  7:59 service.yaml
.rw-r--r--  355 dex 16 Aug  7:59 support-bundle.yaml
```

You can verify this yaml with `replicated release lint`:

```shell script
replicated release lint --yaml-dir=manifests
```

If there are no errors, you'll an empty list and a zero exit code.

```text
RULE    TYPE    FILENAME    LINE    MESSAGE
```

#### Initializing repo

If you haven't already, you'll want to initialize this project as a git repo so you can track your history.
Additionally, the Replicated CLI will read git metadata to help with generation of release metadata like version labels -- more on that later.

```shell script
git init
git add .
git commit -m "Initial Commit: CLI Quickstart"
```

* * *

### 4. Creating our first release


Now that we have some YAML, let's create a release and promote it to the `Unstable` channel so we can test it internally.
 We'll use the `--auto` flag to tell the CLI to generate release notes and metadata based on the git status.


```shell script
replicated release create --auto
```

You'll see output similar to the following:

```text
    • Reading Environment ✓  

  Prepared to create release with defaults:

      yaml-dir        "./manifests"
      promote         "Unstable"
      version         "Unstable-ba710e5"
      release-notes   "CLI release of master triggered by dex [SHA: ba710e5] [28 Sep 20 09:15 CDT]"
      ensure-channel  true

  Create with these properties? [Y/n]
```

you can confirm the prompt by pressing Enter/Return. You'll see the release created and promoted:

```text
  • Reading manifests from ./manifests ✓  
  • Creating Release ✓  
    • SEQUENCE: 1
  • Promoting ✓  
    • Channel VEr0nhJBBUdaWpPvOIK-SOryKZEwa3Mg successfully set to release 1
```

You can verify the release was created with `release ls`:

```text
$ replicated release ls
SEQUENCE    CREATED                      EDITED                  ACTIVE_CHANNELS
1           2020-09-03T11:48:45-07:00    0001-01-01T00:00:00Z    Unstable
```

* * *

### 5. Creating a Customer License

Now that we've created a release, we need to create a "customer" object.
A customer represents a single licensed end user of your application.

In this example, we'll create a customer named `Some Big Bank` with an expiration in 10 days.
Since we created our release on the `Unstable` channel, we'll assign the customer to this channel.

```shell script
replicated customer create \
  --name "Some-Big-Bank" \
  --expires-in "240h" \
  --channel "Unstable"
```

Your output should look something like this:

```text
ID                             NAME             CHANNELS     EXPIRES                          TYPE
1h0yojS7MmpAUcZk8ekt7gn0M4q    Some-Big-Bank     Unstable    2020-09-13 19:48:00 +0000 UTC    dev
```

You can also verify this with `replicated customer ls`.

```text
replicated customer ls
```

Now that we have a customer, we can download a license file

```shell script
replicated customer download-license \
  --customer "Some-Big-Bank"
```

You'll notice this just dumps the license to stdout, so you'll probably want to redirect the output to a file:

```shell script
export LICENSE_FILE=~/Desktop/Some-Big-Bank-${REPLICATED_APP}-license.yaml
replicated customer download-license --customer "Some-Big-Bank" > "${LICENSE_FILE}"
```

You can verify the license was written properly with `cat` or `head`:

```text
$ head ${LICENSE_FILE}

apiVersion: kots.io/v1beta1
kind: License
metadata:
  name: some-big-bank
spec:
  appSlug: kots-dex
  channelName: Unstable
  customerName: Some-Big-Bank
  endpoint: https://replicated.app
```
 * * *

### 6. Getting an install command

Next, let's get the install commands for the Unstable channel with `channel inspect`:

```text
$ replicated channel inspect Unstable
ID:             VEr0nhJBBUdaWpPaOIK-SOryKZEwa3Mg
NAME:           Unstable
DESCRIPTION:
RELEASE:        1
VERSION:        Unstable-ba710e5
EXISTING:

    curl -fsSL https://kots.io/install | bash
    kubectl kots install cli-quickstart/unstable

EMBEDDED:

    curl -fsSL https://k8s.kurl.sh/cli-quickstart-unstable | sudo bash

AIRGAP:

    curl -fSL -o cli-quickstart-unstable.tar.gz https://k8s.kurl.sh/bundle/cli-quickstart-unstable.tar.gz
    # ... scp or sneakernet cli-quickstart-unstable.tar.gz to airgapped machine, then
    tar xvf cli-quickstart-unstable.tar.gz
    sudo bash ./install.sh airgap
```

* * *

### 7. Installing KOTS

From here you can choose whether you'd like to do an [Embedded cluster install](/kotsadm/installing/installing-embedded-cluster/) or an [Existing Cluster install](/kotsadm/installing/online-install/).
We'll skip Airgap for now, as it is covered in great depth in [other guides](/vendor/guides).
For the sake of simplicity, we'll run with an "embedded cluster" install on a single VM, since those are usually easier to come by than a full Kubernetes cluster.

First we will need a server. We'll use Google Cloud for this example but any cloud provider or [local virtual machine](https://github.com/replicatedhq/replicated-automation/tree/master/vendor/vagrant-boxes) will suffice. For this guide, let's create a server with:

- Ubuntu 18.04
- At least 8 GB of RAM
- 4 CPU cores
- At least 40GB of disk space


###### On the Server

Next, ssh into the server we just created, and run the install script from above, using the `EMBEDDED` version:

```shell
curl -sSL https://kurl.sh/<your-app-name-and-channel> | sudo bash
```

This script will install Docker, Kubernetes, and the KOTS admin console containers (kotsadm).

Installation should take about 5-10 minutes.

Once the installation script is completed, it will show the URL you can connect to in order to continue the installation:

```text

Kotsadm: http://[ip-address]:8800
Login with password (will not be shown again): [password]


To access the cluster with kubectl, reload your shell:

    bash -l

The UIs of Prometheus, Grafana and Alertmanager have been exposed on NodePorts 30900, 30902 and 30903 respectively.

To access Grafana use the generated user:password of admin:[password] .

To add worker nodes to this installation, run the following script on your other nodes
    curl -sSL https://kurl.sh/cli-quickstart-unstable/join.sh | sudo bash -s kubernetes-master-address=[ip-address]:6443 kubeadm-token=[token] kubeadm-token-ca-hash=sha256:[sha] kubernetes-version=1.16.4 docker-registry-ip=[ip-address]

```

Following the instructions on the screen, you can reload the shell and `kubectl` will now work:

```bash
user@kots-guide:~$ kubectl get pods
NAME                                  READY   STATUS      RESTARTS   AGE
kotsadm-585579b884-v4s8m              1/1     Running     0          4m47s
kotsadm-migrations                    0/1     Completed   2          4m47s
kotsadm-operator-fd9d5d5d7-8rrqg      1/1     Running     0          4m47s
kotsadm-postgres-0                    1/1     Running     0          4m47s
kurl-proxy-kotsadm-77c59cddc5-qs5bm   1/1     Running     0          4m46s
user@kots-guide:~$
```

* * *

### 8. Install the Application

At this point, Kubernetes and the Admin Console are running, but the application isn't deployed yet.
To complete the installation, visit the URL that the installation script displays when completed.
You'll notice that the [kurl.sh](https://kurl.sh) KOTS cluster has provisioned a self-signed certificate, and that it provides

Once you've bypassed the insecure certificate warning, you have the option of uploading a trusted cert and key.
For production installations we recommend using a trusted cert, but for this tutorial we'll click the "skip this step" button to proceed with the self-signed cert.

![Console TLS](/images/guides/kots/admin-console-tls.png)

Next, you'll be asked for a password -- you'll want to grab the password from the CLI output and use it to log in to the console.

Until this point, this server is just running Docker, Kubernetes, and the kotsadm containers.
The next step is to upload a license file so KOTS can pull containers and run your application.
Click the Upload button and select your `.yaml` file to continue, or drag and drop the license file from your desktop.

The settings page is here with default configuration items.
For now, if you're using the defaults you'll want to check the "Enable Ingress" box.
You can leave the "Ingress Hostname" field blank.
Later you'll customize what appears on this screen to collect the configuration your application needs from the customer.

![Settings Page](/images/guides/kots/configuration.png)

Preflight checks are designed to ensure this server has the minimum system and software requirements to run the application.
Depending on your YAML in `preflight.yaml`, you may see some of the example preflight checks fail.
If you have failing checks, you can click continue -- the UI will show a warning that will need to be dismissed before you can continue.

You should now be on the version history page, which will show the initial version that was check deployed.
Later, we'll come back to this page to deploy an update to the application.

Click the Application link on the top to see the status of the application and some basic monitoring stats (CPU, memory, disk space).
If you are still connected to this server over ssh, `kubectl get pods` will now show the example nginx service we just deployed.

![Cluster](/images/guides/kots/application.png)

### View the application

Since we used the default nginx application and enabled the ingress object, we can view the application at `http://${INSTANCE_IP}/` with no port, and you should see a basic (perhaps familiar) nginx server running:

![Cluster](/images/guides/kots/example-nginx.png)

Next, we'll walk through creating and delivering an update to the application we just installed.

* * *

### 9. Iterating

From our local repo, we can update the nginx deployment to test a simple update to the application.
We'll add a line to `deployment.yaml`, right after `spec:`. The line to add is

```yaml
  replicas: 2
```

Using `head` to view the first 10 lines of the file should give the output below

```shell script
head manifests/deployment.yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: example-nginx
  labels:
    app: example
    component: nginx
spec:
  replicas: 2
  selector:
```

Once you've added the `replicas` line, you can create a new release:

```shell script
replicated release create --auto
```

### Update the Test Server

To install and test this new release, we need to connect to the Admin Console dashboard on port :8800 using a web browser.
At this point, it will likely show that our test application is "Up To Date" and that "No Updates" Are Available.
The Admin Console can be configured to check for new updates at regular intervals but for now we'll trigger a check manually by clicking "Check for Updates".
You should see a new release in the history now.
You can click the +/- diff numbers to review the diff, but for now let's click "Deploy" to roll out this new version.

![View Update](/images/guides/kots/view-update.png)

Clicking the Deploy button will apply the new YAML which will change the number of nginx replicas, this should only take a few seconds.
You can verify this on the server by running

```shell script
kubectl get pod -l component=nginx
```

You should see two pods running.

* * *

### Next Steps

From here, it's time to start iterating on your application.
Continue making changes and using `replicated release create --auto` to publish them.
You can add `-y` to the command to skip the prompt.


If you want to learn more about KOTS features, you can explore some of the [intermediate and advanced guides](/vendor/guides), some good next steps might be

- [Integrating your release workflow with CI](/vendor/guides/ci-cd-integration)
- [Integrating a Helm Chart](/vendor/guides/helm-chart)

If you already have a release published in https://vendor.replicated.com you'd like to use as a starting point, check out the help docs for `replicated release download`:

```shell script
replicated release download --help
```
