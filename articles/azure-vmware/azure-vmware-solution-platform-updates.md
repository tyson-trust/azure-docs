---
title: What's new in Azure VMware Solution
description: Learn about the platform updates to Azure VMware Solution.
ms.topic: reference
ms.custom: "references_regions, engagement-fy23"
ms.service: azure-vmware
ms.date: 4/24/2023
---

# What's new in Azure VMware Solution

Microsoft will regularly apply important updates to the Azure VMware Solution for new features and software lifecycle management. You'll receive a notification through Azure Service Health that includes the timeline of the maintenance. For more information, see [Host maintenance and lifecycle management](concepts-private-clouds-clusters.md#host-maintenance-and-lifecycle-management).

## May 2023

**Azure VMware Solution in Azure Gov**
 
Azure VMware Service will become generally available on May 17, 2023, to US Federal and State and Local Government (US) customers and their partners, in the regions of Arizona and Virgina. With this release, we are combining world-class Azure infrastructure together with VMware technologies by offering Azure VMware Solutions on Azure Government, which is designed, built, and supported by Microsoft. 

 
**New Azure VMware Solution Region: Qatar**

We are excited to announce that the Azure VMware Solution has gone live in Qatar Central and is now available to customers. 

With the introduction of AV36P in Qatar, customers will receive access to 36 cores, 2.6 GHz clock speed, 768GB of RAM, and 19.2TB of SSD storage.

To learn more about available regions of Azure products, see [Azure Products by Region](https://azure.microsoft.com/explore/global-infrastructure/products-by-region/?products=azure-vmware&regions=all)

## April 2023

**VMware HCX Run Commands**

Introducing Run Commands for VMware HCX on Azure VMware Solution. You can use these run commands to restart VMware HCX Cloud Manager in your Azure VMware Solution private cloud. Additionally, you can also scale VMware HCX Cloud Manager using Run Commands. To learn how to use run commands for VMware HCX, see [Use VMware HCX Run commands](use-hcx-run-commands.md).

## February 2023

All new Azure VMware Solution private clouds are being deployed with VMware NSX-T Data Center version 3.2.2. NSX-T Data Center versions in existing private clouds will be upgraded to NSX-T Data Center version 3.2.2 through April 2023.

**VMware HCX Enterprise Edition - Default**

VMware HCX Enterprise is now available and supported on Azure VMware Solution at no extra cost. VMware HCX Enterprise brings valuable [services](https://docs.vmware.com/en/VMware-HCX/4.5/hcx-user-guide/GUID-32AF32BD-DE0B-4441-95B3-DF6A27733EED.html), like Replicated Assisted vMotion (RAV) and Mobility Optimized Networking (MON). VMware HCX Enterprise is now automatically installed for all new VMware HCX add-on requests, and existing VMware HCX Advanced customers can upgrade to VMware HCX Enterprise using the Azure portal. Learn more on how to [Install and activate VMware HCX in Azure VMware Solution](install-vmware-hcx.md).

**Azure Log analytics - monitor Azure VMware Solution**

The data in Azure Log Analytics offer insights into issues by searching using Kusto Query Language.

**New SKU availability - AV36P and AV52 nodes**

The AV36P is now available in the West US Region. This node size is used for memory and storage workloads by offering increased Memory and NVME based SSDs.  

AV52 is now available in the East US 2 Region. This node size is used for intensive workloads with higher physical core count, additional memory, and larger capacity NVME based SSDs.

**Customer-managed keys using Azure Key Vault**

You can use customer-managed keys to bring and manage your master encryption keys to encrypt vSAN. Azure Key Vault allows you to store your privately managed keys securely to access your Azure VMware Solution data.

**Azure NetApp Files - more storage options available**    

You can use Azure NetApp Files volumes as a file share for Azure VMware Solution workloads using Network File System (NFS) or Server Message Block (SMB).

**Stretched clusters - increase uptime with Stretched Clusters (Preview)**

Stretched clusters for Azure VMware Solution, provides 99.99% uptime for mission critical applications that require the highest availability.

For more information, see [Azure Migration and Modernization blog](https://techcommunity.microsoft.com/t5/azure-migration-and/bg-p/AzureMigrationBlog). 

## January 2023

Starting January 2023, all new Azure VMware Solution private clouds are being deployed with Microsoft signed TLS certificate for vCenter and NSX.

## November 2022

AV36P and AV52 node sizes available in Azure VMware Solution. The new node sizes increase memory and storage options to optimize your workloads. The gains in performance enable you to do more per server, break storage bottlenecks, and lower transaction costs of latency-sensitive workloads. The availability of the new nodes allows for large latency-sensitive services to be hosted efficiently on the Azure VMware Solution infrastructure.
  
For pricing and region availability, see the [Azure VMware Solution pricing page](https://azure.microsoft.com/pricing/details/azure-vmware/) and see the [Products available by region page](https://azure.microsoft.com/explore/global-infrastructure/products-by-region/?products=azure-vmware&regions=all).

## July 2022

VMware HCX Cloud Manager in Azure VMware Solution can now be accessible over a public IP address. You can pair VMware HCX sites and create a service mesh from on-premises to Azure VMware Solution private cloud using Public IP.

VMware HCX with public IP is especially useful in cases where On-premises sites aren't connected to Azure via ExpressRoute or VPN. VMware HCX service mesh appliances can be configured with public IPs to avoid lower tunnel MTUs due to double encapsulation if a VPN is used for on-premises to cloud connections. For more information, please see [Enable VMware HCX over the internet](./enable-hcx-access-over-internet.md)

All new Azure VMware Solution private clouds are now deployed with VMware vCenter Server version 7.0 Update 3c and ESXi version 7.0 Update 3c. 

Any existing private clouds will be upgraded to those versions. For more information, please see [VMware ESXi 7.0 Update 3c Release Notes](https://docs.vmware.com/en/VMware-vSphere/7.0/rn/vsphere-esxi-70u3c-release-notes.html) and [VMware vCenter Server 7.0 Update 3c Release Notes](https://docs.vmware.com/en/VMware-vSphere/7.0/rn/vsphere-vcenter-server-70u3c-release-notes.html).

You'll receive a notification through Azure Service Health that includes the timeline of the upgrade. You can reschedule an upgrade as needed. This notification also provides details on the upgraded component, its effect on workloads, private cloud access, and other Azure services.

## June 2022

All new Azure VMware Solution private clouds in regions (East US2, Canada Central, North Europe, and Japan East), are now deployed in with VMware vCenter Server version 7.0 Update 3c and ESXi version 7.0 Update 3c.

Any existing private clouds in the above mentioned regions will also be upgraded to these versions. For more information, please see [VMware ESXi 7.0 Update 3c Release Notes](https://docs.vmware.com/en/VMware-vSphere/7.0/rn/vsphere-esxi-vcenter-server-70-release-notes.html) and [VMware vCenter Server 7.0 Update 3c Release Notes](https://docs.vmware.com/en/VMware-vSphere/7.0/rn/vsphere-vcenter-server-70u3c-release-notes.html).

## May 2022

All new Azure VMware Solution private clouds in regions (Germany West Central, Australia East, Central US and UK West), are now deployed with VMware vCenter Server version 7.0 Update 3c and ESXi version 7.0 Update 3c.

Any existing private clouds in the previously mentioned regions will be upgraded to those versions. For more information, please see [VMware ESXi 7.0 Update 3c Release Notes](https://docs.vmware.com/en/VMware-vSphere/7.0/rn/vsphere-esxi-70u3c-release-notes.html) and [VMware vCenter Server 7.0 Update 3c Release Notes](https://docs.vmware.com/en/VMware-vSphere/7.0/rn/vsphere-vcenter-server-70u3c-release-notes.html). 

You'll receive a notification through Azure Service Health that includes the timeline of the upgrade. You can reschedule an upgrade as needed. This notification also provides details on the upgraded component, its effect on workloads, private cloud access, and other Azure services.

All new Azure VMware Solution private clouds in regions (France Central, Brazil South, Japan West, Australia Southeast, Canada East, East Asia, and Southeast Asia), are now deployed with VMware vCenter Server version 7.0 Update 3c and ESXi version 7.0 Update 3c.

 Any existing private clouds in the previously mentioned regions will be upgraded to those versions. For more information, please see [VMware ESXi 7.0 Update 3c Release Notes](https://docs.vmware.com/en/VMware-vSphere/7.0/rn/vsphere-esxi-70u3c-release-notes.html) and [VMware vCenter Server 7.0 Update 3c Release Notes](https://docs.vmware.com/en/VMware-vSphere/7.0/rn/vsphere-vcenter-server-70u3c-release-notes.html).  

You'll receive a notification through Azure Service Health that includes the timeline of the upgrade. You can reschedule an upgrade as needed. This notification also provides details on the upgraded component, its effect on workloads, private cloud access, and other Azure services.

## February 2022

Per VMware security advisory [VMSA-2022-0004](https://www.vmware.com/security/advisories/VMSA-2022-0004.html), multiple vulnerabilities in VMware ESXi have been reported to VMware.

To address the vulnerabilities (CVE-2021-22040 and CVE-2021-22041) reported in this VMware security advisory, ESXi hosts have been patched in all Azure VMware Solution private clouds to ESXi 6.7, Patch Release ESXi670-202111001. All new Azure VMware Solution private clouds are deployed with the same version.

For more information on this ESXi version, see [VMware ESXi 6.7, Patch Release ESXi670-202111001](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202111001.html).

No further action is required.

## December 2021

Azure VMware Solution has completed maintenance  activities to address critical vulnerabilities in Apache Log4j. The fixes documented in the VMware security advisory [VMSA-2021-0028.6](https://www.vmware.com/security/advisories/VMSA-2021-0028.html) to address CVE-2021-44228 and CVE-2021-45046 have been applied to these Azure VMware Solution managed VMware products: vCenter Server, NSX-T Data Center, SRM and HCX. We strongly encourage customers to apply the fixes to on-premises HCX connector appliances. 

We also recommend customers to review the security advisory and apply the fixes for other affected VMware products or workloads. 
  
If you need any assistance or have questions, [contact us](https://portal.azure.com/#home).

VMware has announced a security advisory [VMSA-2021-0028](https://www.vmware.com/security/advisories/VMSA-2021-0028.html), addressing a critical vulnerability in Apache Log4j identified by CVE-2021-44228.  Azure VMware Solution is actively monitoring this issue. We're addressing this issue by applying VMware recommended workarounds or patches for Azure VMware Solution managed VMware components as they become available.

Note that you may experience intermittent connectivity to these components when we apply a fix. We strongly recommend that you read the advisory and patch or apply the recommended workarounds for other VMware products you may have deployed in Azure VMware Solution. If you need any assistance or have questions, [contact us](https://portal.azure.com).

## November 2021

Per VMware security advisory [VMSA-2021-0027](https://www.vmware.com/security/advisories/VMSA-2021-0027.html), multiple vulnerabilities in VMware vCenter Server have been reported to VMware.

To address the vulnerabilities (CVE-2021-21980 and CVE-2021-22049) reported in VMware security advisory, vCenter Server has been updated to 6.7 Update 3p release in all Azure VMware Solution private clouds.

For more information, see [VMware vCenter Server 6.7 Update 3p Release Notes](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3p-release-notes.html)

No further action is required.

## September 2021

Per VMware security advisory [VMSA-2021-0020](https://www.vmware.com/security/advisories/VMSA-2021-0020.html), multiple vulnerabilities in the VMware vCenter Server have been reported to VMware.  To address the vulnerabilities (CVE-2021-21991, CVE-2021-21992, CVE-2021-21993, CVE-2021-22005, CVE-2021-22006, CVE-2021-22007, CVE-2021-22008, CVE-2021-22009, CVE-2021-22010, CVE-2021-22011, CVE-2021-22012,CVE-2021-22013, CVE-2021-22014, CVE-2021-22015, CVE-2021-22016, CVE-2021-22017, CVE-2021-22018, CVE-2021-22019, CVE-2021-22020) reported in VMware security advisory [VMSA-2021-0020](https://www.vmware.com/security/advisories/VMSA-2021-0020.html), vCenter Server has been updated to 6.7 Update 3o in all Azure VMware Solution private clouds. All new Azure VMware Solution private clouds are deployed with vCenter Server version 6.7 Update 3o.   For more information, see [VMware vCenter Server 6.7 Update 3o Release Notes](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3o-release-notes.html).  No further action is required.

All new Azure VMware Solution private clouds are now deployed with ESXi version ESXi670-202103001 (Build number: 17700523). ESXi hosts in existing private clouds have been patched to this version. For more information on this ESXi version, see [VMware ESXi 6.7, Patch Release ESXi670-202103001](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202103001.html).

## July 2021

All new Azure VMware Solution private clouds are now deployed with NSX-T Data Center version 3.1.1. NSX-T Data Center version in existing private clouds will be upgraded through September 2021 to NSX-T Data Center 3.1.1 release.
 
You'll receive an email with the planned maintenance date and time. You can reschedule an upgrade. The email also provides details on the upgraded component, its effect on workloads, private cloud access, and other Azure services. 

For more information on this NSX-T Data Center version, see [VMware NSX-T Data Center 3.1.1 Release Notes](https://docs.vmware.com/en/VMware-NSX/3.1/rn/VMware-NSX-T-Data-Center-311-Release-Notes.html).

## May 2021

Per VMware security advisory [VMSA-2021-0010](https://www.vmware.com/security/advisories/VMSA-2021-0010.html), multiple vulnerabilities in VMware ESXi and vSphere Client (HTML5) have been reported to VMware.  To address the vulnerabilities ([CVE-2021-21985](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-21985) and [CVE-2021-21986](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-21986)) reported in VMware security advisory [VMSA-2021-0010](https://www.vmware.com/security/advisories/VMSA-2021-0010.html), vCenter Server has been updated in all Azure VMware Solution private clouds. No further action is required.

Azure VMware Solution service will do maintenance work through May 23, 2021, to apply important updates to the vCenter Server in your private cloud.  You'll receive a notification through Azure Service Health that includes the timeline of the maintenance for your private cloud.  During this time, VMware vCenter Server will be unavailable and you won't be able to manage VMs (stop, start, create, or delete). It's recommended that, during this time, you don't plan any other activities like scaling up private cloud, creating new networks, and so on, in your private cloud. There's no impact to workloads running in your private cloud.

## April 2021

All new Azure VMware Solution private clouds are now deployed with VMware vCenter Server version 6.7U3l and NSX-T Data Center version 2.5.2. We're not using NSX-T Data Center 3.1.1 for new private clouds because of an identified issue in NSX-T Data Center 3.1.1 that impacts customer VM connectivity. 

The VMware recommended mitigation was applied to all existing private clouds currently running NSX-T Data Center 3.1.1 on Azure VMware Solution. The workaround has been confirmed that there's no impact to customer VM connectivity.

## March 2021

All new Azure VMware Solution private clouds are deployed with VMware vCenter Server version 6.7U3l and NSX-T Data Center version 3.1.1. Any existing private clouds will be updated and upgraded **through June 2021** to the releases mentioned above.   You'll receive an email with the planned maintenance date and time. You can reschedule an upgrade. The email also provides details on the upgraded component, its effect on workloads, private cloud access, and other Azure services.  An hour before the upgrade, you'll receive a notification and then again when it finishes.

Azure VMware Solution service will do maintenance work **through March 19, 2021,** to update the vCenter Server in your private cloud to vCenter Server 6.7 Update 3l version. 
VMware vCenter Server will be unavailable during this time, so you can't manage your VMs (stop, start, create, delete) or private cloud scaling (adding/removing servers and clusters). However, VMware High Availability (HA) will continue to operate to protect existing VMs.  
For more information on this vCenter version, see [VMware vCenter Server 6.7 Update 3l Release Notes](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html).

Azure VMware Solution will apply the [VMware ESXi 6.7, Patch Release ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html) to existing privates **through March 15, 2021**.

Documented workarounds for the vSphere stack, as per [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html), will also be applied **through March 15, 2021**.
 
>[!NOTE]
>This is non-disruptive and should not impact the Azure VMware Solution service or workloads. During maintenance, various VMware vSphere alerts, such as _Lost network connectivity on DVPorts_ and _Lost uplink redundancy on DVPorts_, appear in vCenter Server and clear automatically as the maintenance progresses.

## Post update
Once complete, newer versions of VMware solution components will appear. If you notice any issues or have any questions, contact our support team by opening a support ticket.




