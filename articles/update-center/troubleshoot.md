---
title: Troubleshoot known issues with update management center (preview)
description: The article provides details on the known issues and troubleshooting any problems with update management center (preview).
ms.service: update-management-center
ms.date: 05/30/2023
ms.topic: conceptual
ms.author: sudhirsneha
author: SnehaSudhirG
---

# Troubleshoot issues with update management center (preview)

This article describes the errors that might occur when you deploy or use update management center (preview), how to resolve them and the known issues and limitations of scheduled patching.  


## General troubleshooting

The following troubleshooting steps apply to the Azure VMs related to the patch extension on Windows and Linux machines.   

### Azure Linux VM

To verify if the Microsoft Azure Virtual Machine Agent (VM Agent) is running, has triggered appropriate actions on the machine, and the sequence number for the Auto-Patching request, check the agent log for more details in `/var/log/waagent.log`. Every Auto-Patching request has a unique sequence number associated with it on the machine. Look for a log similar to: `2021-01-20T16:57:00.607529Z INFO ExtHandler`.

The package directory for the extension is `/var/lib/waagent/Microsoft.CPlat.Core.Edp.LinuxPatchExtension-<version>` and in the `/status` subfolder is a `<sequence number>.status` file, which includes a brief description of the actions performed during a single Auto-Patching request, and the status. It also includes a short list of errors that occurred while applying updates. 

To review the logs related to all actions performed by the extension, check for more details in `/var/log/azure/Microsoft.CPlat.Core.Edp.LinuxPatchExtension/`. It includes the following two log files of interest:

* `<seq number>.core.log`: Contains details related to the patch actions, such as the patches assessed and installed on the machine, and any issues encountered in the process.
* `<Date and Time>_<Handler action>.ext.log`: There is a wrapper above the patch action, which is used to manage the extension and invoke specific patch operation. This log contains details about the wrapper. For Auto-Patching, the `<Date and Time>_Enable.ext.log` has details on whether the specific patch operation was invoked.

### Azure Windows VM 

To verify if the Microsoft Azure Virtual Machine Agent (VM Agent) is running, has triggered appropriate actions on the machine, and the sequence number for the Auto-Patching request, check the agent log for more details in `C:\WindowsAzure\Logs\AggregateStatus`. The package directory for the extension is `C:\Packages\Plugins\Microsoft.CPlat.Core.WindowsPatchExtension<version>`.

To review the logs related to all actions performed by the extension, check for more details in `C:\WindowsAzure\Logs\Plugins\Microsoft.CPlat.Core.WindowsPatchExtension<version>`. It includes the following two log files of interest:

* `WindowsUpdateExtension.log`: Contains details related to the patch actions, such as the patches assessed and installed on the machine, and any issues encountered in the process.
* `CommandExecution.log`: There is a wrapper above the patch action, which is used to manage the extension and invoke specific patch operation. This log contains details about the wrapper. For Auto-Patching, the log has details on whether the specific patch operation was invoked.

## Unable to change the patch orchestration option to manual updates from automatic updates

### Issue 

Azure machine has the patch orchestration option as AutomaticByOS/Windows automatic updates and you are unable to change the patch orchestration to Manual Updates using Change update settings.

### Resolution

If you don't want any patch installation to be orchestrated by Azure or aren't using custom patching solutions, you can change the patch orchestration option to **Customer Managed Schedules (Preview)** or **AutomaticByPlatform/ByPassPlatformSafetyChecksOnUserSchedule** and not associate a schedule/maintenance configuration to the machine. This will ensure that no patching is performed on the machine until you change it explicitly. For more information, see **scenario 2** in [User scenarios](prerequsite-for-schedule-patching.md#user-scenarios).

:::image type="content" source="./media/troubleshoot/known-issue-update-settings-failed.png" alt-text="Screenshot that shows a notification of failed update settings.":::

## Machine shows as "Not assessed" and shows an HRESULT exception

### Issue

* You have machines that show as `Not assessed` under **Compliance**, and you see an exception message below them.
* You see an HRESULT error code in the portal.

### Cause

The Update Agent (Windows Update Agent on Windows; the package manager for a Linux distribution) isn't configured correctly. Update Management relies on the machine's Update Agent to provide the updates that are needed, the status of the patch, and the results of deployed patches. Without this information, Update Management can't properly report on the patches that are needed or installed.

### Resolution

Try to perform updates locally on the machine. If this operation fails, it typically means that there's an update agent configuration error.

This problem is frequently caused by network configuration and firewall issues. Use the following checks to correct the issue.

* For Linux, check the appropriate documentation to make sure you can reach the network endpoint of your package repository.

* For Windows, check your agent configuration as listed in [Updates aren't downloading from the intranet endpoint (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).

  * If the machines are configured for Windows Update, make sure that you can reach the endpoints described in [Issues related to HTTP/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * If the machines are configured for Windows Server Update Services (WSUS), make sure that you can reach the WSUS server configured by the [WUServer registry key](/windows/deployment/update/waas-wu-settings).

If you see an HRESULT, double-click the exception displayed in red to see the entire exception message. Review the following table for potential resolutions or recommended actions.

|Exception  |Resolution or action  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Search the relevant error code in [Windows update error code list](https://support.microsoft.com/help/938205/windows-update-error-code-list) to find additional details about the cause of the exception.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | These indicate network connectivity issues. Make sure your machine has network connectivity to Update Management. See the [network planning](../automation/update-management/plan-deployment.md#ports) section for a list of required ports and addresses.        |
|`0x8024001E`| The update operation didn't complete because the service or system was shutting down.|
|`0x8024002E`| Windows Update service is disabled.|
|`0x8024402C`     | If you're using a WSUS server, make sure the registry values for `WUServer` and `WUStatusServer` under the  `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` registry key specify the correct WSUS server.        |
|`0x80072EE2`|There's a network connectivity issue or an issue in talking to a configured WSUS server. Check WSUS settings and make sure the service is accessible from the client.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Make sure the Windows Update service (wuauserv) is running and not disabled.        |
|`0x80070005`| An access denied error can be caused by any one of the following:<br> Infected computer<br> Windows Update settings not configured correctly<br> File permission error with %WinDir%\SoftwareDistribution folder<br> Insufficient disk space on the system drive (C:).
|Any other generic exception     | Run a search on the internet for possible resolutions, and work with your local IT support.         |

Reviewing the **%Windir%\Windowsupdate.log** file can also help you determine possible causes. For more information about how to read the log, see [How to read the Windowsupdate.log file](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file).

You can also download and run the [Windows Update troubleshooter](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) to check for any issues with Windows Update on the machine.

> [!NOTE]
> The [Windows Update troubleshooter](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) documentation indicates that it's for use on Windows clients, but it also works on Windows Server.

### Arc-enabled servers

For Arc-enabled servers, review the [troubleshoot VM extensions](../azure-arc/servers/troubleshoot-vm-extensions.md) article for general troubleshooting steps.

To review the logs related to all actions performed by the extension, on Windows check for more details in `C:\ProgramData\GuestConfig\extension_Logs\Microsoft.SoftwareUpdateManagement\WindowsOsUpdateExtension`. It includes the following two log files of interest:

* `WindowsUpdateExtension.log`: Contains details related to the patch actions, such as the patches assessed and installed on the machine, and any issues encountered in the process.
* `cmd_execution_<numeric>_stdout.txt`: There is a wrapper above the patch action, which is used to manage the extension and invoke specific patch operation. This log contains details about the wrapper. For Auto-Patching, the log has details on whether the specific patch operation was invoked.
* `cmd_excution_<numeric>_stderr.txt`

## Known issues in schedule patching

- For concurrent/conflicting schedule, only one schedule will be triggered. The other schedule will be triggered once a schedule is finished.
- If a machine is newly created, the schedule might have 15 minutes of schedule trigger delay in case of Azure VMs.
- Policy definition *[Preview]: Schedule recurring updates using Update Management Center* with version 1.0.0-preview successfully remediates resources however, it will always show them as non-compliant. The current value of the existence condition is a placeholder that will always evaluate to false.

### Scenario: Unable to apply patches for the shutdown machines 

#### Issue

Patches aren’t getting applied for the machines that are in shutdown state, and you may also see that machines are losing their associated maintenance configurations/Schedules.

#### Cause

The machines are in a shutdown state.

### Resolution: 

Keep your machines turned on at least 15 minutes before the scheduled update. For more information, see, [Shut down machines](../virtual-machines/maintenance-configurations.md#shut-down-machines).


### Scenario: Patch run failed with Maintenance window exceeded property showing true even if time remained

#### Issue

When you view an update deployment in **Update History**, the property **Failed with Maintenance window exceeded** shows **true** even though enough time was left for execution. In this case, the one of the following is possible:

* No updates are shown.
* One or more updates are in a **Pending** state.
* Reboot status is **Required**, but a reboot wasn't attempted even when the reboot setting passed was `IfRequired` or `Always`.

#### Cause

During an update deployment, it checks for maintenance window utilization at multiple steps. 10 minutes of the maintenance window are reserved for reboot at any point. Before getting a list of missing updates or downloading/installing any update (except Windows service pack updates), it checks to verify if there are 15 minutes + 10 minutes for reboot (that is, 25 mins left in the maintenance window). 
For  Windows service pack updates, we check for 20 minutes + 10 minutes for reboot (that is, 30 minutes). If the deployment doesn't have the sufficient left, it skips the scan/download/install of updates. The deployment run then checks if a reboot is needed and if there's ten minutes left in the maintenance window. If there is, the deployment triggers a reboot, otherwise the reboot is skipped. In such cases, the status is updated to **Failed**, and the Maintenance window exceeded property is updated to ***true**. For cases where the time left is less than 25 minutes, updates aren't scanned or attempted for installation. 

More details can be found by reviewing the logs in the file path provided in the error message of the deployment run.

#### Resolution

Setting a longer time range for maximum duration when triggering an [on-demand update deployment](deploy-updates.md) helps avoid the problem.




## Next steps

* To learn more about Azure Update management center (preview), see the [Overview](overview.md).
* To view logged results from all your machines, see [Querying logs and results from update management center (preview)](query-logs.md).