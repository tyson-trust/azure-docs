---
title: Use Azure Container Storage Preview with Ephemeral Disk
description: Configure Azure Container Storage Preview for use with Ephemeral Disk (NVMe). Create a storage pool, select a storage class, create a persistent volume claim, and attach the persistent volume to a pod.
author: khdownie
ms.service: azure-container-storage
ms.topic: how-to
ms.date: 07/03/2023
ms.author: kendownie
ms.custom: references_regions
---

# Use Azure Container Storage Preview with Ephemeral Disk
[Azure Container Storage](container-storage-introduction.md) is a cloud-based volume management, deployment, and orchestration service built natively for containers. This article shows you how to configure Azure Container Storage to use Ephemeral Disk as back-end storage for your Kubernetes workloads. At the end, you'll have a pod that's using Ephemeral Disk as its storage.

> [!IMPORTANT]
> Azure Container Storage Preview only supports NVMe for local disk. Temp drives and local SSD aren't currently supported. Local NVMe disks are ephemeral, meaning that they're created on the local virtual machine (VM) storage and not saved to an Azure storage service. Data will be lost on these disks if you stop/deallocate your VM.

## Prerequisites

- This article requires version 2.0.64 or later of the Azure CLI. See [How to install the Azure CLI](/cli/azure/install-azure-cli). If you're using Azure Cloud Shell, the latest version is already installed. If you plan to run the commands locally instead of in Azure Cloud Shell, be sure to run them with administrative privileges.
- You'll need an Azure Kubernetes Service (AKS) cluster with a node pool of at least three [storage optimized VMs](../../virtual-machines/sizes-storage.md) with NVMe drives such as **standard_l8s_v3**. We recommend that each VM have a minimum of four virtual CPUs (vCPUs).
- Follow the instructions in [Install Azure Container Storage](container-storage-aks-quickstart.md) to assign [Contributor](../../role-based-access-control/built-in-roles.md#contributor) role to the AKS managed identity and install Azure Container Storage Preview.

## Regional availability

Azure Container Storage Preview is only available in the following Azure regions:

- East US
- West Europe
- West US 2
- West US 3

## Create a storage pool

First, create a storage pool, which is a logical grouping of storage for your Kubernetes cluster, by defining it in a YAML manifest file. Follow these steps to create a storage pool using local disk.

1. Use your favorite text editor to create a YAML manifest file such as `code acstor-storagepool.yaml`.

1. Paste in the following code and save the file. The storage pool **name** value can be whatever you want.

   ```yml
   apiVersion: containerstorage.azure.com/v1alpha1
   kind: StoragePool
   metadata:
     name: ephemeraldisk
     namespace: acstor
   spec:
     poolType:
       ephemeralDisk: {}
   ```

1. Apply the YAML manifest file to create the storage pool.
   
   ```azurecli-interactive
   kubectl apply -f acstor-storagepool.yaml 
   ```
   
   When storage pool creation is complete, you'll see a message like:
   
   ```output
   storagepool.containerstorage.azure.com/ephemeraldisk created
   ```
   
   You can also run this command to check the status of the storage pool. Replace `<storage-pool-name>` with your storage pool **name** value. For this example, the value would be **ephemeraldisk**.
   
   ```azurecli-interactive
   kubectl describe sp <storage-pool-name> -n acstor
   ```

When the storage pool is created, Azure Container Storage will create a storage class on your behalf, using the naming convention `acstor-<storage-pool-name>`.

## Display the available storage classes

When the storage pool is ready to use, you must select a storage class to define how storage is dynamically created when creating persistent volume claims and deploying persistent volumes.

Run `kubectl get sc` to display the available storage classes. You should see a storage class called `acstor-<storage-pool-name>`.

> [!IMPORTANT]
> Don't use the storage class that's marked **internal**. It's an internal storage class that's needed for Azure Container Storage to work.

## Create a persistent volume claim

A persistent volume claim (PVC) is used to automatically provision storage based on a storage class. Follow these steps to create a PVC using the new storage class. 

1. Use your favorite text editor to create a YAML manifest file such as `code acstor-pvc.yaml`.

1. Paste in the following code and save the file. The PVC `name` value can be whatever you want.

   ```yml
   apiVersion: v1
   kind: PersistentVolumeClaim
   metadata:
     name: ephemeralpvc
   spec:
     accessModes:
       - ReadWriteOnce
     storageClassName: acstor-ephemeraldisk # replace with the name of your storage class if different
     resources:
       requests:
         storage: 100Gi
   ```

1. Apply the YAML manifest file to create the PVC.
   
   ```azurecli-interactive
   kubectl apply -f acstor-pvc.yaml
   ```
   
   You should see output similar to:
   
   ```output
   persistentvolumeclaim/ephemeralpvc created
   ```
   
   You can verify the status of the PVC by running the following command:
   
   ```azurecli-interactive
   kubectl describe pvc ephemeralpvc
   ```

Once the PVC is created, it's ready for use by a pod.

## Deploy a pod and attach a persistent volume

Create a pod using [Fio](https://github.com/axboe/fio) (Flexible I/O Tester) for benchmarking and workload simulation, and specify a mount path for the persistent volume. For **claimName**, use the **name** value that you used when creating the persistent volume claim.

1. Use your favorite text editor to create a YAML manifest file such as `code acstor-pod.yaml`.

1. Paste in the following code and save the file.

   ```yml
   kind: Pod
   apiVersion: v1
   metadata:
     name: fiopod
   spec:
     nodeSelector:
       acstor.azure.com/io-engine: acstor
     volumes:
       - name: ephemeralpv
         persistentVolumeClaim:
           claimName: ephemeralpvc
     containers:
       - name: fio
         image: nixery.dev/shell/fio
         args:
           - sleep
           - "1000000"
         volumeMounts:
           - mountPath: "/volume"
             name: ephemeralpv
   ```

1. Apply the YAML manifest file to deploy the pod.
   
   ```azurecli-interactive
   kubectl apply -f acstor-pod.yaml
   ```
   
   You should see output similar to the following:
   
   ```output
   pod/fiopod created
   ```

1. Check that the pod is running and that the persistent volume claim has been bound successfully to the pod:

   ```azurecli-interactive
   kubectl describe pod fiopod
   kubectl describe pvc ephemeralpvc
   ```

1. Check fio testing to see its current status:

   ```azurecli-interactive
   kubectl exec -it fiopod -- fio --name=benchtest --size=800m --filename=/volume/test --direct=1 --rw=randrw --ioengine=libaio --bs=4k --iodepth=16 --numjobs=8 --time_based --runtime=60
   ```

You've now deployed a pod that's using Ephemeral Disk as its storage, and you can use it for your Kubernetes workloads.

## Detach and reattach a persistent volume

To detach a persistent volume, delete the pod that the persistent volume is attached to. Replace `<pod-name>` with the name of the pod, for example **fiopod**.

```azurecli-interactive
kubectl delete pods <pod-name>
```

To reattach a persistent volume, simply reference the persistent volume claim name in the YAML manifest file as described in [Deploy a pod and attach a persistent volume](#deploy-a-pod-and-attach-a-persistent-volume).

To check which persistent volume a persistent volume claim is bound to, run `kubectl get pvc <persistent-volume-claim-name>`.

## Delete the storage pool

If you want to delete a storage pool, run the following command. Replace `<storage-pool-name>` with the storage pool name.

```azurecli-interactive
kubectl delete sp -n acstor <storage-pool-name>
```

## See also

- [What is Azure Container Storage?](container-storage-introduction.md)
