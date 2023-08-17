---
title: Azure Firewall preview features
description: Learn about Azure Firewall preview features that are currently publicly available.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 07/05/2023
ms.author: victorh
---

# Azure Firewall preview features

The following Azure Firewall preview features are available publicly for you to deploy and test. Some of the preview features are available on the Azure portal, and some are only visible using a feature flag.

> [!IMPORTANT]
> These features are currently in PREVIEW.
> See the [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) for legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.

## Feature flags

As new features are released to preview, some of them will be behind a feature flag. To enable the functionality in your environment, you must enable the feature flag on your subscription. These features are applied at the subscription level for all firewalls (VNet firewalls and SecureHub firewalls).  

This article will be updated to reflect the features that are currently in preview with instructions to enable them. When the features move to General Availability (GA), they're available to all customers without the need to enable a feature flag. 

## Preview features

The following features are available in preview.

### Explicit proxy (preview)

With the Azure Firewall Explicit proxy set on the outbound path, you can configure a proxy setting on the sending application (such as a web browser) with Azure Firewall configured as the proxy. As a result, traffic from a sending application goes to the firewall's private IP address, and therefore egresses directly from the firewall without using a user defined route (UDR).

For more information, see [Azure Firewall Explicit proxy (preview)](explicit-proxy.md).

### Resource Health (preview)

With the Azure Firewall Resource Health check, you can now diagnose and get support for service problems that affect your Azure Firewall resource. Resource Health allows IT teams to receive proactive notifications on potential health degradations, and recommended mitigation actions per each health event type.  The resource health is also available in a dedicated page in the Azure portal resource page.
Starting in August 2023, this preview will be automatically enabled on all firewalls and no action will be required to enable this functionality.
For more information, see [Resource Health overview](../service-health/resource-health-overview.md).

## Next steps

To learn more about Azure Firewall, see [What is Azure Firewall?](overview.md).