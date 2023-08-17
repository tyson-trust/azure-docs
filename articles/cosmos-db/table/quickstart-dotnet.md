---
title: Quickstart - Azure Cosmos DB for Table for .NET
description: Learn how to build a .NET app to manage Azure Cosmos DB for Table resources in this quickstart.
author: seesharprun
ms.author: sidandrews
ms.service: cosmos-db
ms.subservice: table
ms.devlang: csharp
ms.topic: quickstart
ms.date: 08/22/2022
ms.custom: devx-track-csharp, ignite-2022, devguide-csharp, cosmos-db-dev-journey, devx-track-azurecli, devx-track-dotnet
---

# Quickstart: Azure Cosmos DB for Table for .NET

[!INCLUDE[Table](../includes/appliesto-table.md)]

> [!div class="op_single_selector"]
>
> * [.NET](quickstart-dotnet.md)
> * [Java](quickstart-java.md)
> * [Node.js](quickstart-nodejs.md)
> * [Python](quickstart-python.md)
>

This quickstart shows how to get started with the Azure Cosmos DB for Table from a .NET application. The Azure Cosmos DB for Table is a schemaless data store allowing applications to store structured NoSQL data in the cloud. You'll learn how to create tables, rows, and perform basic tasks within your Azure Cosmos DB resource using the [Azure.Data.Tables Package (NuGet)](https://www.nuget.org/packages/Azure.Data.Tables/).

> [!NOTE]
> The [example code snippets](https://github.com/Azure-Samples/cosmos-db-table-api-dotnet-samples) are available on GitHub as a .NET project.

[API for Table reference documentation](../../storage/tables/index.yml) | [Azure.Data.Tables Package (NuGet)](https://www.nuget.org/packages/Azure.Data.Tables/)

## Prerequisites

* An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free).
* [.NET 6.0](https://dotnet.microsoft.com/download)
* [Azure Command-Line Interface (CLI)](/cli/azure/) or [Azure PowerShell](/powershell/azure/)

### Prerequisite check

* In a terminal or command window, run ``dotnet --list-sdks`` to check that .NET 6.x is one of the available versions.
* Run ``az --version`` (Azure CLI) or ``Get-Module -ListAvailable AzureRM`` (Azure PowerShell) to check that you have the appropriate Azure command-line tools installed.

## Setting up

This section walks you through how to create an Azure Cosmos DB account and set up a project that uses the API for Table NuGet packages.

### Create an Azure Cosmos DB account

This quickstart will create a single Azure Cosmos DB account using the API for Table.

#### [Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-create-resource-group-and-resource](./includes/quickstart-dotnet/azure-cli-create-resource-group-and-resource.md)]

#### [PowerShell](#tab/azure-powershell)

[!INCLUDE [Powershell - create resource group and resources](<./includes/quickstart-dotnet/powershell-create-resource-group-and-resource.md>)]

#### [Portal](#tab/azure-portal)

[!INCLUDE [Portal - create resource](<./includes/quickstart-dotnet/portal-create-resource.md>)]

---

### Get API for Table connection string

#### [Azure CLI](#tab/azure-cli)

[!INCLUDE [Azure CLI - get connection string](<./includes/quickstart-dotnet/azure-cli-get-connection-string.md>)]

#### [PowerShell](#tab/azure-powershell)

[!INCLUDE [Powershell - get connection string](<./includes/quickstart-dotnet/powershell-get-connection-string.md>)]

#### [Portal](#tab/azure-portal)

[!INCLUDE [Portal - get connection string](<./includes/quickstart-dotnet/portal-get-connection-string-from-resource.md>)]

---

### Create a new .NET app

Create a new .NET application in an empty folder using your preferred terminal. Use the [``dotnet new console``](/dotnet/core/tools/dotnet-new) to create a new console app.

```console
dotnet new console --output <app-name>
```

### Install the NuGet package

Add the [Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables) NuGet package to the new .NET project. Use the [``dotnet add package``](/dotnet/core/tools/dotnet-add-package) command specifying the name of the NuGet package.

```console
dotnet add package Azure.Data.Tables
```

### Configure environment variables

[!INCLUDE [Multi-tab](<./includes/quickstart-dotnet/environment-variables-connection-string.md>)]

## Code examples

* [Authenticate the client](#authenticate-the-client)
* [Create a table](#create-a-table)
* [Create an item](#create-an-item)
* [Get an item](#get-an-item)
* [Query items](#query-items)

The sample code described in this article creates a table named ``adventureworks``. Each table row contains the details of a product such as name, category, quantity, and a sale indicator. Each product also contains a unique identifier.

You'll use the following API for Table classes to interact with these resources:

* [``TableServiceClient``](/dotnet/api/azure.data.tables.tableserviceclient) - This class provides methods to perform service level operations with Azure Cosmos DB for Table.
* [``TableClient``](/dotnet/api/azure.data.tables.tableclient) - This class allows you to interact with tables hosted in the Azure Cosmos DB table API.
* [``TableEntity``](/dotnet/api/azure.data.tables.tableentity) - This class is a reference to a row in a table that allows you to manage properties and column data.

### Authenticate the client

From the project directory, open the *Program.cs* file. In your editor, add a using directive for ``Azure.Data.Tables``.

:::code language="csharp" source="~/azure-cosmos-tableapi-dotnet/001-quickstart/Program.cs" id="using_directives":::

Define a new instance of the ``TableServiceClient`` class using the constructor, and [``Environment.GetEnvironmentVariable``](/dotnet/api/system.environment.getenvironmentvariables) to read the connection string you set earlier.

:::code language="csharp" source="~/azure-cosmos-tableapi-dotnet/001-quickstart/Program.cs"  id="client_credentials":::

### Create a table

Retrieve an instance of the `TableClient` using the `TableServiceClient` class. Use the [``TableClient.CreateIfNotExistsAsync``](/dotnet/api/azure.data.tables.tableclient.createifnotexistsasync) method on the `TableClient` to create a new table if it doesn't already exist. This method will return a reference to the existing or newly created table.

:::code language="csharp" source="~/azure-cosmos-tableapi-dotnet/001-quickstart/Program.cs" id="create_table" :::

### Create an item

The easiest way to create a new item in a table is to create a class that implements the [``ITableEntity``](/dotnet/api/azure.data.tables.itableentity) interface. You can then add your own properties to the class to populate columns of data in that table row.

:::code language="csharp" source="~/azure-cosmos-tableapi-dotnet/001-quickstart/Product.cs" id="type" :::

Create an item in the collection using the `Product` class by calling [``TableClient.AddEntityAsync<T>``](/dotnet/api/azure.data.tables.tableclient.addentityasync).

:::code language="csharp" source="~/azure-cosmos-tableapi-dotnet/001-quickstart/Program.cs" id="create_object_add" :::

### Get an item

You can retrieve a specific item from a table using the [``TableEntity.GetEntityAsync<T>``](/dotnet/api/azure.data.tables.tableclient.getentity) method. Provide the `partitionKey` and `rowKey` as parameters to identify the correct row to perform a quick *point read* of that item.

:::code language="csharp" source="~/azure-cosmos-tableapi-dotnet/001-quickstart/Program.cs" id="read_item" :::

### Query items

After you insert an item, you can also run a query to get all items that match a specific filter by using the `TableClient.Query<T>` method. This example filters products by category using [Linq](/dotnet/standard/linq) syntax, which is a benefit of using typed `ITableEntity` models like the `Product` class.

> [!NOTE]
> You can also query items using [OData](/rest/api/storageservices/querying-tables-and-entities) syntax. You can see an example of this approach in the [Query Data](./tutorial-query.md) tutorial.

:::code language="csharp" source="~/azure-cosmos-tableapi-dotnet/001-quickstart/Program.cs" id="query_items" :::

## Run the code

This app creates an Azure Cosmos DB Table API table. The example then creates an item and then reads the exact same item back. Finally, the example creates a second item and then performs a query that should return multiple items. With each step, the example outputs metadata to the console about the steps it has performed.

To run the app, use a terminal to navigate to the application directory and run the application.

```dotnetcli
dotnet run
```

The output of the app should be similar to this example:

```output
Single product name: 
Yamba Surfboard
Multiple products:
Yamba Surfboard
Sand Surfboard
```

## Clean up resources

When you no longer need the Azure Cosmos DB for Table account, you can delete the corresponding resource group.

### [Azure CLI](#tab/azure-cli)

Use the [``az group delete``](/cli/azure/group#az-group-delete) command to delete the resource group.

```azurecli-interactive
az group delete --name $resourceGroupName
```

### [PowerShell](#tab/azure-powershell)

Use the [``Remove-AzResourceGroup``](/powershell/module/az.resources/remove-azresourcegroup) cmdlet to delete the resource group.

```azurepowershell-interactive
$parameters = @{
    Name = $RESOURCE_GROUP_NAME
}
Remove-AzResourceGroup @parameters
```

### [Portal](#tab/azure-portal)

1. Navigate to the resource group you previously created in the Azure portal.

    > [!TIP]
    > In this quickstart, we recommended the name ``msdocs-cosmos-quickstart-rg``.
1. Select **Delete resource group**.

   :::image type="content" source="media/dotnet-quickstart/delete-resource-group-option.png"  alt-text="Screenshot of the Delete resource group option in the navigation bar for a resource group.":::

1. On the **Are you sure you want to delete** dialog, enter the name of the resource group, and then select **Delete**.

   :::image type="content" source="media/dotnet-quickstart/delete-confirmation.png" alt-text="Screenshot of the delete confirmation page for a resource group.":::

---

## Next steps

In this quickstart, you learned how to create an Azure Cosmos DB for Table account, create a table, and manage entries using the .NET SDK. You can now dive deeper into the SDK to learn how to perform more advanced data queries and management tasks in your Azure Cosmos DB for Table resources.

> [!div class="nextstepaction"]
> [Get started with Azure Cosmos DB for Table and .NET](./how-to-dotnet-get-started.md)
