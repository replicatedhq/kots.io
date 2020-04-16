---
date: "2020-02-20T00:00:00Z"
lastmod: "2020-02-20T00:00:00Z"
title: "Preparing for Production Deploys"
weight: "1009"
draft: false
---

This guide is intended as a lightweight checklist to ensure you have explored all the features of KOTS and integrated the ones that will help ensure your end users are most likely to be successful deploying and running your KOTS application. This guide is intended to grow as more features are integrated and documented in KOTS.

It is split into three parts:

- [Implementation Steps](#implementation-steps)
- [Further Reading](#further-reading)
- [Appendix A: A Questionnaire for your End Users](#appendix-a-a-questionnaire-for-your-end-users)


* * *

## Implementation Steps

### Basic Whitelabeling

All applications should be whitelabeled with [a title and an icon](/reference/v1beta1/application/#title). 

### Status Informers

Applications should integrate [StatusInformers](/vendor/config/application-status) to ensure information about app readiness is presented to the user during initial install and start-up.

### Allowing Rollbacks

If an application is guaranteed not to introduce backwards-incompatible versions (e.g. via database migrations), the [allowRollback](/reference/v1beta1/application/#allowrollback) flag can allow end users to easily roll back to previous versions (this will not revert any state, just the YAML manifests that are applied to the cluster).


### Adding Preflight Checks

Adding preflight checks to validate an end user's environment is a great way to streamline initial installations and greatly reduce the number of support escalations when installing an application. There are a number of basic examples for checking CPU, memory, and disk capacity under the [Node Resources Analyzer](https://troubleshoot.sh/reference/analyzers/node-resources/).


<!-- coming soon, wait for guide
### Embedded Cluster

When deploying with the embedded cluster...

-->

### Managing Stateful Services

In the [persistent datastores guide](/vendor/guides/persistent-datastores), we review best practices for integrating persistent stores like databases, queues, and caches. Explore ways to give an end user the option to either embed an instance alongside the application, or connect an appplication to an external instance that they will manage. 

If you expect to also install stateful services into existing clusters, you'll likely want to expose [preflight analyzers that check for the existence of a storage class](https://troubleshoot.sh/reference/analyzers/storage-class/).


### Namespaces

It is *strongly* advised that applications be architected to deploy a single application into a single namespace when possible. This will give the most flexibility when deploying to end user environments. Don't specify a namespace in your YAML resources, or try to make this user-configurable using the `kots.io` `Config` object, just leave namespace blank. Letting the end user manage namespaces will be the easiest way to reduce friction. The ability to manage namespaces in kotsadm will be added in an upcoming KOTS release, so if you intend to  deploy multiple apps using a single Admin Console, it is be reasonable to architect your app assuming the end user will manage the Namespaces that each component runs in.

### Helm

Helm charts are supported by KOTS but not required. If for applications that are already packaged using helm, then the helm support in KOTS can help get an app packaged faster. If an application does not presently use helm, there's no requirement to use helm, as the KOTS built-in templating includes much of the same functionality, and the Admin Console includes a deep [kustomize.io integration](/vendor/kotsadm/updating/patching-with-kustomize) to greatly reduce the amount of templating required by app maintainers in the first place.

### Operators
Operators are good for specific use cases, we've written in-depth about them in our [Operators Blog Post](https://blog.replicated.com/operators-in-kots/). In general we recommend thinking deeply about the problem space an application solves before going down the operator path. They're really cool and powerful, but take a lot of time to build and maintain


<!-- coming soon, wait for guide
### Licensing and Entitlements
  - how we think about it 
  - basic enforcement
  - advanced enforcement via API

Basic license enforcement comes baked in for free -- expiration 

Our recommendation is always to enforce licensing with a strong contract, but if you have strict licensing needs, you can always implement [Runtime License Validation](/vendor/guides/runtime-license-validation) in your application. This involves modifying your application code to query an API exposed by KOTS to consume the license payload, and validating the signature in the hot code paths in your application's core business logic.
-->

### Bundling and Analyzing Logs with Support bundle

A robust support bundle is essential to minimizing back-and-forth when things go wrong. At a very minimum every app's support bundle should contain logs for an application's core pods. Usually this will be done with label selectors. To get the labels for an application, either inspect the YAML, or run

```shell
kubectl get pods --show-labels
```

against a running instance to see what labels are used. Once the labels are discovered, a [logs collector](https://troubleshoot.sh/reference/collectors/pod-logs/) can be used to include logs from these pods in a bundle. Depending on the complexity of an app's labeling schema, you may need a few different declarations of the `logs` collector.

As common issues are encountered in the field, it will make sense to add not only collectors but also analyzers to an apps's support stack. For example, when an error in a log file is discovered that should be surfaced to an end user in the future, a simple [Text Analyzer](https://troubleshoot.sh/reference/analyzers/regex/) can detect specific log line in the future and inform an end user of remediation steps.

<!-- coming soon
For a full breakdown check out our [Support Bundle Guide](/vendor/guides/support-bundle)
-->

### Adding Prometheus Graphs

If an application exposes Prometheus metrics, we recommend integrating [Custom Graphs](/vendor/config/dashboard-graphs) to expose deep application metrics to end users.


### Building a Collaborative Workflow

We recommend using a git-based workflow, as presented in the [KOTS starter Repo](https://github.com/replicatedhq/replicated-starter-kots). This will allow teams to map git branches to channels in the Vendor Portal, and allow multiple team members to seamlessly collaborate across features and releases.

###### Tagging Releases for Production

In addition to the starter `Makefile` included in the [KOTS starter Repo](https://github.com/replicatedhq/replicated-starter-kots), Replicated provides an [Advanced Makefile](https://github.com/replicatedhq/replicated-starter-kots/tree/master/Makefile.advanced) that builds on the basic one. It adds logic for making production releases using git tags. The basic outline is:

- On pushes to the `master` branch, create a release on unstable with the name `Unstable-${SHA}`
- On pushing a git tag, create a release on the beta branch, using the name `Beta-${TAG}` for the release version.
- Our recommendation is that these tags be tested, and then the release be manually promoted to the `Stable` channel using [vendor.replicated.com](https://vendor.replicated.com). The goal in using manual promotion to restrict who can cause new versions to go out to users via RBAC roles in the Vendor Portal.

The advandced makefile also includes glue code for utility tasks such as computing new [SemVer](https://semver.org) tags and pushing them.

* * *

## Further Reading

- [EnterpriseReady](https://enterpriseready.io) is a great guide for understanding all the dimensions of product that contribute to making your application "enterprise ready".
<!-- coming soon 
- [OnPrem.org](https://onprem.org) 
-->
- [Appendix A: A Questionnaire for your End Users](#appendix-a-a-questionnaire-for-your-end-users)

* * *

## Appendix A: A Questionnaire for your End Users

When packaging an application, it can useful to get a sense of your environments you'll being deploying into. We here provide a short questionnaire that you can copy and customize for distributing to your end users. You can copy this and replace $APP with your application name

* * *

### $APP Deployment Questionnaire

#### Your Infrastructure

This section includes questions about your infrastructure and how you deploy software, both internally written and Commercial Off The Shelf (COTS) applications. If it’s more convenient, limit answers to the scope of the target infrastructure for deploying $APP.

- Do you use any IaaS like AWS, GCP, or Azure? 

- If you deploy to a physical datacenter, do you use a Hypervisor like VSphere? 

- Do you ever install on bare metal?


- Do you have any restrictions on what operating systems are used?

- Does the target infrastructure have a direct outbound internet connection? Can it connect out via a Proxy?

- If the environment has no outbound network, do machines in a DMZ have direct network access to the airgapped infrastructure, or do release artifacts need to be copied to physical media for installation?

- If there is an issue causing downtime in the on-prem application, would you be willing to give the $APP team direct SSH access to the instance(s)?

#### Deployment

- This section covers your development and deployment processes.

- Do you require applications be deployed by a configuration management framework like Chef, Ansible, or Puppet?

- Do you run any container-based workloads today?

- If you run container workloads, do you run any kind of orchestration like Kubernetes, Mesos, or Docker Swarm?

- If you run container workloads, what tools do you use to host and serve container images?

- If you run container workloads, what tools do you use to scan and secure container images?

- If you are deploying $APP to your existing Kubernetes cluster, can your cluster nodes pull images from the public internet, or do you require images to be stored in an internal registry?

#### Change Management

- How do you test new releases of COTS software? Do you have a UAT or Staging environment? Are there other change management requirements?

- How often do you like to receive planned (non-critical) software updates? Quarterly? Monthly? As often as possible?

- For critical updates, what is your target deployment time for new patches? Do you have a requirement for how quickly patches are made available after a vulnerability is announced?

- Do you drive production deploys automatically from version control (“gitops”)?


#### Application Usage

This section covers your product requirements from a usage and policy point of view.

- For applications that expose a web UI, how will you be connecting to the instance? As much as possible, include details about your workstation, any tunneling/VPN/proxy infrastructure, and what browsers you intend to use.

- Do you require a disaster recovery strategy for deployed applications? If so, where are backups stored today? (SFTP? NAS? S3-compliant object store? Something else?)

- Do you require deployed COTS applications to support logins with an internal identity provider like OpenLDAP, Windows AD or SAML?

- Do you require an audit log of all user activity performed in $APP? What are your needs around exporting / aggregating audit log data?

- Do you anticipate the need to scale the capacity of $APP up and down during its lifetime?

- What are your requirements around log aggregation? What downstream systems do you need system logs to be piped to?
