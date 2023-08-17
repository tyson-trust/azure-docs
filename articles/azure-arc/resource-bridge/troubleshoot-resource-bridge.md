---
title: Troubleshoot Azure Arc resource bridge (preview) issues
description: This article tells how to troubleshoot and resolve issues with the Azure Arc resource bridge (preview) when trying to deploy or connect to the service.
ms.date: 03/15/2023
ms.topic: conceptual
---

# Troubleshoot Azure Arc resource bridge (preview) issues

This article provides information on troubleshooting and resolving issues that may occur while attempting to deploy, use, or remove the Azure Arc resource bridge (preview). The resource bridge is a packaged virtual machine, which hosts a *management* Kubernetes cluster. For general information, see [Azure Arc resource bridge (preview) overview](./overview.md).

## General issues

### Logs

For issues encountered with Arc resource bridge, collect logs for further investigation using the Azure CLI [`az arcappliance logs`](/cli/azure/arcappliance/logs) command. This command needs to be run from the same management machine that was used to run commands to deploy the Arc resource bridge. If there is a problem collecting logs, most likely the management machine is unable to reach the Appliance VM, and the network administrator needs to allow communication between the management machine to the Appliance VM.

The `az arcappliance logs` command requires SSH to the Azure Arc resource bridge VM. The SSH key is saved to the management machine. To use a different machine to run the logs command, make sure the following files are copied to the machine in the same location:

```azurecli
$HOME\.KVA\.ssh\logkey.pub
$HOME\.KVA\.ssh\logkey 
```

### Remote PowerShell is not supported

If you run `az arcappliance` CLI commands for Arc Resource Bridge via remote PowerShell, you may experience various problems. For instance, you might see an [authentication handshake failure error when trying to install the resource bridge on an Azure Stack HCI cluster](#authentication-handshake-failure) or another type of error.

Using `az arcappliance` commands from remote PowerShell is not currently supported. Instead, sign in to the node through Remote Desktop Protocol (RDP) or use a console session.

### Resource bridge cannot be updated

In this release, all the parameters are specified at time of creation. To update the Azure Arc resource bridge, you must delete it and redeploy it again.

For example, if you specified the wrong location, or subscription during deployment, later the resource creation fails. If you only try to recreate the resource without redeploying the resource bridge VM, you'll see the status stuck at `WaitForHeartBeat`.

To resolve this issue, delete the appliance and update the appliance YAML file. Then redeploy and create the resource bridge.

### Connection closed before server preface received

When there are multiple attempts to deploy Arc resource bridge, expired credentials left on the management machine may cause future deployments to fail. The error will contain the message `Unavailable desc = connection closed before server preface received`. This error will surface in various `az arcappliance` commands including `validate`, `prepare` and `delete`.

To resolve this error, the .wssd\python and .wssd\kva folders in the user profile directory need to be manually deleted from the management machine. Depending on where  the deployment errored, there may not be a kva folder to delete. You can delete these folders manually by navigating to the user profile directory (typically `C:\Users\<username>`), then deleting the `.wssd\python` and `.wssd\kva` folders. After they are deleted, retry the command that failed.

### Token refresh error

When you run the Azure CLI commands, the following error may be returned: *The refresh token has expired or is invalid due to sign-in frequency checks by conditional access.* The error occurs because when you sign in to Azure, the token has a maximum lifetime. When that lifetime is exceeded, you need to sign in to Azure again by using the `az login` command.

### Default host resource pools are unavailable for deployment

When using the `az arcappliance createConfig` or `az arcappliance run` command, there will be an interactive experience which shows the list of the VMware entities where user can select to deploy the virtual appliance. This list will show all user-created resource pools along with default cluster resource pools, but the default host resource pools aren't listed.

When the appliance is deployed to a host resource pool, there is no high availability if the host hardware fails. Because of this, we recommend that you don't try to deploy the appliance in a host resource pool.

### Resource bridge status "Offline" and `provisioningState` "Failed"

When deploying Arc resource bridge, the bridge may appear to be successfully deployed, because no errors were encountered when running `az arcappliance deploy` or `az arcappliance create`. However, when viewing the bridge in Azure portal, you may see status shows as **Offline**, and `az arcappliance show` may show the `provisioningState` as **Failed**. This happens when required providers are not registered before the bridge is deployed.

To resolve this problem, delete the resource bridge, register the providers, then redeploy the resource bridge.

1. Delete the resource bridge:

   ```azurecli
   az arcappliance delete <fabric> --config-file <path to appliance.yaml>
   ```

1. Register the providers:

   ```azurecli
   az provider register --namespace Microsoft.ExtendedLocation –-wait
   az provider register --namespace Microsoft.ResourceConnector –-wait
   ```

1. Redeploy the resource bridge.

> [!NOTE]
> Partner products (such as Arc-enabled VMware vSphere) may have their own required providers to register. To see additional providers that must be registered, see the product's documentation.

### Expired credentials in the appliance VM

Arc resource bridge consists of an appliance VM that is deployed to the on-premises infrastructure. The appliance VM maintains a connection to the management endpoint of the on-premises infrastructure using locally stored credentials. If these credentials are not updated, the resource bridge is no longer able to communicate with the management endpoint. This may cause problems when trying to upgrade the resource bridge or manage VMs through Azure.

To fix this, the credentials in the appliance VM need to be updated. For more information, see [Update credentials in the appliance VM](maintenance.md#update-credentials-in-the-appliance-vm).


## Networking issues

### Back-off pulling image error

When trying to deploy Arc resource bridge, you may see an error that contains `back-off pulling image \\\"url"\\\: FailFastPodCondition`. This error is caused when the appliance VM can't reach the URL specified in the error. To resolve this issue, make sure the appliance VM meets system requirements, including internet access connectivity to [required allowlist URLs](network-requirements.md).

### Not able to connect to URL

If you receive an error that contains `Not able to connect to https://example.url.com`, check with your network administrator to ensure your network allows all of the required firewall and proxy URLs to deploy Arc resource bridge. For more information, see [Azure Arc resource bridge (preview) network requirements](network-requirements.md). 

### .local not supported

When trying to set the configuration for Arc resource bridge, you may receive an error message similar to: 

`"message": "Post \"https://esx.lab.local/52b-bcbc707ce02c/disk-0.vmdk\": dial tcp: lookup esx.lab.local: no such host"`

This occurs when a `.local` path is provided for a configuration setting, such as proxy, dns, datastore or management endpoint (such as vCenter). Arc resource bridge appliance VM uses Azure Linux OS, which doesn't support `.local` by default. A workaround could be to provide the IP address where applicable.


### Azure Arc resource bridge is unreachable

Azure Arc resource bridge (preview) runs a Kubernetes cluster, and its control plane requires a static IP address. The IP address is specified in the `infra.yaml` file. If the IP address is assigned from a DHCP server, the address can change if not reserved. Rebooting the Azure Arc resource bridge (preview) or VM can trigger an IP address change, resulting in failing services.

Intermittently, the resource bridge (preview) can lose the reserved IP configuration. This is due to the behavior described in [loss of VIPs when systemd-networkd is restarted](https://github.com/acassen/keepalived/issues/1385). When the IP address isn't assigned to the Azure Arc resource bridge (preview) VM, any call to the resource bridge API server will fail. As a result, you can't create any new resource through the resource bridge (preview), ranging from connecting to Azure Arc private cloud, create a custom location, create a VM, etc.

Another possible cause is slow disk access. Azure Arc resource bridge uses etcd which requires 10 ms latency or less per [recommendation](https://docs.openshift.com/container-platform/4.6/scalability_and_performance/recommended-host-practices.html#recommended-etcd-practices_). If the underlying disk has low performance, it can impact the operations, and causing failures.

To resolve this issue, reboot the resource bridge (preview) VM, and it should recover its IP address. If the address is assigned from a DHCP server, reserve the IP address associated with the resource bridge (preview).

### SSL proxy configuration issues

Be sure that the proxy server on your management machine trusts both the SSL certificate for your SSL proxy and the SSL certificate of the Microsoft download servers.

For more information, see [SSL proxy configuration](network-requirements.md#ssl-proxy-configuration).

### KVA timeout error

While trying to deploy Arc Resource Bridge, a "KVA timeout error" may appear. The "KVA timeout error" is a generic error that can be the result of a variety of network misconfigurations that involve the management machine, Appliance VM, or Control Plane IP not having communication with each other, to the internet, or required URLs. This communication failure is often due to issues with DNS resolution, proxy settings, network configuration, or internet access.  

For clarity, "management machine" refers to the machine where deployment CLI commands are being run. "Appliance VM" is the VM that hosts Arc resource bridge. "Control Plane IP" is the IP of the control plane for the Kubernetes management cluster in the Appliance VM.

#### Top causes of the KVA timeout error  

- Management machine is unable to communicate with Control Plane IP and Appliance VM IP.
- Appliance VM is unable to communicate with the management machine, vCenter endpoint (for VMware), or MOC cloud agent endpoint (for Azure Stack HCI).  
- Appliance VM does not have internet access.
- Appliance VM has internet access, but connectivity to one or more required URLs is being blocked, possibly due to a proxy or firewall.
- Appliance VM is unable to reach a DNS server that can resolve internal names, such as vCenter endpoint for vSphere or cloud agent endpoint for Azure Stack HCI. The DNS server must also be able to resolve external addresses, such as Azure service addresses and container registry names.  
- Proxy server configuration on the management machine or Arc resource bridge configuration files is incorrect. This can impact both the management machine and the Appliance VM. When the `az arcappliance prepare` command is run, the management machine won't be able to connect and download OS images if the host proxy isn't correctly configured. Internet access on the Appliance VM might be broken by incorrect or missing proxy configuration, which impacts the VM’s ability to pull container images.  

#### Troubleshoot KVA timeout error

To resolve the error, one or more network misconfigurations may need to be addressed. Follow the steps below to address the most common reasons for this error.

1. When there is a problem with deployment, the first step is to collect logs by Appliance VM IP (not by kubeconfig, as the kubeconfig may be empty if deploy command did not complete). Problems collecting logs are most likely due to the management machine being unable to reach the Appliance VM.

   Once logs are collected, extract the folder and open kva.log. Review the kva.log for more information on the failure to help pinpoint the cause of the KVA timeout error.

1. The management machine must be able to communicate with the Appliance VM IP and Control Plane IP. Ping the Control Plane IP and Appliance VM IP from the management machine and verify there is a response from both IPs.

   If a request times out, the management machine is not able to communicate with the IP(s). This could be caused by a closed port, network misconfiguration or a firewall block. Work with your network administrator to allow communication between the management machine to the Control Plane IP and Appliance VM IP.

1. Appliance VM IP and Control Plane IP must be able to communicate with the management machine and vCenter endpoint (for VMware) or MOC cloud agent endpoint (for HCI). Work with your network administrator to ensure the network is configured to permit this. This may require adding a firewall rule to open port 443 from the Appliance VM IP and Control Plane IP to vCenter or port 65000 and 55000 for Azure Stack HCI MOC cloud agent. Review [network requirements for Azure Stack HCI](/azure-stack/hci/manage/azure-arc-vm-management-prerequisites#network-port-requirements) and [VMware](../vmware-vsphere/quick-start-connect-vcenter-to-arc-using-script.md) for Arc resource bridge.

1. Appliance VM IP and Control Plane IP need internet access to [these required URLs](#not-able-to-connect-to-url). Azure Stack HCI requires [additional URLs](/azure-stack/hci/manage/azure-arc-vm-management-prerequisites). Work with your network administrator to ensure that the IPs can access the required URLs.

1. In a non-proxy environment, the management machine must have external and internal DNS resolution. The management machine must be able to reach a DNS server that can resolve internal names such as vCenter endpoint for vSphere or cloud agent endpoint for Azure Stack HCI. The DNS server also needs to be able to [resolve external addresses](#not-able-to-connect-to-url), such as Azure URLs and OS image download URLs. Work with your system administrator to ensure that the management machine has internal and external DNS resolution. In a proxy environment, the DNS resolution on the proxy server should resolve internal endpoints and [required external addresses](#not-able-to-connect-to-url).

   To test DNS resolution to an internal address from the management machine in a non-proxy scenario, open command prompt and run `nslookup <vCenter endpoint or HCI MOC cloud agent IP>`. You should receive an answer if the management machine has internal DNS resolution in a non-proxy scenario.  

1. Appliance VM needs to be able to reach a DNS server that can resolve internal names such as vCenter endpoint for vSphere or cloud agent endpoint for Azure Stack HCI. The DNS server also needs to be able to resolve external/internal addresses, such as Azure service addresses and container registry names for download of the Arc resource bridge container images from the cloud.

   Verify that the DNS server IP used to create the configuration files has internal and external address resolution. If not, [delete the appliance](/cli/azure/arcappliance/delete), recreate the Arc resource bridge configuration files with the correct DNS server settings, and then deploy Arc resource bridge using the new configuration files.

## Move Arc resource bridge location 
Resource move of Arc resource bridge isn't currently supported. You'll need to delete the Arc resource bridge, then re-deploy it to the desired location. 

## Azure Arc-enabled VMs on Azure Stack HCI issues

For general help resolving issues related to Azure Arc-enabled VMs on Azure Stack HCI, see [Troubleshoot Azure Arc-enabled virtual machines](/azure-stack/hci/manage/troubleshoot-arc-enabled-vms).

### Authentication handshake failure

When running an `az arcappliance` command, you may see a connection error: `authentication handshake failed: x509: certificate signed by unknown authority`

This is usually caused when trying to run commands from remote PowerShell, which is not supported by Azure Arc resource bridge.

To install Azure Arc resource bridge on an Azure Stack HCI cluster, `az arcappliance` commands must be run locally on a node in the cluster. Sign in to the node through Remote Desktop Protocol (RDP) or use a console session to run these commands.

## Azure Arc-enabled VMware VCenter issues

### `az arcappliance prepare` failure

The `arcappliance` extension for Azure CLI enables a [prepare](/cli/azure/arcappliance/prepare) command, which enables you to download an OVA template to your vSphere environment. This OVA file is used to deploy the Azure Arc resource bridge. The `az arcappliance prepare` command uses the vSphere SDK and can result in the following error:

```azurecli
$ az arcappliance prepare vmware --config-file <path to config> 

Error: Error in reading OVA file: failed to parse ovf: strconv.ParseInt: parsing "3670409216": 
value out of range.
```

This error occurs when you run the Azure CLI commands in a 32-bit context, which is the default behavior. The vSphere SDK only supports running in a 64-bit context. The specific error returned from the vSphere SDK is `Unable to import ova of size 6GB using govc`. When you install the Azure CLI, it's a 32-bit Windows Installer package. However, the Azure CLI `az arcappliance` extension needs to run in a 64-bit context.

To resolve this issue, perform the following steps to configure your management machine with the Azure CLI 64-bit version:

1. Uninstall the current version of the Azure CLI on Windows following these [steps](/cli/azure/install-azure-cli-windows#uninstall).
1. Install version 3.6 or higher of [Python](https://www.python.org/downloads/windows/) (64-bit).

   > [!IMPORTANT]
   > After you install Python, make sure to confirm that its path is added to the PATH environmental variable.

1. Install the [pip](https://pypi.org/project/pip/) package installer for Python.
1. Verify Python is installed correctly by running `py` in a Command Prompt.
1. From an elevated PowerShell console, run `pip install azure-cli` to install the Azure CLI from PyPI.

After you complete these steps, you can get started using the Azure Arc appliance CLI extension in a new PowerShell console.

### Error during host configuration

When you deploy the resource bridge on VMware vCenter, if you have been using the same template to deploy and delete the appliance multiple times, you may encounter the following error:

`Appliance cluster deployment failed with error:
Error: An error occurred during host configuration`

To resolve this issue, delete the existing template manually. Then run [`az arcappliance prepare`](/cli/azure/arcappliance/prepare) to download a new template for deployment.

### Unable to find folders

When deploying the resource bridge on VMware vCenter, you specify the folder in which the template and VM will be created. The folder must be VM and template folder type. Other types of folder, such as storage folders, network folders, or host and cluster folders, can't be used by the resource bridge deployment.

### Insufficient permissions

When deploying the resource bridge on VMware Vcenter, you may get an error saying that you have insufficient permission. To resolve this issue, make sure that your user account has all of the following privileges in VMware vCenter and then try again.

```
"Datastore.AllocateSpace"
"Datastore.Browse"
"Datastore.DeleteFile"
"Datastore.FileManagement"
"Folder.Create"
"Folder.Delete"
"Folder.Move"
"Folder.Rename"
"InventoryService.Tagging.CreateTag"
"Sessions.ValidateSession"
"Network.Assign"
"Resource.ApplyRecommendation"
"Resource.AssignVMToPool"
"Resource.HotMigrate"
"Resource.ColdMigrate"
"StorageViews.View"
"System.Anonymous"
"System.Read"
"System.View"
"VirtualMachine.Config.AddExistingDisk"
"VirtualMachine.Config.AddNewDisk"
"VirtualMachine.Config.AddRemoveDevice"
"VirtualMachine.Config.AdvancedConfig"
"VirtualMachine.Config.Annotation"
"VirtualMachine.Config.CPUCount"
"VirtualMachine.Config.ChangeTracking"
"VirtualMachine.Config.DiskExtend"
"VirtualMachine.Config.DiskLease"
"VirtualMachine.Config.EditDevice"
"VirtualMachine.Config.HostUSBDevice"
"VirtualMachine.Config.ManagedBy"
"VirtualMachine.Config.Memory"
"VirtualMachine.Config.MksControl"
"VirtualMachine.Config.QueryFTCompatibility"
"VirtualMachine.Config.QueryUnownedFiles"
"VirtualMachine.Config.RawDevice"
"VirtualMachine.Config.ReloadFromPath"
"VirtualMachine.Config.RemoveDisk"
"VirtualMachine.Config.Rename"
"VirtualMachine.Config.ResetGuestInfo"
"VirtualMachine.Config.Resource"
"VirtualMachine.Config.Settings"
"VirtualMachine.Config.SwapPlacement"
"VirtualMachine.Config.ToggleForkParent"
"VirtualMachine.Config.UpgradeVirtualHardware"
"VirtualMachine.GuestOperations.Execute"
"VirtualMachine.GuestOperations.Modify"
"VirtualMachine.GuestOperations.ModifyAliases"
"VirtualMachine.GuestOperations.Query"
"VirtualMachine.GuestOperations.QueryAliases"
"VirtualMachine.Hbr.ConfigureReplication"
"VirtualMachine.Hbr.MonitorReplication"
"VirtualMachine.Hbr.ReplicaManagement"
"VirtualMachine.Interact.AnswerQuestion"
"VirtualMachine.Interact.Backup"
"VirtualMachine.Interact.ConsoleInteract"
"VirtualMachine.Interact.CreateScreenshot"
"VirtualMachine.Interact.CreateSecondary"
"VirtualMachine.Interact.DefragmentAllDisks"
"VirtualMachine.Interact.DeviceConnection"
"VirtualMachine.Interact.DisableSecondary"
"VirtualMachine.Interact.DnD"
"VirtualMachine.Interact.EnableSecondary"
"VirtualMachine.Interact.GuestControl"
"VirtualMachine.Interact.MakePrimary"
"VirtualMachine.Interact.Pause"
"VirtualMachine.Interact.PowerOff"
"VirtualMachine.Interact.PowerOn"
"VirtualMachine.Interact.PutUsbScanCodes"
"VirtualMachine.Interact.Record"
"VirtualMachine.Interact.Replay"
"VirtualMachine.Interact.Reset"
"VirtualMachine.Interact.SESparseMaintenance"
"VirtualMachine.Interact.SetCDMedia"
"VirtualMachine.Interact.SetFloppyMedia"
"VirtualMachine.Interact.Suspend"
"VirtualMachine.Interact.TerminateFaultTolerantVM"
"VirtualMachine.Interact.ToolsInstall"
"VirtualMachine.Interact.TurnOffFaultTolerance"
"VirtualMachine.Inventory.Create"
"VirtualMachine.Inventory.CreateFromExisting"
"VirtualMachine.Inventory.Delete"
"VirtualMachine.Inventory.Move"
"VirtualMachine.Inventory.Register"
"VirtualMachine.Inventory.Unregister"
"VirtualMachine.Namespace.Event"
"VirtualMachine.Namespace.EventNotify"
"VirtualMachine.Namespace.Management"
"VirtualMachine.Namespace.ModifyContent"
"VirtualMachine.Namespace.Query"
"VirtualMachine.Namespace.ReadContent"
"VirtualMachine.Provisioning.Clone"
"VirtualMachine.Provisioning.CloneTemplate"
"VirtualMachine.Provisioning.CreateTemplateFromVM"
"VirtualMachine.Provisioning.Customize"
"VirtualMachine.Provisioning.DeployTemplate"
"VirtualMachine.Provisioning.DiskRandomAccess"
"VirtualMachine.Provisioning.DiskRandomRead"
"VirtualMachine.Provisioning.FileRandomAccess"
"VirtualMachine.Provisioning.GetVmFiles"
"VirtualMachine.Provisioning.MarkAsTemplate"
"VirtualMachine.Provisioning.MarkAsVM"
"VirtualMachine.Provisioning.ModifyCustSpecs"
"VirtualMachine.Provisioning.PromoteDisks"
"VirtualMachine.Provisioning.PutVmFiles"
"VirtualMachine.Provisioning.ReadCustSpecs"
"VirtualMachine.State.CreateSnapshot"
"VirtualMachine.State.RemoveSnapshot"
"VirtualMachine.State.RenameSnapshot"
"VirtualMachine.State.RevertToSnapshot"
```

## Next steps

[Understand recovery operations for resource bridge in Azure Arc-enabled VMware vSphere disaster scenarios](../vmware-vsphere/disaster-recovery.md)

If you don't see your problem here or you can't resolve your issue, try one of the following channels for support:

- Get answers from Azure experts through [Microsoft Q&A](/answers/topics/azure-arc.html).

- Connect with [@AzureSupport](https://twitter.com/azuresupport), the official Microsoft Azure account for improving customer experience. Azure Support connects the Azure community to answers, support, and experts.

- [Open an Azure support request](../../azure-portal/supportability/how-to-create-azure-support-request.md).
