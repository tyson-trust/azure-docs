---
title: Connect an existing Azure Cosmos DB account with virtual network service endpoints
description: Connect an existing Azure Cosmos DB account with virtual network service endpoints
author: seesharprun
ms.author: sidandrews
ms.reviewer: mjbrown
ms.service: cosmos-db
ms.custom: ignite-2022, devx-track-azurecli
ms.topic: sample
ms.date: 02/21/2022
---

# Connect an existing Azure Cosmos DB account with virtual network service endpoints using Azure CLI

[!INCLUDE[NoSQL, MongoDB, Cassandra, Gremlin, Table](../../../includes/appliesto-nosql-mongodb-cassandra-gremlin-table.md)]

The script in this article demonstrates connecting an existing Azure Cosmos DB account to an existing new virtual network where the subnet is not yet configured for service endpoints by using the `ignore-missing-vnet-service-endpoint` parameter. This allows the configuration for the Azure Cosmos DB account to complete without error before the configuration to the virtual network's subnet is completed. Once the subnet configuration is complete, the Azure Cosmos DB account is accessible through the configured subnet.

This script uses a API for NoSQL account. To use this sample for other APIs, apply the `enable-virtual-network` and `virtual-network-rules` parameters in the script below to your API specific script.

[!INCLUDE [quickstarts-free-trial-note](../../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](~/articles/reusable-content/azure-cli/azure-cli-prepare-your-environment.md)]

- This article requires version 2.9.1 or later of the Azure CLI. If using Azure Cloud Shell, the latest version is already installed.

## Sample script

[!INCLUDE [cli-launch-cloud-shell-sign-in.md](../../../../../includes/cli-launch-cloud-shell-sign-in.md)]

### Run the script

:::code language="azurecli" source="~/azure_cli_scripts/cosmosdb/common/service-endpoints-ignore-missing-vnet.sh" id="FullScript":::

## Clean up resources

[!INCLUDE [cli-clean-up-resources.md](../../../../../includes/cli-clean-up-resources.md)]

```azurecli
az group delete --name $resourceGroup
```

## Sample reference

This script uses the following commands. Each command in the table links to command specific documentation.

| Command | Notes |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Creates a resource group in which all resources are stored. |
| [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) | Creates an Azure virtual network. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) | Creates a subnet for an Azure virtual network. |
| [az network vnet subnet show](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-show) | Returns a subnet for an Azure virtual network. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Creates an Azure Cosmos DB account. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) | Updates a subnet for an Azure virtual network. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Deletes a resource group including all nested resources. |

## Next steps

For more information on the Azure Cosmos DB CLI, see [Azure Cosmos DB CLI documentation](/cli/azure/cosmosdb).

For Azure CLI samples for specific APIs see:

- [CLI Samples for Cassandra](../../../cassandra/cli-samples.md)
- [CLI Samples for Gremlin](../../../graph/cli-samples.md)
- [CLI Samples for API for MongoDB](../../../mongodb/cli-samples.md)
- [CLI Samples for SQL](../../../sql/cli-samples.md)
- [CLI Samples for Table](../../../table/cli-samples.md)
