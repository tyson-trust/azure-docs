---
title: Bulk delete users in the Azure portal
description: Delete users in bulk in the Azure admin center in Azure Active Directory 
services: active-directory 
author: barclayn
ms.author: barclayn
manager: amycolannino
ms.date: 06/24/2022
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
---

# Bulk delete users in Azure Active Directory

Using the admin center in Azure Active Directory (Azure AD), part of Microsoft Entra, you can remove a large number of members to a group by using a comma-separated values (CSV) file to bulk delete users.

## CSV template structure

![The CSV file contains names and IDs of the users to delete](./media/users-bulk-delete/delete-csv-file.png)

The rows in a downloaded CSV template are as follows:

- **Version number**: The first row containing the version number must be included in the upload CSV.
- **Column headings**: `User name [userPrincipalName] Required`. Older versions of the template might vary.
- **Examples row**: We have included in the template an example of an acceptable value. `Example: chris@contoso.com` You must remove the example row and replace it with your own entries.

### Additional guidance

- The first two rows of the template must not be removed or modified, or the template can't be processed.
- The required columns are listed first.
- Don't add new columns to the template. Any additional columns you add are ignored and not processed.
- Download the latest version of the CSV template before making new changes.

## To bulk delete users

[!INCLUDE [portal updates](~/articles/active-directory/includes/portal-update.md)]

1. Sign in to the [Azure portal](https://portal.azure.com) with an account that is a User Administrator in the organization.
1. Browse to **Azure Active Directory** > **Users** > **Bulk operations** > **Bulk delete**.
1. On the **Bulk delete user** page, select **Download** to download the latest version of the CSV template.
1. Open the CSV file and add a line for each user you want to delete. The only required value is **User principal name**. Save the file.
1. On the **Bulk delete user** page, under **Upload your csv file**, browse to the file. When you select the file and click submit, validation of the CSV file starts.
1. When the file contents are validated, you’ll see **File uploaded successfully**. If there are errors, you must fix them before you can submit the job.
1. When your file passes validation, select **Submit** to start the Azure bulk operation that deletes the users.
1. When the deletion operation completes, you'll see a notification that the bulk operation succeeded.

If there are errors, you can download and view the results file on the **Bulk operation results** page. The file contains the reason for each error.

## Check status

You can see the status of all of your pending bulk requests in the **Bulk operation results** page.

   [![Check delete status in the Bulk Operations Results page.](./media/users-bulk-delete/bulk-center.png)](./media/users-bulk-delete/bulk-center.png#lightbox)

Next, you can check to see that the users you deleted exist in the Azure AD organization either in the Azure portal or by using PowerShell.

## Verify deleted users in the Azure portal

1. Sign in to the [Azure portal](https://portal.azure.com) with an account that is a User administrator in the organization.
1. In the navigation pane, select **Azure Active Directory**.
1. Under **Manage**, select **Users**.
1. Under **Show**, select **All users** only and verify that the users you deleted are no longer listed.

### Verify deleted users with PowerShell

Run the following command:

``` PowerShell
Get-MgUser -Filter "UserType eq 'Member'"
```

Verify that the users that you deleted are no longer listed.

## Next steps

- [Bulk add users](users-bulk-add.md)
- [Download list of users](users-bulk-download.md)
- [Bulk restore users](users-bulk-restore.md)
