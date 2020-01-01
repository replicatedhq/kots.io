---
date: 2019-12-27
linktitle: "HelmChart"
title: HelmChart
description: "HelmChart defines an instance of a chart"
weight: 30050
---

HelmChart is the vendor-supplied instruction for how KOTS should process a Helm chart to create a single deployable instance of the chart (if multiple deployments of the chart are required, multiple HelmChart CRs are required).


```yaml
apiVersion: kots.io/v1beta1
kind: HelmChart
metadata:
  name: samplechart
spec:
  # chart identifies a matching chart from a .tgz
  chart:
    name: samplechart
    chartVersion: 3.1.7

  exclude: "repl{{ ConfigOptionEquals `include_chart` `include_chart_no`}}"

  # values are used in the customer environment, as a pre-render step
  # these values will be supplied to helm template
  values:
    postgresql:
      enabled: repl{{ ConfigOptionEquals `postgres_type` `embedded_postgres`}}

  optionalValues:
    - when: "repl{{ ConfigOptionEquals `postgres_type` `external_postgres`}}"
      values:
        postgresql:
          postgresqlDatabase: "repl{{ if ConfigOptionEquals `postgres_type` `external_postgres`}}repl{{ ConfigOption `external_postgres_database`}}repl{{ end}}"
          postgresqlUsername: "repl{{ if ConfigOptionEquals `postgres_type` `external_postgres`}}repl{{ ConfigOption `external_postgres_username`}}repl{{ end}}"
          postgresqlHost: "repl{{ if ConfigOptionEquals `postgres_type` `external_postgres`}}repl{{ ConfigOption `external_postgres_host`}}repl{{ end}}"
          postgresqlPassword: "repl{{ if ConfigOptionEquals `postgres_type` `external_postgres`}}repl{{ ConfigOption `external_postgres_password`}}repl{{ end}}"
          postgresqlPort: "repl{{ if ConfigOptionEquals `postgres_type` `external_postgres`}}repl{{ ConfigOption `external_postgres_port`}}repl{{ end}}"

  # builder values provide a way to render the chart with all images
  # and manifests. this is used in replicated to create airgap packages
  builder:
    postgresql:
      enabled: true
```

## chart

The `chart` key allows for a mapping between the data in this definition and the chart archive itself. More than one `kind: HelmChart` can reference a single chart archive, if different settings are needed.

### `chart.name`
The name of the chart. This must match the `name` field from a Chart.yaml in a .tgz chart archive that's also included in the release.

### `chart.chartVersion`
The version of the chart. This must match the `version` field from a Chart.yaml in a .tgz chart archive that's also included in the release.

## values

The `values` key allows for values to be changed in the chart, or for a mapping between the [Replicated Config screen](/vendor/config/config-screen/) and the values. This makes it possible to use the config screen UI to control the Helm values.yaml.

The keys below `values` should map exactly to the keys in your `values.yaml`. Only include the keys that you wish to change, these will be merged with the `values.yaml` in the chart archive.

## optionalValues

The `optionalValues` array is provided for advanced use cases to make value overrides completely optional. Not all charts treat `""` and missing as the same value. If it's required to only optionally have a value set, and an empty string does not provide the same functionality as "not set", then use the values here.

### `optionalValues[].when`

The `when` field in `optionalValues` provides a string-based, [template-function-evaluated](/reference/template-functions/contexts/) method to defer evalulation of the conditional to render time in the customer environment.

## builder

The `builder` key allows for defaults that will be set when Replicated is creating airgap packages. This is an opportunity to ensure that all YAML and images are rendered "on" so they are included.
