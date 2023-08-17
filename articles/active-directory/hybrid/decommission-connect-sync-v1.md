---
title: 'Decommissioning Azure AD Connect V1'
description: This article describes Azure AD Connect V1 decommissioning and how to migrate to V2.
services: active-directory
documentationcenter: ''
author: billmath
manager: amycolannino
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/31/2023
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
---


# Decommission Azure AD Connect V1

The one-year advanced notice of Azure AD Connect V1's retirement was announced in August 2021.  As of August 31, 2022, all V1 versions went out of support and were subject to stop working unexpectedly at any point.

On **October 1, 2023**, Azure AD cloud services will stop accepting connections from Azure AD Connect V1 servers, and identities will no longer synchronize.

If you are still using Azure AD Connect V1 you must take action immediately.

>[!IMPORTANT]
>Azure AD Connect V1 will stop working on October 1st 2023.  You need to migrate to cloud sync or connect sync V2.

##  Migrate to cloud sync
Before moving to Azure AD Connect V2, you should see if cloud sync is right for you instead.  Cloud sync uses a light-weight provisioning agent and is fully configurable through the portal. To choose the best sync tool for your situation, use the [Wizard to evaluate sync options.](https://aka.ms/EvaluateSyncOptions)

Based on your environment and needs, you may qualify for moving to cloud sync.  For a comparison of cloud sync and connect sync, see [Comparison between cloud sync and connect sync](cloud-sync/what-is-cloud-sync.md#comparison-between-azure-ad-connect-and-cloud-sync). To learn more, read [What is cloud sync?](cloud-sync/what-is-cloud-sync.md) and [What is the provisioning agent?](cloud-sync/what-is-provisioning-agent.md)

## Migrating to Azure AD Connect V2
If you aren't yet eligible to move to cloud sync, use this table for more information on migrating to V2.

|Title|Description|
|-----|-----|
|[Information on deprecation](connect/deprecated-azure-ad-connect.md)|Information on Azure AD Connect V1 deprecation|
|[What is Azure AD Connect V2?](connect/whatis-azure-ad-connect-v2.md)|Information on the latest version of Azure AD Connect|
|[Upgrading from a previous version](connect/how-to-upgrade-previous-version.md)|Information on moving from one version of Azure AD Connect to another


## Frequently asked questions



## Next steps

- [What is Azure AD Connect V2?](./connect/whatis-azure-ad-connect-v2.md)
- [Azure AD Cloud Sync](./cloud-sync/what-is-cloud-sync.md)
- [Azure AD Connect version history](./connect/reference-connect-version-history.md)
