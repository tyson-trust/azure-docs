---
title: Pre-populate contact information for self-service password reset
description: Learn how to pre-populate contact information for users of Azure Active Directory self-service password reset (SSPR) so they can use the feature without completing a registration process.

services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/26/2023

ms.author: justinha
author: justinha
manager: amycolannino
ms.reviewer: tilarso

ms.collection: M365-identity-device-management 
ms.custom: has-azure-ad-ps-ref
---
# Pre-populate user authentication contact information for Azure Active Directory self-service password reset (SSPR)

To use Azure Active Directory (Azure AD) self-service password reset (SSPR), authentication information for a user must be present. Most organizations have users register their authentication data themselves while collecting information for MFA. Some organizations prefer to bootstrap this process through synchronization of authentication data that already exists in Active Directory Domain Services (AD DS). This synchronized data is made available to Azure AD and SSPR without requiring user interaction. When users need to change or reset their password, they can do so even if they haven't previously registered their contact information.

You can pre-populate authentication contact information if you meet the following requirements:

* You have properly formatted the data in your on-premises directory.
* You have configured [Azure AD Connect](../hybrid/connect/how-to-connect-install-express.md) for your Azure AD tenant.

Phone numbers must be in the format *+CountryCode PhoneNumber*, such  as *+1 4251234567*.

> [!NOTE]
> There must be a space between the country code and the phone number.
>
> Password reset doesn't support phone extensions. Even in the *+1 4251234567X12345* format, extensions are removed before the call is placed.

## Fields populated

If you use the default settings in Azure AD Connect, the following mappings are made to populate authentication contact information for SSPR:

| On-premises Active Directory | Azure AD     |
|------------------------------|--------------|
| telephoneNumber              | Office phone |
| mobile                       | Mobile phone |

After a user verifies their mobile phone number, the *Phone* field under **Authentication contact info** in Azure AD is also populated with that number.

## Authentication contact info

On the **Authentication methods** page for an Azure AD user in the Azure portal, a Global Administrator can manually set the authentication contact information. You can review existing methods under the *Usable authentication methods* section, or **+Add authentication methods**, as shown in the following example screenshot:

:::image type="content" source="media/howto-sspr-authenticationdata/user-authentication-contact-info.png" alt-text="Manage authentication methods from the Azure portal":::

The following considerations apply for this authentication contact info:

* If the *Phone* field is populated and *Mobile phone* is enabled in the SSPR policy, the user sees that number on the password reset registration page and during the password reset workflow.
* If the *Email* field is populated and *Email* is enabled in the SSPR policy, the user sees that email on the password reset registration page and during the password reset workflow.

## Security questions and answers

The security questions and answers are stored securely in your Azure AD tenant and are only accessible to users via My Security-Info's [Combined registration experience](https://aka.ms/mfasetup). Administrators can't see, set, or modify the contents of another users' questions and answers.

## What happens when a user registers

When a user registers, the registration page sets the following fields:

* **Authentication Phone**
* **Authentication Email**
* **Security Questions and Answers**

If you provided a value for *Mobile phone* or *Alternate email*, users can immediately use those values to reset their passwords, even if they haven't registered for the service.

Users also see those values when they register for the first time, and can modify them if they want to. After they successfully register, these values are persisted in the *Authentication Phone* and *Authentication Email* fields, respectively.

## Set and read the authentication data through PowerShell

The following fields can be set through PowerShell:

* *Alternate email*
* *Mobile phone*
* *Office phone*
    * Can only be set if you're not synchronizing with an on-premises directory.

> [!IMPORTANT]
> Azure AD PowerShell is planned for deprecation. You can start using [Microsoft Graph PowerShell](/powershell/microsoftgraph/overview) to interact with Azure AD as you would in Azure AD PowerShell, or use the [Microsoft Graph REST API for managing authentication methods](/graph/api/resources/authenticationmethods-overview).

### Use Microsoft Graph PowerShell

To get started, [download and install the Microsoft Graph PowerShell module](/powershell/microsoftgraph/overview).

To quickly install from recent versions of PowerShell that support `Install-Module`, run the following commands. The first line checks to see if the module is already installed:

```PowerShell
Get-Module Microsoft.Graph
Install-Module Microsoft.Graph
Select-MgProfile -Name "beta"
Connect-MgGraph -Scopes "User.ReadWrite.All"
```

After the module is installed, use the following steps to configure each field.

#### Set the authentication data with Microsoft Graph PowerShell

```PowerShell
Connect-MgGraph -Scopes "User.ReadWrite.All"

Update-MgUser -UserId 'user@domain.com' -otherMails @("emails@domain.com")
Update-MgUser -UserId 'user@domain.com' -mobilePhone "+1 4251234567"
Update-MgUser -UserId 'user@domain.com' -businessPhones "+1 4252345678"

Update-MgUser -UserId 'user@domain.com' -otherMails @("emails@domain.com") -mobilePhone "+1 4251234567" -businessPhones "+1 4252345678"
```

#### Read the authentication data with Microsoft Graph PowerShell

```PowerShell
Connect-MgGraph -Scopes "User.Read.All"

Get-MgUser -UserId 'user@domain.com' | select otherMails
Get-MgUser -UserId 'user@domain.com' | select mobilePhone
Get-MgUser -UserId 'user@domain.com' | select businessPhones

Get-MgUser -UserId 'user@domain.com' | Select businessPhones, mobilePhone, otherMails | Format-Table
```

### Use Azure AD PowerShell

To get started, [download and install the Azure AD version 2 PowerShell module](/powershell/module/azuread/).

To quickly install from recent versions of PowerShell that support `Install-Module`, run the following commands. The first line checks to see if the module is already installed:

```PowerShell
Get-Module AzureAD
Install-Module AzureAD
Connect-AzureAD
```

After the module is installed, use the following steps to configure each field.

#### Set the authentication data with Azure AD PowerShell version 2

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 4251234567"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 4252345678"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 4251234567" -TelephoneNumber "+1 4252345678"
```

#### Read the authentication data with Azure AD PowerShell version 2

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## Next steps

Once authentication contact information is pre-populated for users, complete the following tutorial to enable self-service password reset:

> [!div class="nextstepaction"]
> [Enable Azure AD self-service password reset](tutorial-enable-sspr.md)
