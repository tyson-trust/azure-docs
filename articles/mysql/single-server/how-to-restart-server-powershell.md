---
title: Restart server - Azure PowerShell - Azure Database for MySQL
description: This article describes how you can restart an Azure Database for MySQL server using PowerShell.
ms.service: mysql
ms.subservice: single-server
author: SudheeshGH
ms.author: sunaray
ms.topic: how-to
ms.date: 06/20/2022
ms.custom: devx-track-azurepowershell
---

# Restart Azure Database for MySQL server using PowerShell

[!INCLUDE[applies-to-mysql-single-server](../includes/applies-to-mysql-single-server.md)]

[!INCLUDE[azure-database-for-mysql-single-server-deprecation](../includes/azure-database-for-mysql-single-server-deprecation.md)]

This topic describes how you can restart an Azure Database for MySQL server. You may need to restart
your server for maintenance reasons, which causes a short outage during the operation.

The server restart is blocked if the service is busy. For example, the service may be processing a
previously requested operation such as scaling vCores.

The amount of time required to complete a restart depends on the MySQL recovery process. To reduce
the restart time, we recommend you minimize the amount of activity occurring on the server before
the restart.

## Prerequisites

To complete this how-to guide, you need:

- The [Az PowerShell module](/powershell/azure/install-azure-powershell) installed locally or
  [Azure Cloud Shell](https://shell.azure.com/) in the browser
- An [Azure Database for MySQL server](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> While the Az.MySql PowerShell module is in preview, you must install it separately from the Az
> PowerShell module using the following command: `Install-Module -Name Az.MySql -AllowPrerelease`.
> Once the Az.MySql PowerShell module is generally available, it becomes part of future Az
> PowerShell module releases and available natively from within Azure Cloud Shell.

>[!NOTE]
>If the user restarting the server is part of [custom role](../../role-based-access-control/custom-roles.md) the user should have write privilege on the server.

If you choose to use PowerShell locally, connect to your Azure account using the
[Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) cmdlet.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## Restart the server

Restart the server with the following command:

```azurepowershell-interactive
Restart-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```


## Next steps

> [!div class="nextstepaction"]
> [Create an Azure Database for MySQL server using PowerShell](quickstart-create-mysql-server-database-using-azure-powershell.md)
