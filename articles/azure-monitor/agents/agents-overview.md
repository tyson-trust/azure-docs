---
title: Azure Monitor Agent overview
description: Overview of the Azure Monitor Agent, which collects monitoring data from the guest operating system of virtual machines.
ms.topic: conceptual
author: guywi-ms
ms.author: guywild
ms.date: 7/19/2023
ms.custom: references_regions
ms.reviewer: jeffwo

#customer-intent: As an IT manager, I want to understand the capabilities of Azure Monitor Agent to determine whether I can use the agent to collect the data I need from the operating systems of my virtual machines. 
---

# Azure Monitor Agent overview

Azure Monitor Agent (AMA) collects monitoring data from the guest operating system of Azure and hybrid virtual machines and delivers it to Azure Monitor for use by features, insights, and other services, such as [Microsoft Sentinel](../../sentintel/../sentinel/overview.md) and [Microsoft Defender for Cloud](../../defender-for-cloud/defender-for-cloud-introduction.md). Azure Monitor Agent replaces all of Azure Monitor's legacy monitoring agents. This article provides an overview of Azure Monitor Agent's capabilities and supported use cases.

Here's a short **introduction to Azure Monitor agent video**, which includes a quick demo of how to set up the agent from the Azure portal:  [ITOps Talk: Azure Monitor Agent](https://www.youtube.com/watch?v=f8bIrFU8tCs)  

## Benefits
Using Azure Monitor agent, you get immediate benefits as shown below:  

:::image type="content" source="media/azure-monitor-agent-overview/azure-monitor-agent-benefits.png" lightbox="media/azure-monitor-agent-overview/azure-monitor-agent-benefits.png" alt-text="Snippet of the Azure Monitor Agent benefits at a glance. This is described in more details below.":::

- **Cost savings** by [using data collection rules](data-collection-rule-azure-monitor-agent.md):
  - Enables targeted and granular data collection for a machine or subset(s) of machines, as compared to the "all or nothing" approach of legacy agents.
  - Allows filtering rules and data transformations to reduce the overall data volume being uploaded, thus lowering ingestion and storage costs significantly.  
- **Simpler management** including efficient troubleshooting:
  - Supports data uploads to multiple destinations (multiple Log Analytics workspaces, i.e. *multihoming* on Windows and Linux) including cross-region and cross-tenant data collection (using Azure LightHouse).
  - Centralized agent configuration "in the cloud" for enterprise scale throughout the data collection lifecycle, from onboarding to deployment to updates and changes over time. 
  - Any change in configuration is rolled out to all agents automatically, without requiring a client side deployment.
  - Greater transparency and control of more capabilities and services, such as Microsoft Sentinel, Defender for Cloud, and VM Insights.
- **Security and Performance**
  - Enhanced security through Managed Identity and Azure Active Directory (Azure AD) tokens (for clients).
  - Higher event throughput that is 25% better than the legacy Log Analytics (MMA/OMS) agents.
- **A single agent** that serves all data collection needs across [supported](#supported-operating-systems) servers and client devices. A single agent is the goal, although Azure Monitor Agent is currently converging with the Log Analytics agents.

## Consolidating legacy agents

Deploy Azure Monitor Agent on all new virtual machines, scale sets, and on-premises servers to collect data for [supported services and features](./azure-monitor-agent-migration.md#migrate-additional-services-and-features).

If you have machines already deployed with legacy Log Analytics agents, we recommend you [migrate to Azure Monitor Agent](./azure-monitor-agent-migration.md) as soon as possible. The legacy Log Analytics agent will not be supported after August 2024.

Azure Monitor Agent replaces the Azure Monitor legacy monitoring agents:

- [Log Analytics Agent](./log-analytics-agent.md): Sends data to a Log Analytics workspace and supports monitoring solutions. This is fully consolidated into Azure Monitor agent.
- [Telegraf agent](../essentials/collect-custom-metrics-linux-telegraf.md): Sends data to Azure Monitor Metrics (Linux only). Only basic Telegraf plugins are supported today in Azure Monitor agent.
- [Diagnostics extension](./diagnostics-extension-overview.md): Sends data to Azure Monitor Metrics (Windows only), Azure Event Hubs, and Azure Storage. This is not consolidated yet.

## Install the agent and configure data collection

Azure Monitor Agent uses [data collection rules](../essentials/data-collection-rule-overview.md), where you define which data you want each agent to collect. Data collection rules let you manage data collection settings at scale and define unique, scoped configurations for subsets of machines. You can define a rule to send data from multiple machines to multiple destinations across regions and tenants.

> [!NOTE]
> To send data across tenants, you must first enable [Azure Lighthouse](../../lighthouse/overview.md).

**To collect data using Azure Monitor Agent:**

1. Install the agent on the resource.

    | Resource type | Installation method | More information |
    |:---|:---|:---|
    | Virtual machines, scale sets | [Virtual machine extension](./azure-monitor-agent-manage.md#virtual-machine-extension-details) | Installs the agent by using Azure extension framework. |
    | On-premises servers (Azure Arc-enabled servers) | [Virtual machine extension](./azure-monitor-agent-manage.md#virtual-machine-extension-details) (after installing the [Azure Arc agent](../../azure-arc/servers/deployment-options.md)) | Installs the agent by using Azure extension framework, provided for on-premises by first installing [Azure Arc agent](../../azure-arc/servers/deployment-options.md). |
    | Windows 10, 11 desktops, workstations | [Client installer](./azure-monitor-agent-windows-client.md) | Installs the agent by using a Windows MSI installer. |
    | Windows 10, 11 laptops | [Client installer](./azure-monitor-agent-windows-client.md) | Installs the agent by using a Windows MSI installer. The installer works on laptops, but the agent *isn't optimized yet* for battery or network consumption. |
    
1. Define a data collection rule and associate the resource to the rule.

    The table below lists the types of data you can currently collect with the Azure Monitor Agent and where you can send that data.

    | Data source | Destinations | Description |
    |:---|:---|:---|
    | Performance | Azure Monitor Metrics (Public preview)<sup>1</sup> - Insights.virtualmachine namespace<br>Log Analytics workspace - [Perf](/azure/azure-monitor/reference/tables/perf) table | Numerical values measuring performance of different aspects of operating system and workloads |
    | Windows event logs (including sysmon events) | Log Analytics workspace - [Event](/azure/azure-monitor/reference/tables/Event) table | Information sent to the Windows event logging system |
    | Syslog | Log Analytics workspace - [Syslog](/azure/azure-monitor/reference/tables/syslog)<sup>2</sup> table | Information sent to the Linux event logging system. [Collect syslog with Azure Monitor Agent](data-collection-syslog.md) |
    |	Text logs and Windows IIS logs	|	Log Analytics workspace - custom table(s) created manually |	[Collect text logs with Azure Monitor Agent](data-collection-text-log.md)	|


    <sup>1</sup> On Linux, using Azure Monitor Metrics as the only destination is supported in v1.10.9.0 or higher.<br>
    <sup>2</sup> Azure Monitor Linux Agent versions 1.15.2 and higher support syslog RFC formats including Cisco Meraki, Cisco ASA, Cisco FTD, Sophos XG, Juniper Networks, Corelight Zeek, CipherTrust, NXLog, McAfee, and Common Event Format (CEF).

    > [!NOTE]
    > On rsyslog-based systems, Azure Monitor Linux Agent adds forwarding rules to the default ruleset defined in the rsyslog configuration. If multiple rulesets are used, inputs bound to non-default ruleset(s) are **not** forwarded to Azure Monitor Agent. For more information about multiple rulesets in rsyslog, see the [official documentation](https://www.rsyslog.com/doc/master/concepts/multi_ruleset.html).

    > [!NOTE]
    > Azure Monitor Agent also supports Azure service [SQL Best Practices Assessment](/sql/sql-server/azure-arc/assess/) which is currently Generally available. For more information, refer [Configure best practices assessment using Azure Monitor Agent](/sql/sql-server/azure-arc/assess#enable-best-practices-assessment).

## Supported services and features

For a list of features and services that use Azure Monitor Agent for data collection, see [Migrate to Azure Monitor Agent from Log Analytics agent](../agents/azure-monitor-agent-migration.md#migrate-additional-services-and-features).

## Supported regions

Azure Monitor Agent is available in all public regions, Azure Government and China clouds, for generally available features. It's not yet supported in air-gapped clouds. For more information, see [Product availability by region](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&rar=true&regions=all).

## Costs

There's no cost for the Azure Monitor Agent, but you might incur charges for the data ingested and stored. For information on Log Analytics data collection and retention and for customer metrics, see [Azure Monitor pricing](https://azure.microsoft.com/pricing/details/monitor/).

## Compare to legacy agents

The tables below provide a comparison of Azure Monitor Agent with the legacy the Azure Monitor telemetry agents for Windows and Linux.

### Windows agents

|	Category |	Area	|	Azure Monitor Agent	|	Log Analytics Agent	|	Diagnostics extension (WAD)	|
|:---|:---|:---|:---|:---|
|	**Environments supported**	|		|		|		|		|
|		|	Azure	|	X	|	X	|	X	|
|		|	Other cloud (Azure Arc)	|	X	|	X	|		|
|		|	On-premises (Azure Arc)	|	X	|	X	|		|
|		|	Windows Client OS	|	X	|		|		|
|	**Data collected**	|		|		|		|		|
|		|	Event Logs	|	X	|	X	|	X	|
|		|	Performance	|	X	|	X	|	X	|
|		|	File based logs	|	X 	|	X	|	X	|
|		|	IIS logs	|	X 	|	X	|	X	|
|		|	ETW events	|		|		|	X	|
|		|	.NET app logs	|		|		|	X	|
|		|	Crash dumps	|		|		|	X	|
|		|	Agent diagnostics logs	|		|		|	X	|
|	**Data sent to**	|		|		|		|		|
|		|	Azure Monitor Logs	|	X	|	X	|		|
|		|	Azure Monitor Metrics<sup>1</sup>	|	X (Public preview)	|		|	X (Public preview)	|
|		|	Azure Storage	|		|		|	X	|
|		|	Event Hub	|		|		|	X	|
|	**Services and features supported**	|		|		|		|		|
|		|	Microsoft Sentinel 	|	X ([View scope](./azure-monitor-agent-migration.md#migrate-additional-services-and-features))	|	X	|		|
|		|	VM Insights	|	X (Public preview)	|	X	|		|
|		|	Microsoft Defender for Cloud	|	X (Public preview)	|	X	|		|
|		|	Automation Update Management	|	|	X	|		|
|   | Azure Stack HCI | X |  |  |
|		|	Update Management Center	|	N/A (Public preview, independent of monitoring agents)	|		|		|
|		|	Change Tracking	| X (Public preview) |	X	|		|
|       |   SQL Best Practices Assessment | X |     |       |

### Linux agents

|	Category	|	Area	|	Azure Monitor Agent	|	Log Analytics Agent	|	Diagnostics extension (LAD)	|	Telegraf agent	|
|:---|:---|:---|:---|:---|:---|
|	**Environments supported**	|		|		|		|		|		|
|		|	Azure	|	X	|	X	|	X	|	X	|
|		|	Other cloud (Azure Arc)	|	X	|	X	|		|	X	|
|		|	On-premises (Azure Arc)	|	X	|	X	|		|	X	|
|	**Data collected**	|		|		|		|		|		|
|		|	Syslog	|	X	|	X	|	X	|		|
|		|	Performance	|	X	|	X	|	X	|	X	|
|		|	File based logs	|	X	|		|		|		|
|	**Data sent to**	|		|		|		|		|		|
|		|	Azure Monitor Logs	|	X	|	X	|		|		|
|		|	Azure Monitor Metrics<sup>1</sup>	|	X (Public preview)	|		|		|	X (Public preview)	|
|		|	Azure Storage	|		|		|	X	|		|
|		|	Event Hub	|		|		|	X	|		|
|	**Services and features supported**	|		|		|		|		|		|
|		|	Microsoft Sentinel 	|	X ([View scope](./azure-monitor-agent-migration.md#migrate-additional-services-and-features))	|	X	|		|
|		|	VM Insights	|	X (Public preview)	|	X 	|		|
|		|	Microsoft Defender for Cloud	|	X (Public preview)	|	X	|		|
|		|	Automation Update Management	|		|	X	|		|
|		|	Update Management Center	|	N/A (Public preview, independent of monitoring agents)	|		|		|
|		|	Change Tracking	| X (Public preview) |	X	|		|

<sup>1</sup> To review other limitations of using Azure Monitor Metrics, see [quotas and limits](../essentials/metrics-custom-overview.md#quotas-and-limits). On Linux, using Azure Monitor Metrics as the only destination is supported in v.1.10.9.0 or higher.

## Supported operating systems

The following tables list the operating systems that Azure Monitor Agent and the legacy agents support. All operating systems are assumed to be x64. x86 isn't supported for any operating system.  
View [supported operating systems for Azure Arc Connected Machine agent](../../azure-arc/servers/prerequisites.md#supported-operating-systems), which is a prerequisite to run Azure Monitor agent on physical servers and virtual machines hosted outside of Azure (that is, on-premises) or in other clouds.

### Windows

| Operating system | Azure Monitor agent | Log Analytics agent (legacy) | Diagnostics extension | 
|:---|:---:|:---:|:---:|
| Windows Server 2022                                      | X | X |   |
| Windows Server 2022 Core                                 | X |   |   |
| Windows Server 2019                                      | X | X | X |
| Windows Server 2019 Core                                 | X |   |   |
| Windows Server 2016                                      | X | X | X |
| Windows Server 2016 Core                                 | X |   | X |
| Windows Server 2012 R2                                   | X | X | X |
| Windows Server 2012                                      | X | X | X |
| Windows Server 2008 R2 SP1                               | X | X | X |
| Windows Server 2008 R2                                   |   |   | X |
| Windows Server 2008 SP2                                  |   | X |   |
| Windows 11 Client and Pro                                | X<sup>2</sup>, <sup>3</sup> |  |  |
| Windows 11 Enterprise<br>(including multi-session)       | X<sup>1</sup> |  |  |
| Windows 10 1803 (RS4) and higher                         | X<sup>2</sup> |  |  |
| Windows 10 Enterprise<br>(including multi-session) and Pro<br>(Server scenarios only<sup>1</sup>)  | X | X | X | 
| Windows 8 Enterprise and Pro<br>(Server scenarios only<sup>1</sup>)  |   | X |   |
| Windows 7 SP1<br>(Server scenarios only<sup>1</sup>)                 |   | X |   |
| Azure Stack HCI                                          | X | X |   |

<sup>1</sup> Running the OS on server hardware, for example, machines that are always connected, always turned on, and not running other workloads (PC, office, browser).<br>
<sup>2</sup> Using the Azure Monitor agent [client installer](./azure-monitor-agent-windows-client.md).<br>
<sup>3</sup> Also supported on Arm64-based machines.

### Linux

| Operating system | Azure Monitor agent <sup>1</sup> | Log Analytics agent (legacy) <sup>1</sup> | Diagnostics extension <sup>2</sup>|
|:---|:---:|:---:|:---:|
| AlmaLinux 8                                                 | X<sup>3</sup> | X |   |
| Amazon Linux 2017.09                                        |  | X |   |
| Amazon Linux 2                                              | X | X |   |
| CentOS Linux 8                                              | X | X |   |
| CentOS Linux 7                                              | X<sup>3</sup> | X | X |
| CBL-Mariner 2.0                                             | X<sup>3,4</sup> |   |   |
| Debian 11                                                   | X<sup>3</sup> |   |   |
| Debian 10                                                   | X | X |   |
| Debian 9                                                    | X | X | X |
| Debian 8                                                    |   | X |   |
| OpenSUSE 15                                                 | X |   |   |
| Oracle Linux 8                                              | X | X |   |
| Oracle Linux 7                                              | X | X | X |
| Oracle Linux 6.4+                                           |   |  | X |
| Red Hat Enterprise Linux Server 9+                         | X |  |   |
| Red Hat Enterprise Linux Server 8.6                         | X<sup>3</sup> | X<sup>2</sup> | X<sup>2</sup> |
| Red Hat Enterprise Linux Server 8+                           | X | X<sup>2</sup> | X<sup>2</sup> |
| Red Hat Enterprise Linux Server 7                           | X | X | X |
| Red Hat Enterprise Linux Server 6.7+                        |   |  | X |
| Rocky Linux 8                                               | X | X |   |
| SUSE Linux Enterprise Server 15 SP4                         | X<sup>3</sup> |   |   |
| SUSE Linux Enterprise Server 15 SP3                         | X |   |   |
| SUSE Linux Enterprise Server 15 SP2                         | X |   |   |
| SUSE Linux Enterprise Server 15 SP1                         | X | X |   |
| SUSE Linux Enterprise Server 15                             | X | X |   |
| SUSE Linux Enterprise Server 12                             | X | X | X |
| Ubuntu 22.04 LTS                                            | X |   |   |
| Ubuntu 20.04 LTS                                            | X<sup>3</sup> | X | X |
| Ubuntu 18.04 LTS                                            | X<sup>3</sup> | X | X |
| Ubuntu 16.04 LTS                                            | X | X | X |
| Ubuntu 14.04 LTS                                            |   | X | X |

<sup>1</sup> Requires Python (2 or 3) to be installed on the machine.<br>
<sup>2</sup> Requires Python 2 to be installed on the machine and aliased to the `python` command.<br>
<sup>3</sup> Also supported on Arm64-based machines.<br>
<sup>4</sup> Requires at least 4GB of disk space allocated (not provided by default).

> [!NOTE]
> Machines and appliances that run heavily customized or stripped-down versions of the above distributions and hosted solutions that disallow customization by the user are not supported. Azure Monitor and legacy agents rely on various packages and other baseline functionality that is often removed from such systems, and their installation may require some environmental modifications considered to be disallowed by the appliance vendor. For instance, [GitHub Enterprise Server](https://docs.github.com/en/enterprise-server/admin/overview/about-github-enterprise-server) is not supported due to heavy customization as well as [documented, license-level disallowance](https://docs.github.com/en/enterprise-server/admin/overview/system-overview#operating-system-software-and-patches) of operating system modification.

> [!NOTE]
> CBL-Mariner 2.0's disk size is by default around 1GB to provide storage COGS savings, compared to other Azure VMs that are around 30GB. However, the Azure Monitor Agent requires at least 4GB disk size in order to install and run successfully. Please check out [CBL-Mariner's documentation](https://eng.ms/docs/products/mariner-linux/gettingstarted/azurevm/azurevm#disk-size) for more information and instructions on how to increase disk size before installing the agent.

### Linux Hardening Standards

The Azure Monitoring Agent for Linux now officially supports various hardening standards for Linux operating systems and distros. Every release of the agent is tested and certified against the supported hardening standards. We test against the images that are publicly available on the Azure Marketplace and published by CIS and only support the settings and hardening that are applied to those images. If you apply additional customizations on your own golden images, and those settings are not covered by the CIS images, it will be considered a non-supported scenario.

*Only the Azure Monitoring Agent for Linux will support these hardening standards. There are no plans to support this in the Log Analytics Agent (legacy) or the Diagnostics Extension*

Currently supported hardening standards:
- SELinux
- CIS Lvl 1 and 2<sup>1</sup>

On the roadmap
- STIG
- FIPs

| Operating system | Azure Monitor agent <sup>1</sup> | Log Analytics agent (legacy) <sup>1</sup> | Diagnostics extension <sup>2</sup>|
|:---|:---:|:---:|:---:|
| CentOS Linux 7                                                 | X |   |   |
| Debian 10                                      | X |   |   |
| Ubuntu 18                                             | X |   |   |
| Ubuntu 20                                              | X |   |   |
| Red Hat Enterprise Linux Server 7                                              | X |   |   |
| Red Hat Enterprise Linux Server 8                                              | X |   |   |

<sup>1</sup> Supports only the above distros and versions

## Next steps

- [Install the Azure Monitor Agent](azure-monitor-agent-manage.md) on Windows and Linux virtual machines.
- [Create a data collection rule](data-collection-rule-azure-monitor-agent.md) to collect data from the agent and send it to Azure Monitor.
