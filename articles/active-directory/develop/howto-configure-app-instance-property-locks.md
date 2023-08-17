---
title: "How to configure app instance property lock in your applications"
description: How to increase app security by configuring property modification locks for sensitive properties of the application.
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/03/2022
author: henrymbuguakiarie
ms.author: henrymbugua
ms.reviewer: madansr7
# Customer intent: As an application developer, I want to learn how to protect properties of my application instance of being modified.
---
# How to configure app instance property lock for your applications (Preview)

Application instance lock is a feature in Azure Active Directory (Azure AD) that allows sensitive properties of a multi-tenant application object to be locked for modification after the application is provisioned in another tenant. 
This feature provides application developers with the ability to lock certain properties if the application doesn't support scenarios that require configuring those properties.  


## What are sensitive properties?

The following property usage scenarios are considered as sensitive:

- Credentials (`keyCredentials`, `passwordCredentials`) where usage type is `Sign`. This is a scenario where your application supports a SAML flow.
- Credentials (`keyCredentials`, `passwordCredentials`) where usage type is `Verify`. In this scenario, your application supports an OIDC client credentials flow.
- `TokenEncryptionKeyId` which specifies the keyId of a public key from the keyCredentials collection. When configured, Azure AD encrypts all the tokens it emits by using the key to which this property points. The application code that receives the encrypted token must use the matching private key to decrypt the token before it can be used for the signed-in user.

## Configure an app instance lock

[!INCLUDE [portal updates](~/articles/active-directory/includes/portal-update.md)]

To configure an app instance lock using the Azure portal:

1. Sign in to the <a href="https://portal.azure.com/" target="_blank">Azure portal</a>.
1. If you have access to multiple tenants, use the **Directories + subscriptions** filter :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in the top menu to switch to the tenant that contains the app registration you want to configure. 
1. Search for and select **Azure Active Directory**.
1. Under **Manage**, select **App registrations**, and then select the application you want to configure.
1. Select **Authentication**, and then select **Configure** under the *App instance property lock* section.

   :::image type="content" source="media/howto-configure-app-instance-property-locks/app-instance-lock-configure-overview.png" alt-text="Screenshot of an app registration's app instance lock in the Azure portal.":::

2. In the **App instance property lock** pane, enter the settings for the lock. The table following the image describes each setting and their parameters.

   :::image type="content" source="media/howto-configure-app-instance-property-locks/app-instance-lock-configure-properties.png" alt-text="Screenshot of an app registration's app instance property lock context pane in the Azure portal.":::

   | Field                                    | Description                                                                                                                                                                                                                                                                                                       |
   | ---------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | **Enable property lock**                 | Specifies if the property locks are enabled.    | 
   | **All properties**                 | Locks all sensitive properties without needing to select each property scenario. |
   | **Credentials used for verification**                                | Locks the ability to add or update credential properties (`keyCredentials`, `passwordCredentials`) where usage type is `verify`. | 
   | **Credentials used for signing tokens**                                | Locks the ability to add or update credential properties (`keyCredentials`, `passwordCredentials`) where usage type is `sign`. | 
   | **Token Encryption KeyId**                                | Locks the ability to change the `tokenEncryptionKeyId` property.  | 

3. Select **Save** to save your changes.


## Configure app instance lock using Microsoft Graph

You manage the app instance lock feature through the **servicePrincipalLockConfiguration** property of the [application](/graph/api/resources/application) object of the multi-tenant app. For more information, see [Lock sensitive properties for service principals](/graph/tutorial-applications-basics#lock-sensitive-properties-for-service-principals).
