---
date: "2020-09-06T00:00:00Z"
lastmod: "2020-09-06T00:00:00Z"
title: "Configure Application Snapshots"
weight: "1005"
---

KOTS provides [snapshot capabilities](https://kots.io/vendor/snapshots/overview/) by leveraging the [Velero](https://velero.io/) open source project. This is an advanced topic 

## Objective 

The purpose of this guide is to walk you through the nescessary steps to enable and configure snaphots in KOTS. 
In this guide, we'll use a Postgres `StatefulSet` as this is a common use case.

## Prerequisites & Assumptions

This power-user's guide assumes you have completed the [standard quickstart](https://kots.io/vendor/guides/quickstart/) or the [CLI quickstart](https://kots.io/vendor/guides/cli-quickstart/) guides as this guide is a continuation of those guides.
As with the previous guides, we will also need a VM to install the application with the following minimim requirements:

* Ubuntu 18.04
* At least 8 GB of RAM
* 4 CPU cores
* At least 100GB of disk space

## Overview

The guide is divided into the following steps:

  [1. Set Up Testing Environment](#1-set-up-testing-environment) 

  [2. Enable Snashots in KOTS](#2-enable-snashots-in-kots) 
  
  [3. Configure the Deployed Application](#3-configure-the-deployed-application) 
  
  [4. Take the First Snaphots](#4-take-the-first-snaphots) 
  
  [5. Add Another Record to the Table](#5-add-another-record-to-the-table) 
  
  [6. Restore the First Snapshot](#6-restore-the-first-snapshot)
  
  [7. Verify Table Contents](#7-verify-table-contents)
  
  [Optional/Advanced Use Case: Backup Hooks](#optionaladvanced-use-case-backup-hooks)
  
  [Appendix A - Manifest Files](#appendix-a---manifest-files)

## 1. Set Up Testing Environment

For this guide, we are simply deploying a Postgres `StatefulSet` and then will use `psql` to connect, to create a table and add records.
We will also use it to query the table and check for records.

### Install PSQL

To install `psql` and not the PostgreSQL database, follow [this Blog](https://blog.timescale.com/tutorials/how-to-install-psql-on-mac-ubuntu-debian-windows/), which covers installing just the psql client on all Operating Systems.
The steps for MAC OS X and Linux are basically the same, update the installer/package manager and then install it. 

### Create Sample Application and deploy the first release

In this section, we cover at a high level the steps to create a new application and install it on a VM. As mentioned earlier, it's assumed you have completed the [standard quickstart](https://kots.io/vendor/guides/quickstart/) or the [CLI quickstart](https://kots.io/vendor/guides/cli-quickstart/) guide, which cover these steps in detail.

To create our sample application follow these steps:

* Create a new application in Replicated and call it 'SnapshotApp'. 
* Create the first release using the default definition files and promote it to the *unstable* channel.
* Create a customer, assign it to the *Unstable* channel and download the license file after creating the customer.
* Install the application to a Virtual Machine

Once you have installed the first release of the sample application you should arrive at this screen in the Admin Console:

![kots-admin-v1](/images/guides/kots/snap_guide_admin_console_v1.png)


### Update Application to Deploy Postgres

For our second release, remove the default deployment.yaml and add two new files, [postgres-deployment.yaml](#postgres-deploymentyaml) and [postgres-service.yaml](#postgres-serviceyaml) which are included in the Appendix A of this guide.

Promote the release and update the install.

### Connect to Database and Add Table

Now we are going to use psql to connect to the database.
Below is an example how to connect to the postgres database created by our deployment. Note that the `hostname or IP address` refers to the VM where you installed the application. 
It should be the IP address or hostname you use to browse to the Admin Console.

The syntax of the command to connect to the database is:

```sql
$ psql -U <username> -h <hostname or IP address> "dbname=<name-of-db>" 
```

The command above will then prompt for the password.
Based on credentials set in the StatefulSet definition file, and assuming an IP address of x.x.x.x, the command to connect to the default *postgres* database would be as follows:

```sql
$ psql -U postgres -h x.x.x.x "dbname=postgres" 
```
A prompt to enter the password should follow. 
The password set in the Postgres StatefulSet Definition file is 'postgres'.

```sql
Password for user postgres: 
```

A succesful connection should return something similar to this:
```sql
psql (12.4, server 9.6.19)
Type "help" for help.
postgres=
```

Now we are going to create a table which we'll later add some records.
Enter the SQL command on the *postgres=* prompt as follows:

```sql
postgres=#CREATE TABLE tbl_records (sample_field VARCHAR(255) NOT NULL); 
```

If the table is correctly created, it should return:
```sql
CREATE TABLE
```

To verify that the table was in fact created, we can query for records:

```sql
postgres=# SELECT * FROM tbl_records;
```

The output shoulld be similar to this:

```sql
 sample_field 
--------------
(0 rows)
```

Let's add a couple of rows to our table

```sql
postgres=# INSERT INTO tbl_records (sample_field) VALUES ('hello!');

```

To verify that the record was added we can query the table:

```sql
postgres=# SELECT * FROM tbl_records;
 sample_field 
--------------
 hello!
(1 row)
```

Now that our table has some data, let's configure snapshots in KOTS.

* * *

## 2. Enable Snashots in KOTS

At a high level, the steps to enable snapshots in the application are:

- [Add Backup Resource File](#add-backup-resource-file) - Adding this file pratcially enables snaphots in the application
- [Configure the Volume to backup](#configure-volumes) - Volumes are only part of the snapshot if they are annotated to be included.
- [Update the Kubnernetes Installer](#configuring-the-cluster) - Adds the nescessary Add-on for taking snaphots in the cluster.


### Add Backup Resource File

The first step is to add a backup resource file.
Like the Postgres StatefulSet Definition file, the file contents are available in the [Appendix A]
Create a new release and add the [backup.yaml](#backupyaml) file to the application manifests. 
This contains the minimal configuration and will suffice.
For further details on all of the available specs, please check the Velero documentation [here](https://velero.io/docs/v1.3.2/api-types/backup/)

### Configure Volumes

Volumes by default are not part of the snapshot unless they are configured to be included.
To configure which volumes should be part of the snaphot, add the ```backup.velero.io/backup-volumes: ``` label annotation on the pod itself.

In the sample Postgres StatefulSet, the `postgresql-vct` Volume is mounted to the Postgres data directory so let's add the label annotation.
Below shows the addition to make to the file: 

```diff
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
+     annotations:
+       backup.velero.io/backup-volumes: postgresql-vct # this volume will be included in snapshot
    spec:
      containers:
```
### Configuring the Cluster

KOTS uses the [Velero](https://velero.io/) open source project to provide back up capabilities and will need to be added to the cluster. 
Since we used KOTS to install the Kubernetes cluster on the VM, we need to update the Kubernetes intaller.

Update the Kubernetes Installer as shown below and promote it to the *Unstable* channel

```diff
  apiVersion: cluster.kurl.sh/v1beta1
  kind: Installer
  metadata:
    name: ""
  spec:
    contour:
      version: latest
    docker:
      version: latest
    kotsadm:
      version: latest
    kubernetes:
      version: latest
    prometheus:
      version: latest
    registry:
      version: latest
    rook:
      version: latest
    weave:
      version: latest
+   velero:
+     version: latest
```
* * *

## 3. Configure the Deployed Application

Once the updates covered in the previous step have been pushed to a release and promoted, anyone that installs or updates the application to this release will now have the ability to configure Snapshots.
To update the Kubernetes cluster, we'll need to ssh into the VM again and run the install script from the *Unstable* channel.


```shell
$ curl -sSL https://k8s.kurl.sh/snapshotapp-unstable | sudo bash
```

The user will know that Snapshots are now available to configure when the **Snaphot Settings** tab becomes avialabe in the KOTS Admin Console as illustrated below.

![initialwindow](/images/guides/kots/snap_guide_initial_window_v.png)

To configure snapshpots in the Admin Console, click on the **Snapshot Settings** tab. 
Here, we'll configure settings that apply to this instance of KOTS which allows you to specify a Storage Option and check the Velero install.

![storage_opts](/images/guides/kots/snap_guide_storage_conf.png)

#### Choose Storage

There are three options currently available to store the snapshots:

- Amazon S3 Bucket
- S3 Compatible Bucket
- Local Storage

For now we'll go with Local Storage.

#### Check Velero Installation

This allows you to verify that KOTS detects Velero in the cluster as shown in the image above. 
If KOTS is unable to detect Velero, or the cluster does not have Velero installed yet, you will see the following instead:

![novelero](/images/guides/kots/snap_guide_no_velero.png)

You may see this if you deployed the application without updating the cluster.

* * *

## 4. Take the First Snaphots

Once Snapshots have been properly configured, the Snapshots tile and the Snapshots tab are now visible in the Application Dashboard as highlighted in the image below.

![admin_console](/images/guides/kots/snap_guide_snap_options.png)

To manage snapshots for the applicatin itself, click on the **Snaphots** tab that just became available. 
The **Snaphots** tab displays the snapshots that have been taken. Since the first time you access this tab you may not have any snapshots yet, it may look like this:

![no_snapshots](/images/guides/kots/snap_guide_no_snap.png)

You can either select to run your first snapshot or configure your backup schedule and retention policy.

Click on **Start a snapshot** to create our first snapshot.
This snapshot should include our table 'tbl_records' as well as the one row containing the string 'hello!'.

* * *

## 5. Add Another Record to the Table

Now that we have a snapshot, let's add a new row to the table.
This will give us a way to compare the contents of the table after we restore the snapshot.
To add a new record, we'll run:

```shell
postgres=# INSERT INTO tbl_records (sample_field) VALUES ('bye!');
```

To verify that the new row was added, let's query the table:

```shell
postgres=# SELECT * FROM tbl_records;
 sample_field 
--------------
 hello!
 bye!
(2 rows)

```

* * *

## 6. Restore the First Snapshot

To access snapshots, adjust settings, etc... go to the **Snapshots** tab in the Admin Console Snaphots tab:

![snapshot_list](/images/guides/kots/snap_guide_snap_list_v.png)

You should see the snapshot we took previously.
To restore the snapshot click on **restore**.

The process may take several minutes and KOTS will first undeploy the current application. 
This means that all existing application manifests will be removed from the cluster and all PersistentVolumeClaims will be deleted. 
All Application Manifests will then be redeployed to the namespace along with initContainers used by Velero in the restore process.

For mote details on the restore process, please check [this](https://velero.io/docs/v1.2.0/restic/#how-backup-and-restore-work-with-restic) documentation page from Velero.

Once the snapshot restore process completes, you will need to login to the Admin Console again.

* * *

## 7. Verify Table Contents

To verify that the restore process actually worked, let's check the database.
Note that as part of the restore process, the connection to the Postgres database was lost so we'll need to connect to the database again

```shell
psql -U postgres -h 35.238.229.162 "dbname=postgres"
Password for user postgres: 
psql (12.4, server 9.6.19)
Type "help" for help.
```

To check the contents, we query the table again:

```shell

postgres=# SELECT * FROM tbl_records;
 sample_field 
--------------
 hello!
(1 row)

```

* * *

## Optional/Advanced Use Case: Backup Hooks

In many cases, simply taking a snapshot of a volume is not enough. 
For example, the recommend way to backup Postgres is to use `pg_dump` and only take a snapshot of the backup. 
A similar approach would also be needed for applications that use an 'in memory' database that requires a service shut down before any backup is taken.

Velero provides both pre and post [backup hooks](https://velero.io/docs/v1.4/hooks/#docs). 
These can be configured by adding annotations to the pod itself or in the [Backup spec](https://velero.io/docs/v1.2.0/api-types/backup/). 
For the purposes of this guide, we'll do the former and use label annotations.

To accomplish this we'll need to:

- Add the ```pre.hook.backup.velero.io/command``` label annotation to execute ```pg_dump``` and put the backup in a new folder.
- Add the ```pre.hook.backup.velero.io/timeout``` label annotation to give the backup time to run and store the output.
  Per the Velero documentation, "The hook is considered in error if the command exceeds the timeout." so make sure this value exceeds how long you expect the command to take.
- Add a new volume to the pod and mount it to the new folder containing the ```pg_dump``` output.

Below highlights the changes to the Postgres definition file:

```diff
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
-         backup.velero.io/backup-volumes: postgresql-vct # this volume will be included in snapshot
+         pre.hook.backup.velero.io/command: '["/bin/bash", "-c", "PGPASSWORD=$POSTGRES_PASSWORD pg_dump -U $POSTGRES_USER -d postgres -h 127.0.0.1 > /backup/backup.sql"]'
+         pre.hook.backup.velero.io/timeout: 3m
+         backup.velero.io/backup-volumes: postgresql-backup
      spec:
```
Below show the addition of the Volume mounted to the /backup directory.

```diff
          volumeMounts:
          - name: postgresql-vct
            mountPath: /var/lib/postgresql/data/pgdata
            subPath: postgresql-db
+         - name: postgresql-backup
+           mountPath: /backup
+           subPath: postgresql-db
    volumeClaimTemplates:
    - metadata:
        name: postgresql-vct
      spec:
        accessModes:
        - ReadWriteOnce
        resources:
          requests:
            storage: 2Gi
+   - metadata:
+       name: postgresql-backup
+     spec:
+      accessModes:
+       - ReadWriteOnce
+       resources:
+         requests:
+           storage: 2Gi

 ```
As of the time of this writing, Restore Hooks are not currently supported in KOTS. 
This means that the process to restore from the backup file will be a manual one.
Please refer to [this](https://www.postgresqltutorial.com/postgresql-restore-database/) tutorial to learn more about that process.

## Appendix A - Manifest Files

### postgres-deployment.yaml

Postgres Deployment Definition File:

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgresql
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
      - name: postgresql
        image: "postgres:9.6"
        env:
        - name: POSTGRES_USER
          value: "postgres"
          # Required for pg_isready in the health probes.
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
### postgres-service.yaml

Service Defnition File:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: postgresql-service
  labels:
    app: postgresql
spec:
  type: NodePort
  ports:
  - name: postgresql
    port: 5432
    nodePort: 5432
    targetPort: postgresql
  selector:
    app: postgresql
```

### backup.yaml

```yaml
apiVersion: velero.io/v1
kind: Backup
metadata:
  name: backup
spec: {}
```
### postgres-deployment-pgdump.yaml

```yaml

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
        pre.hook.backup.velero.io/command: '["/bin/bash", "-c", "PGPASSWORD=$POSTGRES_PASSWORD pg_dump -U $POSTGRES_USER -d postgres -h 127.0.0.1 > /backup/backup.sql"]'
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
* * *
## Further Reading

* [Postgres Snapshot Sample Application](https://github.com/replicatedhq/kotsapps/tree/master/postgres-snapshots)For another working example of a KOTS application check the Potgres Snapshot application in GitHub.

* [KOTS Snashot Documentation](https://kots.io/vendor/snapshots/overview/) For more details about snapshots in KOTS, please review the docs for KOTS.

* [Velero Documentation](https://velero.io/docs/)
