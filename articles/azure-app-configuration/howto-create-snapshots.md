---
title: How to create snapshots (preview) in Azure App Configuration
description: How to create and manage snapshots Azure App Configuration store.
author: Muksvso
ms.author: mubatra
ms.service: azure-app-configuration
ms.topic: how-to 
ms.date: 05/16/2023
---

# Use snapshots (preview)

In this article, learn how to create and manage snapshots in Azure App Configuration. Snapshot is a set of App Configuration settings stored in an immutable state.

## Prerequisites

- An App Configuration store. [Create a store](./quickstart-azure-app-configuration-create.md#create-an-app-configuration-store).
- "DataOwner" role in the App Configuration store. Details on required [role and permissions for snapshots](./concept-snapshots.md)

### Add key-values to the App configuration store

In your App Configuration store, go to **Operations** > **Configuration explorer** and add the following key-values. Leave **Content Type** with its default value. For more information about how to add key-values to a store using the Azure portal or the CLI, go to [Create a key-value](./quickstart-azure-app-configuration-create.md#create-a-key-value).

| Key            | Value          | Label    |
|----------------|----------------|----------|
| *app2/bgcolor* | *Light Gray*   | *label2* |
| *app1/color*   | *Black*        | No label |
| *app1/color*   | *Blue*         | *label1* |
| *app1/color*   | *Green*        | *label2* |
| *app1/color*   | *Yellow*       | *label3* |
| *app1/message* | *Hello*        | *label1* |
| *app1/message* | *Hi!*          | *label2* |
| *app2/message* | *Good morning!*| *label1* |

## Create a snapshot

 > [!IMPORTANT]
   > You may see any error "You are not authorized to view this configuration store data" when you switch to the Snapshots blade in the Azure portal if you opt to use Azure AD authentication in the Configuration explorer or the Feature manager blades. This is a known issue in the Azure portal, and we are working on addressing it. It doesn't affect any scenarios other than the Azure Portal regarding accessing snapshots with Azure AD authentication.

As a temporary workaround, you can switch to using Access keys authentication from either the Configuration explorer or the Feature manager blades. You should then see the Snapshot blade displayed properly, assuming you have permission for the access keys.

Under **Operations** > **Snapshots (preview)**, select **Create a new snapshot**.

1. Enter a **snapshot name** and optionally also add **Tags**.
1. Under  **Choose the composition type**, keep the default value **Key (default)**.
   - With the *Key* composition type, if your store has identical keys with different labels, only the  key-value specified in the last applicable filter is included in the snapshot. Identical key-values with other labels are left out of the snapshot.
   - With the *Key-Label* composition type, if your store has identical keys with different labels, all  key-values with identical keys but different labels are included in the snapshot depending on the specified filters.
1. Select **Add filters** to select the key-values for your snapshot. Filtering is done by selecting filters: **Equals**, **Starts with**, **Any of** and **All** for keys and for labels. You can enter between one and three filters.
   1. Add the first filter:
      - Under **Key**, select **Starts with** and enter *app1*
      - Under **Label**, select **Equals** and select *label2* from the drop-down menu.
   1. Add the second filter:
      - Under **Key**, select **Starts with** and enter *app1*
      - Under **Label**, select **Equals** and select *label1*  from the drop-down menu.

1. If you archive a snapshot, by default, it will be retained for 30 days after archival. Optionally, under **Recovery options**, decrease the number of retention days the snapshot will be available after archival.

   > [!NOTE]
   > The duration of the retention period can't be updated once the snapshot has been created.

1. Select **Create** to generate the snapshot. In this example, the created snapshot has **Key** composition type and below filters:
   - Keys that start with *app1*, with *label2* label
   - Keys that start with *app1*, with *label1* label.
  
    :::image type="content" source="./media/howto-create-snapshots/create-snapshot.png" alt-text="Screenshot of the Create form with data filled as above steps and Create button highlighted.":::
  
1. Check the table to understand which key-values from the configuration store end up in the snapshot based on the provided parameters.  

    | Key            | Value          | Label    | Included in snapshot                                                                                              |
    |----------------|----------------|----------|-------------------------------------------------------------------------------------------------------------------|
    | *app2/bgcolor* | *Light Gray*   | *label2* | No: doesn't start with *app1*.
    | *app1/color*   | *Black*        | No label | No: doesn't have the label *label2* or *label1*.
    | *app1/color*   | *Blue*         | *label1* | Yes: Has the right label *label1* from the last of applicable filters.
    | *app1/color*   | *Green*        | *label2* | No: Same key with label *label1* selected by the second filter overrides this one though it has the selected label, *label2*.
    | *app1/color*   | *Yellow*       | *label3* | No: doesn't have the label *label2* or *label1*.
    | *app1/message* | *Hello*        | *label1* | Yes: Has the right label *label1* from the last of applicable filters.
    | *app1/message* | *Hi!*          | *label2* | No:  Same key with label *label1* selected by the second filter overrides this one though it has the selected label, *label2*.
    | *app2/message* | *Good morning!*| *label1* | No: doesn't start with *app1*.

## Create sample snapshots

To create sample snapshots and check how the snapshots feature work, use the snapshot sandbox. This sandbox contains sample data you can play with to better understand how snapshot's composition type and filters work.

1. In **Operations** > **Snapshots (preview)** > **Active snapshots**, select **Test in sandbox**.
1. Review the sample data and practice creating snapshots by filling out the form with a composition type and one or more filters.
1. Select **Create** to generate the sample snapshot.
1. Check out the snapshot result generated under **Generated sample snapshot**. The sample snapshot displays all keys that are included in the sample snapshot, according to your selection.

## Manage active snapshots

The page under **Operations** > **Snapshots (preview)** displays two tabs: **Active snapshots** and **Archived snapshots**. Select **Active snapshots** to view the list of all active snapshots in an App Configuration store.

   :::image type="content" source="./media/howto-create-snapshots/snapshots-view-list.png" alt-text="Screenshot of the list of active snapshots.":::

### View existing snapshot

In the **Active snapshots** tab, select the ellipsis **...** on the right of an existing snapshot and select **View** to view a snapshot. This action opens a Snapshot details page that displays the snapshot's settings and the key-values included in the snapshot.

   :::image type="content" source="./media/howto-create-snapshots/snapshot-details-view.png" alt-text="Screenshot of the detailed view of an active snapshot.":::

### Archive a snapshot

In the **Active snapshots** tab, select the ellipsis **...** on the right of an existing snapshot and select **Archive** to archive a snapshot. Confirm archival by selecting **Yes** or cancel with **No**. Once a snapshot has been archived, a notification appears to confirm the operation and the list of active snapshots is updated.

   :::image type="content" source="./media/howto-create-snapshots/archive-snapshot.png" alt-text="Screenshot of the archive option in the active snapshots.":::

## Manage archived snapshots

Go to **Operations** > **Snapshots (preview)** > **Archived snapshots** to view the list of all archived snapshots in an App Configuration store. Archived snapshots remain accessible for the retention period that was selected during their creation.

   :::image type="content" source="./media/howto-create-snapshots/archived-snapshots.png" alt-text="Screenshot of the list of archived snapshots.":::

### View archived snapshot

Detailed view of snapshot is available in the archive state as well. In the **Archived snapshots** tab, select the ellipsis **...** on the right of an existing snapshot and select **View** to view a snapshot. This action opens a Snapshot details page that displays the snapshot's settings and the key-values included in the snapshot.

   :::image type="content" source="./media/howto-create-snapshots/archived-snapshots-details.png" alt-text="Screenshot of the detailed view of an archived snapshot.":::

### Recover an archived snapshot

In the **Archived snapshots** tab, select the ellipsis **...** on the right of an archived snapshot and select **Recover** to recover a snapshot. Confirm App Configuration snapshot recovery by selecting **Yes** or cancel with **No**. Once a snapshot has been recovered, a notification appears to confirm the operation and the list of archived snapshots is updated.

   :::image type="content" source="./media/howto-create-snapshots/recover-snapshots.png" alt-text="Screenshot of the recover option in the archived snapshots.":::

## Next steps

> [!div class="nextstepaction"]
> [Snapshots in Azure App Configuration](./concept-snapshots.md)
