---
date: 2020-01-03
linktitle: "Helm Airgap Builder"
title: Helm Airgap Builder
weight: 20550
---

> This topic applies to both native Helm and Replicated Helm installations.

For all Replicated Business & Enterprise Plans, Replicated Vendor Portal automatically builds airgap bundles for every promoted release. 
This is generally done by processing the application YAML manifests and exporting all of the images listed in the manifests and bundling them with the manifests. 
In order to ensure that a Helm chart based KOTS application is able to be fully built as an airgapped package, Replicated needs a `helm template` version of the application manifests that include all optional components/images as `enabled`. 
This can be tricky for Helm chart based KOTS applications as they might have [optional charts](/vendor/helm/optional-charts) that are not included by default.

To solve this problem, the KOTS [HelmChart custom resource](/reference/v1beta1/helmchart/) contains a `builder` attribute for vendors to provide values that will `helm template` to the full manifest.

The values passed in `builder` should be the bare minimum [Helm Values](https://helm.sh/docs/chart_template_guide/values_files/) to be able to template out the Helm chart so that it will have the correct image tags. 
Upon promotion to a release channel, on the back-end Airgap Builder will, template the chart with the builder values, render the resulting YAML manifests and export any images referenced to build the airgap package. 

Values in the `builder` attribute only determine the images used within the Helm chart.
These values should not be templates because they are not used within a customer installation and do not change.

To define a resource such as a required namespace, include a hardcoded "default" entry in `builder` and a templated entry in `values`. For more information about defining a templated entry in `values`, see [values](/reference/v1beta1/helmchart/#values) in _HelmChart_.

## Builder Attribute
```yaml
# builder values provide a way to render the chart with all images
# and manifests. this is used in replicated to create airgap packages
builder:
  postgresql:
    enabled: true
```
