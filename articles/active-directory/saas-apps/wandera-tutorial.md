---
title: 'Tutorial: Azure Active Directory integration with Wandera RADAR Admin'
description: Learn how to configure single sign-on between Azure Active Directory and Wandera RADAR Admin.
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/21/2022
ms.author: jeedes
---

# Tutorial: Integrate Wandera RADAR Admin with Azure Active Directory

In this tutorial, you'll learn how to integrate Wandera RADAR Admin with Azure Active Directory (Azure AD). When you integrate Wandera RADAR Admin with Azure AD, you can:

* Control in Azure AD who has access to Wandera RADAR Admin.
* Enable your users to be automatically signed-in to Wandera RADAR Admin with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Wandera RADAR Admin single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Wandera RADAR Admin supports **IDP** initiated SSO.

> [!NOTE]
> Identifier of this application is a fixed string value so only one instance can be configured in one tenant.

## Add Wandera RADAR Admin from the gallery

To configure the integration of Wandera RADAR Admin into Azure AD, you need to add Wandera RADAR Admin from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Wandera RADAR Admin** in the search box.
1. Select **Wandera RADAR Admin** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

 Alternatively, you can also use the [Enterprise App Configuration Wizard](https://portal.office.com/AdminPortal/home?Q=Docs#/azureadappintegration). In this wizard, you can add an application to your tenant, add users/groups to the app, assign roles, as well as walk through the SSO configuration as well. [Learn more about Microsoft 365 wizards.](/microsoft-365/admin/misc/azure-ad-setup-guides)

## Configure and test Azure AD SSO for Wandera RADAR Admin

Configure and test Azure AD SSO with Wandera RADAR Admin using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Wandera RADAR Admin.

To configure and test Azure AD SSO with Wandera RADAR Admin, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
   1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
   1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Wandera RADAR Admin SSO](#configure-wandera-radar-admin-sso)** - to configure the Single Sign-On settings on application side.
   1. **[Create Wandera RADAR Admin test user](#create-wandera-radar-admin-test-user)** - to have a counterpart of B.Simon in Wandera RADAR Admin that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the Azure portal, on the **Wandera RADAR Admin** application integration page, find the **Manage** section and select **Single sign-on**.
1. On the **Select a Single sign-on method** page, select **SAML**.
1. On the **Set up Single Sign-On with SAML** page, click the pencil icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, perform the following step:

    In the **Reply URL** text box, type a URL using the following pattern:
    `https://radar.wandera.com/saml/acs/<TENANT_ID>`

    > [!NOTE]
    > The value is not real. Update the value with the actual Reply URL. Contact [Wandera RADAR Admin Client support team](https://www.wandera.com/about-wandera/contact/#supportsection) to get the value. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal. Carefully replace the \<tenant id\> part of the above URL with the Tenant ID shown in the **Settings** > **Administration** > **Single Sign-On** page within your Wandera account.

1. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section,  find **Federation Metadata XML** and select **Download** to download the certificate and save it on your computer.

    ![The Certificate download link](common/metadataxml.png)

1. On the **Set up Single Sign-On with SAML** page, click the pencil icon for **SAML Signing Certificate** to edit the settings.

    ![Signing Option](common/signing-option.png)

    1. Select **Signing Option** as **Sign SAML response and assertion**.

    1. Select **Signing Algorithm** as **SHA-256**.

1. On the **Set up Wandera RADAR Admin** section, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Wandera RADAR Admin.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Wandera RADAR Admin**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.
1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.
1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you are expecting a role to be assigned to the users, you can select it from the **Select a role** dropdown. If no role has been set up for this app, you see "Default Access" role selected.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure Wandera RADAR Admin SSO




1. In a different web browser window, sign in to your Wandera RADAR Admin company site as an administrator

4. On the top-right corner of the page, click on **Settings** > **Administration** > **Single Sign-On** and then check the option **Enable SAML 2.0** to perform the following steps.

    ![Wandera RADAR Admin configuration](./media/wandera-tutorial/configure.png)

    a. Click on **Or manually enter the required fields**.

    b. In the **IdP EntityId** text box, Paste the **Azure AD Identifier** value, which you have copied from the Azure portal.

    c. Open the Federation Metadata XML in notepad, copy its content and paste it into the **IdP Public X.509 Certificate** text box.

    d. Click **Save**.

### Create Wandera RADAR Admin test user

In this section, you create a user called B.Simon in Wandera RADAR Admin. Work with [Wandera RADAR Admin support team](https://www.wandera.com/about-wandera/contact/#supportsection) to add the users in the Wandera RADAR Admin platform. Users must be created and activated before you use single sign-on.

## Test SSO

In this section, you test your Azure AD single sign-on configuration with following options.

* Click on Test this application in Azure portal and you should be automatically signed in to the Wandera RADAR Admin for which you set up the SSO.

* You can use Microsoft My Apps. When you click the Wandera RADAR Admin tile in the My Apps, you should be automatically signed in to the Wandera RADAR Admin for which you set up the SSO. For more information about the My Apps, see [Introduction to the My Apps](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## Next steps

Once you configure Wandera RADAR Admin you can enforce session control, which protects exfiltration and infiltration of your organization's sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
