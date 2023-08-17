---
title: Tutorial - Integrate an existing forest and a new forest with a single Azure AD tenant using Azure AD Connect cloud sync.
description: Learn how to add cloud sync to an existing hybrid identity environment.
services: active-directory
author: billmath
manager: amycolannino
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2023
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
---

# Integrate an existing forest and a new forest with a single Azure AD tenant

This tutorial walks you through adding cloud sync to an existing hybrid identity environment. 

![Diagram that shows the Azure AD Connect cloud sync flow.](media/tutorial-existing-forest/existing-forest-new-forest-2.png)

You can use the environment you create in this tutorial for testing or for getting more familiar with how a hybrid identity works. 

In this scenario, there's an existing forest synced using Azure AD Connect sync to an Azure AD tenant. And you have a new forest that you want to sync to the same Azure AD tenant. You'll set up cloud sync for the new forest. 

## Prerequisites
### In the Azure portal

1. Create a cloud-only global administrator account on your Azure AD tenant. This way, you can manage the configuration of your tenant should your on-premises services fail or become unavailable. Learn about [adding a cloud-only global administrator account](../../fundamentals/add-users.md). Completing this step is critical to ensure that you don't get locked out of your tenant.
2. Add one or more [custom domain names](../../fundamentals/add-custom-domain.md) to your Azure AD tenant. Your users can sign in with one of these domain names.

### In your on-premises environment

1. Identify a domain-joined host server running Windows Server 2012 R2 or greater with minimum of 4-GB RAM and .NET 4.7.1+ runtime 

2. If there's a firewall between your servers and Azure AD, configure the following items:
   - Ensure that agents can make *outbound* requests to Azure AD over the following ports:

     | Port number | How it's used |
     | --- | --- |
     | **80** | Downloads the certificate revocation lists (CRLs) while validating the TLS/SSL certificate |
     | **443** | Handles all outbound communication with the service |
     | **8080** (optional) | Agents report their status every 10 minutes over port 8080, if port 443 is unavailable. This status is displayed on the Azure portal. |
     
     If your firewall enforces rules according to the originating users, open these ports for traffic from Windows services that run as a network service.
   - If your firewall or proxy allows you to specify safe suffixes, then add  connections to **\*.msappproxy.net** and **\*.servicebus.windows.net**. If not, allow access to the [Azure datacenter IP ranges](https://www.microsoft.com/download/details.aspx?id=41653), which are updated weekly.
   - Your agents need access to **login.windows.net** and **login.microsoftonline.com** for initial registration. Open your firewall for those URLs as well.
   - For certificate validation, unblock the following URLs: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80**, and **www\.microsoft.com:80**. Since these URLs are used for certificate validation with other Microsoft products, you may already have these URLs unblocked.

## Install the Azure AD Connect provisioning agent

If you're using the  [Basic AD and Azure environment](tutorial-basic-ad-azure.md) tutorial, it would be DC1. To install the agent, follow these steps: 

[!INCLUDE [active-directory-cloud-sync-how-to-install](../../../../includes/active-directory-cloud-sync-how-to-install.md)]


## Verify agent installation

[!INCLUDE [active-directory-cloud-sync-how-to-verify-installation](../../../../includes/active-directory-cloud-sync-how-to-verify-installation.md)]

## Configure Azure AD Connect cloud sync

[!INCLUDE [portal updates](~/articles/active-directory/includes/portal-update.md)]

Use the following steps to configure provisioning:

1. Sign in to the [Azure portal](https://portal.azure.com).
2. Select **Azure Active Directory**
3. Select **Azure AD Connect**
4. Select **Manage cloud sync**

    ![Screenshot showing "Manage cloud sync" link.](media/how-to-configure/manage-1.png)

5. Select **New Configuration**

    ![Screenshot of Azure AD Connect cloud sync screen with "New configuration" link highlighted.](media/tutorial-single-forest/configure-1.png)

6. On the configuration screen, enter a **Notification email**, move the selector to **Enable** and select **Save**.

    ![Screenshot of Configure screen with Notification email filled in and Enable selected.](media/how-to-configure/configure-2.png)

7. The configuration status should now be **Healthy**.

    ![Screenshot of Azure AD Connect cloud sync screen showing Healthy status.](media/how-to-configure/manage-4.png)

## Verify users are created and synchronization is occurring

You'll now verify that the users that you had in our on-premises directory have been synchronized and now exist in our Azure AD tenant.  This process may take a few hours to complete.  To verify users are synchronized, do the following:

1. Sign in to the [Azure portal](https://portal.azure.com) and sign in with an account that has an Azure subscription.
2. On the left, select **Azure Active Directory**
3. Under **Manage**, select **Users**.
4. Verify that you see the new users in our tenant

## Test signing in with one of our users

1. Browse to [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Sign in with a user account that was created in our new tenant.  You'll need to sign in using the following format: (user@domain.onmicrosoft.com). Use the same password that the user uses to sign in on-premises.

   ![Screenshot that shows the my apps portal with a signed in users.](media/tutorial-single-forest/verify-1.png)

You have now successfully set up a hybrid identity environment that you can use to test and familiarize yourself with what Azure has to offer.

## Next steps 

- [What is provisioning?](../what-is-provisioning.md)
- [What is Azure AD Connect cloud sync?](what-is-cloud-sync.md)
