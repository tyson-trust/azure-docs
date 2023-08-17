---
title: 'On-demand provisioning using cloud sync'
description: This article describes how to use on-demand provisioning with Azure AD Connect cloud sync.
services: active-directory
documentationcenter: ''
author: billmath
manager: amycolannino
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/11/2023
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
---

# On-demand provisioning using cloud sync

You can use cloud sync to test configuration changes by applying these changes to a single user. This on-demand provisioning helps you validate and verify that the changes made to the configuration were applied properly and are being correctly synchronized to Azure AD.  This feature is only available in cloud sync and not Azure AD Connect.

## Steps to use on-demand provisioning
To use on-demand provisioning, follow these steps:

 1.  In the Azure portal, select **Azure Active Directory**.
 2.  On the left, select **Azure AD Connect**.
 3.  On the left, select **Cloud sync**.
 4. Under **Configuration**, select your configuration.
 5. On the left, select **Provision on demand**.
 6. Enter the distinguished name of a user and select the **Provision** button.
 7. Once the process completes, a success screen appears with four green check marks. Any errors appear on the left side of the screen.

## Next steps

For more information, see [on-demand provisioning in cloud sync](cloud-sync/how-to-on-demand-provision.md)