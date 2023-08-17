---
title: 'Tutorial: Use a Remote Desktop client to connect to a dev box'
titleSuffix: Microsoft Dev Box
description: In this tutorial, you learn how to download a Remote Desktop client and connect to your dev box. You also learn how to configure a dev box to use multiple monitors during a remote desktop session. 
services: dev-box
ms.service: dev-box
ms.author: rosemalcolm
author: RoseHJM
ms.date: 04/25/2023
ms.topic: tutorial
---

# Tutorial: Use a Remote Desktop client to connect to a dev box

After you configure the Microsoft Dev Box service and create dev boxes, you can connect to them by using a browser or by using a Remote Desktop client.

Remote Desktop apps let you use and control a dev box from almost any device. For your desktop or laptop, you can choose to download the Remote Desktop client for Windows Desktop or Microsoft Remote Desktop for Mac. You can also download a Remote Desktop app for your mobile device: Microsoft Remote Desktop for iOS or Microsoft Remote Desktop for Android.

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Download a remote desktop client.
> * Connect to an existing dev box.
> * Configure the remote desktop client for multiple monitors.

## Prerequisites

To complete this tutorial, you must first:

- [Configure Microsoft Dev Box ](./quickstart-configure-dev-box-service.md).
- [Create a dev box](./quickstart-create-dev-box.md#create-a-dev-box) on the [developer portal](https://aka.ms/devbox-portal).

## Download the client and connect to your dev box

Remote Desktop clients are available for many operating systems and devices. In this tutorial, you can view the steps for Windows or the steps for a non-Windows operating system by selecting the relevant tab.

# [Windows](#tab/windows)

### Download the Remote Desktop client for Windows

To download and set up the Remote Desktop client for Windows:

1. Sign in to the [developer portal](https://aka.ms/devbox-portal).

1. Select **Open in RDP client** for the dev box that you want to connect.

   :::image type="content" source="./media/tutorial-connect-to-dev-box-with-remote-desktop-app/windows-open-rdp-client.png" alt-text="Screenshot of the card for a user's dev boxes with the option for opening in an RDP client.":::

1. Select **Download Windows Desktop** to download the client.

   :::image type="content" source="./media/tutorial-connect-to-dev-box-with-remote-desktop-app/download-windows-desktop.png" alt-text="Screenshot of the option to download the Windows Desktop client.":::

1. Open the remote desktop MSI file and follow the prompts to install the remote desktop app. After the client is installed, you'll use the developer portal to connect to your dev box.


### Connect to your dev box

To open the Remote Desktop client:

1. Sign in to the [developer portal](https://aka.ms/devbox-portal).

1. Select **Open in RDP client** for the dev box that you want to connect.

   :::image type="content" source="./media/tutorial-connect-to-dev-box-with-remote-desktop-app/windows-open-rdp-client.png" alt-text="Screenshot of the option to open a dev box in an RDP client.":::

1. Select **Open Windows Desktop** to connect to your dev box in the Remote Desktop client.

   :::image type="content" source="./media/tutorial-connect-to-dev-box-with-remote-desktop-app/open-windows-desktop.png" alt-text="Screenshot of the option to open the Windows desktop client in the connection dialog.":::

# [Non-Windows](#tab/non-Windows)

### Download the Remote Desktop client 

To use a non-Windows Remote Desktop client to connect to your dev box:

1. Sign in to the [developer portal](https://aka.ms/devbox-portal).

1. Under **Quick actions**, select **Configure Remote Desktop**.

   :::image type="content" source="./media/tutorial-connect-to-dev-box-with-remote-desktop-app/configure-remote-desktop-non-windows.png" alt-text="Screenshot of the button for configuring Remote Desktop in the area for quick actions.":::

1. In the **Configure Remote Desktop** dialog, select **Download** to download the client.

   :::image type="content" source="./media/tutorial-connect-to-dev-box-with-remote-desktop-app/download-non-windows-rdp-client.png" alt-text="Screenshot of the download button in the dialog for configuring Remote Desktop.":::

1. Copy the subscription feed URL. After the Remote Desktop client is installed, you'll connect to your dev box by using this URL.

   :::image type="content" source="./media/tutorial-connect-to-dev-box-with-remote-desktop-app/copy-subscription-url-non-windows.png" alt-text="Screenshot of the subscription feed URL in the Configure Remote Desktop dialog.":::

### Connect to your dev box

1. Open the Remote Desktop client, select **Add Workspace**, and paste the subscription feed URL in the box.

   :::image type="content" source="./media/tutorial-connect-to-dev-box-with-remote-desktop-app/non-windows-rdp-subscription-feed.png" alt-text="Screenshot of the dialog for adding a workspace URL.":::

1. Your dev box appears in the Remote Desktop client's **Workspaces** area. Double-click it to connect.

   :::image type="content" source="./media/tutorial-connect-to-dev-box-with-remote-desktop-app/non-windows-rdp-connect-dev-box.png" alt-text="Screenshot of a dev box in a non-Windows Remote Desktop client workspace.":::

---

## Configure Remote Desktop to use multiple monitors

Microsoft Remote Desktop for Windows and Microsoft Remote Desktop for Mac both support up to 16 monitors. Use the following steps to configure Remote Desktop to use multiple monitors.

# [Windows](#tab/windows)

1. Open Remote Desktop.
 
1. Right-click the dev box you want to configure, and then select **Settings**.
 
1. On the settings pane, turn off **Use default settings**.
 
   :::image type="content" source="media/tutorial-connect-to-dev-box-with-remote-desktop-app/turn-off-default-settings.png" alt-text="Screenshot showing the Use default settings slider.":::
 
1. In **Display Settings**, in the **Display configuration** list, select the displays to use:
 
   |Value  |Description  |
   |---------|---------|
   |All displays |Remote desktop uses all available displays. |
   |Single display |Remote desktop uses a single display. |
   |Select displays |Remote Desktop uses only the monitors you select. |

   :::image type="content" source="media/tutorial-connect-to-dev-box-with-remote-desktop-app/remote-desktop-select-display.png" alt-text="Screenshot showing the remote desktop display settings. ":::

1. Close the settings pane, and then select your dev box to begin the remote desktop session.

# [Non-Windows](#tab/non-Windows)

1. Open Remote Desktop.
 
1. Select **PCs**.

1. On the Connections menu, select **Edit PC**.
 
1. Select **Display**.
 
1. On the Display tab, select **Use all monitors**, and then select **Save**.

   :::image type="content" source="media/tutorial-connect-to-dev-box-with-remote-desktop-app/remote-desktop-for-mac.png" alt-text="Screenshot showing the Edit PC dialog box with the display configuration options.":::

1. Select your dev box to begin the remote desktop session.

--- 


## Clean up resources

Dev boxes incur costs whenever they're running. When you finish using your dev box, shut down or stop it to avoid incurring unnecessary costs.

You can stop a dev box from the developer portal:

1. Sign in to the [developer portal](https://aka.ms/devbox-portal).

1. For the dev box that you want to stop, select the **Actions** menu, and then select **Stop**.

   :::image type="content" source="./media/tutorial-connect-to-dev-box-with-remote-desktop-app/stop-dev-box.png" alt-text="Screenshot of the menu command to stop a dev box.":::

The dev box might take a few moments to stop.

## Next steps

To learn about managing Microsoft Dev Box , see:

- [Provide access to project admins](./how-to-project-admin.md)
- [Provide access to dev box users](./how-to-dev-box-user.md)
