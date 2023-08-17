---
title: Back up Azure Kubernetes Service (AKS) using Azure Backup 
description: This article explains how to back up Azure Kubernetes Service (AKS) using Azure Backup.
ms.topic: how-to
ms.service: backup
ms.date: 05/25/2023
author: AbhishekMallick-MS
ms.author: v-abhmallick
---

# Back up Azure Kubernetes Service using Azure Backup (preview) 

This article describes how to configure and back up Azure Kubernetes Service (AKS).

Azure Backup now allows you to back up AKS clusters (cluster resources and persistent volumes attached to the cluster) using a backup extension, which must be installed in the cluster. Backup vault communicates with the cluster via this Backup Extension to perform backup and restore operations. 

## Before you start

- Currently, AKS backup supports Azure Disk-based persistent volumes (enabled by CSI driver) only. The backups are stored in operational datastore only (backup data is stored in your tenant only and isn't moved to a vault). The Backup vault and AKS cluster should be in the same region.

- AKS backup uses a blob container and a resource group to store the backups. The blob container has the AKS cluster resources stored in it, whereas the persistent volume snapshots are stored in the resource group. The AKS cluster and the storage locations must reside in the same region. Learn [how to create a blob container](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container).

- Currently, AKS backup supports once-a-day backup. It also supports more frequent backups (in every *4*, *8*, and *12* hours intervals) per day. This solution allows you to retain your data for restore for up to 360 days. Learn to [create a backup policy](#create-a-backup-policy).

- You must [install the Backup Extension](azure-kubernetes-service-cluster-manage-backups.md#install-backup-extension) to configure backup and restore operations on an AKS cluster. Learn more [about Backup Extension](azure-kubernetes-service-cluster-backup-concept.md#backup-extension).

- Ensure that `Microsoft.KubernetesConfiguration`, `Microsoft.DataProtection`, and the `TrustedAccessPreview` feature flag on `Microsoft.ContainerService` are registered for your subscription before initiating the backup configuration and restore operations.

- Ensure to perform [all the prerequisites](azure-kubernetes-service-cluster-backup-concept.md) before initiating backup or restore operation for AKS backup.

For more information on the supported scenarios, limitations, and availability, see the [support matrix](azure-kubernetes-service-cluster-backup-support-matrix.md).

## Create a Backup vault

A Backup vault is a management entity that stores recovery points created over time and provides an interface to perform backup operations. These include taking on-demand backups, performing restores, and creating backup policies. Though operational backup of AKS cluster is a local backup and doesn't *store data* in the vault, the vault is required for various management operations. AKS backup requires the Backup vault and the AKS cluster to be in the same region.

>[!Note]
>The Backup vault is a new resource used for backing up newly supported workloads and is different from the already existing Recovery Services vault.

Learn [how to create a Backup vault](create-manage-backup-vault.md#create-a-backup-vault).


## Create a backup policy

Before you configure backups, you need to create a backup policy that defines the frequency of backup and retention duration of backups before getting deleted. You can also create a backup policy during the backup configuration.

To create a backup policy, follow these steps:

1. Go to **Backup center** and select  **+ Policy** to create a new backup policy.

   :::image type="content" source="./media/azure-kubernetes-service-cluster-backup/create-backup-policy.png" alt-text="Screenshot shows how to start creating a backup policy.":::

   Alternatively, go to **Backup center** > **Backup policies** > **Add**.

1. Select **Datasource type** as **Kubernetes Service** and continue.

   :::image type="content" source="./media/azure-kubernetes-service-cluster-backup/select-datasource-type.png" alt-text="Screenshot shows the selection of datasource type.":::

1. Enter a name for the backup policy (for example, *Default Policy*) and select the *Backup vault* (the new Backup vault you created) where the backup policy needs to be created. 

   :::image type="content" source="./media/azure-kubernetes-service-cluster-backup/enter-backup-policy-name.png" alt-text="Screenshot shows providing the backup policy name.":::

1. On the **Schedule + retention** tab, select the *backup frequency* – (*Hourly* or *Daily*), and then choose the *retention duration for the backups*. 

   :::image type="content" source="./media/azure-kubernetes-service-cluster-backup/select-backup-frequency.png" alt-text="Screenshot shows selection of backup frequency.":::

   You can edit the retention duration with default retention rule. You can't delete the default retention rule.

   :::image type="content" source="./media/azure-kubernetes-service-cluster-backup/select-retention-period.png" alt-text="Screenshot shows selection of retention period.":::

   You can also create additional retention rules to store backups taken daily or weekly to be stored for a longer duration.

1. Once the backup frequency and retention settings configurations are complete, select **Next**.

   :::image type="content" source="./media/azure-kubernetes-service-cluster-backup/review-create-policy.png" alt-text="Screenshot shows the completion of backup policy creation.":::

1. On the **Review + create** tab, review the information, and then select **Create**.

## Configure backups

AKS backup allows you to back up an entire cluster or specific cluster resources deployed in the cluster, as required. You can also protect a cluster multiple times as per the deployed applications schedule and retention requirements or security requirements.

>[!Note]
>You can set up multiple backup instances for the same AKS cluster by:
>- Configuring backup in the same Backup vault with a different backup policy.
>- Configuring backup in a different Backup vault.

To configure backups for AKS cluster, follow these steps:

1. In the Azure portal, go to the **AKS Cluster** you want to back up, and then under **Settings**, select the **Backup** tab.

   :::image type="content" source="./media/azure-kubernetes-service-cluster-backup/view-azure-kubernetes-cluster.png" alt-text="Screenshot shows viewing AKS cluster for backup.":::

1. To prepare AKS cluster for backup or restore, you need to install backup extension in the cluster by selecting **Install Extension**.

1. Provide a *storage account* and *blob container* as input.

   Your AKS cluster backups will be stored in this blob container. The storage account needs to be in the same region and subscription as the cluster.

    Select **Next**.

    :::image type="content" source="./media/azure-kubernetes-service-cluster-backup/add-storage-details-for-backup.png" alt-text="Screenshot shows how to add storage and blob details for backup."::: 

1.  Review the extension installation details provided, and then select **Create**.

    The deployment begins to install the extension.

    :::image type="content" source="./media/azure-kubernetes-service-cluster-backup/install-extension.png" alt-text="Screenshot shows how to review and install the backup extension.":::

1. Once the  backup extension is installed successfully, start configuring backups for your AKS cluster by selecting **Configure Backup**.

   You can also perform this action from the **Backup center**.

   :::image type="content" source="./media/azure-kubernetes-service-cluster-backup/configure-backup.png" alt-text="Screenshot shows the selection of Configure Backup.":::


1. Now, select the *Backup vault* to configure backup.

   :::image type="content" source="./media/azure-kubernetes-service-cluster-backup/select-vault.png" alt-text="Screenshot shows how to choose a vault.":::

   The Backup vault should have *Trusted Access* enabled for the AKS cluster to be backed up. You can enable *Trusted Access* by selecting *Grant Permission*. If it's already enabled, select **Next**.

   :::image type="content" source="./media/azure-kubernetes-service-cluster-backup/grant-permission.png" alt-text="Screenshot shows how to proceed to the next step after granting permission.":::

   >[!Note]
   >- Before you enable *Trusted Access*, enable the *TrustedAccessPreview* feature flag for the `Microsoft.ContainerServices` resource provider on the subscription.
   >- If the AKS cluster doesn't have the backup extension installed, you can perform the installation step that configures backup.

1. Select the *backup policy*, which defines the schedule for backups and their retention period. Then select **Next**.

   :::image type="content" source="./media/azure-kubernetes-service-cluster-backup/select-backup-policy.png" alt-text="Screenshot shows how to choose a backup policy.":::

1. Select **Add/Edit** to define the **Backup Instance Configuration**.
 
   :::image type="content" source="./media/azure-kubernetes-service-cluster-backup/define-backup-instance-configuration.png" alt-text="Screenshot shows how to define the Backup Instance Configuration.":::

1. In the *context* pane, define the cluster resources you want to back up.

   Learn more about [backup configurations]().

   :::image type="content" source="./media/azure-kubernetes-service-cluster-backup/define-cluster-resources-for-backup.png" alt-text="Screenshot shows how to define the cluster resources for backup.":::

1. Select **Snapshot Resource Group** where Persistent volumes (Azure Disk) Snapshots will be stored. Then select **Validate**.

   :::image type="content" source="./media/azure-kubernetes-service-cluster-backup/validate-snapshot-resource-group-selection.png" alt-text="Screenshot shows how to validate the Snapshot Resource Group.":::

   After validation is complete, if appropriate roles aren't assigned to the vault on Snapshot resource group, an error appears. See the following screenshot to check the error.

   :::image type="content" source="./media/azure-kubernetes-service-cluster-backup/validation-error-on-permissions-not-assigned.png" alt-text="Screenshot shows validation error when appropriate permissions aren't assigned.":::

1. To resolve the error, select the checkbox next to the **Datasource**, and then select **Assign missing roles**.
 
   :::image type="content" source="./media/azure-kubernetes-service-cluster-backup/start-role-assignment.png" alt-text="Screenshot shows how to start assigning roles.":::

   The following screenshot shows the list of roles you can select.

   :::image type="content" source="./media/azure-kubernetes-service-cluster-backup/select-missing-roles.png" alt-text="Screenshot shows how to select missing roles.":::

1. Once the role assignment is complete, select **Next** and proceed for backup.

   :::image type="content" source="./media/azure-kubernetes-service-cluster-backup/proceed-for-backup.png" alt-text="Screenshot shows how to proceed for backup.":::

1. Select **Configure backup**.

   :::image type="content" source="./media/azure-kubernetes-service-cluster-backup/finish-backup-configuration.png" alt-text="Screenshot shows how to finish backup configuration.":::

   Once the configuration is complete, the Backup Instance will be created.

   :::image type="content" source="./media/azure-kubernetes-service-cluster-backup/list-of-backup-instances.png" alt-text="Screenshot shows the list of created backup instances.":::

   :::image type="content" source="./media/azure-kubernetes-service-cluster-backup/backup-instance-details.png" alt-text="Screenshot shows the backup instance details.":::


### Backup configurations

As a part of AKS backup capability, you can back up all or specific cluster resources by using the filters available for the backup configurations. The defined backup configurations are referenced by the **Backup Instance Name**. You can use the following options to choose the *Namespaces* for backup:

- **All (including future Namespaces)**: This backs up all the current and future *Namespaces* with the underlying cluster resources.
- **Choose from list**: Select the specific *Namespaces* in the AKS cluster to be backed up.

  If you want to check specific cluster resources, you can use labels attached to them in the textbox. Only the resources with entered labels are backed up. You can use multiple labels. You can also back up cluster scoped resources, secrets, and persistent volumes, and select the specific checkboxes under **Other Options**. 

  >[!Note]
  >You should add the labels to every single *Yaml* file that is deployed and to be backed up. This includes both *Namespace scoped resources* such as *Persistent Volume Claims*, and *Cluster scoped resources* such as *Persistent Volumes*.

  If you also want to back up cluster scoped resources, secrets, and Persistent Volumes, select the specific checkboxes under *Other Options*.

:::image type="content" source="./media/azure-kubernetes-service-cluster-backup/various-backup-configurations.png" alt-text="Screenshot shows various backup configurations.":::

## Next steps

- [Restore Azure Kubernetes Service cluster (preview)](azure-kubernetes-service-cluster-restore.md)
- [Manage Azure Kubernetes Service cluster backups (preview)](azure-kubernetes-service-cluster-manage-backups.md)
- [About Azure Kubernetes Service cluster backup (preview)](azure-kubernetes-service-cluster-backup-concept.md)
