---
title: 'Tutorial: Azure AD SSO integration with GaggleAMP'
description: Learn how to configure single sign-on between Azure Active Directory and GaggleAMP.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/03/2023
ms.author: jeedes
---
# Tutorial: Azure AD SSO integration with GaggleAMP

In this tutorial, you'll learn how to integrate GaggleAMP with Azure Active Directory (Azure AD). When you integrate GaggleAMP with Azure AD, you can:

* Control in Azure AD who has access to GaggleAMP.
* Enable your users to be automatically signed-in to GaggleAMP with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* GaggleAMP single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD single sign-on in a test environment.

* GaggleAMP supports **SP** and **IDP** initiated SSO.

* GaggleAMP supports **Just In Time** user provisioning.

> [!NOTE]
> Identifier of this application is a fixed string value so only one instance can be configured in one tenant.

## Add GaggleAMP from the gallery

To configure the integration of GaggleAMP into Azure AD, you need to add GaggleAMP from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **GaggleAMP** in the search box.
1. Select **GaggleAMP** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

 Alternatively, you can also use the [Enterprise App Configuration Wizard](https://portal.office.com/AdminPortal/home?Q=Docs#/azureadappintegration). In this wizard, you can add an application to your tenant, add users/groups to the app, assign roles, as well as walk through the SSO configuration as well. [Learn more about Microsoft 365 wizards.](/microsoft-365/admin/misc/azure-ad-setup-guides)

## Configure and test Azure AD SSO for GaggleAMP

Configure and test Azure AD SSO with GaggleAMP using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in GaggleAMP.

To configure and test Azure AD SSO with GaggleAMP, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure GaggleAMP SSO](#configure-gaggleamp-sso)** - to configure the single sign-on settings on application side.
    1. **[Create GaggleAMP test user](#create-gaggleamp-test-user)** - to have a counterpart of B.Simon in GaggleAMP that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the Azure portal, on the **GaggleAMP** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the pencil icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, If you wish to configure the application in **IDP** initiated mode, perform the following step:

    In the **Identifier** text box, type the URL:
    `https://accounts.gaggleamp.com/auth/saml/callback`

1. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section, click **Download** to download the **Certificate (Base64)** from the given options as per your requirement and save it on your computer.

	![The Certificate download link](common/certificatebase64.png)

1. On the **Set up GaggleAMP** section, copy the appropriate URL(s) as per your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to GaggleAMP.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **GaggleAMP**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.
1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.
1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you are expecting a role to be assigned to the users, you can select it from the **Select a role** dropdown. If no role has been set up for this app, you see "Default Access" role selected.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure GaggleAMP SSO

1. In another browser instance, navigate to the SAML SSO page created for you by the Gaggle support team (for example: *https://accounts.gaggleamp.com/saml_configurations/oXH8sQcP79dOzgFPqrMTyw/edit*).

2. On your **SAML SSO** page, perform the following steps:  
   
    ![GaggleAMP Single Sign-On](./media/gaggleamp-tutorial/configuration.png)

	a. Select **Other** from the **Identity provider** dropdown menu.
	
	b. In the **Identity Provider Issuer** textbox, paste the value of **Azure Ad Identifier** which you have copied from Azure portal.
	
	c. In the **Identity Provider Single Sign-On URL** textbox, paste the  value of **Login URL** which you have copied from Azure portal.
	
	d. Open your downloaded **Certificate(Base64)** file in notepad, copy the content of it into your clipboard, and then paste it to the **X.509 Certificate** textbox.
	
	e. Click **Save**.

### Create GaggleAMP test user

In this section, a user called Britta Simon is created in GaggleAMP. GaggleAMP supports just-in-time user provisioning, which is enabled by default. There is no action item for you in this section. If a user doesn't already exist in GaggleAMP, a new one is created after authentication.

## Test SSO 

In this section, you test your Azure AD single sign-on configuration with following options. 

#### SP initiated:

* Visit the invitation page of your Gaggle, you can find its unique link on the Manager View, menu **option Members > Invite**.

* Click the **Sign in with SAML** button to initiate the login flow from there.

#### IdP initiated:

* Click on **Test this application** in Azure portal and you should be automatically signed in to GaggleAMP.

You can also use Microsoft My Apps to test the application in any mode. When you click the GaggleAMP tile in the My Apps, if configured in SP mode you would be redirected to the application sign on page for initiating the login flow and if configured in IDP mode, you should be automatically signed in to the GaggleAMP for which you set up the SSO. For more information about the My Apps, see [Introduction to the My Apps](../user-help/my-apps-portal-end-user-access.md).

## Next steps

Once you configure GaggleAMP you can enforce session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).