---
title: 'Tutorial: Azure Active Directory integration with Bugsnag'
description: Learn how to configure single sign-on between Azure Active Directory and Bugsnag.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/23/2023
ms.author: jeedes
---
# Tutorial: Azure Active Directory integration with Bugsnag

In this tutorial, you learn how to integrate Bugsnag with Azure Active Directory (Azure AD). When you integrate Bugsnag with Azure AD, you can:

* Control in Azure AD who has access to Bugsnag.
* Enable your users to be automatically signed-in to Bugsnag with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Bugsnag single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD single sign-on in a test environment.

* Bugsnag supports **SP and IDP** initiated SSO.
* Bugsnag supports **Just In Time** user provisioning.

> [!NOTE]
> Identifier of this application is a fixed string value so only one instance can be configured in one tenant.

## Add Bugsnag from the gallery

To configure the integration of Bugsnag into Azure AD, you need to add Bugsnag from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Bugsnag** in the search box.
1. Select **Bugsnag** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

 Alternatively, you can also use the [Enterprise App Configuration Wizard](https://portal.office.com/AdminPortal/home?Q=Docs#/azureadappintegration). In this wizard, you can add an application to your tenant, add users/groups to the app, assign roles, and walk through the SSO configuration as well. [Learn more about Microsoft 365 wizards.](/microsoft-365/admin/misc/azure-ad-setup-guides)

## Configure and test Azure AD SSO for Bugsnag

Configure and test Azure AD SSO with Bugsnag using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Bugsnag.

To configure and test Azure AD SSO with Bugsnag, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Bugsnag SSO](#configure-bugsnag-sso)** - to configure the single sign-on settings on application side.
    1. **[Create Bugsnag test user](#create-bugsnag-test-user)** - to have a counterpart of B.Simon in Bugsnag that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the Azure portal, on the **Bugsnag** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the pencil icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

4. On the **Basic SAML Configuration** section, If you wish to configure the application in **IDP** initiated mode, perform the following step:

    In the **Reply URL** text box, type a URL using the following pattern:
    `https://app.bugsnag.com/user/sign_in/saml/<org_slug>/acs`

    > [!NOTE]
    > The Reply URL value is not real. Update this value with the actual Reply URL. Contact [Bugsnag Client support team](mailto:support@bugsnag.com) to get this value. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

5. Click **Set additional URLs** and perform the following step if you wish to configure the application in **SP** initiated mode:

    In the **Sign-on URL** text box, type the URL:
    `https://app.bugsnag.com/user/identity_provider`

6. On the **Set up Single Sign-On with SAML** page, In the **SAML Signing Certificate** section, click copy button to copy **App Federation Metadata Url** and save it on your computer.

    ![The Certificate download link](common/copy-metadataurl.png)

### Create an Azure AD test user

In this section, you create a test user in the Azure portal called B.Simon.

1. From the left pane in the Azure portal, select **Azure Active Directory**, select **Users**, and then select **All users**.
1. Select **New user** at the top of the screen.
1. In the **User** properties, follow these steps:
   1. In the **Name** field, enter `B.Simon`.  
   1. In the **User name** field, enter the username@companydomain.extension. For example, `B.Simon@contoso.com`.
   1. Select the **Show password** check box, and then write down the value that's displayed in the **Password** box.
   1. Click **Create**.

### Assign the Azure AD test user

In this section, you enable B.Simon to use Azure single sign-on by granting access to Bugsnag.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Bugsnag**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.
1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.
1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting a role to be assigned to the users, you can select it from the **Select a role** dropdown. If no role has been set up for this app, you see "Default Access" role selected.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure Bugsnag SSO

1. Sign into the Bugsnag website as an administrator.

1. In BugSnag settings, select **Organization settings -> Single sign-on**.

    ![Screenshot of Authentication page.](./media/bugsnag-tutorial/authentication.png)

1. Perform the following steps in the **Enable single sign-on** page:

    ![Screenshot of SSO settings page.](./media/bugsnag-tutorial/enable-sso.png)

    a. In the **SAML/IdP Metadata** field, enter the **App Federation Metadata Url** value, which you copied from Azure portal.

    b. Copy the **SAML Endpoint URL** value and paste this value into the **Reply URL** text box in the **Basic SAML Configuration** section in the Azure portal.

    c. Click **ENABLE SSO**.

> [!NOTE]
> For more information on the Bugsnag SSO configuration, please follow [this](https://docs.bugsnag.com/product/single-sign-on/other/#setup-saml) guide.

### Create Bugsnag test user

In this section, a user called Britta Simon is created in Bugsnag. Bugsnag supports just-in-time user provisioning, which is enabled by default. There's no action item for you in this section. If a user doesn't already exist in Bugsnag, a new one is created after authentication.

## Test SSO

In this section, you test your Azure AD single sign-on configuration with following options. 

#### SP initiated:

* Click on **Test this application** in Azure portal. This will redirect to Bugsnag Sign on URL where you can initiate the login flow.  

* Go to Bugsnag Sign-on URL directly and initiate the login flow from there.

#### IDP initiated:

* Click on **Test this application** in Azure portal and you should be automatically signed in to the Bugsnag for which you set up the SSO. 

You can also use Microsoft My Apps to test the application in any mode. When you click the Bugsnag tile in the My Apps, if configured in SP mode you would be redirected to the application sign-on page for initiating the login flow and if configured in IDP mode, you should be automatically signed in to the Bugsnag for which you set up the SSO. For more information about the My Apps, see [Introduction to the My Apps](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## Next steps

Once you configure Bugsnag you can enforce session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
