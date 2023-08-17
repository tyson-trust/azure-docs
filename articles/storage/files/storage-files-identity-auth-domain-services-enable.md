---
title: Use Azure Active Directory Domain Services (Azure AD DS) to authorize user access to Azure Files over SMB
description: Learn how to enable identity-based authentication over Server Message Block (SMB) for Azure Files through Azure Active Directory Domain Services (Azure AD DS). Your domain-joined Windows VMs can then access Azure file shares by using Azure AD credentials.
author: khdownie
ms.service: azure-file-storage
ms.topic: how-to
ms.date: 07/17/2023
ms.author: kendownie
ms.custom: engagement-fy23, devx-track-azurecli, devx-track-azurepowershell
recommendations: false
---

# Enable Azure Active Directory Domain Services authentication on Azure Files
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

This article focuses on enabling and configuring Azure AD DS for identity-based authentication with Azure file shares. In this authentication scenario, Azure AD credentials and Azure AD DS credentials are the same and can be used interchangeably.

We strongly recommend that you review the [How it works section](./storage-files-active-directory-overview.md#how-it-works) to select the right AD source for authentication. The setup is different depending on the AD source you choose.

If you're new to Azure Files, we recommend reading our [planning guide](storage-files-planning.md) before reading this article.

> [!NOTE]
> Azure Files supports Kerberos authentication with Azure AD DS with RC4-HMAC and AES-256 encryption. We recommend using AES-256.
>
> Azure Files supports authentication for Azure AD DS with full or partial (scoped) synchronization with Azure AD. For environments with scoped synchronization present, administrators should be aware that Azure Files only honors Azure RBAC role assignments granted to principals that are synchronized. Role assignments granted to identities not synchronized from Azure AD to Azure AD DS will be ignored by the Azure Files service.

## Applies to
| File share type | SMB | NFS |
|-|:-:|:-:|
| Standard file shares (GPv2), LRS/ZRS | ![Yes](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Standard file shares (GPv2), GRS/GZRS | ![Yes](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Premium file shares (FileStorage), LRS/ZRS | ![Yes](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |

## Prerequisites

Before you enable Azure AD DS over SMB for Azure file shares, make sure you've completed the following prerequisites:

1.  **Select or create an Azure AD tenant.**

    You can use a new or existing tenant. The tenant and the file share that you want to access must be associated with the same subscription.

    To create a new Azure AD tenant, you can [Add an Azure AD tenant and an Azure AD subscription](/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). If you have an existing Azure AD tenant but want to create a new tenant for use with Azure file shares, see [Create an Azure Active Directory tenant](/rest/api/datacatalog/create-an-azure-active-directory-tenant).

1.  **Enable Azure AD Domain Services on the Azure AD tenant.**

    To support authentication with Azure AD credentials, you must enable Azure AD DS for your Azure AD tenant. If you aren't the administrator of the Azure AD tenant, contact the administrator and follow the step-by-step guidance to [Enable Azure Active Directory Domain Services using the Azure portal](../../active-directory-domain-services/tutorial-create-instance.md).

    It typically takes about 15 minutes for an Azure AD DS deployment to complete. Verify that the health status of Azure AD DS shows **Running**, with password hash synchronization enabled, before proceeding to the next step.

1.  **Domain-join an Azure VM with Azure AD DS.**

    To access an Azure file share by using Azure AD credentials from a VM, your VM must be domain-joined to Azure AD DS. For more information about how to domain-join a VM, see [Join a Windows Server virtual machine to a managed domain](../../active-directory-domain-services/join-windows-vm.md). Azure AD DS authentication over SMB with Azure file shares is supported only on Azure VMs running on OS versions above Windows 7 or Windows Server 2008 R2.

    > [!NOTE]
    > Non-domain-joined VMs can access Azure file shares using Azure AD DS authentication only if the VM has line-of-sight to the domain controllers for Azure AD DS. Usually this requires either site-to-site or point-to-site VPN.

1.  **Select or create an Azure file share.**

    Select a new or existing file share that's associated with the same subscription as your Azure AD tenant. For information about creating a new file share, see [Create a file share in Azure Files](storage-how-to-create-file-share.md).
    For optimal performance, we recommend that your file share be in the same region as the VM from which you plan to access the share.

1.  **Verify Azure Files connectivity by mounting Azure file shares using your storage account key.**

    To verify that your VM and file share are properly configured, try mounting the file share using your storage account key. For more information, see [Mount an Azure file share and access the share in Windows](storage-how-to-use-files-windows.md).

## Regional availability

Azure Files authentication with Azure AD DS is available in [all Azure Public, Gov, and China regions](https://azure.microsoft.com/global-infrastructure/locations/).

## Overview of the workflow

Before you enable Azure AD DS authentication over SMB for Azure file shares, verify that your Azure AD and Azure Storage environments are properly configured. We recommend that you walk through the [prerequisites](#prerequisites) to make sure you've completed all the required steps.

Follow these steps to grant access to Azure Files resources with Azure AD credentials:

1. Enable Azure AD DS authentication over SMB for your storage account to register the storage account with the associated Azure AD DS deployment.
1. Assign share-level permissions to an Azure AD identity (a user, group, or service principal).
1. Connect to your Azure file share using a storage account key and configure Windows access control lists (ACLs) for directories and files.
1. Mount an Azure file share from a domain-joined VM.

The following diagram illustrates the end-to-end workflow for enabling Azure AD DS authentication over SMB for Azure Files.

![Diagram showing Azure AD over SMB for Azure Files workflow](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## Enable Azure AD DS authentication for your account

To enable Azure AD DS authentication over SMB for Azure Files, you can set a property on storage accounts by using the Azure portal, Azure PowerShell, or Azure CLI. Setting this property implicitly "domain joins" the storage account with the associated Azure AD DS deployment. Azure AD DS authentication over SMB is then enabled for all new and existing file shares in the storage account.

Keep in mind that you can enable Azure AD DS authentication over SMB only after you've successfully deployed Azure AD DS to your Azure AD tenant. For more information, see the [prerequisites](#prerequisites).

# [Portal](#tab/azure-portal)

To enable Azure AD DS authentication over SMB with the [Azure portal](https://portal.azure.com), follow these steps:

1. In the Azure portal, go to your existing storage account, or [create a storage account](../common/storage-account-create.md).
1. In the **File shares** section, select **Active directory: Not Configured**.

    :::image type="content" source="media/storage-files-active-directory-enable/files-azure-ad-enable-storage-account-identity.png" alt-text="Screenshot of the File shares pane in your storage account, Active directory is highlighted." lightbox="media/storage-files-active-directory-enable/files-azure-ad-enable-storage-account-identity.png":::

1. Select **Azure Active Directory Domain Services** then enable the feature by ticking the checkbox.
1. Select **Save**.

    :::image type="content" source="media/storage-files-active-directory-enable/files-azure-ad-ds-highlight.png" alt-text="Screenshot of the Active Directory pane, Azure Active Directory Domain Services is enabled." lightbox="media/storage-files-active-directory-enable/files-azure-ad-ds-highlight.png":::

# [PowerShell](#tab/azure-powershell)

To enable Azure AD DS authentication over SMB with Azure PowerShell, install the latest Az module (2.4 or newer) or the Az.Storage module (1.5 or newer). For more information about installing PowerShell, see [Install Azure PowerShell on Windows with PowerShellGet](/powershell/azure/install-azure-powershell).

To create a new storage account, call [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount), and then set the **EnableAzureActiveDirectoryDomainServicesForFile** parameter to **true**. In the following example, remember to replace the placeholder values with your own values. (If you were using the previous preview module, the parameter for enabling the feature is **EnableAzureFilesAadIntegrationForSMB**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

To enable this feature on existing storage accounts, use the following command:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


# [Azure CLI](#tab/azure-cli)

To enable Azure AD authentication over SMB with Azure CLI, install the latest CLI version (Version 2.0.70 or newer). For more information about installing Azure CLI, see [Install the Azure CLI](/cli/azure/install-azure-cli).

To create a new storage account, call [az storage account create](/cli/azure/storage/account#az-storage-account-create), and set the `--enable-files-aadds` argument. In the following example, remember to replace the placeholder values with your own values. (If you were using the previous preview module, the parameter for feature enablement is **file-aad**.)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds
```

To enable this feature on existing storage accounts, use the following command:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds
```
---

## Recommended: Use AES-256 encryption

By default, Azure AD DS authentication uses Kerberos RC4 encryption. We recommend configuring it to use Kerberos AES-256 encryption instead by following these instructions.

The action requires running an operation on the Active Directory domain that's managed by Azure AD DS to reach a domain controller to request a property change to the domain object. The cmdlets below are Windows Server Active Directory PowerShell cmdlets, not Azure PowerShell cmdlets. Because of this, these PowerShell commands must be run from a client machine that's domain-joined to the Azure AD DS domain.

> [!IMPORTANT]
> The Windows Server Active Directory PowerShell cmdlets in this section must be run in Windows PowerShell 5.1 from a client machine that's domain-joined to the Azure AD DS domain. PowerShell 7.x and Azure Cloud Shell won't work in this scenario.

Log into the domain-joined client machine as an Azure AD DS user with the required permissions. You must have write access to the `msDS-SupportedEncryptionTypes` attribute of the domain object. Typically, members of the **AAD DC Administrators** group will have the necessary permissions. Open a normal (non-elevated) PowerShell session and execute the following commands.

```powershell
# 1. Find the service account in your managed domain that represents the storage account.

$storageAccountName= “<InsertStorageAccountNameHere>”
$searchFilter = "Name -like '*{0}*'" -f $storageAccountName
$userObject = Get-ADUser -filter $searchFilter

if ($userObject -eq $null)
{
   Write-Error "Cannot find AD object for storage account:$storageAccountName" -ErrorAction Stop
}

# 2. Set the KerberosEncryptionType of the object

Set-ADUser $userObject -KerberosEncryptionType AES256

# 3. Validate that the object now has the expected (AES256) encryption type.

Get-ADUser $userObject -properties KerberosEncryptionType
```

> [!IMPORTANT]
> If you were previously using RC4 encryption and update the storage account to use AES-256, you should run `klist purge` on the client and then remount the file share to get new Kerberos tickets with AES-256.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

## Next steps

To grant additional users access to your file share, follow the instructions in [Assign share-level permissions](#assign-share-level-permissions) and [Configure Windows ACLs](#configure-windows-acls).

For more information about identity-based authentication for Azure Files, see these resources:

- [Overview of Azure Files identity-based authentication support for SMB access](storage-files-active-directory-overview.md)
- [FAQ](storage-files-faq.md)
