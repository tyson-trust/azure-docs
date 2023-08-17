---
title: Azure Maps QPS rate limits
description: Azure Maps limitation on the number of Queries Per Second.
author: eriklindeman
ms.author: eriklind
ms.date: 10/15/2021
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
ms.custom: mode-other
---

# Azure Maps QPS rate limits

Azure Maps doesn't have any maximum daily limits on the number of requests that can be made, however there are limits to the maximum number of queries per second (QPS).

The following list shows the QPS usage limits for each Azure Maps service by Pricing Tier.

| Azure Maps service | QPS Limit: Gen 2 Pricing Tier | QPS Limit: Gen 1 S1 Pricing Tier | QPS Limit: Gen 1 S0 Pricing Tier |
|  ----------------- |  :--------------------------: | :------------------------------: | :------------------------: |
| Copyright service | 10 | 10 | 10 |
| Creator - Alias, TilesetDetails | 10 | Not Available | Not Available |
| Creator - Conversion, Dataset, Feature State, WFS | 50 | Not Available | Not Available |
| Data service | 50 | 50 |  Not Available  |
| Geolocation service | 50 | 50 | 50 |
| Render service - Traffic tiles and Static maps | 50 | 50 | 50 |
| Render service - Road tiles | 500 | 500 | 50 |
| Render service - Satellite tiles | 250 | 250 | Not Available |
| Render service - Weather tiles | 100 | 100 | 50 |
| Route service - Batch | 10 | 10 | Not Available |
| Route service - Non-Batch | 50 | 50 | 50 |
| Search service - Batch | 10 | 10 | Not Available |
| Search service - Non-Batch | 500 | 500 | 50 |
| Search service - Non-Batch Reverse | 250 | 250 | 50 |
| Spatial service | 50 | 50 |  Not Available  |
| Timezone service | 50 | 50 | 50 |
| Traffic service | 50 | 50 | 50 |
| Weather service | 50 | 50 | 50 |

When QPS limits are reached, an HTTP 429 error is returned. If you're using the Gen 2 or Gen 1 S1 pricing tiers, you can create an Azure Maps *Technical* Support Request in the [Azure portal] to increase a specific QPS limit if needed. QPS limits for the Gen 1 S0 pricing tier can't be increased.

[Azure portal]: https://portal.azure.com/
