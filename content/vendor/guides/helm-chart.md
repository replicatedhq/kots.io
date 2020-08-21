---
date: "2020-02-20T00:00:00Z"
lastmod: "2020-02-20T00:00:00Z"
title: "Package a Helm Chart"
weight: "1005"
---

In this guide, we'll explore how you can leverage software bundled using Helm to integrate an off-the-shelf application into your KOTS app bundle. 
We'll use the Grafana helm chart as an example in this case. 
The guide is divided into three parts:

- [Adding a Chart to your Application](#adding-a-chart-to-your-application)
- [Releasing and Testing](#releasing-and-testing)
- [Mapping Config Screen to Helm Values](#mapping-config-screen-to-helm-values)

### Prerequisites

This guide will assume you've already completed one of the [Getting Started Guides](/vendor/guides/#getting-started) including having running instance of `kotsadm` to iterate against in either an existing or embedded cluster, and a local git checkout of your KOTS app manifests. 
We'll assume this is checked out in `~/helm-grafana`.

### Accompanying Code Examples

A full example of the code for this guide can be found in the [kotsapps repository](https://github.com/replicatedhq/kotsapps/tree/master/helm-grafana).

* * *

## Adding a Chart to your Application

### Getting a Chart Archive

We can get the Helm Chart archive in a couple of ways, either using `helm fetch` or `helm package`.

##### Using helm fetch

Add a Helm repo

```text
$ helm repo add stable https://kubernetes-charts.storage.googleapis.com

"stable" has been added to your repositories
```

Verify the repo has been added

```text
$ helm repo ls

NAME    URL
stable  https://kubernetes-charts.storage.googleapis.com
```

For this example we'll fetch the grafana helm chart

```shell
helm fetch stable/grafana
```

##### Using helm package

Clone the upstream `helm/charts` repo.

```shell
git clone git@github.com:helm/charts.git
```

Helm package the Grafana install.

```text
$ helm package charts/stable/grafana

Successfully packaged chart and saved it to: ~/grafana-5.0.13.tgz
```

##### Verify Helm chart

From `Chart.yaml` get the `name` and `version`, we'll need these for the next step when creating the `HelmChart` kots kind.

```text
$ tar -xOf grafana-5.0.13.tgz grafana/Chart.yaml

...
name: grafana
...
version: 5.0.13
```

### Adding the Archive to your manifests

Copy the helm chart archive to kots app
```shell
cp grafana-5.0.13.tgz ~/helm-grafana/manifests/
```

Create `HelmChart` kots kind file and use the `name` and `version` from previous step for `name` and `chartVersion`, respectively.



```shell
cat <<EOF >>~/helm-grafana/manifests/grafana.yaml
apiVersion: kots.io/v1beta1
kind: HelmChart
metadata:
  name: grafana
spec:
  chart:
    name: grafana
    chartVersion: 5.0.13
  values:
    adminUser: 'admin'
    adminPassword: 'admin'
EOF
```

You may have noticed that we have also used this opportunity to set just two of the values for this chart: `adminUser` and `adminPassword`. 
We'll hardcode them to `admin`/`admin` for now, but we'll make these user-configurable later.

* * *

## Releasing and Testing

Make a new release with the Helm chart in your kots app.

```shell
cd ~/helm-grafana
make release
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

For now, these fields will have no effect. 
Next, we'll map these user-supplied values to Helm Chart values.

### Map to Helm Chart

In `~/helm-grafana/manifests/grafana.yaml`, update `values` with the `ConfigOption` template function.

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
