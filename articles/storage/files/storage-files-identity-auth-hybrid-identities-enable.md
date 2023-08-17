---
title: Use Azure Active Directory to access Azure file shares over SMB for hybrid identities using Kerberos authentication
description: Learn how to enable identity-based Kerberos authentication for hybrid user identities over Server Message Block (SMB) for Azure Files through Azure Active Directory (Azure AD). Your users can then access Azure file shares by using their Azure AD credentials.
author: khdownie
ms.service: azure-file-storage
ms.topic: how-to
ms.date: 08/03/2023
ms.author: kendownie
ms.custom: engagement-fy23
recommendations: false
---

# Enable Azure Active Directory Kerberos authentication for hybrid identities on Azure Files

This article focuses on enabling and configuring Azure Active Directory (Azure AD) for authenticating [hybrid user identities](../../active-directory/hybrid/whatis-hybrid-identity.md), which are on-premises AD DS identities that are synced to Azure AD. Cloud-only identities aren't currently supported.

This configuration allows hybrid users to access Azure file shares using Kerberos authentication, using Azure AD to issue the necessary Kerberos tickets to access the file share with the SMB protocol. This means your end users can access Azure file shares over the internet without requiring line-of-sight to domain controllers from hybrid Azure AD-joined and Azure AD-joined clients. However, configuring Windows access control lists (ACLs)/directory and file-level permissions for a user or group requires line-of-sight to the on-premises domain controller.

For more information on supported options and considerations, see [Overview of Azure Files identity-based authentication options for SMB access](storage-files-active-directory-overview.md). For more information about Azure AD Kerberos, see [Deep dive: How Azure AD Kerberos works](https://techcommunity.microsoft.com/t5/itops-talk-blog/deep-dive-how-azure-ad-kerberos-works/ba-p/3070889).

> [!IMPORTANT]
> You can only use one AD method for identity-based authentication with Azure Files. If Azure AD Kerberos authentication for hybrid identities doesn't fit your requirements, you might be able to use [on-premises Active Directory Domain Service (AD DS)](storage-files-identity-auth-active-directory-enable.md) or [Azure Active Directory Domain Services (Azure AD DS)](storage-files-identity-auth-domain-services-enable.md) instead. The configuration steps and supported scenarios are different for each method.

## Applies to
| File share type | SMB | NFS |
|-|:-:|:-:|
| Standard file shares (GPv2), LRS/ZRS | ![Yes](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Standard file shares (GPv2), GRS/GZRS | ![Yes](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Premium file shares (FileStorage), LRS/ZRS | ![Yes](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |

## Prerequisites

Before you enable Azure AD Kerberos authentication over SMB for Azure file shares, make sure you've completed the following prerequisites.

> [!NOTE]
> Your Azure storage account can't authenticate with both Azure AD and a second method like AD DS or Azure AD DS. If you've already chosen another AD method for your storage account, you must disable it before enabling Azure AD Kerberos.

The Azure AD Kerberos functionality for hybrid identities is only available on the following operating systems:

  - Windows 11 Enterprise/Pro single or multi-session.
  - Windows 10 Enterprise/Pro single or multi-session, versions 2004 or later with the latest cumulative updates installed, especially the [KB5007253 - 2021-11 Cumulative Update Preview for Windows 10](https://support.microsoft.com/topic/november-22-2021-kb5007253-os-builds-19041-1387-19042-1387-19043-1387-and-19044-1387-preview-d1847be9-46c1-49fc-bf56-1d469fc1b3af).
  - Windows Server, version 2022 with the latest cumulative updates installed, especially the [KB5007254 - 2021-11 Cumulative Update Preview for Microsoft server operating system version 21H2](https://support.microsoft.com/topic/november-22-2021-kb5007254-os-build-20348-380-preview-9a960291-d62e-486a-adcc-6babe5ae6fc1).

To learn how to create and configure a Windows VM and log in by using Azure AD-based authentication, see [Log in to a Windows virtual machine in Azure by using Azure AD](../../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md).

Clients must be Azure AD-joined or [hybrid Azure AD-joined](../../active-directory/devices/hybrid-join-plan.md). Azure AD Kerberos isn’t supported on clients joined to Azure AD DS or joined to AD only.

This feature doesn't currently support user accounts that you create and manage solely in Azure AD. User accounts must be [hybrid user identities](../../active-directory/hybrid/whatis-hybrid-identity.md), which means you'll also need AD DS and either [Azure AD Connect](../../active-directory/hybrid/whatis-azure-ad-connect.md) or [Azure AD Connect cloud sync](../../active-directory/cloud-sync/what-is-cloud-sync.md). You must create these accounts in Active Directory and sync them to Azure AD. To assign Azure Role-Based Access Control (RBAC) permissions for the Azure file share to a user group, you must create the group in Active Directory and sync it to Azure AD.

You must disable multi-factor authentication (MFA) on the Azure AD app representing the storage account.

With Azure AD Kerberos, the Kerberos ticket encryption is always AES-256. But you can set the SMB channel encryption that best fits your needs.

## Regional availability

This feature is supported in the [Azure Public, Azure US Gov, and Azure China 21Vianet clouds](https://azure.microsoft.com/global-infrastructure/locations/).

## Enable Azure AD Kerberos authentication for hybrid user accounts

You can enable Azure AD Kerberos authentication on Azure Files for hybrid user accounts using the Azure portal, PowerShell, or Azure CLI.

# [Portal](#tab/azure-portal)

To enable Azure AD Kerberos authentication using the [Azure portal](https://portal.azure.com), follow these steps.

1. Sign in to the Azure portal and select the storage account you want to enable Azure AD Kerberos authentication for.
1. Under **Data storage**, select **File shares**.
1. Next to **Active Directory**, select the configuration status (for example, **Not configured**).
 
   :::image type="content" source="media/storage-files-identity-auth-hybrid-identities-enable/configure-active-directory.png" alt-text="Screenshot of the Azure portal showing file share settings for a storage account. Active Directory configuration settings are selected." lightbox="media/storage-files-identity-auth-hybrid-identities-enable/configure-active-directory.png" border="true":::

1. Under **Azure AD Kerberos**, select **Set up**.
1. Select the **Azure AD Kerberos** checkbox.

   :::image type="content" source="media/storage-files-identity-auth-hybrid-identities-enable/enable-azure-ad-kerberos.png" alt-text="Screenshot of the Azure portal showing Active Directory configuration settings for a storage account. Azure AD Kerberos is selected." lightbox="media/storage-files-identity-auth-hybrid-identities-enable/enable-azure-ad-kerberos.png" border="true":::

1. **Optional:** If you want to configure directory and file-level permissions through Windows File Explorer, then you need to specify the domain name and domain GUID for your on-premises AD. You can get this information from your domain admin or by running the following Active Directory PowerShell cmdlet from an on-premises AD-joined client: `Get-ADDomain`. Your domain name should be listed in the output under `DNSRoot` and your domain GUID should be listed under `ObjectGUID`. If you'd prefer to configure directory and file-level permissions using icacls, you can skip this step. However, if you want to use icacls, the client will need line-of-sight to the on-premises AD.

1. Select **Save**.

# [Azure PowerShell](#tab/azure-powershell)

To enable Azure AD Kerberos using Azure PowerShell, run the following command. Remember to replace placeholder values, including brackets, with your values.

```azurepowershell
Set-AzStorageAccount -ResourceGroupName <resourceGroupName> -StorageAccountName <storageAccountName> -EnableAzureActiveDirectoryKerberosForFile $true
```

**Optional:** If you want to configure directory and file-level permissions through Windows File Explorer, then you also need to specify the domain name and domain GUID for your on-premises AD. If you'd prefer to configure directory and file-level permissions using icacls, you can skip this step. However, if you want to use icacls, the client will need line-of-sight to the on-premises AD.

You can get this information from your domain admin or by running the following Active Directory PowerShell cmdlets from an on-premises AD-joined client:

```PowerShell
$domainInformation = Get-ADDomain
$domainGuid = $domainInformation.ObjectGUID.ToString()
$domainName = $domainInformation.DnsRoot
```

To specify the domain name and domain GUID for your on-premises AD, run the following Azure PowerShell command. Remember to replace placeholder values, including brackets, with your values.

```azurepowershell
Set-AzStorageAccount -ResourceGroupName <resourceGroupName> -StorageAccountName <storageAccountName> -EnableAzureActiveDirectoryKerberosForFile $true -ActiveDirectoryDomainName $domainName -ActiveDirectoryDomainGuid $domainGuid
```

# [Azure CLI](#tab/azure-cli)
  
To enable Azure AD Kerberos using Azure CLI, run the following command. Remember to replace placeholder values, including brackets, with your values.

```azurecli
az storage account update --name <storageaccountname> --resource-group <resourcegroupname> --enable-files-aadkerb true
```

**Optional:** If you want to configure directory and file-level permissions through Windows File Explorer, then you also need to specify the domain name and domain GUID for your on-premises AD. If you'd prefer to configure directory and file-level permissions using icacls, you can skip this step. However, if you want to use icacls, the client will need line-of-sight to the on-premises AD.

You can get this information from your domain admin or by running the following Active Directory PowerShell cmdlets from an on-premises AD-joined client:

```PowerShell
$domainInformation = Get-ADDomain
$domainGuid = $domainInformation.ObjectGUID.ToString()
$domainName = $domainInformation.DnsRoot
```

To specify the domain name and domain GUID for your on-premises AD, run the following command. Remember to replace placeholder values, including brackets, with your values.

```azurecli
az storage account update --name <storageAccountName> --resource-group <resourceGroupName> --enable-files-aadkerb true --domain-name <domainName> --domain-guid <domainGuid>
```

---

> [!WARNING]
> If you've previously enabled Azure AD Kerberos authentication through manual limited preview steps to store FSLogix profiles on Azure Files for Azure AD-joined VMs, the password for the storage account's service principal is set to expire every six months. Once the password expires, users won't be able to get Kerberos tickets to the file share. To mitigate this, see "Error - Service principal password has expired in Azure AD" under [Potential errors when enabling Azure AD Kerberos authentication for hybrid users](/troubleshoot/azure/azure-storage/files-troubleshoot-smb-authentication?toc=/azure/storage/files/toc.json#potential-errors-when-enabling-azure-ad-kerberos-authentication-for-hybrid-users).

## Grant admin consent to the new service principal

After enabling Azure AD Kerberos authentication, you'll need to explicitly grant admin consent to the new Azure AD application registered in your Azure AD tenant. This service principal is auto-generated and isn't used for authorization to the file share, so don't make any edits to the service principal other than those documented here. If you do, you might get an error.

You can configure the API permissions from the [Azure portal](https://portal.azure.com) by following these steps:

1. Open **Azure Active Directory**.
2. Select **App registrations** on the left pane.
3. Select **All Applications**.

   :::image type="content" source="media/storage-files-identity-auth-hybrid-identities-enable/azure-portal-azuread-app-registrations.png" alt-text="Screenshot of the Azure portal. Azure Active Directory is open. App registrations is selected in the left pane. All applications is highlighted in the right pane." lightbox="media/storage-files-identity-auth-hybrid-identities-enable/azure-portal-azuread-app-registrations.png":::

4. Select the application with the name matching **[Storage Account] `<your-storage-account-name>`.file.core.windows.net**.
5. Select **API permissions** in the left pane.
6. Select **Grant admin consent for [Directory Name]** to grant consent for the three requested API permissions (openid, profile, and User.Read) for all accounts in the directory.
7. Select **Yes** to confirm.

  > [!IMPORTANT]
  > If you're connecting to a storage account via a private endpoint/private link using Azure AD Kerberos authentication, you'll also need to add the private link FQDN to the storage account's Azure AD application. For instructions, see the entry in our [troubleshooting guide](/troubleshoot/azure/azure-storage/files-troubleshoot-smb-authentication?toc=/azure/storage/files/toc.json#error-1326---the-username-or-password-is-incorrect-when-using-private-link).

## Disable multi-factor authentication on the storage account

Azure AD Kerberos doesn't support using MFA to access Azure file shares configured with Azure AD Kerberos. You must exclude the Azure AD app representing your storage account from your MFA conditional access policies if they apply to all apps.

The storage account app should have the same name as the storage account in the conditional access exclusion list. When searching for the storage account app in the conditional access exclusion list, search for: **[Storage Account] `<your-storage-account-name>`.file.core.windows.net**

Remember to replace `<your-storage-account-name>` with the proper value.

  > [!IMPORTANT]
  > If you don't exclude MFA policies from the storage account app, you won't be able to access the file share. Trying to map the file share using `net use` will result in an error message that says "System error 1327: Account restrictions are preventing this user from signing in. For example: blank passwords aren't allowed, sign-in times are limited, or a policy restriction has been enforced."

For guidance on disabling MFA, see the following:

- [Add exclusions for service principals of Azure resources](../../active-directory/conditional-access/howto-conditional-access-policy-all-users-mfa.md#user-exclusions)
- [Create a conditional access policy](../../active-directory/conditional-access/howto-conditional-access-policy-all-users-mfa.md#create-a-conditional-access-policy)

## Assign share-level permissions

When you enable identity-based access, you can set for each share which users and groups have access to that particular share. Once a user is allowed into a share, Windows ACLs (also called NTFS permissions) on individual files and directories take over. This allows for fine-grained control over permissions, similar to an SMB share on a Windows server.

To set share-level permissions, follow the instructions in [Assign share-level permissions to an identity](storage-files-identity-ad-ds-assign-permissions.md).

## Configure directory and file-level permissions

Once share-level permissions are in place, you can assign directory/file-level permissions to the user or group. **This requires using a device with line-of-sight to an on-premises AD**. To use Windows File Explorer, the device also needs to be domain-joined.  

There are two options for configuring directory and file-level permissions with Azure AD Kerberos authentication:

- **Windows File Explorer:** If you choose this option, then the client must be domain-joined to the on-premises AD.
- **icacls utility:** If you choose this option, then the client doesn't need to be domain-joined, but needs line-of-sight to the on-premises AD.

To configure directory and file-level permissions through Windows File Explorer, you also need to specify domain name and domain GUID for your on-premises AD. You can get this information from your domain admin or from an on-premises AD-joined client. If you prefer to configure using icacls, this step is not required.

> [!TIP]
> If Azure AD hybrid joined users from two different forests will be accessing the share, it's best to use icacls to configure directory and file-level permissions. This is because Windows File Explorer ACL configuration requires the client to be domain joined to the Active Directory domain that the storage account is joined to.

To configure directory and file-level permissions, follow the instructions in [Configure directory and file-level permissions over SMB](storage-files-identity-ad-ds-configure-permissions.md).

## Configure the clients to retrieve Kerberos tickets

Enable the Azure AD Kerberos functionality on the client machine(s) you want to mount/use Azure File shares from. You must do this on every client on which Azure Files will be used.

Use one of the following three methods:

- Configure this Intune [Policy CSP](/windows/client-management/mdm/policy-configuration-service-provider) and apply it to the client(s): [Kerberos/CloudKerberosTicketRetrievalEnabled](/windows/client-management/mdm/policy-csp-kerberos#kerberos-cloudkerberosticketretrievalenabled)
- Configure this group policy on the client(s): `Administrative Templates\System\Kerberos\Allow retrieving the Azure AD Kerberos Ticket Granting Ticket during logon`
- Create the following registry value on the client(s): `reg add HKLM\SYSTEM\CurrentControlSet\Control\Lsa\Kerberos\Parameters /v CloudKerberosTicketRetrievalEnabled /t REG_DWORD /d 1`

Changes are not instant, and require a policy refresh or a reboot to take effect.

> [!IMPORTANT]
> Once this change is applied, the client(s) won't be able to connect to storage accounts that are configured for on-premises AD DS integration without configuring Kerberos realm mappings. If you want the client(s) to be able to connect to storage accounts configured for AD DS as well as storage accounts configured for Azure AD Kerberos, follow the steps in [Configure coexistence with storage accounts using on-premises AD DS](#configure-coexistence-with-storage-accounts-using-on-premises-ad-ds).

### Configure coexistence with storage accounts using on-premises AD DS

If you want to enable client machines to connect to storage accounts that are configured for AD DS as well as storage accounts configured for Azure AD Kerberos, follow these steps. If you're only using Azure AD Kerberos, skip this section.

Add an entry for each storage account that uses on-premises AD DS integration. Use one of the following three methods to configure Kerberos realm mappings:

- Configure this Intune [Policy CSP](/windows/client-management/mdm/policy-configuration-service-provider) and apply it to the client(s): [Kerberos/HostToRealm](/windows/client-management/mdm/policy-csp-admx-kerberos#hosttorealm)
- Configure this group policy on the client(s): `Administrative Template\System\Kerberos\Define host name-to-Kerberos realm mappings`
- Run the `ksetup` Windows command on the client(s): `ksetup /addhosttorealmmap <hostname> <realmname>`
  - For example, `ksetup /addhosttorealmmap <your storage account name>.file.core.windows.net contoso.local`

Changes aren't instant, and require a policy refresh or a reboot to take effect.

## Disable Azure AD authentication on your storage account

If you want to use another authentication method, you can disable Azure AD authentication on your storage account by using the Azure portal, Azure PowerShell, or Azure CLI.

> [!NOTE]
> Disabling this feature means that there will be no Active Directory configuration for file shares in your storage account until you enable one of the other Active Directory sources to reinstate your Active Directory configuration.

# [Portal](#tab/azure-portal)

To disable Azure AD Kerberos authentication on your storage account by using the Azure portal, follow these steps.

1. Sign in to the Azure portal and select the storage account you want to disable Azure AD Kerberos authentication for.
1. Under **Data storage**, select **File shares**.
1. Next to **Active Directory**, select the configuration status.
1. Under **Azure AD Kerberos**, select **Configure**.
1. Uncheck the **Azure AD Kerberos** checkbox.
1. Select **Save**.

# [Azure PowerShell](#tab/azure-powershell)

To disable Azure AD Kerberos authentication on your storage account by using Azure PowerShell, run the following command. Remember to replace placeholder values, including brackets, with your values.

```azurepowershell
Set-AzStorageAccount -ResourceGroupName <resourceGroupName> -StorageAccountName <storageAccountName> -EnableAzureActiveDirectoryKerberosForFile $false
```

# [Azure CLI](#tab/azure-cli)

To disable Azure AD Kerberos authentication on your storage account by using Azure CLI, run the following command. Remember to replace placeholder values, including brackets, with your values.

```azurecli
az storage account update --name <storageaccountname> --resource-group <resourcegroupname> --enable-files-aadkerb false
```

---

## Next steps

For more information, see these resources:

- [Potential errors when enabling Azure AD Kerberos authentication for hybrid users](files-troubleshoot-smb-authentication.md#potential-errors-when-enabling-azure-ad-kerberos-authentication-for-hybrid-users)
- [Overview of Azure Files identity-based authentication support for SMB access](storage-files-active-directory-overview.md)
- [Create a profile container with Azure Files and Azure Active Directory](../../virtual-desktop/create-profile-container-azure-ad.md)
- [FAQ](storage-files-faq.md)
