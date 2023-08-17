---
title: Concepts - Network interconnectivity
description: Learn about key aspects and use cases of networking and interconnectivity in Azure VMware Solution.
ms.topic: conceptual
ms.service: azure-vmware
ms.date: 6/27/2023
ms.custom: engagement-fy23
---

# Azure VMware Solution networking and interconnectivity concepts

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

There are two ways to interconnectivity in the Azure VMware Solution private cloud:

- [**Basic Azure-only interconnectivity**](#azure-virtual-network-interconnectivity) lets you manage and use your private cloud with only a single virtual network in Azure. This implementation is best suited for Azure VMware Solution evaluations or implementations that don't require access from on-premises environments.

- [**Full on-premises to private cloud interconnectivity**](#on-premises-interconnectivity) extends the basic Azure-only implementation to include interconnectivity between on-premises and Azure VMware Solution private clouds.
 
This article covers the key concepts that establish networking and interconnectivity, including requirements and limitations. In addition, this article provides you with the information you need to know to work with Azure VMware Solution to configure your networking.

## Azure VMware Solution private cloud use cases

The use cases for Azure VMware Solution private clouds include:
- New VMware vSphere VM workloads in the cloud
- VM workload bursting to the cloud (on-premises to Azure VMware Solution only)
- VM workload migration to the cloud (on-premises to Azure VMware Solution only)
- Disaster recovery (Azure VMware Solution to Azure VMware Solution or on-premises to Azure VMware Solution)
- Consumption of Azure services

> [!TIP]
> All use cases for the Azure VMware Solution service are enabled with on-premises to private cloud connectivity.

## Azure virtual network interconnectivity

You can interconnect your Azure virtual network with the Azure VMware Solution private cloud implementation. You can manage your Azure VMware Solution private cloud, consume workloads in your private cloud, and access other Azure services.

The diagram below shows the basic network interconnectivity established at the time of a private cloud deployment. It shows the logical networking between a virtual network in Azure and a private cloud. This connectivity is established via a backend ExpressRoute that is part of the Azure VMware Solution service. The interconnectivity fulfills the following primary use cases:

- Inbound access to vCenter Server and NSX-T Manager that is accessible from VMs in your Azure subscription.
- Outbound access from VMs on the private cloud to Azure services.
- Inbound access of workloads running in the private cloud.

> [!IMPORTANT]
> When connecting **production** Azure VMware Solution private clouds to an Azure virtual network, an ExpressRoute virtual network gateway with the Ultra Performance Gateway SKU should be used with FastPath enabled to achieve 10Gbps connectivity. Less critical environments can use the Standard or High Performance Gateway SKUs for slower network performance.

> [!NOTE]
> If connecting more than four Azure VMware Solution private clouds in the same Azure region to the same Azure virtual network is a requirement, use [AVS Interconnect](connect-multiple-private-clouds-same-region.md) to aggregate private cloud connectivity within the Azure region.

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Diagram showing the basic network interconnectivity established at the time of an Azure VMware Solution private cloud deployment." border="false" lightbox="media/concepts/adjacency-overview-drawing-single.png":::

## On-premises interconnectivity

In the fully interconnected scenario, you can access the Azure VMware Solution from your Azure virtual network(s) and on-premises. This implementation is an extension of the basic implementation described in the previous section. An ExpressRoute circuit is required to connect from on-premises to your Azure VMware Solution private cloud in Azure.

The diagram below shows the on-premises to private cloud interconnectivity, which enables the following use cases:

- Hot/Cold vSphere vMotion between on-premises and Azure VMware Solution.
- On-premises to Azure VMware Solution private cloud management access.

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Diagram showing the virtual network and on-premises to private cloud interconnectivity." border="false" lightbox="media/concepts/adjacency-overview-drawing-double.png":::

For full interconnectivity to your private cloud, you need to enable ExpressRoute Global Reach and then request an authorization key and private peering ID for Global Reach in the Azure portal. The authorization key and peering ID are used to establish Global Reach between an ExpressRoute circuit in your subscription and the ExpressRoute circuit for your private cloud. Once linked, the two ExpressRoute circuits route network traffic between your on-premises environments to your private cloud. For more information on the procedures, see the [tutorial for creating an ExpressRoute Global Reach peering to a private cloud](tutorial-expressroute-global-reach-private-cloud.md).

> [!IMPORTANT]
> Customers should not advertise bogon routes over ExpressRoute from on-premises or their Azure VNet.  Examples of bogon routes include 0.0.0.0/5 or 192.0.0.0/3.


## Route advertisement guidelines to Azure VMware Solution
 You need to follow these guidelines while advertising routes from your on-premises and Azure VNET to Azure VMware Solution over ExpressRoute:

| **Supported** |**Not supported**|
| ---------------| ---------------|
| Default route – 0.0.0.0/0*| Bogon routes. For example: ``0.0.0.0/1, 128.0.0.0/1 0.0.0.0/5``, or ``192.0.0.0/3.``|
|RFC-1918 address blocks. For example, (``10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16``) or its subnets ( ``10.1.0.0/16, 172.24.0.0/16, 192.168.1.0/24``).| Special address block reserved by IANA. For example,``RFC 6598-100.64.0.0/10`` and its subnets. |
|Customer owned public-IP CIDR block or its subnets.||

> [!NOTE]
> The customer-advertised default route to Azure VMware Solution can't be used to route back the traffic when the customer accesses Azure VMware Solution management appliances (vCenter Server, NSX-T Manager, HCX Manager). The customer needs to advertise a more specific route to Azure VMware Solution for that traffic to be routed back.


## Limitations
[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

## Next steps 

Now that you've covered Azure VMware Solution network and interconnectivity concepts, you may want to learn about:

- [Azure VMware Solution storage concepts](concepts-storage.md)
- [Azure VMware Solution identity concepts](concepts-identity.md)
- [Enabling the Azure VMware Solution resource provider](deploy-azure-vmware-solution.md#register-the-microsoftavs-resource-provider)

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-private-clouds-clusters#host-maintenance-and-lifecycle-management
[concepts-storage]: ./concepts-storage.md
