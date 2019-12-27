---
date: 2019-12-27
linktitle: "Optional Value Keys"
title: Optional Value Keys
weight: 20540
---

Some advanced cases involve writing values to a values file only if there's a value to apply. Helm charts like postgres will not treat an empty value(`""`) the same as the key being missing from the chart.
