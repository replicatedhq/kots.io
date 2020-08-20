---
date: 2019-12-06
linktitle: "Template Function Contexts"
title: Template Function Contexts
weight: 90020
---

[Template functions](/vendor/packaging/template-functions/) are grouped into different contexts, depending on the phase of the application lifecycle when the function is available and the context data that is provided. Static, Config, and License contexts are available everywhere, while kURL context functions are not available on the config page.

## Static Context
Template functions in the static context can be used in any YAML, at any time. The static context also includes the [Masterminds Sprig](http://masterminds.github.io/sprig/) function library.

[For a list of all Replicated functions available in the static context, click here.](/reference/template-functions/static-context)

## Config Context
Template functions in the config context are available when rendering an application that has a config screen. At execution time, template functions in this context also can use the static context functions.

[For a list of all Replicated functions available in the config context, click here.](/reference/template-functions/config-context)

## License Context
Template functions in the license context have access to the license data.

[For a list of all Replicated functions available in the license context, click here.](/reference/template-functions/license-context)

## kURL Context
Template functions in the kURL context have access to kURL installation information.

[For a list of all Replicated functions available in the kURL context, click here.](/reference/template-functions/kurl-context)