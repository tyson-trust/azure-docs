---
title: 'Tutorial: Configure Servicely for automatic user provisioning with Azure Active Directory'
description: Learn how to automatically provision and deprovision user accounts from Azure AD to Servicely.
services: active-directory
author: twimmers
writer: twimmers
manager: jeedes
ms.assetid: be3af02b-da77-4a88-bec3-e634e2af38b3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/16/2023
ms.author: thwimmer
---

# Tutorial: Configure Servicely for automatic user provisioning

This tutorial describes the steps you need to perform in both Servicely and Azure Active Directory (Azure AD) to configure automatic user provisioning. When configured, Azure AD automatically provisions and deprovisions users and groups to [Servicely](https://servicely.ai/) using the Azure AD Provisioning service. For important details on what this service does, how it works, and frequently asked questions, see [Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory](../app-provisioning/user-provisioning.md). 


## Supported capabilities
> [!div class="checklist"]
> * Create users in Servicely.
> * Remove users in Servicely when they do not require access anymore.
> * Keep user attributes synchronized between Azure AD and Servicely.
> * Provision groups and group memberships in Servicely.

## Prerequisites

The scenario outlined in this tutorial assumes that you already have the following prerequisites:

* [An Azure AD tenant](../develop/quickstart-create-new-tenant.md). 
* A user account in Azure AD with [permission](../roles/permissions-reference.md) to configure provisioning (for example, Application Administrator, Cloud Application administrator, Application Owner, or Global Administrator).
* An Servicely tenant.
* A user account in Servicely with Admin permissions.

## Step 1. Plan your provisioning deployment
1. Learn about [how the provisioning service works](../app-provisioning/user-provisioning.md).
1. Determine who is in [scope for provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Determine what data to [map between Azure AD and Servicely](../app-provisioning/customize-application-attributes.md).

## Step 2. Configure Servicely to support provisioning with Azure AD
Contact Servicely support to configure Servicely to support provisioning with Azure AD.

## Step 3. Add Servicely from the Azure AD application gallery

Add Servicely from the Azure AD application gallery to start managing provisioning to Servicely. Learn more about adding an application from the gallery [here](../manage-apps/add-application-portal.md). 

## Step 4. Define who is in scope for provisioning 

The Azure AD provisioning service allows you to scope who is provisioned based on assignment to the application and/or based on attributes of the user. If you choose to scope who is provisioned to your app based on assignment, you can use the following [steps](../manage-apps/assign-user-or-group-access-portal.md) to assign users and groups to the application. If you choose to scope who is provisioned based solely on attributes of the user or group, you can use a scoping filter as described [here](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Start small. Test with a small set of users and groups before rolling out to everyone. When scope for provisioning is set to assigned users and groups, you can control this by assigning one or two users or groups to the app. When scope is set to all users and groups, you can specify an [attribute based scoping filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* If you need more roles, you can [update the application manifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) to add new roles.


## Step 5. Configure automatic user provisioning to Servicely 

This section guides you through the steps to configure the Azure AD provisioning service to create, update, and disable users and/or groups in TestApp based on user and/or group assignments in Azure AD.

### To configure automatic user provisioning for Servicely in Azure AD:

1. Sign in to the [Azure portal](https://portal.azure.com). Select **Enterprise Applications**, then select **All applications**.

	![Screenshot of Enterprise applications blade.](common/enterprise-applications.png)

1. In the applications list, select **Servicely**.

	![Screenshot of the Servicely link in the Applications list.](common/all-applications.png)

1. Select the **Provisioning** tab.

	![Screenshot of Provisioning tab.](common/provisioning.png)

1. Set the **Provisioning Mode** to **Automatic**.

	![Screenshot of Provisioning tab automatic.](common/provisioning-automatic.png)

1. Under the **Admin Credentials** section, input your Servicely Tenant URL and Secret Token. Click **Test Connection** to ensure Azure AD can connect to Servicely. If the connection fails, ensure your Servicely account has Admin permissions and try again.

 	![Screenshot of Token.](common/provisioning-testconnection-tenanturltoken.png)

1. In the **Notification Email** field, enter the email address of a person or group who should receive the provisioning error notifications and select the **Send an email notification when a failure occurs** check box.

	![Screenshot of Notification Email.](common/provisioning-notification-email.png)

1. Select **Save**.

1. Under the **Mappings** section, select **Synchronize Azure Active Directory Users to Servicely**.

1. Review the user attributes that are synchronized from Azure AD to Servicely in the **Attribute-Mapping** section. The attributes selected as **Matching** properties are used to match the user accounts in Servicely for update operations. If you choose to change the [matching target attribute](../app-provisioning/customize-application-attributes.md), you need to ensure that the Servicely API supports filtering users based on that attribute. Select the **Save** button to commit any changes.

   |Attribute|Type|Supported for filtering|Required by Servicely|
   |---|---|---|---|
   |userName|String|&check;|&check;
   |active|Boolean||
   |externalId|String||
   |emails[type eq "work"].value|String||
   |name.givenName|String||
   |name.familyName|String||
   |title|String||
   |preferredLanguage|String||
   |phoneNumbers[type eq "work"].value|String||
   |phoneNumbers[type eq "mobile"].value|String||
   |timezone|String||
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String||
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|String||

1. Under the **Mappings** section, select **Synchronize Azure Active Directory Groups to Servicely**.

1. Review the group attributes that are synchronized from Azure AD to Servicely in the **Attribute-Mapping** section. The attributes selected as **Matching** properties are used to match the groups in Servicely for update operations. Select the **Save** button to commit any changes.

   |Attribute|Type|Supported for filtering|Required by Servicely|
   |---|---|---|---|
   |displayName|String|&check;|&check;
   |externalId|String|&check;|&check;
   |members|Reference||
   
1. To configure scoping filters, refer to the following instructions provided in the [Scoping filter tutorial](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. To enable the Azure AD provisioning service for Servicely, change the **Provisioning Status** to **On** in the **Settings** section.

	![Screenshot of Provisioning Status Toggled On.](common/provisioning-toggle-on.png)

1. Define the users and/or groups that you would like to provision to Servicely by choosing the desired values in **Scope** in the **Settings** section.

	![Screenshot of Provisioning Scope.](common/provisioning-scope.png)

1. When you're ready to provision, click **Save**.

	![Screenshot of Saving Provisioning Configuration.](common/provisioning-configuration-save.png)

This operation starts the initial synchronization cycle of all users and groups defined in **Scope** in the **Settings** section. The initial cycle takes longer to perform than subsequent cycles, which occur approximately every 40 minutes as long as the Azure AD provisioning service is running. 

## Step 6. Monitor your deployment
Once you've configured provisioning, use the following resources to monitor your deployment:

* Use the [provisioning logs](../reports-monitoring/concept-provisioning-logs.md) to determine which users have been provisioned successfully or unsuccessfully
* Check the [progress bar](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) to see the status of the provisioning cycle and how close it's to completion
* If the provisioning configuration seems to be in an unhealthy state, the application goes into quarantine. Learn more about quarantine states [here](../app-provisioning/application-provisioning-quarantine-status.md).

## More resources

* [Managing user account provisioning for Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## Next steps

* [Learn how to review logs and get reports on provisioning activity](../app-provisioning/check-status-user-account-provisioning.md)
