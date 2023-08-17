---
author: kengaderdus
ms.service: active-directory
ms.subservice: ciam
ms.topic: include
ms.date: 07/12/2023
ms.author: kengaderdus
---

1. Sign in to the [Microsoft Entra admin center](https://entra.microsoft.com) as a Conditional Access Administrator, Security Administrator, or Global Administrator.
1. Make sure you're using the directory that contains your Azure AD customer tenant: Select the **Directories + subscriptions** icon :::image type="icon" source="../../media/common/portal-directory-subscription-filter.png" border="false"::: for switching directories in the toolbar, and then find your customer tenant in the list. If it's not the current directory, select **Switch**.
1. In the left menu, under **Applications**, select **App registrations**, and then select the application you want to define app roles in.
1. Select **App roles**, and then select **Create app role**.
1. In the **Create app role** pane, enter the settings for the role. The following table describes each setting and its parameters.
    
   | Field    | Description | Example |
   | ----- | ----- | ----- |
   | **Display name** | Display name for the app role that appears in the app assignment experiences. This value may contain spaces. | `Orders manager`|
   | **Allowed member types**  | Specifies whether this app role can be assigned to users, applications, or both. | `Users/Groups`                |
   | **Value** | Specifies the value of the roles claim that the application should expect in the token. The value should exactly match the string referenced in the application's code. The value can't contain spaces.| `Orders.Manager`               |
   | **Description** | A more detailed description of the app role displayed during admin app assignment experiences. | `Manage online orders.` |
   | **Do you want to enable this app role?** | Specifies whether the app role is enabled. To delete an app role, deselect this checkbox and apply the change before attempting the delete operation.| _Checked_ |

1. Select **Apply** to create the application role.