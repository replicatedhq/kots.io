---
date: "2020-02-20T00:00:00Z"
lastmod: "2020-02-20T00:00:00Z"
title: "Integrating Persistent Datastores"
weight: "1006"
---

In this guide, we'll review best practices for integrating persistent stores like databases, queues, and caches. We'll explore ways to give your end user the option to either embed an instance with the application, or connect your appplication to an external instance that they will manage. We'll use a PostgreSQL database as an example, configuring an example app to connect. The latter part of this guide starts to explore advanced topics like custom preflight checks, workload coordination, credential management, and refactoring your application's user-facing configuration.

It is split into 5 sections:

- [The Example Application](#the-example-application)
- [User-Facing Configuration](#user-facing-configuration)
- [Embedding a Database](#embedding-a-database)
- [Connecting to an External Database](#connecting-to-an-external-database)
- [Validating User-supplied Configuration with Preflight Checks](#connecting-to-an-external-database)
- [Embedded: Managing Credentials and Integrating an InitContainer](#embedded-managing-credentials)

### Prerequisites

You should have completed one of the [Getting Started Guides](../#getting-started), this guide assumes you have a running instance of `kotsadm` to iterate against in either an existing or embedded cluster, and a local git checkout of your KOTS app manifests.

### Accompanying Code Examples

A full example of the code for this guide can be found in the [kotsapps repository](https://github.com/replicatedhq/kotsapps/tree/master/persistent-datastores).

* * *


### The Example Application

For demonstration purposes, we'll use a simple app that connects to a postgres database via the `psql` CLI. Once you've finished this guide, you should feel confident replacing it with any Kubernetes workload(s) that need to connect to a database. The deployment we'll use can be seen below:

```yaml
# pg-consumer.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: pg-consumer 
spec:
  selector:
    matchLabels:
      app: pg-consumer
  template:
    metadata:
      labels: 
        app: pg-consumer
    spec:
      containers:
        - name: pg-consumer
          image: postgres:10
          # connect to the database every 10 seconds
          command:
            - /bin/sh
            - -c
            - |
              while :; do 
                 psql --host ${DB_HOST} \
                      --port ${DB_PORT} \ 
                      --user ${DB_USER} \
                      --password ${DB_PASSWORD} \
                      --dbname ${DB_NAME} \
                      --command 'SELECT NOW()'
                 sleep 10
              done
          # hard coded for now, we'll wire these up later
          env:
            - name: DB_HOST
              value: postgres
            - name: DB_PORT
              value: 5432
            - name: DB_USER
              value: postgres
            - name: DB_PASSWORD
              value: postgres
            - name: DB_NAME
              value: postgres
```

This app simply connects to the database every 10 seconds and rights the server timestamp to it's container stdout. Even though `psql` supports [default environment variables](https://www.postgresql.org/docs/current/libpq-envars.html) for host, username, etc that can be read transparently, we're intentionally using these generic `DB_` variables for clarity. Later, you can change these environment variable names to whatever format your application consumes. 

For now we'll hard code the variable values, in the next sections we'll wire these up to the user-provided configuration.


### Deploying the example application
 
 Once you've added this deployment to you application's `manifests` directory, create a release by pushing a commit to your [starter repo copy](../quickstart#automating-your-workflow) or by running `make release` locally. Then head to your kotsadm instance and click "Check for Updates" on the Version History tab to pull the new release:
 
![View Update](/images/guides/kots/view-update.png)

After clicking "Deploy", you should be able to review the logs and see `deployment.apps/pg-consumer created` in `applyStdout`:


![Deployed PG Consumer](/images/guides/kots/pg-consumer-deployed.png)


Once it's deployed, you can run `kubectl get pods` to inspect the cluster. We should expect the Pod to be crashlooping at this point, since there's no database to connect to just yet:

```text
$ kubectl get pod
NAME                               READY   STATUS             RESTARTS   AGE
kotsadm-5bbf54df86-p7kqg           1/1     Running            0          12m
kotsadm-api-cbccb97ff-b6qxp        1/1     Running            2          12m
kotsadm-minio-0                    1/1     Running            0          12m
kotsadm-operator-84477b5c4-tplcp   1/1     Running            0          12m
kotsadm-postgres-0                 1/1     Running            0          12m
pg-consumer-75f49bfb69-mljr6       0/1     CrashLoopBackOff   1          10s
```

Checking the logs, we should see a connect error. 

```text
$ kubectl logs -l app=pg-consumer
psql: could not translate host name "postgres" to address: Name or service not known
```

If the `kubectl logs` command hangs, you can try using the `--previous` flag to fetch the logs of the most recent crash:

 
```text
$ kubectl logs -l app=pg-consumer --previous
psql: could not translate host name "postgres" to address: Name or service not known
```

Now that our test app is deployed, we'll walk through presenting options to the end user for connecting a postgres instance to this app.

* * *

## User-Facing Configuration

The core of this guide will be around how give your end users the option to either

1. Bring their own PostgreSQL instance for your app to connect to
1. Use an "embedded" database bundled in with the application

The first step here is to present that option to the user, then we'll walk through implementing each scenario in KOTS. The `kots.io/v1beta1` `Config` resource controls what configuration options are presented to the end user. If you followed one of the Getting Started guides, you probably have a `config.yaml` in your manifests that looks something like this:

```yaml
apiVersion: kots.io/v1beta1
kind: Config
metadata:
  name: config-sample
spec:
  groups:
    - name: example_settings
      title: My Example Config
      description: Configuration to serve as an example for creating your own. See [https://kots.io/reference/v1beta1/config/](https://kots.io/reference/v1beta1/config/) for configuration docs. In this case, we provide example fields for configuring an Ingress object.
      items:
        - name: use_ingress
          title: Use Ingress?
          help_text: An example field to toggle inclusion of an Ingress Object
          type: bool
          default: "0"
        - name: ingress_hostname
          title: Ingress Hostname
          help_text: If desired, enter the hostname for ingress to this application. You can enter the IP of this instance, or a DNS hostname.
          type: text
          when: repl{{ ConfigOptionEquals "use_ingress" "1" }}
```

To add a database section, we'll modify it to include some database settings. In this case we'll remove the Ingress toggle that is included as an example, although you might also choose to leave this in. None of these database settings will have any effect yet, but we'll still be able to preview what the end user will see. Modify your yaml to include this database section:

```yaml
apiVersion: kots.io/v1beta1
kind: Config
metadata:
  name: config-sample
spec:
  groups:
    - name: database
      title: Database
      items:
        - name: postgres_type
          help_text: Would you like to use an embedded postgres instance, or connect to an external instance that you manage?
          type: select_one
          title: Postgres
          default: embedded_postgres
          items:
            - name: embedded_postgres
              title: Embedded Postgres
            - name: external_postgres_inline
              title: External Postgres
        - name: embedded_postgres_password
          hidden: true
          type: password
          value: "{{repl RandomString 32}}"          
```

This creates a toggle to allow the user to choose between an embedded or external Postgres instance, and a `hidden` field to generate a unique password for the embedded instance.

As mentioned in the introduction, a full example of the code for this guide can be found in the [kotsapps repository](https://github.com/replicatedhq/kotsapps/tree/master/persistent-datastores).


### Validating Config Changes

Even thought the options aren't wired, let's create a new release to validate the configuration screen was modified. Create a release by pushing a commit to your [starter repo copy](../quickstart#automating-your-workflow) or by running `make release` locally. Then head to your kotsadm instance and click "Check for Updates" on the Version History tab to pull the new release:
 
![View Update](/images/guides/kots/view-update.png)

Once the update is deployed, we can head over to the Config tab and review our new toggle. You might also notice that we've removed the Ingress settings to simplify things for this guide:

![Database Config](/images/guides/kots/database-config.png)

Now that we have the configuration screen started, we can proceed to implement the "Embedded Postgres" option.

* * *

## Embedding a Database

To implement the embedded Database option, we'll add a Kubernetes [Statefulset](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/), and use the [KOTS annotations for optional resources](/vendor/packaging/optional-resources/) to control when it will be included in the application.

### Adding the Secret and StatefulSet

Firse, we'll create a secret to store the root password for our embedded postgres instance. 

```yaml
# postgres-secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: postgres
data:
  DB_PASSWORD: '{{repl ConfigOption "embedded_postgres_password" | Base64Encode }}'
```

Next, create a new YAML file in your `manifests` directory with the following contents. Note the use of `kots.io/when` to only conditionally include this based on end-user inputs.

```yaml
# postgres-statefulset.yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgres
  labels:
    app: pg-provider
  annotations:
    kots.io/when: '{{repl ConfigOptionEquals "postgres_type" "embedded_postgres" }}'
spec:
  replicas: 1
  selector:
    matchLabels:
      app: pg-provider
  serviceName: postgres
  template:
    metadata:
      labels:
        app: pg-provider
    spec:
      containers:
      - env:
        - name: PGDATA
          value: /var/lib/postgresql/data/pgdata
        # create a db called "postgres"
        - name: POSTGRES_DB
          value: postgres
        # create admin user with name "postgres"
        - name: POSTGRES_USER
          value: postgres
        # use admin password from secret
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              key: DB_PASSWORD
              name: postgres-secret
        image: postgres:10
        name: postgres
        volumeMounts:
        - mountPath: /var/lib/postgresql/data
          name: pgdata
      volumes:
      - name: pgdata
        persistentVolumeClaim:
          claimName: pgdata
  volumeClaimTemplates:
  - metadata:
      name: pgdata
    spec:
      accessModes:
      - ReadWriteOnce
      resources:
        requests:
          storage: 1Gi
```

Finally, lets add a Service object so we can route traffic to our postgres instance, again using `kots.io/when` to conditionally include this resource.


```yaml
apiVersion: v1
kind: Service
metadata:
  name: postgres
  labels:
    app: pg-provider
  annotations:
    kots.io/when: '{{repl ConfigOptionEquals "postgres_type" "embedded_postgres" }}'
spec:
  ports:
  - port: 5432
  selector:
    app: pg-provider
  type: ClusterIP
```

### Validating the embedded Database

Once you've added the these resources, you can push a new release and update in kotsadm. You should see the following in the deployment logs:

![Embedded PG Deployed](/images/guides/kots/embedded-pg-deployed.png)

We should now see an instance of postgres running in our namespace as well. The consumer may still be crashlooping, but we can see the error is different now:

```text
$ kubectl logs -l app=pg-consumer 
psql: FATAL:  password authentication failed for user "postgres"
```

This is because we still need to deliver the generated password to our workload pod. In `pg-consumer.yaml`, we'll remove this section

```yaml
            - name: DB_PASSWORD
              value: postgres
```

and replace it with

```yaml
            - name: DB_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: postgres
                  key: DB_PASSWORD
```

The full Deployment should now look like:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: pg-consumer
spec:
  selector:
    matchLabels:
      app: pg-consumer
  template:
    metadata:
      labels:
        app: pg-consumer
    spec:
      containers:
        - name: pg-consumer
          image: 'postgres:10'
          # connect to the database every 10 seconds
          command:
            - /bin/sh
            - -c
            - |
              while :; do 
                 psql --host ${DB_HOST} \
                      --port ${DB_PORT} \ 
                      --user ${DB_USER} \
                      --password ${DB_PASSWORD} \
                      --dbname ${DB_NAME} \
                      --command 'SELECT NOW()'
                 sleep 10
              done
          # hard coded for now, we'll wire these up later            
          env:
            - name: DB_HOST
              value: postgres
            - name: DB_PORT
              value: "5432"
            - name: DB_USER
              value: postgres
            - name: DB_NAME
              value: postgres
            - name: DB_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: postgres
                  key: DB_PASSWORD
```

From here, make another release and deploy it. You should see the consumer pod is now able to connect to the database:


```text
$ kubectl get pod
NAME                               READY   STATUS    RESTARTS   AGE
kotsadm-5bbf54df86-p7kqg           1/1     Running   0          144m
kotsadm-api-cbccb97ff-b6qxp        1/1     Running   2          144m
kotsadm-minio-0                    1/1     Running   0          144m
kotsadm-operator-84477b5c4-tplcp   1/1     Running   0          144m
kotsadm-postgres-0                 1/1     Running   0          144m
pg-consumer-77b868d7d8-xdn9v       1/1     Running   0          20s
postgres-0                         1/1     Running   0          6m22s
```

Checking the logs, we can connect now!

```text
$ kubectl logs -l app=pg-consumer
              now              
-------------------------------
 2020-04-12 17:11:45.019293+00
(1 row)

              now              
-------------------------------
 2020-04-12 17:11:55.072041+00
(1 row)
```

Now that we've configured our application to read from an embedded postgres instance, we'll switch to allowing the end user to provide their own database connection parameters.

* * *

## Connecting to an external Database

* * *

## External: Validating User-supplied Configuration with Preflight Checks

* * *

## Embedded: Managing Credentials, Using an InitContainer to wait for the database to be ready


* * *

## Eliminating Code duplication with Hidden Config Fields

- lots of duplication

* * *

## Next Steps
