---
title: B2B collaboration user claims mapping
description: Customize the user claims that are issued in the SAML token for Azure Active Directory (Azure AD) B2B users.

services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 11/24/2022

ms.author: cmulligan
author: csmulligan
manager: celestedg


ms.collection: engagement-fy23, M365-identity-device-management
---

# B2B collaboration user claims mapping in Azure Active Directory

Azure Active Directory (Azure AD) supports customizing the claims that are issued in the SAML token for [B2B collaboration](what-is-b2b.md) users. When a user authenticates to the application, Azure AD issues a SAML token to the app that contains information (or claims) about the user that uniquely identifies them. By default, this claim includes the user's user name, email address, first name, and last name.

In the [Azure portal](https://portal.azure.com), you can view or edit the claims that are sent in the SAML token to the application. To access the settings, select **Azure Active Directory** > **Enterprise applications** > the application that's configured for single sign-on > **Single sign-on**. See the SAML token settings in the **User Attributes** section.

:::image type="content" source="media/claims-mapping/view-claims-in-saml-token-attributes.png" alt-text="Screenshot of the SAML token attributes in the UI.":::

There are two possible reasons why you might need to edit the claims that are issued in the SAML token:

1. The application requires a different set of claim URIs or claim values.

2. The application requires the NameIdentifier claim to be something other than the user principal name [(UPN)](../hybrid/connect/plan-connect-userprincipalname.md#what-is-userprincipalname) that's stored in Azure AD.

For information about how to add and edit claims, see [Customizing claims issued in the SAML token for enterprise applications in Azure Active Directory](../develop/saml-claims-customization.md).

For B2B collaboration users, mapping NameID and UPN cross-tenant are prevented for security reasons.

## Next steps

- For information about B2B collaboration user properties, see [Properties of an Azure Active Directory B2B collaboration user](user-properties.md).
- For information about user tokens for B2B collaboration users, see [Understand user tokens in Azure AD B2B collaboration](user-token.md).
