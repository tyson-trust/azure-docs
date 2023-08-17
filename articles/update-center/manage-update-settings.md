---
title: Manage update configuration settings in Update management center (preview)
description: The article describes how to manage the update settings for your Windows and Linux machines managed by Update management center (preview).
ms.service: update-management-center
author: snehasudhirG
ms.author: sudhirsneha
ms.date: 05/30/2023
ms.topic: conceptual
---

# Manage Update configuration settings

**Applies to:** :heavy_check_mark: Windows VMs :heavy_check_mark: Linux VMs :heavy_check_mark: On-premises environment :heavy_check_mark: Azure Arc-enabled servers.

The article describes how to configure update settings from Update management center (preview) in Azure, to control the update settings on your Azure VMs and Arc-enabled servers for one or more machines.

:::image type="content" source="./media/manage-update-settings/manage-update-settings-option-inline.png" alt-text="Screenshot Viewing the update management center manage update settings option." lightbox="./media/manage-update-settings/manage-update-settings-option-expanded.png":::


## Configure settings on single VM

To configure update settings on your machines on a single VM, follow these steps:

>[!NOTE]
> You can schedule updates from the Overview blade or Machines blade in update management center (preview) page or from the selected VM.

# [From Overview blade](#tab/manage-single-overview)

1. Sign in to the [Azure portal](https://portal.azure.com).
1. In **Update management center**, select **Overview**, select your **Subscription**, and select **Update settings**.
1. In **Change update settings**, select **+Add machine** to select the machine for which you want to change the update settings.
1. In **Select resources**, select the machine and select **Add**.
1. In the **Change update settings** page, you will see the machine classified as per the operating system with the list of following updates that you can select and apply.

    :::image type="content" source="./media/manage-update-settings/update-setting-to-change.png" alt-text="Highlighting the Update settings to change option in the Azure portal.":::
 
    The following update settings are available for configuration for the selected machine(s):

   - **Periodic assessment** - The **periodic Assessment** is set to run every 24 hours. You can either enable or disable this setting.
     
    - **Hot patch** - You can enable [hot patching](../automanage/automanage-hotpatch.md) for Windows Server Azure Edition Virtual Machines (VMs). Hot patching is a new way to install updates on supported *Windows Server Azure Edition* virtual machines that doesn't require a reboot after installation. You can use update management center (preview) to install other patches by scheduling patch installation or triggering immediate patch deployment. You can enable, disable or reset this setting.

    - **Patch orchestration** option provides the following:
    
      - **Customer Managed Schedules (Preview)**—enables schedule patching on your existing VMs. The new patch orchestration option enables the two VM properties - **Patch mode = Azure-orchestrated** and **BypassPlatformSafetyChecksOnUserSchedule = TRUE** on your behalf after receiving your consent.
      - **Azure Managed - Safe Deployment**—for a group of virtual machines undergoing an update, the Azure platform will orchestrate updates. (not applicable for Arc-enabled server). The VM is set to [automatic VM guest patching](../virtual-machines/automatic-vm-guest-patching.md).(i.e), the patch mode is **AutomaticByPlatform**. There are different implications depending on whether customer schedule is attached to it or not. For more information, see the [user scenarios](prerequsite-for-schedule-patching.md#user-scenarios).
          - Available *Critical* and *Security* patches are downloaded and applied automatically on the Azure VM using [automatic VM guest patching](../virtual-machines/automatic-vm-guest-patching.md). This process kicks off automatically every month when new patches are released. Patch assessment and installation are automatic, and the process includes rebooting the VM as required.
      - **Windows Automatic Updates** (AutomaticByOS) - When the workload running on the VM doesn't have to meet availability targets, the operating system updates are automatically downloaded and installed. Machines are rebooted as needed.
      - **Manual updates** - This mode disables Windows automatic updates on VMs. Patches are installed manually or using a different solution.
      - **Image Default** - Only supported for Linux Virtual Machines, this mode uses the default patching configuration in the image used to create the VM.

1. After you make the selection, select **Save**.


# [From Machines blade](#tab/manage-single-machines)

1. Sign in to the [Azure portal](https://portal.azure.com).
1. In **Update management center**, select **Machines** > your **subscription**. 
1. Select the checkbox of your machine from the list and select **Update settings**.
1. Select **Update Settings** to proceed with the type of update for your machine.
1. In **Change update settings**, select **+Add machine** to select the machine for which you want to change the update settings.
1. In **Select resources**, select the machine and select **Add** and follow the procedure from step 5 listed in **From Overview blade** of [Configure settings on single VM](#configure-settings-on-single-vm).

# [From a selected VM](#tab/singlevm-schedule-home)

1. Select your virtual machine and the **virtual machines | Updates** page opens.
1. Under **Operations**, select **Updates**.
1. In **Updates (Preview)**, select **Update Settings**.
1. In **Change update settings**, you can select the update settings that you want to change for your machine and follow the procedure from step 3 listed in **From Overview blade** of [Configure settings on single VM](#configure-settings-on-single-vm).

---

A notification appears to confirm that the update settings are successfully changed.

## Configure settings at scale

To configure update settings on your machines at scale, follow these steps:

>[!NOTE]
> You can schedule updates from the Overview blade or Machines blade.

# [From Overview blade](#tab/manage-scale-overview)
 
1. Sign in to the [Azure portal](https://portal.azure.com).

1. In **Update management center**, select **Overview**, select your **Subscription** and select **Update settings**.

1. In **Change update settings**, select the update settings that you want to change for your machines. Follow the procedure from step 3 listed in **From Overview blade** of [Configure settings on single VM](#configure-settings-on-single-vm).

# [From Machines blade](#tab/manage-scale-machines)

1. Sign in to the [Azure portal](https://portal.azure.com).
1. In **Update management center**, select **Machines** > your **subscription**, and select the checkbox for all your machines from the list.
1. Select **Update Settings** to proceed with the type of update for your machines.
1. In **Change update settings**, you can select the update settings that you want to change for your machine and follow the procedure from step 3 listed in **From Overview blade** of [Configure settings on single VM](#configure-settings-on-single-vm).

---

A notification appears to confirm that the update settings are successfully changed.



## Next steps

* [View assessment compliance](view-updates.md) and [deploy updates](deploy-updates.md) for a selected Azure VM or Arc-enabled server, or across [multiple machines](manage-multiple-machines.md) in your subscription in the Azure portal.
* To view update assessment and deployment logs generated by update management center (preview), see [query logs](query-logs.md).
* To troubleshoot issues, see the [Troubleshoot](troubleshoot.md) update management center (preview).