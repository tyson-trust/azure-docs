---
title: Migrate Microsoft SQL Server Standalone to Azure VMware Solution
description: Learn how to migrate Microsoft SQL Server Standalone to Azure VMware Solution.
ms.topic: how-to
ms.service: azure-vmware
ms.date: 3/20/2023
ms.custom: engagement-fy23
---

# Migrate Microsoft SQL Server Standalone to Azure VMware Solution

In this article, you learn how to migrate Microsoft SQL Server standalone to Azure VMware Solution. 

When migrating Microsoft SQL Server Standalone to Azure VMware Solution, VMware HCX offers two migration profiles that can be used:

- HCX vMotion
- HCX Cold Migration

In both cases, consider the size and criticality of the database being migrated. For this how-to procedure, we have validated VMware HCX vMotion. VMware HCX Cold Migration is also valid, but it requires a longer downtime period. 

:::image type="content" source="media/sql-server-hybrid-benefit/migrated-sql-standalone-cluster.png" alt-text="Diagram showing the architecture of standalone SQL server for  Azure VMware Solution." border="false" lightbox="media/sql-server-hybrid-benefit/migrated-sql-standalone-cluster.png"::: 

## Prerequisites

- Review and record the storage and network configuration of every node in the cluster.
- Back up the full database.
- Back up the virtual machine running the Microsoft SQL Server instance. 
- Remove all cluster node VMs from any Distributed Resource Scheduler (DRS) groups and rules. 

- Configure VMware HCX  between your on-premises datacenter and the Azure VMware Solution private cloud that runs the migrated workloads. For more information about configuring VMware HCX, see [Azure VMware Solution documentation](install-vmware-hcx.md) .
- Ensure that all the network segments in use by the Microsoft SQL Server are extended into your Azure VMware Solution private cloud.For verify this step in the procedure, see [Configure VMware HCX network extension](configure-hcx-network-extension.md).

VMware HCX over VPN is supported in Azure VMware Solution for workload migration. However, due to the size of database workloads, VMware HCX over VPN isn't recommended for Microsoft SQL Server Failover Cluster Instance and Microsoft SQL Server Always-On migrations, especially for production workloads. ExpressRoute connectivity is recommended as more performant and reliable. For Microsoft SQL Server Standalone and non-production workloads HCX over VPN can be suitable, depending on the size of the database, to migrate. 

Microsoft SQL Server (2019 and 2022) were tested with Windows Server (2019 and 2022) Data Center edition with the virtual machines deployed in the on-premises environment. Windows Server and SQL Server have been configured following best practices and recommendations from Microsoft and VMware. The on-premises source infrastructure was VMware vSphere 7.0 Update 3 and VMware vSAN running on Dell PowerEdge servers and Intel Optane P4800X SSD NVMe devices.

## Downtime considerations

Predicting downtime during a migration depends upon the size of the database to be migrated and the speed of the private network connection to Azure cloud. Migration of SQL Server standalone instance doesn't require database downtime since it will be done using the VMware HCX vMotion mechanism. We recommend the migration during off-peak hours with an pre-approved change window.

This table indicates the estimated downtime for each Microsoft SQL Server topology.

| **Scenario** | **Downtime expected** | **Notes** |
|:---|:-----|:-----|
| **Standalone instance** | Low | Migration is done using VMware vMotion, the DB is available during migration time, but it isn't recommended to commit any critical data during it. |
| **Always-On Availability Group** | Low | The primary replica will always be available during the migration of the first secondary replica and the secondary replica will become the primary after the initial failover to Azure. |
| **Failover Cluster Instance** | High | All nodes of the cluster are shutdown and migrated using VMware HCX Cold Migration. Downtime duration depends upon database size and private network speed to Azure cloud. |

## Migrate Microsoft SQL Server standalone

1. Log into your on-premises **vCenter Server** and access the VMware HCX plugin. 
1. Under **Services** select **Migration** > **Migrate**. 
   - Select the Microsoft SQL Server virtual machine.
   - Set the vSphere cluster in the remote private cloud of the migrated SQL cluster as the **Compute Container**.
   - Select the vSAN Datastore as remote storage.
   - Select a folder. This isn't mandatory, but we recommended separating the different workloads in your Azure VMware Solution private cloud.
   - Keep **Same format as source**.
   - Select **vMotion** as Migration profile. 
   - In **Extended Options** select **Migrate Custom Attributes**.
   - Verify that on-premises network segments have the correct remote stretched segment in Azure VMware Solution.
   - Select **Validate** and ensure that all checks are completed with pass status. 
   - Select **Go** to start the migration. 
1. After the migration has completed, access the virtual machine using VMware Remote Console in the vSphere Client.
   - Verify the network configuration and check connectivity both with on-premises and Azure VMware Solution resources.
   - Using SQL Server Management Studio verify you can access the database.  

    :::image type="content" source="media/sql-server-hybrid-benefit/sql-standalone-1.png" alt-text="Diagram showing a SQL Server Management Studio connection to the migrated database." border="false" lightbox="media/sql-server-hybrid-benefit/sql-standalone-1.png":::  

## Next steps

- [Enable SQL Azure hybrid benefit for Azure VMware Solution](enable-sql-azure-hybrid-benefit.md). 
- [Create a placement policy in Azure VMware Solution](create-placement-policy.md)  
- [Windows Server Failover Clustering Documentation](https://learn.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [Microsoft SQL Server 2019 Documentation](https://learn.microsoft.com/sql/sql-server/?view=sql-server-ver15)
- [Microsoft SQL Server 2022 Documentation](https://learn.microsoft.com/sql/sql-server/?view=sql-server-ver16)
- [Windows Server Technical Documentation](https://learn.microsoft.com/windows-server/)
- [Planning Highly Available, Mission Critical SQL Server Deployments with VMware vSphere](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/solutions/vmware-vsphere-highly-available-mission-critical-sql-server-deployments.pdf)
- [Microsoft SQL Server on VMware vSphere Availability and Recovery Options](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/solutions/sql-server-on-vmware-availability-and-recovery-options.pdf)
- [VMware KB 100 2951 – Tips for configuring Microsoft SQL Server in a virtual machine](https://kb.vmware.com/s/article/1002951)
- [Microsoft SQL Server 2019 in VMware vSphere 7.0 Performance Study](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/techpaper/performance/vsphere7-sql-server-perf.pdf)
- [Architecting Microsoft SQL Server on VMware vSphere – Best Practices Guide](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/solutions/sql-server-on-vmware-best-practices-guide.pdf)
- [Setup for Windows Server Failover Cluster in VMware vSphere 7.0](https://docs.vmware.com/en/VMware-vSphere/7.0/vsphere-esxi-vcenter-server-703-setup-wsfc.pdf)
