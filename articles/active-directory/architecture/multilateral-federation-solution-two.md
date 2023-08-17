---
title: 'Solution 2: Azure AD with Shibboleth as a SAML proxy'
description: This article describes design considerations for using Azure AD with Shibboleth as a SAML proxy as a multilateral federation solution for universities.
services: active-directory
author: janicericketts
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2023
ms.author: jricketts
ms.custom: "it-pro"
ms.collection: M365-identity-device-management
---

# Solution 2: Azure AD with Shibboleth as a SAML proxy

In Solution 2, Azure Active Directory (Azure AD) acts as the primary identity provider (IdP). The federation provider acts as a Security Assertion Markup Language (SAML) proxy to the Central Authentication Service (CAS) apps and the multilateral federation apps. In this example, [Shibboleth acts as the SAML proxy](https://shibboleth.atlassian.net/wiki/spaces/KB/pages/1467056889/Using+SAML+Proxying+in+the+Shibboleth+IdP+to+connect+with+Azure+AD) to provide a reference link.

[![Diagram that shows Shibboleth used as a SAML proxy provider.](media/multilateral-federation-solution-two/azure-ad-shibboleth-as-sp-proxy.png)](media/multilateral-federation-solution-two/azure-ad-shibboleth-as-sp-proxy.png#lightbox)

Because Azure AD is the primary IdP, all student and faculty apps are integrated with Azure AD. All Microsoft 365 apps are also integrated with Azure AD. If Azure Active Directory Domain Services is in use, it also is synchronized with Azure AD.

The SAML proxy feature of Shibboleth integrates with Azure AD. In Azure AD, Shibboleth appears as a non-gallery enterprise application. Universities can get single sign-on (SSO) for their CAS apps and can participate in the InCommon environment. Additionally, Shibboleth provides integration for Lightweight Directory Access Protocol (LDAP) directory services.

## Advantages

Advantages of using this solution include:

* **Cloud authentication for all apps**: All apps authenticate through Azure AD.

* **Ease of execution**: This solution provides short-term ease of execution for universities that are already using Shibboleth.

## Considerations and trade-offs

Here are some of the trade-offs of using this solution:

* **Higher complexity and security risk**: An on-premises footprint might mean higher complexity for the environment and extra security risks, compared to a managed service. Increased overhead and fees might also be associated with managing on-premises components.

* **Suboptimal authentication experience**: For multilateral federation and CAS apps, the authentication experience for users might not be seamless because of redirects through Shibboleth. The options for customizing the authentication experience for users are limited.

* **Limited third-party multifactor authentication (MFA) integration**: The number of integrations available to third-party MFA solutions might be limited.

* **No granular Conditional Access support**: Without granular Conditional Access support, you have to choose between the least common denominator (optimize for less friction but have limited security controls) or the highest common denominator (optimize for security controls at the expense of user friction). Your ability to make granular decisions is limited.

## Migration resources

The following resources can help with your migration to this solution architecture.

| Migration resource   | Description           |
| - | - |
| [Resources for migrating applications to Azure Active Directory](../manage-apps/migration-resources.md) | List of resources to help you migrate application access and authentication to Azure AD |
| [Configuring Shibboleth as a SAML proxy](https://shibboleth.atlassian.net/wiki/spaces/KB/pages/1467056889/Using+SAML+Proxying+in+the+Shibboleth+IdP+to+connect+with+Azure+AD) | Shibboleth article that describes how to use the SAML proxying feature to connect the Shibboleth IdP to Azure AD |
| [Azure AD Multi-Factor Authentication deployment considerations](../authentication/howto-mfa-getstarted.md) | Guidance for configuring Azure AD Multi-Factor Authentication |

## Next steps

See these related articles about multilateral federation:

[Multilateral federation introduction](multilateral-federation-introduction.md)

[Multilateral federation baseline design](multilateral-federation-baseline.md)

[Multilateral federation Solution 1: Azure AD with Cirrus Bridge](multilateral-federation-solution-one.md)

[Multilateral federation Solution 3: Azure AD with AD FS and Shibboleth](multilateral-federation-solution-three.md)

[Multilateral federation decision tree](multilateral-federation-decision-tree.md)
