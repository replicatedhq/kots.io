---
date: 2021-09-10
linktitle: "customer download-license"
title: "customer download-license"
weight: 90117
draft: false
---

Print the license YAML for a customer

### Usage
```bash
replicated customer download-license
```

| Flag                 | Type | Description |
|:----------------------|------|-------------|
| `--customer` | string | The Customer Name or ID |
| `-h, --help`   |  |          help for the command |
| `--app string` | |   The app slug or app id to use in all calls (default uses `$REPLICATED_APP` env variable) |
| `--token string` | |  The API token to use to access your app in the Vendor API (default uses `$REPLICATED_API_TOKEN` env variable) |

### Examples
```bash
replicated customer download-license --customer cli-customer
apiVersion: kots.io/v1beta1
kind: License
metadata:
  name: cli-customer
spec:
  appSlug: laverya-cli-app
  channelID: 1xy9tHhAwHyoc0HEybppxPpbPn6
  channelName: Unstable
  customerName: cli-customer
  endpoint: https://staging.replicated.app
  entitlements:
    expires_at:
      description: License Expiration
      title: Expiration
      value: ""
      valueType: String
  licenseID: 1xyCOBjfm5FVkDoCYOAyvVnELfZ
  licenseSequence: 1
  licenseType: dev
  signature: (ommitted)
```