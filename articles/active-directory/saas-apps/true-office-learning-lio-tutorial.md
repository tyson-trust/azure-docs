---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with True Office Learning - LIO'
description: Learn how to configure single sign-on between Azure Active Directory and True Office Learning - LIO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/21/2022
ms.author: jeedes

---

# Tutorial: Azure Active Directory single sign-on (SSO) integration with True Office Learning - LIO

In this tutorial, you'll learn how to integrate True Office Learning - LIO with Azure Active Directory (Azure AD). When you integrate True Office Learning - LIO with Azure AD, you can:

* Control in Azure AD who has access to True Office Learning - LIO.
* Enable your users to be automatically signed-in to True Office Learning - LIO with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* True Office Learning - LIO single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* True Office Learning - LIO supports **SP** initiated SSO.

## Add True Office Learning - LIO from the gallery

To configure the integration of True Office Learning - LIO into Azure AD, you need to add True Office Learning - LIO from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **True Office Learning - LIO** in the search box.
1. Select **True Office Learning - LIO** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

 Alternatively, you can also use the [Enterprise App Configuration Wizard](https://portal.office.com/AdminPortal/home?Q=Docs#/azureadappintegration). In this wizard, you can add an application to your tenant, add users/groups to the app, assign roles, as well as walk through the SSO configuration as well. [Learn more about Microsoft 365 wizards.](/microsoft-365/admin/misc/azure-ad-setup-guides)

## Configure and test Azure AD SSO for True Office Learning - LIO

Configure and test Azure AD SSO with True Office Learning - LIO using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in True Office Learning - LIO.

To configure and test Azure AD SSO with True Office Learning - LIO, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure True Office Learning - LIO SSO](#configure-true-office-learning---lio-sso)** - to configure the single sign-on settings on application side.
    1. **[Create True Office Learning - LIO test user](#create-true-office-learning---lio-test-user)** - to have a counterpart of B.Simon in True Office Learning - LIO that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the Azure portal, on the **True Office Learning - LIO** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the pencil icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, perform the following steps:

    1. In the **Identifier (Entity ID)** text box, type a URL using the following pattern:
    `https://learn-sso.trueoffice.com/simplesaml/module.php/saml/sp/metadata.php/<CUSTOMER_NAME>-sp`

    1. In the **Sign on URL** text box, type a URL using the following pattern:
    `https://learn-sso.trueoffice.com/<CUSTOMER_NAME>`
    
        > [!NOTE]
        > These values are not real. Update these values with the actual Identifier and Sign on URL. Contact [True Office Learning - LIO Client support team](mailto:service@trueoffice.com) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Federation Metadata XML** and select **Download** to download the certificate and save it on your computer.

    ![The Certificate download link](common/metadataxml.png)

1. On the **Set up True Office Learning - LIO** section, copy the appropriate URL(s) based on your requirement.

    ![Copy configuration URLs](common/copy-configuration-urls.png)

### Create an Azure AD test user

In this section, you'll create a test user in the Azure portal called B.Simon.

1. From the left pane in the Azure portal, select **Azure Active Directory**, select **Users**, and then select **All users**.
1. Select **New user** at the top of the screen.
1. In the **User** properties, follow these steps:
   1. In the **Name** field, enter `B.Simon`.  
   1. In the **User name** field, enter the username@companydomain.extension. For example, `B.Simon@contoso.com`.
   1. Select the **Show password** check box, and then write down the value that's displayed in the **Password** box.
   1. Click **Create**.

### Assign the Azure AD test user

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to True Office Learning - LIO.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **True Office Learning - LIO**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.
1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.
1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you are expecting a role to be assigned to the users, you can select it from the **Select a role** dropdown. If no role has been set up for this app, you see "Default Access" role selected.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure True Office Learning - LIO SSO

Please contact [True Office Learning - LIO support team](mailto:service@trueoffice.com) with configuration questions and/or to request a copy of the metadata. 
In your request please provide the following information:
* Company Name.
* CompanyID (if known).
* Existing or new configuration.

### Create True Office Learning - LIO test user

In this section, you create a user called Britta Simon in True Office Learning - LIO. Work with [True Office Learning - LIO support team](mailto:service@trueoffice.com) to add the users in the True Office Learning - LIO platform. Users must be created and activated before you use single sign-on.

## Test SSO 

In this section, you test your Azure AD single sign-on configuration by using the following options:

* Select **Test this application** in the Azure portal. You're redirected to the True Office Learning - LIO Sign-on URL where you can initiate the login flow. 
* Go to the True Office Learning - LIO Sign-on URL directly, and initiate the login flow from that site.
* You can use Microsoft My Apps. When you select the True Office Learning - LIO tile in My Apps, you're redirected to the True Office Learning - LIO Sign-on URL. For more information about My Apps, see [Introduction to My Apps](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## Next steps

After you configure True Office Learning - LIO you can enforce session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
