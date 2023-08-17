---
title: Azure DDoS Protection Plan permissions
description: Learn how to manage permission in a DDoS Protection plan.
services: ddos-protection
author: AbdullahBell
ms.service: ddos-protection
ms.topic: how-to
ms.custom: ignite-2022
ms.workload: infrastructure-services
ms.date: 10/12/2022
ms.author: abell
---

# Manage DDoS Protection Plans: permissions and restrictions

A DDoS protection plan works across regions and subscriptions. The same plan can be linked to virtual networks from other subscriptions in different regions, across your tenant. The subscription the plan is associated to incurs the monthly recurring bill for the plan, as well as overage charges, in case the number of protected public IP addresses exceed 100. For more information on DDoS pricing, see [pricing details](https://azure.microsoft.com/pricing/details/ddos-protection/).

## Prerequisites

- Before you can complete the steps in this tutorial, you must first create an [Azure DDoS Protection plan](manage-ddos-protection.md).

## Permissions

To work with DDoS protection plans, your account must be assigned to the [network contributor](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) role or to a [custom](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) role that is assigned the appropriate actions listed in the following table:

| Action                                            | Name                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Read a DDoS protection plan              |
| Microsoft.Network/ddosProtectionPlans/write       | Create or update a DDoS protection plan  |
| Microsoft.Network/ddosProtectionPlans/delete      | Delete a DDoS protection plan            |
| Microsoft.Network/ddosProtectionPlans/join/action | Join a DDoS protection plan              |

To enable DDoS protection for a virtual network, your account must also be assigned the appropriate [actions for virtual networks](../virtual-network/manage-virtual-network.md#permissions).

> [!IMPORTANT]
> Once a DDoS Protection Plan has been enabled on a Virtual Network, subsequent operations on that Virtual Network still require the `Microsoft.Network/ddosProtectionPlans/join/action` action permission.

## Azure Policy

Creation of more than one plan is not required for most organizations. A plan cannot be moved between subscriptions. If you want to change the subscription a plan is in, you have to delete the existing plan and create a new one.

For customers who have various subscriptions, and who want to ensure a single plan is deployed across their tenant for cost control, you can use Azure Policy to [restrict creation of Azure DDoS Protection plans](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20Policy%20Definitions/Restrict%20creation%20of%20Azure%20DDoS%20Protection%20Standard%20Plans%20with%20Azure%20Policy). This policy will block the creation of any DDoS plans, unless the subscription has been previously marked as an exception. This policy will also show a list of all subscriptions that have a DDoS plan deployed but should not, marking them as out of compliance.


## Next steps

* [View and configure DDoS protection telemetry](telemetry.md)
