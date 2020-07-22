---
date: "2020-02-20T00:00:00Z"
lastmod: "2020-02-20T00:00:00Z"
title: "Deploy an HA Cluster"
weight: "1005"
---

In this guide, we'll walk through the steps nescessary to stand up a 3 node Kubernetes cluster, all as Mater nodes to provide High Availability.

As with the rest of the guides, this is meant as a 'hello world' example to help get you familiared the process of standing up an HA cluster.

At a high level, the process of setting up an HA cluster consists of the following:

- [Procuring the (virtual) hardware](#provisioning-the-virtual-hardware)
- [Setting up and configuring a Load Balancer](#setting-up--configuring-a-load-balancer)
- Run the Kots installer on the first node and deploy the application.
- Wait and verify that the 1 node deployment worked.
- Run command on remaining nodes to join the cluster.
- Verify that the nescessary pods are now running on the two new nodes

To verify that the HA cluster is in fact 'Highly Available' we will perform two tests:

- Deleting Pods and verifying that they get scheduled approprietly
- Shutting off a node and ensure that the remaining 2-node cluster is still stable.

There are a few things to keep in mind about this guide:

- All VMs are going to be generated using Google Cloud. 
- The Load Balancer we'll use for this exercise is HA Proxy. 
- The sample application has a database component as described [here]() .

### Prerequisites

This guide will assume you've already completed one of the [Getting Started Guides](/vendor/guides/#getting-started) including having an application to deploy. Depending on your use cases you'd like to test with the HA cluster, you may need to provide your own application.

You may also choose to use the sample application described below.

### Sample Application

The KOTS application we'll use in this guide is available in the [kotsapps repository] (TODO - here). 

The application consists of two components:

- Python Flask App Deployment - This flask application contains 'routes' to help test connecting, reading and writing to a database. 
- Postgres StatefulSet - This is the database the for the flask application.

A full description of the application is available in the repository.

## Provisioning the (Virtual) Hardware

For this exercise, we are going to create 4 Virtual Machines. One of the VMs will be used to install, configure and run HA Proxy. The remaining 3 VMs will be the 3 Master Nodes for our cluster. 

### Provisioning the HA Proxy VM

For the VM that will host HA Proxy, a bare bones VM should suffice as we will only use it to route traffic. As an example, the command below creates a VM instance with enough juice to run HA Proxy, at least for testing purposes:

```shell
gcloud compute instances create app-direct-ha-proxy  --boot-disk-size=10GB --labels=app=app-direct --image-project ubuntu-os-cloud --image-family ubuntu-1804-lts --machine-type n1-standard-1
```

In the command output, you should note the public and internal IP address. The public address of this VM is what we'll use to connect to both the Kots Admin Application console, the application's UI and pgAdmin. We'll use the interal IP address when we install KOTS in HA mode.

If you'd like to explore HA Proxy requirements for heavier workloads, please check HA Proxy's [documentation](https://www.haproxy.com/documentation/hapee/2-0r1/installation/getting-started/os-hardware/)


### Provisioning the cluster VMs

Like with the HA Proy VM, keep the [kots System Requirements](https://kurl.sh/docs/install-with-kurl/system-requirements) in mind when provisioning the VMs that will form the cluster. The command below creates a VM that meets those requirements, at least for testing purposes.

```shell
gcloud compute instances create app-direct-node-01 --boot-disk-size=200GB --labels=app=app-direct --image-project ubuntu-os-cloud --image-family ubuntu-1804-lts --machine-type n1-standard-4
```
To create the remaining nodes, simply run the same command but increment the node number (i.e., "app-direct-node-02").

Also note the VMs public and private ip addresses as each VM is provisioned. We'll use the internal ip addresses in the HA Proxy to configure traffic accordingly.

## Setting Up & Configuring a Load Balancer

The main purpose of the load balancer is to direct traffic to the proper node. All end-user interactions (i.e., access the web UI of the deployed application) should go through the load balancer.

For this guide, HA Proxy will be the load balancer and will be running on its own VM, provisioned already in the previous step. HA Proxy is configured by editing the haproxy.cfg file that by default resides in '/etc/haproxy'. 

Here are the high level steps we'll take to install & configure HA Proxy.

We will:
- Create the config file in our home folder using our favorite editor.
- Use SCP to copy the file in the VM running HA Proxy.
- Install Ha Proxy.
- Copy the config file to the proper location.
- Restart HA Proxy. 

Note that another way of accomplishing this is by conneting to the instance via SSH and creating the file and editing from the command line inside the VM.

To create the config file, use the editor of your choice and call it 'haproxy.cfg'. For the purposes of this exercise, we'll create the file in our home folder. Below is an example config file for the sample application. Keep in mind the following when creating your file:

- IP addresses below will not work as the VMs were destroyed a long time ago. Replace these with the internal IP addresses of your VMs. 
- If you used a different naming convention for your instance names, you will need to replace 'app-direct-node-0(123)' with your instance names.
- There are two entries specific to the Application, one for each component (app-direct-frontend/backend & postgres frontend/backend). If you are using a different application, adjust based on the endpoints you want to access through the load balancer. The remaining entries are for the KOTs UI and API and should be left as-is other than the instance names and ip addresses

```code
global
defaults
   timeout client          60s
   timeout server          60s
   timeout connect         60s
#############################################################
listen stats
   bind *:8080
   mode http
   stats enable
   stats uri /
   stats hide-version
#############################################################
frontend app-direct-frontend
   mode tcp
   bind    *:80
   default_backend app-direct-backend
backend app-direct-backend
   mode                    tcp
   balance roundrobin
   option tcp-check
   server app-direct-node01 node-01-ip:80 check
   server app-direct-node02 10.240.0.39:80 check
   server app-direct-node03 10.240.0.71:80 check
#############################################################
frontend postgres-frontend
   mode tcp
   bind    *:5432
   default_backend postgres-backend
backend postgres-backend
   mode                    tcp
   balance roundrobin
   option tcp-check
   server app-direct-node01 10.240.0.37:5432 check
   server app-direct-node02 10.240.0.39:5432 check
   server app-direct-node03 10.240.0.71:5432 check
#############################################################
frontend replicated-frontend
   mode tcp
   bind    *:8800
   default_backend replicated-backend
backend replicated-backend
   mode                    tcp
   balance roundrobin
   option tcp-check
   server app-direct-node01 10.240.0.37:8800 check
   server app-direct-node02 10.240.0.39:8800 check
   server app-direct-node03 10.240.0.71:8800 check
###########################################################
frontend kube-api-frontend
   mode tcp
   bind    *:6443
   default_backend kube-api-backend
backend kube-api-backend
   mode                    tcp
   balance roundrobin
   option tcp-check
   server app-direct-node01 10.240.0.37:6443 check
   server app-direct-node02 10.240.0.39:6443 check
   server app-direct-node03 10.240.0.71:6443 check

```

For more details on the structure of the config file, please refer to the HA Proxy [documentation](https://www.haproxy.com/blog/the-four-essential-sections-of-an-haproxy-configuration/)

```shell
gcloud compute scp haproxy.cfg app-direct-ha-proxy:~/
```




Go to `kotsadm` and click on `Version history -> Check for updates`. Once the update is available click on `Deploy`.

![Helm Chart Check for Updates 1](/images/guides/kots/helm-chart-check-for-updates-1.png)


From here, we can verify that the Grafana chart is deployed

```text
$ kubectl get po

NAME                                READY   STATUS              RESTARTS   AGE
grafana-db9df67f6-vfdnd             0/1     ContainerCreating   0          5s
grafana-test                        0/1     ContainerCreating   0          5s
```

The chart by default doesn't have an `Ingress` but we can use `port-forward` for now.
```shell
kubectl port-forward service/grafana 8080:80
```

Log in to the Grafana UI via `http://localhost:8080`, using the `admin/admin` password pair we created in the our via Helm values.

![Helm Chart Grafana Login](/images/guides/kots/helm-chart-grafana-login.png)

* * *

## Mapping Config Screen to Helm Values

Kots allows you to map the [config screen](/vendor/config/config-screen) to the Helm `values.yaml` file.

### Choosing Values

To start, we can read the `values.yaml` to find a few values.

```shell
tar -xOf ~/helm-grafana/manifests/grafana-5.0.13.tgz grafana/values.yaml
```

In this example we used `adminUser` and `adminPassword`.
```yaml
...
adminUser: admin
adminPassword: admin
...
```

### Create config fields

Add username and password fields to `~/helm-grafana/manifests/config.yaml`
```yaml
apiVersion: kots.io/v1beta1
kind: Config
metadata:
  name: grafana-config
spec:
  groups:
    - name: grafana
      title: Grafana
      description: Grafana Configuration
      items:
        - name: admin_user
          title: Admin User
          type: text
          default: 'admin'
        - name: admin_password
          title: Admin Password
          type: password
          default: 'admin'
```

To test this, run `make release`, update the new version, and go to the kotsadm `Config` screen.
![Helm Chart Grafana Config Screen](/images/guides/kots/helm-chart-grafana-config-screen.png)

For now, these fields will have no effect. Next, we'll map these user-supplied values to Helm Chart values.

### Map to Helm Chart

In `~/helm-grafana/manifests/grafana.yaml` update `values` with the `ConfigOption` template function.

```diff
@@ -7,5 +7,5 @@
     name: grafana
     chartVersion: 5.0.13
   values:
-    adminUser: "admin"
-    adminPassword: "admin"
+    adminUser: "repl{{ ConfigOption `admin_user`}}"
+    adminPassword: "repl{{ ConfigOption `admin_password`}}"
```


Before deploying let's quickly check the value of the secret, so we can confirm it changed

```text
$ kubectl get secret grafana -o yaml

apiVersion: v1
data:
  admin-password: YWRtaW4=
  admin-user: YWRtaW4=
...
```

Next, we can make a release and get ready to test it out

```shell
make release
```

Deploy the new release
![Helm Chart Check for Updates 2](/images/guides/kots/helm-chart-check-for-updates-2.png)

Update the User and Password fields in kotsadm `Config` and verify the `grafana` secret got updated.

```text
$ kubectl get secret grafana -o yaml

apiVersion: v1
data:
  admin-password: cGFzc3dvcmQhMTIz
  admin-user: YWRtaW4=
...
```

Login via `http://localhost:8080` with the new credentials.

* * *

## Next Steps

You did it! Next steps are:

- If you're using this chart as the primary artifact in your KOTS application, you might want to take an `icon` from the `Chart.yaml` and add it to your kots `Application` CRD.
- Managing CRDs if you want to use a chart that needs them (advanced guide)
- Airgap / Helm (advanced guide)
- Explore Operators (advanced guide)
