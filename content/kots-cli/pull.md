---
date: 2019-10-09
linktitle: "pull"
title: kots pull
weight: 90110
---

If you’d rather use kubectl or another workflow to deploy to your cluster, you can run `kots pull` to create a directory on your workstation with the Kots application.

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `--downstream` |  strings |  the list of any downstreams to create/update |  
| `--exclude-admin-console` |  bool  |  set to true to exclude the admin console (replicated apps only) |  
| `--exclude-kots-kinds` |  bool  | set to true to exclude rendering kots custom objects to the base directory (default true) |  
| `-h, --help`  |          |  help for pull |  
| `--image-namespace` |  string  |  the namespace/org in the docker registry to push images to (required when --rewrite-images is set) |  
| `--license-file` |  string |   path to a license file to use when download a replicated app |  
| `--local-path` |  string   |   specify a local-path to pull a locally available replicated app (only supported on replicated app types currently) |  
| `-n, --namespace` |  string      |   namespace to render the upstream to in the base (default "default") |  
| `--registry-endpoint` |  string  |   the endpoint of the local docker registry to use when pushing images (required when --rewrite-images is set) |  
| `--repo`  | string  |   repo uri to use when downloading a helm chart |  
| `--rewrite-images` |  bool   |  set to true to force all container images to be rewritten and pushed to a local registry |  
| `--rootdir` |  string  |  root directory that will be used to write the yaml to (default "/path/") |  
| `--set`  | strings  |  values to pass to helm when running helm template |  
| `--shared-password` | string  | shared password to use when deploying the admin console |  
