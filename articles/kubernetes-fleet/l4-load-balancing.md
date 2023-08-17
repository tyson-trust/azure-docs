---
title: "How to set up multi-cluster Layer 4 load balancing across Azure Kubernetes Fleet Manager member clusters (preview)"
description: Learn how to use Azure Kubernetes Fleet Manager to set up multi-cluster Layer 4 load balancing across workloads deployed on multiple member clusters.
ms.topic: how-to
ms.date: 09/09/2022
author: shashankbarsin
ms.author: shasb
ms.service: kubernetes-fleet
ms.custom: ignite-2022, devx-track-azurecli
---

# Set up multi-cluster layer 4 load balancing across Azure Kubernetes Fleet Manager member clusters (preview)

After an application has been deployed across multiple clusters, admins often want to set up load balancing for incoming traffic across these application endpoints on member clusters.

In this how-to guide, you'll set up layer 4 load balancing across workloads deployed across a fleet's member clusters.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## Prerequisites

[!INCLUDE [free trial note](../../includes/quickstarts-free-trial-note.md)]

* You must have a Fleet resource with member clusters to which a workload has been deployed. If you don't have this resource, follow [Quickstart: Create a Fleet resource and join member clusters](quickstart-create-fleet-and-members.md) and [Propagate Kubernetes configurations from a Fleet resource to member clusters](configuration-propagation.md)

* These target clusters should be using [Azure CNI networking](../aks/configure-azure-cni.md).

* The target AKS clusters on which the workloads are deployed need to be present on either the same [virtual network](../virtual-network/virtual-networks-overview.md) or on [peered virtual networks](../virtual-network/virtual-network-peering-overview.md).

* These target clusters have to be [added as member clusters to the Fleet resource](./quickstart-create-fleet-and-members.md#join-member-clusters).

* Follow the [conceptual overview of this feature](./architectural-overview.md#multi-cluster-load-balancing), which provides an explanation of `ServiceExport` and `MultiClusterService` objects referenced in this document.

* Set the following environment variables and obtain the kubeconfigs for the fleet and all member clusters:

    ```bash
    export GROUP=<resource-group>
    export FLEET=<fleet-name>
    export MEMBER_CLUSTER_1=aks-member-1

    az fleet get-credentials --resource-group ${GROUP} --name ${FLEET} --file fleet

    az aks get-credentials --resource-group ${GROUP} --name ${MEMBER_CLUSTER_1} --file aks-member-1
    ```

[!INCLUDE [preview features note](~/articles/reusable-content/azure-cli/azure-cli-prepare-your-environment-no-header.md)]

## Deploy a sample workload to demo clusters

> [!NOTE]
>
> * The steps in this how-to guide refer to a sample application for demonstration purposes only. You can substitute this workload for any of your own existing Deployment and Service objects.
>
> * These steps deploy the sample workload from the Fleet cluster to member clusters using Kubernetes configuration propagation. Alternatively, you can choose to deploy these Kubernetes configurations to each member cluster separately, one at a time.

1. Create a namespace on the fleet cluster:

    ```bash
    KUBECONFIG=fleet kubectl create namespace kuard-demo
    ```

    Output will look similar to the following example:

    ```console
    namespace/kuard-demo created
    ```

1. Apply the Deployment, Service, ServiceExport objects:

    ```bash
    KUBECONFIG=fleet kubectl apply -f https://raw.githubusercontent.com/Azure/AKS/master/examples/fleet/kuard/kuard-export-service.yaml
    ```

    The `ServiceExport` specification in the above file allows you to export a service from member clusters to the Fleet resource. Once successfully exported, the service and all its endpoints will be synced to the fleet cluster and can then be used to set up multi-cluster load balancing across these endpoints. The output will look similar to the following example:

    ```console
    deployment.apps/kuard created
    service/kuard created
    serviceexport.networking.fleet.azure.com/kuard created
    ```

1. Create the following `ClusterResourcePlacement` in a file called `crp-2.yaml`. Notice we're selecting clusters in the `eastus` region:

    ```yaml
    apiVersion: fleet.azure.com/v1alpha1
    kind: ClusterResourcePlacement
    metadata:
      name: kuard-demo
    spec:
      resourceSelectors:
        - group: ""
          version: v1
          kind: Namespace
          name: kuard-demo
      policy:
        affinity:
          clusterAffinity:
            clusterSelectorTerms:
              - labelSelector:
                  matchLabels:
                    fleet.azure.com/location: eastus
    ```

1. Apply the `ClusterResourcePlacement`:

    ```bash
    KUBECONFIG=fleet kubectl apply -f crp-2.yaml
    ```

    If successful, the output will look similar to the following example:

    ```console
    clusterresourceplacement.fleet.azure.com/kuard-demo created
    ```

1. Check the status of the `ClusterResourcePlacement`:


    ```bash
    KUBECONFIG=fleet kubectl get clusterresourceplacements
    ```

    If successful, the output will look similar to the following example:

    ```console
    NAME            GEN   SCHEDULED   SCHEDULEDGEN   APPLIED   APPLIEDGEN   AGE
    kuard-demo      1     True        1              True      1            20s
    ```

## Create MultiClusterService to load balance across the service endpoints in multiple member clusters


1. Check the member clusters in `eastus` region to see if the service is successfully exported:

    ```bash
    KUBECONFIG=aks-member-1 kubectl get serviceexport kuard --namespace kuard-demo
    ```

    Output will look similar to the following example:

    ```console
    NAME    IS-VALID   IS-CONFLICTED   AGE
    kuard   True       False           25s
    ```

    ```bash
    KUBECONFIG=aks-member-2 kubectl get serviceexport kuard --namespace kuard-demo
    ```

    Output will look similar to the following example:

    ```console
    NAME    IS-VALID   IS-CONFLICTED   AGE
    kuard   True       False           55s
    ```

    You should see that the service is valid for export (`IS-VALID` field is `true`) and has no conflicts with other exports (`IS-CONFLICT` is `false`).

    > [!NOTE]
    > It may take a minute or two for the ServiceExport to be propagated.

1. Apply the MultiClusterService on one of these members to load balance across the service endpoints in these clusters:

    ```bash
    KUBECONFIG=aks-member-1 kubectl apply -f https://raw.githubusercontent.com/Azure/AKS/master/examples/fleet/kuard/kuard-mcs.yaml
    ```

    > [!NOTE]
    > To expose the service via the internal IP instead of public one, add the annotation to the MultiClusterService:
    >  
    > ```yaml
    > apiVersion: networking.fleet.azure.com/v1alpha1
    > kind: MultiClusterService
    > metadata:
    >   name: kuard
    >   namespace: kuard-demo
    >   annotations:
    >      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    >   ...
    > ```


    Output will look similar to the following example:

    ```console
    multiclusterservice.networking.fleet.azure.com/kuard created
    ```

1. Verify the MultiClusterService is valid by running the following command:

    ```bash
    KUBECONFIG=aks-member-1 kubectl get multiclusterservice kuard --namespace kuard-demo
    ```

    The output should look similar to the following example:

    ```console
    NAME    SERVICE-IMPORT   EXTERNAL-IP     IS-VALID   AGE
    kuard   kuard            <a.b.c.d>       True       40s
    ```

    The `IS-VALID` field should be `true` in the output. Check out the external load balancer IP address (`EXTERNAL-IP`) in the output. It may take a while before the import is fully processed and the IP address becomes available.

1. Run the following command multiple times using the External IP address from above:

    ```bash
    curl <a.b.c.d>:8080 | grep addrs 
    ```

    Notice that the IPs of the pods serving the request is changing and that these pods are from member clusters `aks-member-1` and `aks-member-2` from the `eastus` region. You can verify the pod IPs by running the following commands on the clusters from `eastus` region:

    ```bash
    KUBECONFIG=aks-member-1 kubectl get pods -n kuard-demo -o wide
    ```

    ```bash
    KUBECONFIG=aks-member-2 kubectl get pods -n kuard-demo -o wide
    ```
