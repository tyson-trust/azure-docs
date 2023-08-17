---
title: 'Tutorial: Configure BLDNG APP for automatic user provisioning with Azure Active Directory'
description: Learn how to automatically provision and de-provision user accounts from Azure AD to BLDNG APP.
services: active-directory
author: twimmers
writer: twimmers
manager: jeedes

ms.assetid: 5ccc1176-c244-4003-8486-67586bcdf317
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/21/2022
ms.author: thwimmer
---

# Tutorial: Configure BLDNG APP for automatic user provisioning in BLDNG.AI

This tutorial describes the steps you need to perform in both BLDNG APP and Azure Active Directory (Azure AD) to configure automatic user provisioning. When configured, Azure AD automatically provisions and de-provisions users and groups to [BLDNG APP](https://dashboard.bldng.ai/) using the Azure AD Provisioning service. For important details on what this service does, how it works, and frequently asked questions, see [Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory](../app-provisioning/user-provisioning.md). 


## Capabilities supported
> [!div class="checklist"]
> * Create users in BLDNG.AI 
> * Remove users in BLDNG.AI  when they do not require access anymore.
> * Keep user attributes synchronized between Azure AD and BLDNG.AI
> * Provision groups and group memberships in BLDNG.AI
> * [Single sign-on](../manage-apps/add-application-portal-setup-oidc-sso.md) to BLDNG.AI (recommended).


## Prerequisites

The scenario outlined in this tutorial assumes that you already have the following prerequisites:

* [An Azure AD tenant](../develop/quickstart-create-new-tenant.md) 
* A user account in Azure AD with [permission](../roles/permissions-reference.md) to configure provisioning (for example, Application Administrator, Cloud Application administrator, Application Owner, or Global Administrator). 
* A [BLDNG.AI](https://dashboard.bldng.ai/) agreement.
* An invitation from BLDNG.AI to enable user provisioning and use BLDNG APP

## Step 1. Plan your provisioning deployment
1. Learn about [how the provisioning service works](../app-provisioning/user-provisioning.md).
1. Determine who will be in [scope for provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Determine what data to [map between Azure AD and BLDNG APP](../app-provisioning/customize-application-attributes.md). 

## Step 2. Configure BLDNG APP to support provisioning with Azure AD

* To configure provisioning of users, user groups and group memberships from Azure you'll need a BLDNG.AI agreement and tenant.
* To attain an agreement, please contact [sales](mailto:salg@bldng.ai) to get in contact with a sales representative. You will not be able to proceed nor use BLDNG APP if an agreement does not exist.
* If you already have an active agreement but need to enable user provisioning only, contact [support](mailto:support@bldng.ai) directly.

When an agreement has been established, you will receive an email with detailed instructions on how to set up user provisioning. The email will also include details regarding admin consent (on behalf of your organization) for using BLDNG APP if needed. 

The email will also include Tenant URL and Secret Token for use when configuring automatic user provisioning. 

## Step 3. Add BLDNG APP from the Azure AD application gallery

Add BLDNG APP from the Azure AD application gallery to start managing provisioning to BLDNG APP. If you have previously setup BLDNG APP for SSO you can use the same application. However it is recommended that you create a separate app when testing out the integration initially. Learn more about adding an application from the gallery [here](../manage-apps/add-application-portal.md). 

## Step 4. Define who will be in scope for provisioning 

The Azure AD provisioning service allows you to scope who will be provisioned based on assignment to the application and or based on attributes of the user / group. If you choose to scope who will be provisioned to your app based on assignment, you can use the following [steps](../manage-apps/assign-user-or-group-access-portal.md) to assign users and groups to the application. If you choose to scope who will be provisioned based solely on attributes of the user or group, you can use a scoping filter as described [here](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Start small. Test with a small set of users and groups before rolling out to everyone. When scope for provisioning is set to assigned users and groups, you can control this by assigning one or two users or groups to the app. When scope is set to all users and groups, you can specify an [attribute based scoping filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* If you need additional roles, you can [update the application manifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) to add new roles.


## Step 5. Configure automatic user provisioning to BLDNG APP 

This section guides you through the steps to configure the Azure AD provisioning service to create, update, and disable users and/or groups in BLDNG APP based on user and/or group assignments in Azure AD.

### To configure automatic user provisioning for BLDNG APP in Azure AD:

1. Sign in to the [Azure portal](https://portal.azure.com). Select **Enterprise Applications**, then select **All applications**.

	![Enterprise applications blade](common/enterprise-applications.png)

1. In the applications list, select **BLDNG APP**.

	![The BLDNG APP link in the Applications list](common/all-applications.png)

1. Select the **Provisioning** tab.

	![Provisioning tab](common/provisioning.png)

1. Set the **Provisioning Mode** to **Automatic**.

	![Provisioning tab automatic](common/provisioning-automatic.png)

1. In the **Admin Credentials** section, input your BLDNG APP **Tenant URL** and **Secret Token**. Click **Test Connection** to ensure Azure AD can connect to BLDNG APP. If the connection fails , ensure your BLDNG APP account has Admin permissions and try again.

	![Token](common/provisioning-testconnection-tenanturltoken.png)

1. In the **Notification Email** field, enter the email address of a person or group who should receive the provisioning error notifications and select the **Send an email notification when a failure occurs** check box.

	![Notification Email](common/provisioning-notification-email.png)

1. Select **Save**.

1. In the **Mappings** section, select **Synchronize Azure Active Directory Users to BLDNG APP**.

1. Review the user attributes that are synchronized from Azure AD to BLDNG APP in the **Attribute-Mapping** section. The attributes selected as **Matching** properties are used to match the user accounts in BLDNG APP for update operations. If you choose to change the [matching target attribute](../app-provisioning/customize-application-attributes.md), you will need to ensure that the BLDNG APP API supports filtering users based on that attribute. Select the **Save** button to commit any changes.

> [!NOTE]
> It is important to note that if you change the mapping of **externalId**, the users in your tenant will not be able to log in using BLDNG APP.

   |Attribute|Type|Supported for filtering|
   |---|---|---|
   |userName|String|&check;
   |active|Boolean|   
   |displayName|String|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   

1. Under the **Mappings** section, select **Synchronize Azure Active Directory Groups to BLDNG APP**.

1. Review the group attributes that are synchronized from Azure AD to BLDNG APP in the **Attribute-Mapping** section. The attributes selected as **Matching** properties are used to match the groups in BLDNG APP for update operations. Select the **Save** button to commit any changes.

      |Attribute|Type|Supported for filtering|
      |---|---|---|
      |displayName|String|&check;
      |members|Reference|
      |externalId|String|      

1. To configure scoping filters, refer to the following instructions provided in the [Scoping filter tutorial](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. To enable the Azure AD provisioning service for BLDNG APP, change the **Provisioning Status** to **On** in the **Settings** section.

	![Provisioning Status Toggled On](common/provisioning-toggle-on.png)

1. Define the users and/or groups that you would like to provision to BLDNG APP by choosing the desired values in **Scope** in the **Settings** section.

	![Provisioning Scope](common/provisioning-scope.png)

1. When you are ready to provision, click **Save**.

	![Saving Provisioning Configuration](common/provisioning-configuration-save.png)

This operation starts the initial synchronization cycle of all users and groups defined in **Scope** in the **Settings** section. The initial cycle takes longer to perform than subsequent cycles, which occur approximately every 40 minutes as long as the Azure AD provisioning service is running. 

## Step 6. Monitor your deployment
Once you've configured provisioning, use the following resources to monitor your deployment:

* Use the [provisioning logs](../reports-monitoring/concept-provisioning-logs.md) to determine which users have been provisioned successfully or unsuccessfully
* Check the [progress bar](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) to see the status of the provisioning cycle and how close it is to completion
* If the provisioning configuration seems to be in an unhealthy state, the application will go into quarantine. Learn more about quarantine states [here](../app-provisioning/application-provisioning-quarantine-status.md).  

## More resources

* [Managing user account provisioning for Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## Next steps

* [Learn how to review logs and get reports on provisioning activity](../app-provisioning/check-status-user-account-provisioning.md)