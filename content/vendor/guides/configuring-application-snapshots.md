---
date: "2020-09-06T00:00:00Z"
lastmod: "2020-09-06T00:00:00Z"
title: "Configure Application Snapshots"
weight: "1005"
---

KOTS provides [snapshot capabilities](https://kots.io/vendor/snapshots/overview/) by leveraging the [Velero](https://velero.io/) open source project.

## Objective 

The purpose of this guide is to walk you through the nescessary steps to enable and configure snaphots in KOTS. 
If you want to follow along, we'll be using the [AppDirect](https://github.com/replicatedhq/kotsapps/tree/master/app-direct) sample application.

## Overview

The guide is divided into three parts. The first part includes the steps performed by the software vendor, or similar organization that is packaging an application for delivery using Replicated KOTS.
The second and third parts include steps that would be performed by the end user or customer.

  [Part I - Enabling Snashots in KOTS](#part-i---enabling-snaphots-in-kots) Includes steps to enable snapshots in an application and configure the backup process.

  [Part II - Application Configuration](#part-ii---application-configuration) This part covers the steps nescessary to enable and configure snaphots in the deployed application.
 
  [Part III - Working with Snaphots](#part-iii---working-with-snaphots) This part covers tasks that are performed after the intial configuration.


## Part I - Enabling Snaphots in KOTS


These steps enable application snaphots by the vendor, which at a high level are:

- [Add Backup Resource File](#add-backup-resource-file) - Adding this file pratcially enables snaphots in the application
- [Configure Volumes](#configure-volumes) - Volumes are only part of the snapshot if they are annotated to be included.
- [Configure Backup Hooks](#optionaladvanced-use-case-backup-hooks) - - May not be applicable to every situation, but these allow you to run commands prior to a backup.


### Add Backup Resource File

Create a new file named backup.yaml and add the following content:

```shell
apiVersion: velero.io/v1
kind: Backup
metadata:
  name: backup
spec: {}

```

This is the minimal configuration and will suffice. 
For further details on all of the available specs, please check the Velero documentation [here](https://velero.io/docs/v1.3.2/api-types/backup/)

### Configure Volumes

Volumes by default are not part of the snapshot.
You configure which volumes to be part of the snaphot by adding the ```backup.velero.io/backup-volumes: ``` label annotation.

In the AppDirect sample application, we have a PostgreSQL stateful set which has the following Volume:

{{< notes title="Note" >}}
This yaml is trucated for readability purposes. 
The entire file is located [here](https://github.com/replicatedhq/kotsapps/blob/master/app-direct/manifests/postgres-deployment.yaml) in the GitHub repo
{{< /notes >}}

```shell
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: app-direct-postgresql
  labels:
    app: postgresql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: postgresql
  serviceName: postgres-service
  template:
    metadata:
      labels:
        app: postgresql   
    spec:
      containers:
...
      volumeMounts:
        - name: postgresql-vct
          mountPath: /var/lib/postgresql/data/pgdata
          subPath: postgresql-db
  volumeClaimTemplates:
  - metadata:
      name: postgresql-vct
    spec:
      accessModes:
      - ReadWriteOnce
      resources:
        requests:
          storage: 2Gi

```

To have the volume included in the snapshot, we add the label annotation as shown below:

```shell
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: app-direct-postgresql
  labels:
    app: postgresql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: postgresql
  serviceName: postgres-service
  template:
    metadata:
      labels:
        app: postgresql 
      annotations:
        backup.velero.io/backup-volumes: postgresql-vct # this volume will be included in snapshot
    spec:
      containers:
...

```

The documentation also provides an example [here](https://kots.io/vendor/snapshots/configuring-backup/#identify-volumes) 

### Optional/Advanced Use Case: Backup Hooks

In the example above, we are simply taking a snapshot of the volume. 
While that may suffice for certain cases, in other cases you may need perform actions before the snapshot takes place. 
For this, we have Backup Hooks. 
Velero provides both pre and post [backup hooks](https://velero.io/docs/v1.4/hooks/#docs). 
These can be configured by adding annotations to the pod itself or in the [Backup spec](https://velero.io/docs/v1.2.0/api-types/backup/). 
For the purposes of this guide, we'll do the former and use label annotations.

In the example above, a more realistic scenario is that we'll probably want to run ```pg_dump``` and only take a snapshot of that backup. 
To accomplish this we'll need to:

- Add the ```pre.hook.backup.velero.io/command``` label annotation to execute ```pg_dump``` and put the backup in a new folder.
- Add the ```pre.hook.backup.velero.io/timeout``` label annotation to give the backup time to run and store the output.
  Per the Velero documentation, "The hook is considered in error if the command exceeds the timeout." so make sure this value exceeds how long you expect the command to take.
- Add a new volume to the pod and mount it to the new folder containing the ```pg_dump``` output.

Below is the same definition file with the modifications:

```shell

apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: app-direct-postgresql
  labels:
    app: postgresql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: postgresql
  serviceName: postgres-service
  template:
    metadata:
      labels:
        app: postgresql
      annotations: 
        pre.hook.backup.velero.io/command: '["/bin/bash", "-c", "PGPASSWORD=$POSTGRES_PASSWORD pg_dump -U $POSTGRES_USER -d appdirectdb -h 127.0.0.1 > /backup/backup.sql"]'
        pre.hook.backup.velero.io/timeout: 3m
        backup.velero.io/backup-volumes: postgresql-backup 
    spec:
      containers:
      - name: app-direct-postgresql
        image: "postgres:9.6"
        env:
        - name: POSTGRES_USER
          value: "postgres"
          # Required for pg_isready in the health probes....
        - name: PGUSER
          value: "postgres"
        - name: POSTGRES_DB
          value: "postgres"
        - name: POSTGRES_INITDB_ARGS
          value: ""
        - name: PGDATA
          value: /var/lib/postgresql/data/pgdata
        - name: POSTGRES_PASSWORD
          value: "postgres"
        - name: POD_IP
          valueFrom: { fieldRef: { fieldPath: status.podIP } }
        ports:
        - name: postgresql
          containerPort: 5432
        livenessProbe:
          exec:
            command:
            - sh
            - -c
            - exec pg_isready --host $POD_IP
          initialDelaySeconds: 60
          timeoutSeconds: 5
          failureThreshold: 6
        readinessProbe:
          exec:
            command:
            - sh
            - -c
            - exec pg_isready --host $POD_IP
          initialDelaySeconds: 5
          timeoutSeconds: 3
          periodSeconds: 5
        resources:
          requests:
            cpu: 100m
            memory: 256Mi

        volumeMounts:
        - name: postgresql-vct
          mountPath: /var/lib/postgresql/data/pgdata
          subPath: postgresql-db
        - name: postgresql-backup
          mountPath: /backup
          subPath: postgresql-db
  volumeClaimTemplates:
  - metadata:
      name: postgresql-vct
    spec:
      accessModes:
      - ReadWriteOnce
      resources:
        requests:
          storage: 2Gi
  - metadata:
      name: postgresql-backup
    spec:
      accessModes:
      - ReadWriteOnce
      resources:
        requests:
          storage: 2Gi

```
{{< notes title="Note" >}}
Note that in the command in the label annotation we are backing up the appdirectdb database, which does not exist when the application is first deployed. 
For testing purposes, you could change it to back up the postgres database.
{{< /notes >}}
### Configuring the Cluster

KOTS uses the [Velero](https://velero.io/) open source project to provide back up capabilities and will need to be added to the cluster. 
The process for adding Velero will depend if the cluster is being managed by KOTS or not.

#### Clusters Managed by KOTS

For those applications running on a cluster [managed by KOTS](https://kots.io/vendor/packaging/embedded-kubernetes/), add the Velero add-on to the Kubernetes KURL installer. 
While there are various configuration options as outlined [here](https://kurl.sh/docs/add-ons/velero), you should only need to add the following to your KURL Kubernetes installer.

```shell
  velero:
    version: latest
```

Make sure to promote the installer to the same channel as the application.

#### Other Clusters

Since KOTS does not manage the cluster in this instance, it has to be done by the end user and covered in [Part II](#check-velero-installation).

## Part II - Application Configuration

Once the configurations covered in Part I have been pushed to a release and promoted, anyone that installs or updates the application to this release will now have the ability to configure Snapshots. 
The user will know that Snapshots are now available to configure when the **Snaphot Settings** tab becomes avialabe as illustrated below.

![initialwindow](/images/guides/kots/snap_guide_initial_window.png)

To configure snapshpots in the Admin Console, click on the **Snapshot Settings** tab. 
Here, we'll configure settings that apply to this instance of KOTS which allows you to specify a Storage Option and check the Velero install.

![storage_opts](/images/guides/kots/snap_guide_storage_conf.png)

#### Choose Storage

There are three options currently available to store the snapshots:

- Amazon S3 Bucket
- S3 Compatible Bucket
- Local Storage

#### Check Velero Installation

This allows you to verify that KOTS detects Velero in the cluster as shown in the image above. 
If KOTS is unable to detect Velero, or the cluster does not have Velero installed yet, you will see the following instead:

![novelero](/images/guides/kots/snap_guide_no_velero.png)

This is what you will see if you install the application to a cluster not manged KOTS. 
Follow the instructions on the screen to install Velero in your cluster.

Once Snapshots have been properly configured, the Snapshots tile and the Snapshots tab are now visible in the Application Dashboard as highlighted in the image below.

![admin_console](/images/guides/kots/snap_guide_admin_console.png)

To manage snapshots for the applicatin itself, click on the **Snaphots** tab that just became available. 
The **Snaphots** tab displays the snapshots that have been taken. Since the first time you access this tab you may not have any snapshots yet, it may look like this:

![no_snapshots](/images/guides/kots/snap_guide_no_snap.png)

You can either select to run your first snapshot or configure your backup schedule and retention policy.

#### Schedule Backups & Retention Policy

Under the **Snapshot** tab under **Configure**, you have the following scheduling options:

![schedule_opts](/images/guides/kots/snap_guide_schedule_opts.png)

The following retention options are also available:

![retention_policy](/images/guides/kots/snap_guide_ret_policy.png)

## Part III - Working with Snaphots

To access snapshots, adjust settings, etc... go to the **Snapshots** tab in the Admin Console Snaphots tab:

![snapshot_list](/images/guides/kots/snap_guide_snap_list.png)

From here, you can view any snapshot details, access snapshot settings, scheduling options and also create a snapshot. 
The details for any given snapshot are available by clicking on the View link. 
The details, as shown below, includes a timeline view to see how long the operation took, the volumes and any scripts invoked by the hooks.

![snapshot_details](/images/guides/kots/snap_guide_snap_details.png)

To restore a specific snapshot, click on the **Restore** link by the desired snaphot. 
The process may take several minutes and KOTS will first undeploy the current application. 
This means that all existing application manifests will be removed from the cluster and all PersistentVolumeClaims will be deleted. 
All Application Manifests will then be redeployed to the namespace along with initContainers used by Velero in the restore process.

For mote details on the restore process, please check [this](https://velero.io/docs/v1.2.0/restic/#how-backup-and-restore-work-with-restic) documentation page from Velero.

## Other Resources

[Postgres Snapshot Sample Application]()For another working example of a KOTS application check the Potgres Snapshot application in GitHub.

[KOTS Snashot Documentation](https://kots.io/vendor/snapshots/overview/) For more details about snapshots in KOTS, please review the docs for KOTS.

[Velero Documentation](https://velero.io/docs/)

