---
title: 'What is Azure AD Connect v2.0?'
description: Learn about the next version of Azure AD Connect.
services: active-directory
author: billmath
manager: amycolannino
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 01/19/2023
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management, has-adal-ref
---

# Introduction to Azure AD Connect V2.0 

Azure AD Connect was released several years ago.  Since this time, several of the components that Azure AD Connect uses have been scheduled for deprecation and updated to newer versions.  Attempting to update all of these components individually would take time and planning. 

To address this issue, we've bundled as many of these newer components into a new, single release, so you only have to update once. This release is Azure AD Connect V2.  This release is a new version of the same software used to accomplish your hybrid identity goals, built using the latest foundational components. 

 >[!NOTE]
 >Azure AD Connect V1 has been retired as of August 31, 2022 and is no longer supported. Azure AD Connect V1 installations may **stop working unexpectedly**. If you are still using a Azure AD Connect V1 you need to upgrade to Azure AD Connect V2 immediately.

## Consider moving to Azure AD Connect cloud sync
Azure AD Connect cloud sync is the future of synchronization for Microsoft.  It replaces Azure AD Connect.  

> [!VIDEO https://www.youtube.com/embed/9T6lKEloq0Q]

Before moving the Azure AD Connect V2.0, you should consider moving to cloud sync.  You can see if cloud sync is right for you, by accessing the [Check sync tool](https://aka.ms/EvaluateSyncOptions) from the portal or via the link provided.

For more information, see [What is cloud sync?](../cloud-sync/what-is-cloud-sync.md)



## What are the major changes? 

### SQL Server 2019 LocalDB 

The previous versions of Azure AD Connect shipped with a SQL Server 2012 LocalDB. V2.0 ships with a SQL Server 2019 LocalDB, which promises enhanced stability and performance and has several security-related bug fixes. SQL Server 2012 will go out of extended support in July 2022. For more information, see [Microsoft SQL 2019](https://www.microsoft.com/sql-server/sql-server-2019).

### MSAL authentication library 

The previous versions of Azure AD Connect shipped with the ADAL authentication library. This library will be deprecated after December 2022. The V2 release ships with the newer MSAL library. For more information, see [Overview of the MSAL library](../../develop/msal-overview.md).

### Visual C++ Redist 14 

SQL Server 2019 requires the Visual C++ Redist 14 runtime, so we're updating the C++ runtime library to use this version. This Redistributable is installed with the Azure AD Connect V2 package, so you don't have to take any action for the C++ runtime update. 

### TLS 1.2 

TLS1.0 and TLS 1.1 are protocols that are deemed unsafe. Microsoft is deprecating them. This release of Azure AD Connect only supports TLS 1.2. 
All versions of Windows Server that are supported for Azure AD Connect V2 already default to TLS 1.2. If your server doesn't support TLS 1.2 you will need to enable this before you can deploy Azure AD Connect V2. For more information, see [TLS 1.2 enforcement for Azure AD Connect](reference-connect-tls-enforcement.md).

### All binaries signed with SHA2 

We noticed that some components had SHA1 signed binaries. We no longer support SHA1 for downloadable binaries and we upgraded all binaries to SHA2 signing. The digital signatures are used to ensure that the updates come directly from Microsoft and were not tampered with during delivery. Because of weaknesses in the SHA-1 algorithm and to align to industry standards, we've changed the signing of Windows updates to use the more secure SHA-2 algorithm."  

There is no action needed from your side. 

### Windows Server 2012 and Windows Server 2012 R2 are no longer supported 

SQL Server 2019 requires Windows Server 2016 or newer as a server operating system. Since Azure AD Connect v2 contains SQL Server 2019 components, we no longer can support older Windows Server versions.  

You can't install this version on an older Windows Server version. We suggest you upgrade your Azure AD Connect server to Windows Server 2019, which is the most recent version of the Windows Server operating system. 

This [article](/windows-server/get-started-19/install-upgrade-migrate-19) describes the upgrade from older Windows Server versions to Windows Server 2019. 

### PowerShell 5.0 

This release of Azure AD Connect contains several cmdlets that require PowerShell 5.0, so this requirement is a new prerequisite for Azure AD Connect.  

More details about PowerShell prerequisites can be found [here](/powershell/scripting/windows-powershell/install/windows-powershell-system-requirements#windows-powershell-50).

 >[!NOTE]
 >PowerShell 5 is already part of Windows Server 2016 so you probably don't have to take action as long as you're on a recent Window Server version. 

## What else do I need to know? 

**Why is this upgrade important for me?** </br>
Next year several of the components in your current Azure AD Connect server installations will no longer be supported. If you are using unsupported products, it will be harder for our support team to provide you with the support experience your organization requires. So we recommend all customers to upgrade to this newer version as soon as they can. 

This upgrade is especially important since we've had to update our prerequisites for Azure AD Connect and you may need additional time to plan and update your servers to the newer versions of these prerequisites 

**Is there any new functionality I need to know about?** </br>
No – the V2.0 release doesn't contain any new functionality. This release only contains updates of some of the foundational components on Azure AD Connect. However, later releases of Azure AD Connect V2 may contain new functionality.

**Can I upgrade from any previous version to V2?** </br>
Yes – upgrades from any previous version of Azure AD Connect to Azure AD Connect V2 is supported. Please follow the guidance in [this article](how-to-upgrade-previous-version.md) to determine what is the best upgrade strategy for you. 

**Can I export the configuration of my current server and import it in Azure AD Connect V2?** </br>
Yes, you can do that, and it is a great way to migrate to Azure AD Connect V2 – especially if you are also upgrading to a new operating system version. You can read more about the Import/export configuration feature and how you can use it in this [article](how-to-connect-import-export-config.md). 

**I have enabled auto upgrade for Azure AD Connect – will I get this new version automatically?** </br> 
Yes - your Azure AD Connect server will be upgraded to the latest release if you have enabled the auto-upgrade feature. However, we can only upgrade your server if you are using Windows Server 2016 or newer and have enabled TLS 1.2.

**I am not ready to upgrade yet – how much time do I have?** </br>
You should upgrade to Azure AD Connect V2 as soon as you can. **__All Azure AD Connect V1 versions have been retired on 31 August, 2022.__** For the time being we will continue to support older versions of Azure AD Connect, but it may prove difficult to provide a good support experience if some of the components in Azure AD Connect have dropped out of support. This upgrade is particularly important for ADAL and TLS1.0/1.1 as these services might stop working unexpectedly after they are deprecated. 

**I use an external SQL database and don't use SQL 2012 LocalDb – do I still have to upgrade?** </br>
Yes, you still need to upgrade to remain in a supported state even if you don't use SQL Server 2012, due to the TLS1.0/1.1 and ADAL deprecation. Note that SQL Server 2012 can still be used as an external SQL database with Azure AD Connect V2.  The SQL 2019 drivers in Azure AD Connect V2 are compatible with SQL Server 2012.

**After the upgrade of my Azure AD Connect instance to V2, will the SQL 2012 components automatically get uninstalled?** </br>
No, the upgrade to SQL 2019 doesn't remove any SQL 2012 components from your server. If you no longer need these components then you should follow [the SQL Server uninstallation instructions](/sql/sql-server/install/uninstall-an-existing-instance-of-sql-server-setup).

**What happens if I don't upgrade?** </br>
Until one of the components that are being retired are actually deprecated, you will not see any impact. Azure AD Connect will keep on working. 

Support for TLS 1.0/1.1 is deprecated in 2022, and you need to make sure you aren't using these protocols by that date as your service may stop working unexpectedly. You can manually configure your server for TLS 1.2 though, and that doesn't require an update of Azure AD Connect to V2 

Azure AD Connect Health may stop working after March 2023. We will auto upgrade all Health agents to a new version before that, but we cannot auto upgrade if you are running AADConnect V1 due to compatibility issues with V versions.

After December 2022, ADAL is planned to go out of support. When ADAL goes out of support, authentication may stop working unexpectedly, and this will block the Azure AD Connect server from working properly. We strongly advise you to upgrade to Azure AD Connect V2 before December 2022. You can't upgrade to a supported authentication library with your current Azure AD Connect version. 

**After upgrading to 2 the ADSync PowerShell cmdlets don't work?** </br>
This is a known issue. Restart your PowerShell session after installing or upgrading to version 2 and then reimport the module. Use the following instructions to import the module.
 
 1.  Open Windows PowerShell with administrative privileges.
 1.  Type or copy and paste the following code: 
 
     ```powershell
     Import-module -Name "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync"
     ```

## License requirements for using Azure AD Connect V2

[!INCLUDE [active-directory-free-license.md](../../../../includes/active-directory-free-license.md)]

## License requirements for using Azure AD Connect Health
[!INCLUDE [active-directory-free-license.md](../../../../includes/active-directory-p1-license.md)]

## Next steps

- [Hardware and prerequisites](how-to-connect-install-prerequisites.md) 
- [Express settings](how-to-connect-install-express.md)
- [Customized settings](how-to-connect-install-custom.md)
