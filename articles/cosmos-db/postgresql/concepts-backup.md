---
title: Backup and restore – Azure Cosmos DB for PostgreSQL
description: Protecting data from accidental corruption or deletion
ms.author: nlarin
author: niklarin
ms.service: cosmos-db
ms.subservice: postgresql
ms.custom: ignite-2022
ms.topic: conceptual
ms.date: 07/10/2023
---

# Backup and restore in Azure Cosmos DB for PostgreSQL

[!INCLUDE [PostgreSQL](../includes/appliesto-postgresql.md)]

Azure Cosmos DB for PostgreSQL automatically creates
backups of each node and stores them in locally redundant storage. Backups can
be used to restore your cluster to a specified time - point-in-time restore (PITR).
Backup and restore are an essential part of any business continuity strategy
because they protect your data from accidental corruption or deletion.

## Backups

Automated process performs backup of each Azure Cosmos DB for PostgreSQL node from the moment your cluster is provisioned and throughout cluster's lifecycle. Azure Cosmos DB for PostgreSQL takes periodic disk snapshots and combines it with the node's [WAL files](https://www.postgresql.org/docs/current/wal-intro.html) streaming to Azure blob storage. 

The backups allow you to restore a
server to any point in time within the retention period. (The retention period
is currently 35 days for all clusters.) All backups are encrypted using
AES 256-bit encryption.

In Azure regions that support availability zones, backup snapshots and WAL files are stored
in three availability zones. As long as at least one availability zone is
online, the cluster is restorable.

Backup files can't be exported. They may only be used for restore operations
in Azure Cosmos DB for PostgreSQL.

### Backup storage cost

For current backup storage pricing, see the Azure Cosmos DB for PostgreSQL
[pricing page](https://azure.microsoft.com/pricing/details/cosmos-db/).

## Restore

You can restore a cluster to any point in time within
the last 35 days.  Point-in-time restore is useful in multiple scenarios. For
example, when a user accidentally deletes data, drops an important table or
database, or if an application accidentally overwrites good data with bad data.

> [!NOTE]
> While cluster backups are always stored for 35 days, you may need to 
> open a support request to restore the cluster to a point that is earlier
> than the latest failover time.  

When all nodes are up and running, you can restore cluster without any data loss. In an extremely rare case of a node experiencing a catastrophic event (and [high availability](./concepts-high-availability.md) isn't enabled on the cluster), you may lose up to 5 minutes of data.

> [!IMPORTANT]
> Deleted clusters can't be restored. If you delete the
> cluster, all nodes that belong to the cluster are deleted and can't
> be recovered. To protect cluster resources, post deployment, from
> accidental deletion or unexpected changes, administrators can leverage
> [management locks](../../azure-resource-manager/management/lock-resources.md).

The restore process creates a new cluster in the same Azure region,
subscription, and resource group as the original. The cluster has the
original's configuration: the same number of nodes, number of vCores, storage
size, user roles, PostgreSQL version, and version of the Citus extension.

Networking settings aren't preserved from the original cluster, they're reset to default values. You'll need to manually adjust these settings after restore to allow access to the restored cluster. In general, see our list of suggested [post-restore tasks](howto-restore-portal.md#post-restore-tasks).

In most cases, cluster restore takes up to 1 hour.

## Next steps

* See the steps to [restore a cluster](howto-restore-portal.md)
  in the Azure portal.
* Learn about [Azure availability zones](../../availability-zones/az-overview.md).
