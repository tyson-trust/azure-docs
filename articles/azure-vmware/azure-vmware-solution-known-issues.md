---
title: Azure VMware Solution known issues
description: This article provides details about the known issues of Azure VMware Solution.
ms.topic: reference
ms.custom: "engagement-fy23"
ms.service: azure-vmware
ms.date: 4/20/2023
---

# Known issues: Azure VMware Solution

This article describes the currently known issues with Azure VMware Solution.

Refer to the table below to find details about resolution dates or possible workarounds. For more information about the different feature enhancements and bug fixes in Azure VMware Solution, see [What's New](azure-vmware-solution-platform-updates.md).

|Issue | Date discovered | Workaround | Date resolved |
| :------------------------------------- | :------------ | :------------- | :------------- |
| [VMSA-2021-002 ESXiArgs](https://www.vmware.com/security/advisories/VMSA-2021-0002.html) OpenSLP vulnerability publicized in February 2023  | 2021  | [Disable OpenSLP service](https://kb.vmware.com/s/article/76372)  | February 2021 - Resolved in [ESXi 7.0 U3c](concepts-private-clouds-clusters.md#vmware-software-versions) |
| After my private cloud NSX-T Data Center upgrade to version [3.2.2](https://docs.vmware.com/en/VMware-NSX/3.2.2/rn/vmware-nsxt-data-center-322-release-notes/index.html), the NSX-T Manager **DNS - Forwarder Upstream Server Timeout** alarm is raised | February 2023  | [Enable private cloud internet Access](concepts-design-public-internet-access.md), alarm is raised because NSX-T Manager cannot access the configured CloudFlare DNS server. Otherwise, [change the default DNS zone to point to a valid and reachable DNS server.](configure-dns-azure-vmware-solution.md) | February 2023 |
| When first logging into the vSphere Client, the **Cluster-n: vSAN health alarms are suppressed** alert is active in the vSphere Client | 2021  | This should be considered an informational message, since Microsoft manages the service. Select the **Reset to Green** link to clear it. | 2021 |
| When adding a cluster to my private cloud, the **Cluster-n: vSAN physical disk alarm 'Operation'** and **Cluster-n: vSAN cluster alarm 'vSAN Cluster Configuration Consistency'** alerts are active in the vSphere Client | 2021  | This should be considered an informational message, since Microsoft manages the service. Select the **Reset to Green** link to clear it. | 2021 |

In this article, you learned about the current known issues with the Azure VMware Solution.

For more information, see [About Azure VMware Solution](introduction.md).


