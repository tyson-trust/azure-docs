---
title: Hide an Enterprise application
description: How to hide an Enterprise application from user's experience in Azure Active Directory access portals or Microsoft 365 launchers.
services: active-directory
author: omondiatieno
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2022
ms.author: jomondi
ms.reviewer: ergreenl, lenalepa
ms.collection: M365-identity-device-management
zone_pivot_groups: enterprise-apps-all
ms.custom: enterprise-apps, has-azure-ad-ps-ref
#customer intent: As an admin, I want to hide an enterprise application from user's experience so that it is not listed in the user's Active directory access portals or Microsoft 365 launchers
---

# Hide an Enterprise application

Learn how to hide enterprise applications in Azure Active Directory. When an application is hidden, users still have permissions to the application.

## Prerequisites

- Application administrator privileges are required to hide an application from the My Apps portal and Microsoft 365 launcher.

- Global administrator privileges are required to hide all Microsoft 365 applications.

## Hide an application from the end user

:::zone pivot="portal"

Use the following steps to hide an application from My Apps portal and Microsoft 365 application launcher.

1. Sign in to the [Azure portal](https://portal.azure.com) as the global administrator for your directory.
1. Select **Azure Active Directory**.
1. Select **Enterprise applications**.
1. Under **Application Type**, select **Enterprise Applications**, if it isn't already selected.
1. Search for the application you want to hide, and select the application.
1. In the left navigation pane, select **Properties**.
1. Select **No** for the **Visible to users?** question.
1. Select **Save**.

:::zone-end

> [!NOTE]
> These instructions apply only to Enterprise applications.

:::zone pivot="aad-powershell"


To hide an application from the My Apps portal, you can manually add the HideApp tag to the service principal for the application. Run the following AzureAD PowerShell commands to set the application's **Visible to Users?** property to **No**.

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```
:::zone-end

:::zone pivot="ms-powershell"

To hide an application from the My Apps portal, you can manually add the HideApp tag to the service principal for the application. Run the following Microsoft Graph PowerShell commands to set the application's **Visible to Users?** property to **No**.

```PowerShell
Connect-MgGraph

$servicePrincipal = Get-MgServicePrincipal -ServicePrincipalId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Update-MgServicePrincipal -ServicePrincipalID  $objectId -Tags $tags
```
:::zone-end

:::zone pivot="ms-graph"

To hide an enterprise application using [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer), run the following queries.

1. Get the application you want to hide.

   ```http
   GET https://graph.microsoft.com/v1.0/servicePrincipals/5f214ccd-3f74-41d7-b683-9a6d845eea4d
   ```
1. Update the application to hide it from users.

   ```http
   PATCH https://graph.microsoft.com/v1.0/servicePrincipals/5f214ccd-3f74-41d7-b683-9a6d845eea4d/
   ```

    Supply the following request body.

    ```json
    {
        "tags": [
        "HideApp"
        ]
    }
    ```
   
   >[!WARNING]
   >If the application has other tags, you must include them in the request body. Otherwise, the query will overwrite them.

:::zone-end

:::zone pivot="portal"

## Hide Microsoft 365 applications from the My Apps portal

[!INCLUDE [portal updates](~/articles/active-directory/includes/portal-update.md)]

Use the following steps to hide all Microsoft 365 applications from the My Apps portal. The applications are still visible in the Office 365 portal.

1. Sign in to the [Azure portal](https://portal.azure.com) as a global administrator for your directory.
1. Select **Azure Active Directory**.
1. Select **Enterprise applications**.
1. Select **App launchers**.
2. Select **Settings**.
3. For **Users can only see Microsoft 365 apps in the Microsoft 365 portal**, select **Yes**.
4. Select **Save**.

:::zone-end
## Next steps

- [Remove a user or group assignment from an enterprise app](./assign-user-or-group-access-portal.md)
