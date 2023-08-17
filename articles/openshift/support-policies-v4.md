---
title: Azure Red Hat OpenShift 4 cluster support policy
description: Understand support policy requirements for Red Hat OpenShift 4
author: johnmarco
ms.author: johnmarc
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 06/22/2023
#Customer intent: I need to understand the Azure Red Hat OpenShift support policies for OpenShift 4.0.
---

# Azure Red Hat OpenShift 4.0 support policy

Certain configurations for Azure Red Hat OpenShift 4 clusters can affect your cluster's supportability. Azure Red Hat OpenShift 4 allows cluster administrators to make changes to internal cluster components, but not all changes are supported. The support policy below shares what modifications violate the policy and void support from Microsoft and Red Hat.

> [!NOTE]
> Features marked Technology Preview in OpenShift Container Platform are not supported in Azure Red Hat OpenShift.

## Cluster configuration requirements

* All OpenShift Cluster operators must remain in a managed state. The list of cluster operators can be returned by running `oc get clusteroperators`.
* The cluster must have a minimum of three worker nodes and three master nodes.
* Don't scale the cluster workers to zero, or attempt a cluster shutdown. Deallocating or powering down any virtual machine in the cluster resource group is not supported.
* Don't add taints that would prevent any default OpenShift components from being scheduled.
* Don't remove or modify the default cluster Prometheus service, except to modify scheduling of the default Prometheus instance.
* Don't remove or modify the default cluster Alertmanager svc, default receiver, or any default alerting rules, except to add additional receivers to notify external systems.
* The ARO-provided Network Security Group can't be modified or replaced. Any attempt to modify or replace it will be reverted.
* Don't remove or modify Azure Red Hat OpenShift service logging (mdsd pods).
* Don't remove or modify the 'arosvc.azurecr.io' cluster pull secret.
* All cluster virtual machines must have direct outbound internet access, at least to the Azure Resource Manager (ARM) and service logging (Geneva) endpoints.  No form of HTTPS proxying is supported.
* Don't override any of the cluster's MachineConfig objects (for example, the kubelet configuration) in any way.
* Don't set any unsupportedConfigOverrides options. Setting these options prevents minor version upgrades.
* The Azure Red Hat OpenShift service accesses your cluster via Private Link Service.  Don't remove or modify service access.
* To avoid disruption resulting from cluster maintenance, in-cluster workloads should be configured with high availability practices, including but not limited to pod affinity and anti-affinity, pod disruption budgets, and adequate scaling.
* Non-RHCOS compute nodes aren't supported. For example, you can't use a RHEL compute node.
* Don't place policies within your subscription or management group that prevent SREs from performing normal maintenance against the Azure Red Hat OpenShift cluster. For example, don't require tags on the Azure Red Hat OpenShift RP-managed cluster resource group.
* Do not run extra workloads on the control plane nodes. While they can be scheduled on the control plane nodes, it will cause extra resource usage and stability issues that can affect the entire cluster.
* Don't circumvent the deny assignment that is configured as part of the service, or perform administrative tasks that are normally prohibited by the deny assignment.
* If you are making use of infrastructure nodes, do not run any undesignated workloads on them as this may affect the Service Level Agreement and cluster stability. Also, it is strongly recommended to have at least 3 infrastructure nodes; one in each availability zone. See [Deploy infrastructure nodes in an Azure Red Hat OpenShift (ARO) cluster](howto-infrastructure-nodes.md) for more information.

## Supported virtual machine sizes

Azure Red Hat OpenShift 4 supports node instances on the following virtual machine sizes:

### Control plane nodes

|Series|Size|vCPU|Memory: GiB|
|-|-|-|-|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|
|Dsv4|Standard_D8s_v4|8|32|
|Dsv4|Standard_D16s_v4|16|64|
|Dsv4|Standard_D32s_v4|32|128|
|Dsv5|Standard_D8s_v5|8|32|
|Dsv5|Standard_D16s_v5|16|64|
|Dsv5|Standard_D32s_v5|32|128|
|Dasv4|Standard_D8as_v4|8|32|
|Dasv4|Standard_D16as_v4|16|64|
|Dasv4|Standard_D32as_v4|32|128|
|Dasv5|Standard_D8as_v5|8|32|
|Dasv5|Standard_D16as_v5|16|64|
|Dasv5|Standard_D32as_v5|32|128|
|Easv4|Standard_E4as_v4|4|32|
|Easv4|Standard_E8as_v4|8|64|
|Easv4|Standard_E16as_v4|16|128|
|Easv4|Standard_E20as_v4|20|160|
|Easv4|Standard_E32as_v4|32|256|
|Easv4|Standard_E48as_v4|48|384|
|Easv4|Standard_E64as_v4|64|512|
|Easv4|Standard_E96as_v4|96|672|
|Easv5|Standard_E8as_v5|8|64|
|Easv5|Standard_E16as_v5|16|128|
|Easv5|Standard_E20as_v5|20|160|
|Easv5|Standard_E32as_v5|32|256|
|Easv5|Standard_E48as_v5|48|384|
|Easv5|Standard_E64as_v5|64|512|
|Easv5|Standard_E96as_v5|96|672|
|Eisv3|Standard_E64is_v3|64|432|
|Eis4|Standard_E80is_v4|80|504|
|Eids4|Standard_E80ids_v4|80|504|
|Eisv5|Standard_E104is_v5|104|672|
|Eidsv5|Standard_E104ids_v5|104|672|
|Esv4|Standard_E8s_v4|8|64|
|Esv4|Standard_E16s_v4|16|128|
|Esv4|Standard_E20s_v4|20|160|
|Esv4|Standard_E32s_v4|32|256|
|Esv4|Standard_E48s_v4|48|384|
|Esv4|Standard_E64s_v4|64|504|
|Esv5|Standard_E8s_v5|8|64|
|Esv5|Standard_E16s_v5|16|128|
|Esv5|Standard_E20s_v5|20|160|
|Esv5|Standard_E32s_v5|32|256|
|Esv5|Standard_E48s_v5|48|384|
|Esv5|Standard_E64s_v5|64|512|
|Esv5|Standard_E96s_v5|96|672|
|Fsv2|Standard_F72s_v2|72|144|
|Mms*|Standard_M128ms|128|3892|

\*Standard_M128ms' does not support encryption at host


### General purpose

|Series|Size|vCPU|Memory: GiB|
|-|-|-|-|
|Dasv4|Standard_D4as_v4|4|16|
|Dasv4|Standard_D8as_v4|8|32|
|Dasv4|Standard_D16as_v4|16|64|
|Dasv4|Standard_D32as_v4|32|128|
|Dasv4|Standard_D64as_v4|64|256|
|Dasv4|Standard_D96as_v4|96|384|
|Dasv5|Standard_D4as_v5|4|16|
|Dasv5|Standard_D8as_v5|8|32|
|Dasv5|Standard_D16as_v5|16|64|
|Dasv5|Standard_D32as_v5|32|128|
|Dasv5|Standard_D64as_v5|64|256|
|Dasv5|Standard_D96as_v5|96|384|
|Dsv2|Standard_D2s_v3|2|8|
|Dsv3|Standard_D4s_v3|4|16|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|
|Dsv4|Standard_D4s_v4|4|16|
|Dsv4|Standard_D8s_v4|8|32|
|Dsv4|Standard_D16s_v4|16|64|
|Dsv4|Standard_D32s_v4|32|128|
|Dsv4|Standard_D64s_v4|64|256|
|Dsv5|Standard_D4s_v5|4|16|
|Dsv5|Standard_D8s_v5|8|32|
|Dsv5|Standard_D16s_v5|16|64|
|Dsv5|Standard_D32s_v5|32|128|
|Dsv5|Standard_D64s_v5|64|256|
|Dsv5|Standard_D96s_v5|96|384|


### Memory optimized

|Series|Size|vCPU|Memory: GiB|
|-|-|-|-|
|Easv4|Standard_E4as_v4|4|32|
|Easv4|Standard_E8as_v4|8|64|
|Easv4|Standard_E16as_v4|16|128|
|Easv4|Standard_E20as_v4|20|160|
|Easv4|Standard_E32as_v4|32|256|
|Easv4|Standard_E48as_v4|48|384|
|Easv4|Standard_E64as_v4|64|512|
|Easv4|Standard_E96as_v4|96|672|
|Easv5|Standard_E8as_v5|8|64|
|Easv5|Standard_E16as_v5|16|128|
|Easv5|Standard_E20as_v5|20|160|
|Easv5|Standard_E32as_v5|32|256|
|Easv5|Standard_E48as_v5|48|384|
|Easv5|Standard_E64as_v5|64|512|
|Easv5|Standard_E96as_v5|96|672|
|Esv3|Standard_E4s_v3|4|32|
|Esv3|Standard_E8s_v3|8|64|
|Esv3|Standard_E16s_v3|16|128|
|Esv3|Standard_E32s_v3|32|256|
|Esv4|Standard_E2s_v4|2|16|
|Esv4|Standard_E4s_v4|4|32|
|Esv4|Standard_E8s_v4|8|64|
|Esv4|Standard_E16s_v4|16|128|
|Esv4|Standard_E20s_v4|20|160|
|Esv4|Standard_E32s_v4|32|256|
|Esv4|Standard_E48s_v4|48|384|
|Esv4|Standard_E64s_v4|64|504|
|Esv5|Standard_E2s_v5|2|16|
|Esv5|Standard_E4s_v5|4|32|
|Esv5|Standard_E8s_v5|8|64|
|Esv5|Standard_E16s_v5|16|128|
|Esv5|Standard_E20s_v5|20|160|
|Esv5|Standard_E32s_v5|32|256|
|Esv5|Standard_E48s_v5|48|384|
|Esv5|Standard_E64s_v5|64|512|
|Esv5|Standard_E96s_v5|96|672|
|Edsv5|Standard_E96ds_v5|96|672|
|Eisv3|Standard_E64is_v3|64|432|
|Eis4|Standard_E80is_v4|80|504|
|Eids4|Standard_E80ids_v4|80|504|
|Eisv5|Standard_E104is_v5|104|672|
|Eidsv5|Standard_E104ids_v5|104|672|


### Compute optimized

|Series|Size|vCPU|Memory: GiB|
|-|-|-|-|
|Fsv2|Standard_F4s_v2|4|8|
|Fsv2|Standard_F8s_v2|8|16|
|Fsv2|Standard_F16s_v2|16|32|
|Fsv2|Standard_F32s_v2|32|64|
|Fsv2|Standard_F72s_v2|72|144|


### Memory and compute optimized

|Series|Size|vCPU|Memory: GiB|
|-|-|-|-|
|Mms*|Standard_M128ms|128|3892|

\*Standard_M128ms' does not support encryption at host


### Storage optimized
|Series|Size|vCPU|Memory: GiB|
|-|-|-|-|
|L4s|Standard_L4s|4|32|
|L8s|Standard_L8s|8|64|
|L16s|Standard_L16s|16|128|
|L32s|Standard_L32s|32|256|
|L8s_v2|Standard_L8s_v2|8|64|
|L16s_v2|Standard_L16s_v2|16|128|
|L32s_v2|Standard_L32s_v2|32|256|
|L48s_v2|Standard_L48s_v2|48|384|
|L64s_v2|Standard_L64s_v2|64|512|
|L8s_v3|Standard_L8s_v3|8|64|
|L16s_v3|Standard_L16s_v3|16|128|
|L32s_v3|Standard_L32s_v3|32|256|
|L48s_v3|Standard_L48s_v3|48|384|
|L64s_v3|Standard_L64s_v3|64|512|


### GPU workload
|Series|Size|vCPU|Memory: GiB|
|-|-|-|-|
|NC4asT4v3|Standard_NC4as_T4_v3|4|28|
|NC6sV3|Standard_NC6s_v3|6|112|
|NC8asT4v3|Standard_NC8as_T4_v3|8|56|
|NC12sV3|Standard_NC12s_v3|12|224|
|NC16asT4v3|Standard_NC16as_T4_v3|16|110|
|NC24sV3|Standard_NC24s_v3|24|448|
|NC24rsV3|Standard_NC24rs_v3|24|448|
|NC64asT4v3|Standard_NC64as_T4_v3|64|440|

<!--
### Memory and storage optimized

|Series|Size|vCPU|Memory: GiB|
|-|-|-|-|
|G*|Standard_G5|32|448|
|G|Standard_GS5|32|448|


\*Does not support Premium_LRS OS Disk, StandardSSD_LRS is used instead
-->