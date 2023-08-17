---
title: Azure Active Directory SSO integration with Vbrick Rev Cloud
description: Learn how to configure single sign-on between Azure Active Directory and Vbrick Rev Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: how-to
ms.date: 07/07/2023
ms.author: jeedes

---

# Azure Active Directory SSO integration with Vbrick Rev Cloud

In this article, you'll learn how to integrate Vbrick Rev Cloud with Azure Active Directory (Azure AD). Rev enterprise video platform is a solution to capture, manage and distribute live and on-demand video. We help organizations meet critical live video needs and innovative uses of on-demand videos. When you integrate Vbrick Rev Cloud with Azure AD, you can:

* Control in Azure AD who has access to Vbrick Rev Cloud.
* Enable your users to be automatically signed-in to Vbrick Rev Cloud with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

You'll configure and test Azure AD single sign-on for Vbrick Rev Cloud in a test environment. Vbrick Rev Cloud supports **SP** initiated single sign-on.

## Prerequisites

To integrate Azure Active Directory with Vbrick Rev Cloud, you need:

* An Azure AD user account. If you don't already have one, you can [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* One of the following roles: Global Administrator, Cloud Application Administrator, Application Administrator, or owner of the service principal.
* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Vbrick Rev Cloud single sign-on (SSO) enabled subscription.

## Add application and assign a test user

Before you begin the process of configuring single sign-on, you need to add the Vbrick Rev Cloud application from the Azure AD gallery. You need a test user account to assign to the application and test the single sign-on configuration.

### Add Vbrick Rev Cloud from the Azure AD gallery

Add Vbrick Rev Cloud from the Azure AD application gallery to configure single sign-on with Vbrick Rev Cloud. For more information on how to add application from the gallery, see the [Quickstart: Add application from the gallery](../manage-apps/add-application-portal.md).

### Create and assign Azure AD test user

Follow the guidelines in the [create and assign a user account](../manage-apps/add-application-portal-assign-users.md) article to create a test user account in the Azure portal called B.Simon.

Alternatively, you can also use the [Enterprise App Configuration Wizard](https://portal.office.com/AdminPortal/home?Q=Docs#/azureadappintegration). In this wizard, you can add an application to your tenant, add users/groups to the app, and assign roles. The wizard also provides a link to the single sign-on configuration pane in the Azure portal. [Learn more about Microsoft 365 wizards.](/microsoft-365/admin/misc/azure-ad-setup-guides). 

## Configure Azure AD SSO

Complete the following steps to enable Azure AD single sign-on in the Azure portal.

1. In the Azure portal, on the **Vbrick Rev Cloud** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, select the pencil icon for **Basic SAML Configuration** to edit the settings.

   [ ![Screenshot shows how to edit Basic SAML Configuration.](common/edit-urls.png "Basic Configuration") ](common/edit-urls.png#lightbox)

1. On the **Basic SAML Configuration** section, perform the following steps:

	a. In the **Identifier** textbox, type a URL using one of the following patterns:

	| **Identifier** |
	|--------------|
	| `https://<CustomerName>.domain.extension:443` |
	| `https://<CustomerName>.au.vbrickrev.com:443` |
	| `https://<CustomerName>.eu.vbrickrev.com:443`|
	| `https://<CustomerName>.rev.vbrick.com:443` |

	b. In the **Reply URL** textbox, type a URL using one of the following patterns:

	| **Reply URL** |
	|------------|
	| `https://<CustomerName>.rev.vbrick.com:443/sso/consume` |
	| `https://<CustomerName>.eu.vbrickrev.com:443/sso/consume` |
	| `https://<CustomerName>.au.vbrickrev.com:443/sso/consume`|
	| `https://<CustomerName>.domain.extension:443/sso/consume` |

	c. In the **Sign on URL** textbox, type a URL using one of the following patterns:
	
	| **Sign on URL** |
	|-----------|
	| `https://<CustomerName>.rev.vbrick.com` |
	| `https://<CustomerName>.eu.vbrickrev.com`| 
	| `https://<CustomerName>.au.vbrickrev.com` |
	| `https://<CustomerName>.domain.extension` |

	> [!Note]
	> These values are not real. Update these values with the actual Identifier, Reply URL and Sign on URL. Contact [Vbrick Rev Cloud support team](mailto:support@vbrick.com) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

1. On the **Set-up single sign-on with SAML** page, in the **SAML Signing Certificate** section, find **Federation Metadata XML** and select **Download** to download the certificate and save it on your computer.

    [ ![Screenshot shows the Certificate download link.](common/metadataxml.png "Certificate") ](common/metadataxml.png#lightbox)

1. On the **Set up Vbrick Rev Cloud** section, copy the appropriate URL(s) based on your requirement.

	[ ![Screenshot shows to copy configuration appropriate URL.](common/copy-configuration-urls.png "Metadata") ](common/copy-configuration-urls.png#lightbox)

## Configure Vbrick Rev Cloud

1. Log in to your Vbrick Rev Cloud company site as an administrator.

1. Navigate to **System Settings** > **Security**.

1. In the **SAML SINGLE SIGN ON** section, perform the following steps:
	
	[ ![Screenshot shows the administration portal.](media/vbrick-rev-cloud-tutorial/manage.png "Admin") ](media/vbrick-rev-cloud-tutorial/manage.png#lightbox)

	1. Check the **Enable Single Sign On** checkbox.

	1. In **Identity Provider Metadata** textbox, paste the **Federation Metadata XML** file, which you have copied from the Azure portal.

	1. For **Signature Algorithm**, select **SHA256WithRSA** from the dropdown list.

	1.  Leave the **Sign SAML Request** checkbox checked and click **Save**.

	> [!Note]
	>  For more information, please visit [this](https://revdocs.vbrick.com/docs/configure-single-sign-on-sso) Vbrick Rev documentation.

### Create Vbrick Rev Cloud test user

In this section, you create a user called B.Simon in Vbrick Rev. Please follow [this](https://revdocs.vbrick.com/docs/user-accounts#add-or-edit-a-user) guide to create the test user. Users must be created and activated before you use single sign-on.

## Test SSO 

In this section, you test your Azure AD single sign-on configuration with following options. 

* Click on **Test this application** in Azure portal. This will redirect to Vbrick Rev Cloud Sign-on URL where you can initiate the login flow. 

* Go to Vbrick Rev Cloud Sign-on URL directly and initiate the login flow from there.

* You can use Microsoft My Apps. When you click the Vbrick Rev Cloud tile in the My Apps, this will redirect to Vbrick Rev Cloud Sign-on URL. For more information about the My Apps, see [Introduction to the My Apps](../user-help/my-apps-portal-end-user-access.md).

## Additional resources

* [What is single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Plan a single sign-on deployment](../manage-apps/plan-sso-deployment.md).

## Next steps

Once you configure Vbrick Rev Cloud you can enforce session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).