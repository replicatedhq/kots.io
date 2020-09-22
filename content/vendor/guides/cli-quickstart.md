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
- [Setting an API Token](#2-setting-an-api-token)
- [Getting some YAML ready](#3-getting-some-yaml)
- [Creating a Release](#4-creating-our-first-release)
- [Creating a Customer License](#5-creating-a-customer-license)
- [Getting an Install Command](#6-getting-an-install-command)
- [Installing KOTS](#7-installing-kots)
- [Installing a License](#8-install-license)
- [Iterating](#9-iterating)

### 1. Install CLI

To start, you'll want to install the `replicated` CLI. You can install with [homebrew](https://brew.sh) or grab the latest version from [the replicatedhq/replicated releases page](https://github.com/replicatedhq/replicated/releases).

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

#### 2. Setting an API Token

Next we'll set up two environment variables. You can export these right in your shell or add them to your favorite dotfiles.

If you haven't already, you'll want to head over to https://vendor.replicated.com and create an Application. You can name the app whatever you want, we'll use "CLI Quickstart" in this case. From there you can head to the settings page, grab the Application Slug, and export it:

![app-slug](/images/guides/kots/cli-setup-quickstart-settings.png)

In this case, we'd run

```shell script
export REPLICATED_APP=cli-quickstart-puma
```

Then you can create a Read/Write capable API token and export it as well:

![app-slug](/images/guides/kots/cli-setup-api-token.png)

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

Next, we'll need some YAML files from which to create a release.
We'll start from the repo at https://github.com/replicatedhq/replicated-starter-kots but you can use any set of Kubernetes YAMLs or a Helm Chart.
When you've finshed this guide, you can explore [Creating a release from an existing Helm Chart](/vendor/guides/helm-chart) for steps on how to add the necessary kots YAMLs to your helm chart.


###### 3a. Using the default starter YAML

If you don't have any YAML to start with, you can download the [kots default yaml](https://github.com/replicatedhq/kots-default-yaml) to your local repo. If you've already created at least one release, skip to [Downloading an Existing release](#3b-downloading-an-existing-release).


```shell script
mkdir -p ./manifests
curl -fSsL https://github.com/replicatedhq/kots-default-yaml/archive/v2020-09-03.tar.gz | \
  tar xzv --strip-components=1 -C ./manifests \
  --exclude README.md --exclude LICENSE --exclude .gitignore
```

######  3b. Downloading an existing release

If you've already created a release in [vendor.replicated.com](https://vendor.replicated.com), you can fetch it with `replicated release download`. First, you'll want to list out your releases, so you can choose which one you want to fetch

```text script
$ replicated release ls 
SEQUENCE    CREATED                      EDITED                  ACTIVE_CHANNELS
2           2020-09-15T10:49:22-05:00    0001-01-01T00:00:00Z
1           2020-09-15T10:49:22-05:00    0001-01-01T00:00:00Z
```

In this case, let's download sequence 2 into a folder called `manifests` in the current directory:

```shell script
export SEQUENCE_NUMBER=2
replicated release download ${SEQUENCE_NUMBER} --dest ./manifests
```

#### 3c. Verifying manifests

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

* * *

### 4. Creating our first release


Assuming our YAMLs are in a `manifests` directory, let's create a release and promote it to the `Unstable` channel so we can text it internally. We'll hard code the release notes and version label for now.


```shell script
replicated release create \
  --yaml-dir manifests \
  --promote Unstable \
  --version dev \
  --release-notes "my first release"
```

You'll see output similar to the following:

```text
  • Reading manifests from manifests ✓
  • Creating Release ✓
    • SEQUENCE: 1
  • Promoting ✓
    • Channel VEr0nhJBBUdaWpPvOIK-SOryKZEwa3Mg successfully set to release 1
```

You can verify the release was created with `release ls`:

```text
$ replicated release ls
SEQUENCE    CREATED                      EDITED                  ACTIVE_CHANNELS
1       2020-09-03T11:48:45-07:00    0001-01-01T00:00:00Z    Unstable
```

* * *

### 5. Creating a Customer License

Now that we've created a release, we need to create a "customer" object. A customer represents a single licensed end user of your application.

In this example, we'll create a customer named `Some Big Bank` with an expiration in 10 days. Since we created our release on the `Unstable` channel, we'll assign the customer to this channel.

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
ID:             VEr0nhJBBUdaWpPvOIK-SOryKZEwa3Mg
NAME:           Unstable
DESCRIPTION:
RELEASE:        130
VERSION:        dev
EXISTING:

    curl -fsSL https://kots.io/install | bash
    kubectl kots install kots-dex/unstable

EMBEDDED:

    curl -fsSL https://k8s.kurl.sh/kots-dex-unstable | sudo bash

AIRGAP:

    curl -fSL -o kots-dex-unstable.tar.gz https://k8s.kurl.sh/bundle/kots-dex-unstable.tar.gz
    # ... scp or sneakernet kots-dex-unstable.tar.gz to airgapped machine, then
    tar xvf kots-dex-unstable.tar.gz
    sudo bash ./install.sh airgap
```

* * *

### 7. Installing KOTS

From here you can choose whether you'd like to do an [Embedded cluster install](/kotsadm/installing/installing-embedded-cluster/) or an [Existing Cluster install](/kotsadm/installing/online-install/). We'll skip Airgap for now, as it is covered in great depth in [other guides](/vendor/guides). For the sake of simplicity, we'll run with an "embedded cluster" install on a single VM, since those are usually easier to come by than a full Kubernetes cluster.

First we will need a server. We'll use Google Cloud for this example but any cloud provider or [local virtual machine](https://github.com/replicatedhq/replicated-automation/tree/master/vendor/vagrant-boxes) will suffice. For this guide, let's create a server with:

- Ubuntu 18.04
- at least 8 GB of RAM
- 4 CPU cores
- at least 100GB of disk space


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
    curl -sSL https://kurl.sh/starter-kots-demo-unstable/join.sh | sudo bash -s kubernetes-master-address=[ip-address]:6443 kubeadm-token=[token] kubeadm-token-ca-hash=sha256:[sha] kubernetes-version=1.16.4 docker-registry-ip=[ip-address]

```

Following the instructions on the screen, you can reload the shell and `kubectl` will now work:

```bash
user@kots-guide:~$ kubectl get pods
NAME                                  READY   STATUS      RESTARTS   AGE
kotsadm-585579b884-v4s8m              1/1     Running     0          4m47s
kotsadm-api-659db65fcd-b7kds          1/1     Running     2          4m47s
kotsadm-migrations                    0/1     Completed   2          4m47s
kotsadm-operator-fd9d5d5d7-8rrqg      1/1     Running     0          4m47s
kotsadm-postgres-0                    1/1     Running     0          4m47s
kurl-proxy-kotsadm-77c59cddc5-qs5bm   1/1     Running     0          4m46s
user@kots-guide:~$
```

* * *

### 8. Install License

At this point, kotsadm and Kubernetes are running, but the application isn't yet. This is also what your customer would be experiencing when installing your application. To complete the installation, visit the URL that the installation script displayed when completed. [kurl.sh](https://kurl.sh) KOTS clusters provision a self-signed certificate on every installation and detects what browser is being used in order to show users how to bypass this.

On the next screen, you have the option of uploading a trusted cert and key. For production installations we recommend using a trusted cert. For this demo let's continue with the KOTS-generated self-signed cert. Click the "skip this step" button.

![Console TLS](/images/guides/kots/admin-console-tls.png)

Now the installation needs a license file to continue. Until this point, this server is just running Docker, Kubernetes, and the kotsadm containers. Once we put a license file on it the server will install our application. Click the Upload button and select your `.yaml` file to continue.

![Upload License](/images/guides/kots/upload-license.png)

The settings page is here with default configuration items. For now, if you're using the defaults you'll want to check the "Enable Ingress" box. Later you'll customize this screen to collect configuration your application needs from the customer.

![Settings Page](/images/guides/kots/configuration.png)

Preflight checks are designed to ensure this server has the minimum system and software requirements to run the application. Depending on your YAML in `preflight.yaml`, you may see some of the example preflight checks fail. If you have failing checks, you can click continue -- the UI will show a warning that will need to be dismissed before you can continue.

![Preflight Checks](/images/guides/kots/preflight.png)

Click the Application link on the top to see the application running. If you are still connected to this server over ssh, `kubectl get pods` will now show the example nginx service we just deployed.

![Dashboard](/images/guides/kots/dashboard.png)

On the nav bar, there's a link to the application page. Clicking that will show you the Kubernetes services that we just deployed.

![Cluster](/images/guides/kots/application.png)

### View the application

If you used the default nginx application, you can view the application at `http://${INSTANCE_IP}/` with no port, and you should see a basic (perhaps familiar) nginx server running:

![Cluster](/images/guides/kots/example-nginx.png)

Next, we'll walk through creating and delivering an update to the application we just installed.

* * *

### 9. Iterating

From our local repo, we can update the nginx deployment to test a simple update to the application. If you're using your own YAML or helm chart, you can simulate an application update by adding an annotation or making any other superficial change.

In the release YAML, find the nginx image to change. The line is in the `deployment.yaml` file and looks like:

```yaml
replicas: 1
```

Change the number to `2` or more.

we can create a new release

```shell script
replicated release create \
  --yaml-dir manifests \
  --promote Unstable \
  --version dev-2 \
  --release-notes "my second release"
```

Alternatively, this might also be a good time to try out the `--auto` flag to populate values for Channel, Version, and Release Notes based on your current git status (note that this requires you to be working in a git repo).

```shell script
replicated release create --auto
```

You'll see output with a prompt:

```text
  • Reading Environment ✓

Prepared to create release with defaults:

    yaml-dir        "./manifests"
    promote         "Unstable"
    version         "Unstable-50b9a7"
    release-notes   "CLI release by dex on 06 Sep 20 12:09 PDT"
    ensure-channel  true

Create release with these properties? [Y/n]
```

Confirm the prompt by hitting Enter, and you should see familiar output:

```text
  • Reading manifests from ./manifests ✓
  • Creating Release ✓
    • SEQUENCE: 132
  • Promoting ✓
    • Channel VEr0nhJBBUdaWpPvOIK-SOryKZEwa3Mg successfully set to release 132
```

### Update the Test Server

To install and test this new release, we need to connect to the Admin Console dashboard on port :8800 using a web browser.
 At this point, it will likely show that our test application is Up To Date and that No Updates Are Available.
  kotsadm will check for new updates about every five hours but we can force it to check now.

In the Application or Version History tab click on the Check For Updates button.
 On the version history page the faded "Deployed" button should become active and say "Deploy."
  You'll also see a diff indicator showing how many lines were changed in the update.
   You can click the diff numbers to view what has changed in the yaml.


![View Update](/images/guides/kots/view-update.png)

Clicking the Deploy button will apply the new YAML which will change the number of nginx replicas.
This should only take a few seconds to deploy.
You can verify this on the server by running

```shell script
kubectl get pod -l component=nginx
```

You should see two pods running.

* * *

### Next Steps

From here, it's time to start iterating on your application. Continue making changes and using `replicated release create --auto` to push them. You can add `-y` to the command to skip the prompt.


If you want to learn more about KOTS features, you can explore some of the [intermediate and advanced guides](/vendor/guides), some good next steps might be

- [Integrating your release workflow with CI](/vendor/guides/ci-cd-integration)
- [Integrating a Helm Chart](/vendor/guides/helm-chart)

<!-- Coming soon
* * *

## Appendix: Bootstrapping the Repo


### Creating a release from an existing Helm Chart

If you already have your helm chart in a git repo somewhere, you can easily add a `kots` directory to set up the directory for CLI usage.

```shell script
replicated init-kots-app path/to/chart/root
```

### Creating a release from a kustomize repo
-->
