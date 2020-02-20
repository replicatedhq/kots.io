---
date: "2018-01-30T04:02:20Z"
title: "Automating your workflow"
description: "Learn best practices around managing your releases in version control to enable collaboration and automation."
weight: "11004"
categories: [ "Kubernetes Guide" ]
index: "guides/kubernetes"
type: "chapter"
gradient: "kubernetes"
icon: "replicatedKubernetes"
---

Now that you've made a release using the vendor.replicated.com UI, its time to check your yaml into source control and start collaborating with your team. We'll use the
[KOTS Starter repository](https://github.com/replicatedhq/replicated-starter-kots/) as a starting point for this.


Replicated Kubernetes Starter
==================

Example project showcasing how power users can leverage the Replicated CLI Tools to manage kots YAMLs using a git repository.

### Get started

This repo is a [GitHub Template Repository](https://help.github.com/en/articles/creating-a-repository-from-a-template). You can create a private copy by using the "Use this Template" link in the repo:

![Template Repo](https://help.github.com/assets/images/help/repository/use-this-template-button.png)

You should use the template to create a new **private** repo in your org, for example `mycompany/kots-app` or `mycompany/replicated-starter-kots`.

Once you've created a repository from the template, you'll want to `git clone` your new repo and `cd` into it locally.

#### Configure environment

You'll need to set up two environment variables to interact with vendor.replicated.com:

```shell
export REPLICATED_APP=...
export REPLICATED_API_TOKEN=...
```

`REPLICATED_APP` should be set to the app slug from the Settings page:

![Replicated App](https://github.com/replicatedhq/replicated-starter-kots/raw/master/doc/REPLICATED_APP.png)



Next, create an API token from the [Teams and Tokens](https://vendor.replicated.com/team/tokens) page:

![Replicated App](https://github.com/replicatedhq/replicated-starter-kots/raw/master/doc/REPLICATED_API_TOKEN.png)



Ensure the token has "Write" access or you'll be unable create new releases. Once you have the values,
set them in your environment.

```shell
export REPLICATED_APP=...
export REPLICATED_API_TOKEN=...
```

You can ensure this is working with

```shell
make list-releases
```

#### Iterating on your release

Once you've made changes to `replicated.yaml`, you can push a new release to a channel with

```shell
make release
```

By default the `Unstable` channel will be used. You can override this with `channel`:

```shell
make release channel=Beta
```

### Integrating with CI

This repo contains a [GitHub Actions](https://help.github.com/en/github/automating-your-workflow-with-github-actions/about-github-actions) workflow for ci at [./.github/workflows/main.yml](./.github/workflows/main.yml). You'll need to [configure secrets](https://help.github.com/en/github/automating-your-workflow-with-github-actions/virtual-environments-for-github-actions#creating-and-using-secrets-encrypted-variables) for `REPLICATED_APP` and `REPLICATED_API_TOKEN`.

### Tools reference

- [replicated vendor cli](https://github.com/replicatedhq/replicated)

### License

MIT