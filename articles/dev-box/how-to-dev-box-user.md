---
title: Provide user access to dev box projects
titleSuffix: Microsoft Dev Box
description: Learn how to provide user-level access to projects for developers so that they can create and manage dev boxes.
services: dev-box
ms.service: dev-box
author: RoseHJM
ms.author: rosemalcolm
ms.date: 04/25/2023
ms.topic: how-to
---

# Provide user-level access to projects for developers

Team members must have access to a specific Microsoft Dev Box project before they can create dev boxes. By using the built-in DevCenter Dev Box User role, you can assign permissions to Active Directory users or groups at the project level.

[!INCLUDE [supported accounts note](./includes/note-supported-accounts.md)]

A DevCenter Dev Box User can:

- View pools within a project.
- Create dev boxes.
- Connect to a dev box.
- Manage dev boxes that they created.
- Delete dev boxes that they created.

## Assign permissions to dev box users

1. Sign in to the [Azure portal](https://portal.azure.com).

1. In the search box, enter **projects**. In the list of results, select **Projects**.

1. Select the project that you want to give your team members access to.

   :::image type="content" source="./media/how-to-dev-box-user/projects-grid.png" alt-text="Screenshot that shows a list of existing projects.":::

1. On the left menu, select **Access Control (IAM)**.

1. Select **Add** > **Add role assignment**.

1. Assign the following role. For detailed steps, see [Assign Azure roles using the Azure portal](../role-based-access-control/role-assignments-portal.md).

    | Setting | Value |
    | --- | --- |
    | **Role** | Select **DevCenter Dev Box User**. |
    | **Assign access to** | Select **User, group, or service principal**. |
    | **Members** | Select the users or groups that you want to have access to the project. |

    :::image type="content" source="media/how-to-dev-box-user/add-role-assignment-user.png" alt-text="Screenshot that shows the pane for adding role assignments.":::

The users can now view the project and all the pools within it. Dev box users can create dev boxes from any of the pools and manage those dev boxes from the [developer portal](https://aka.ms/devbox-portal).

[!INCLUDE [dev box runs on creation note](./includes/note-dev-box-runs-on-creation.md)]

## Next steps

- [Quickstart: Create a dev box by using the developer portal](quickstart-create-dev-box.md)