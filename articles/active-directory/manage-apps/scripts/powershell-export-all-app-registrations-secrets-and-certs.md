---
title: PowerShell sample - Export secrets and certificates for app registrations in Azure Active Directory tenant.
description: PowerShell example that exports all secrets and certificates for the specified app registrations in your Azure Active Directory tenant.
services: active-directory
author: omondiatieno
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.custom:
ms.topic: sample
ms.date: 07/11/2023
ms.author: jomondi
ms.reviewer: mifarca
---

# Export secrets and certificates for app registrations

This PowerShell script example exports all secrets and certificates for the specified app registrations from your directory into a CSV file.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

This sample requires the [Microsoft Graph PowerShell](/powershell/microsoftgraph/installation) SDK module.

## Sample script

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-app-registrations-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified app registrations in your directory.")]

## Script explanation

The script can be used directly without any modifications. The admin will be asked about the expiration date and whether they would like to see already expired secrets or certificates or not.

The "Add-Member" command is responsible for creating the columns in the CSV file.
You can modify the "$Path" variable directly in PowerShell, with a CSV file path, in case you'd prefer the export to be non-interactive.

| Command | Notes |
|---|---|
| [Get-MgApplication](/powershell/module/microsoft.graph.applications/get-mgapplication?view=graph-powershell-1.0&preserve-view=true) | Retrieves an application from your directory. |
| [Get-MgApplicationOwner](/powershell/module/microsoft.graph.applications/get-mgapplicationowner?view=graph-powershell-1.0&preserve-view=true) | Retrieves the owners of an application from your directory. |

## Next steps

For more information on the Microsoft Graph PowerShell module, see [Microsoft Graph PowerShell module overview](/powershell/microsoftgraph/installation).

For other PowerShell examples for Application Management, see [Azure Microsoft Graph PowerShell examples for Application Management](../app-management-powershell-samples.md).
