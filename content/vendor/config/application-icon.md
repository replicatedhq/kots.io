---
date: 2020-07-28
linktitle: "Custom Application Icon"
title: Customize Application Icon
description: "Selecting the right logo is a big part of making sure that your brand is reflected properly in th Kots Admin Console. Here we will go over some tips for adding a 'proper' logo to your application YAML file."
weight: 20010
---

Selecting the right logo is important to making sure that brands are reflected properly in the Kots Admin Console. Here we will go over some tips for adding a 'proper' logo to an application YAML file.

To add a custom icon for an application, include an `icons` key under the `descriptor` where a src url and an image type will be specified.

```yaml
apiVersion: kots.io/v1beta1
kind: Application
metadata:
  name: my-application
spec:
  descriptor:
    icons:
      - src: "https://kots.io/images/kotsadm-logo-large@2x.png"
        type: "image/png"
```

## Proper logo size
For logos to look best in the Kots Admin Console, use a PNG or JPG that is square, at least 250x250 pixels, and exported at 2x. Logo's will be contained to a bounding box so although a logo with a rectangular shape will work, the logo may be small and hard to see. The next section will show an example.

Here is the full Kotsadm wordmark logo shown on the the login screen. Although the entire logo is visible because it's being contained to the circle, it's a little small and will appear even smaller, about half of this size, in some parts of the admin console.

![Kotsadm logo](/images/login-icon-large.png)

Instead it is recommended to use the smaller lettermark logo as the application logo which will appear larger in the bounding boxes.

![Kotsadm lettermark logo](/images/login-icon-small.png)

When possible it's best to use an icon or lettermark as the application icon rather than the full wordmark logo.