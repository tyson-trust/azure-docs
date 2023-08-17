---
title: 'Quickstart: Create a dev box'
titleSuffix: Microsoft Dev Box
description: In this quickstart, you learn how to create a dev box and connect to it through a browser.
services: dev-box
ms.service: dev-box
ms.topic: quickstart
author: RoseHJM
ms.author: rosemalcolm
ms.date: 04/25/2023
#Customer intent: As a dev box user, I want to understand how to create and access a dev box so that I can start work.
---

# Quickstart: Create a dev box by using the developer portal

In this quickstart, you get started with Microsoft Dev Box by creating a dev box through the developer portal. After you create the dev box, you can connect to it with a Remote Desktop session through a browser or through a Remote Desktop app.

You can create and manage multiple dev boxes as a dev box user. Create a dev box for each task that you're working on, and create multiple dev boxes within a single project to help streamline your workflow.

## Prerequisites

To complete this quickstart, you need:

- Permissions as a [Dev Box User](quickstart-configure-dev-box-service.md#6-provide-access-to-a-dev-box-project) for a project that has an available dev box pool. If you don't have permissions to a project, contact your administrator.

## Create a dev box

1. Sign in to the [developer portal](https://aka.ms/devbox-portal).

2. Select **+ Add dev box**.

   :::image type="content" source="./media/quickstart-create-dev-box/dev-portal-welcome.png" alt-text="Screenshot of the developer portal and the button for adding a dev box.":::

3. In **Add a dev box**, enter the following values:

   |Name|Value|
   |----|----|
   |**Name**|Enter a name for your dev box. Dev box names must be unique within a project.|
   |**Project**|Select a project from the dropdown list. |
   |**Dev box pool**|Select a pool from the dropdown list, which includes all the dev box pools for that project. |

   :::image type="content" source="./media/quickstart-create-dev-box/add-dev-box.png" alt-text="Screenshot of the dialog for adding a dev box.":::

4. Select **Add** to begin creating your dev box.

5. Use the home page of the developer portal to track the progress of creation.

   :::image type="content" source="./media/quickstart-create-dev-box/dev-portal-creating.png" alt-text="Screenshot of the developer portal that shows the dev box card with a status of Creating.":::

[!INCLUDE [dev box runs on creation note](./includes/note-dev-box-runs-on-creation.md)]

## Connect to a dev box

After you provision a dev box, one way to access it quickly is through a browser:

1. Sign in to the [developer portal](https://aka.ms/devbox-portal).

1. Select **Open in browser**.

   :::image type="content" source="./media/quickstart-create-dev-box/dev-portal-card-rdp.png" alt-text="Screenshot of dev box card that shows the option for opening in a browser.":::

A new tab opens with a Remote Desktop session through which you can use your dev box.

## Clean up resources

When you no longer need your dev box, you can delete it:

1. Sign in to the [developer portal](https://aka.ms/devbox-portal).

1. For the dev box you that you want to delete, from the **Actions** menu, select **Delete**.

   :::image type="content" source="./media/quickstart-create-dev-box/dev-portal-delete-dev-box.png" alt-text="Screenshot of the menu command for deleting a dev box.":::

1. To confirm the deletion, select **Delete**.

   :::image type="content" source="./media/quickstart-create-dev-box/dev-portal-delete-dev-box-confirm.png" alt-text="Screenshot of the Delete button in the confirmation message about deleting a dev box."::: 

## Next steps

In this quickstart, you created a dev box through the developer portal and connected to it by using a browser. To learn how to connect to a dev box by using a Remote Desktop app, see [Tutorial: Use a Remote Desktop client to connect to a dev box](./tutorial-connect-to-dev-box-with-remote-desktop-app.md).
