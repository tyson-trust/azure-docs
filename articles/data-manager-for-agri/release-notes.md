---
title: Release notes for Microsoft Azure Data Manager for Agriculture Preview 
description: This article provides release notes for Azure Data Manager for Agriculture Preview releases, improvements, bug fixes, and known issues. 
author: gourdsay 
ms.author: angour 
ms.service: data-manager-for-agri 
ms.topic: conceptual 
ms.date: 06/25/2023 
ms.custom: template-concept 
---

# Release Notes for Azure Data Manager for Agriculture Preview 

Azure Data Manager for Agriculture Preview is updated on an ongoing basis. To stay up to date with the most recent developments, this article provides you with information about:

- The latest releases
- Known issues
- Bug fixes
- Deprecated functionality
- Plans for changes

 We provide information on latest releases, bug fixes, & deprecated functionality for Azure Data Manager for Agriculture Preview monthly.

[!INCLUDE [public-preview-notice.md](includes/public-preview-notice.md)]

## June 2023

### Use your license keys via key vault
Azure Data Manager for Agriculture supports a range of data ingress connectors. These connections require customer keys in a Bring Your Own License (BYOL) model. You can use your license keys safely by storing your secrets in the Azure Key Vault, enabling system identity and providing read access to our Data Manager. Details are available [here](concepts-byol-and-credentials.md).

### Sensor integration as both partner and customer
Now you can start pushing data from your own sensors into Data Manager for Agriculture. It's useful in case your sensor provider doesn't want to take steps to onboard their sensors or if you don't have such support from your sensor provider. Details are available [here](how-to-set-up-sensor-as-customer-and-partner.md).

## May 2023

### Understanding throttling
Azure Data Manager for Agriculture implements API throttling to ensure consistent performance by limiting the number of requests within a specified time frame. Throttling prevents resource overuse and maintains optimal performance and reliability for all customers. Details are available [here](concepts-understanding-throttling.md).

## April 2023

### Audit logs
In Azure Data Manager for Agriculture Preview, you can monitor how and when your resources are accessed, and by whom. You can also debug reasons for failure for data-plane requests. [Audit Logs](how-to-set-up-audit-logs.md) are now available for your use.  

### Private links
You can connect to Azure Data Manager for Agriculture service from your virtual network via a private endpoint. You can then limit access to your Azure Data Manager for Agriculture Preview instance over these private IP addresses. [Private Links](how-to-set-up-private-links.md) are now available for your use.  

### BYOL for satellite imagery
To support scalable ingestion of geometry-clipped imagery, we've partnered with Sentinel Hub by Sinergise to provide a seamless bring your own license (BYOL) experience. Read more about our satellite connector [here](concepts-ingest-satellite-imagery.md). 

## March 2023

### Key Announcement: Preview Release
Azure Data Manager for Agriculture is now available in preview. See our blog post [here](https://azure.microsoft.com/blog/announcing-microsoft-azure-data-manager-for-agriculture-accelerating-innovation-across-the-agriculture-value-chain/).

## Next steps
* See the Hierarchy Model and learn how to create and organize your agriculture data  [here](./concepts-hierarchy-model.md).
* Understand our APIs [here](/rest/api/data-manager-for-agri).