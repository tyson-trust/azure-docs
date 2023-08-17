---
title: "Migrate diagnostic settings storage retention to Azure Storage lifecycle management"
description: "How to Migrate from diagnostic settings storage retention to Azure Storage lifecycle management"
author: EdB-MSFT
ms.author: edbaynash
ms.service: azure-monitor
ms.topic: how-to
ms.reviewer: lualderm
ms.date: 08/16/2023

#Customer intent: As a dev-ops administrator I want to migrate my retention setting from diagnostic setting retention storage to Azure Storage lifecycle management so that it continues to work after the feature has been deprecated.
---

# Migrate from diagnostic settings storage retention to Azure Storage lifecycle management

The Diagnostic Settings Storage Retention feature is being deprecated. To configure retention for logs and metrics use Azure Storage Lifecycle Management.  

This guide walks you through migrating from using Azure diagnostic settings storage retention to using [Azure Storage lifecycle management](../../storage/blobs/lifecycle-management-policy-configure.md?tabs=azure-portal) for retention.

> [!IMPORTANT]
> **Deprecation Timeline.**
> - March 31, 2023 –  The Diagnostic Settings Storage Retention feature will no longer be available to configure new retention rules for log data. This includes using the portal, CLI PowerShell, and ARM and Bicep templates.  If you have configured retention settings, you'll still be able to see and change them in the portal. 
> - September 30, 2023 –  You will no longer be able to use the API (CLI, Powershell, or templates), or Azure portal to configure retention setting unless you're changing them to *0*. Existing retention rules will still be respected.
> - September 30, 2025 –  All retention functionality for the Diagnostic Settings Storage Retention feature will be disabled across all environments.



## Prerequisites

An existing diagnostic setting logging to a storage account.

## Migration Procedures

To migrate your diagnostics settings retention rules, follow the steps below:

1. Go to the Diagnostic Settings page for your logging resource and locate the diagnostic setting you wish to migrate
1. Set the retention for your logged categories to *0*
1. Select **Save**
 :::image type="content" source="./media/retention-migration/diagnostics-setting.png" alt-text="A screenshot showing a diagnostics setting page.":::

1. Navigate to the storage account you're logging to
1. Under **Data management**, select **Lifecycle Management** to view or change lifecycle management policies
1. Select List View, and select **Add a rule**
:::image type="content" source="./media/retention-migration/lifecycle-management.png" alt-text="A screenshot showing the lifecycle management screen for a storage account.":::
1. Enter a **Rule name**
1. Under **Rule Scope**, select **Limit blobs with filters**
1. Under **Blob Type**, select  **Append Blobs** and **Base blobs** under **Blob subtype**.
1. Select **Next**
:::image type="content" source="./media/retention-migration/lifecycle-management-add-rule-details.png" alt-text="A screenshot showing the details tab for adding a lifecycle rule.":::

1. Set your retention time, then select **Next**
:::image type="content" source="./media/retention-migration/lifecycle-management-add-rule-base-blobs.png" alt-text="A screenshot showing the Base blobs tab for adding a lifecycle rule.":::

1. On the **Filters** tab, under **Blob prefix** set path or prefix to the container or logs you want the retention rule to apply to.   The path or prefix can be at any level within the container and will apply to all blobs under that path or prefix.
For example, for *all* insight activity logs, use the container *insights-activity-logs* to set the retention for all of the log in that container logs.  
To set the rule for a specific webapp app, use *insights-activity-logs/ResourceId=/SUBSCRIPTIONS/\<your subscription Id\>/RESOURCEGROUPS/\<your resource group\>/PROVIDERS/MICROSOFT.WEB/SITES/\<your webapp name\>*. 

    Use the Storage browser to help you find the path or prefix.   
    The example below shows the prefix for a specific web app: **insights-activity-logs/ResourceId=/SUBSCRIPTIONS/d05145d-4a5d-4a5d-4a5d-5267eae1bbc7/RESOURCEGROUPS/rg-001/PROVIDERS/MICROSOFT.WEB/SITES/appfromdocker1*.  
    To set the rule for all resources in the resource group, use *insights-activity-logs/ResourceId=/SUBSCRIPTIONS/d05145d-4a5d-4a5d-4a5d-5267eae1bbc7/RESOURCEGROUPS/rg-001*.
    :::image type="content" source="./media/retention-migration/blob-prefix.png" alt-text="A screenshot showing the Storage browser and resource path." lightbox="./media/retention-migration/blob-prefix.png":::

1. Select **Add** to save the rule.
:::image type="content" source="./media/retention-migration/lifecycle-management-add-rule-filter-set.png" lightbox="./media/retention-migration/lifecycle-management-add-rule-filter-set.png" alt-text="A screenshot showing the filters tab for adding a lifecycle rule.":::

## Next steps

[Configure a lifecycle management policy](../../storage/blobs/lifecycle-management-policy-configure.md?tabs=azure-portal).