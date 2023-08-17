---
title: Integrate Azure Queue Storage with Service Connector
description: Integrate Azure Queue Storage into your application with Service Connector
author: mcleanbyron
ms.author: mcleans
ms.service: service-connector
ms.topic: how-to
ms.date: 08/11/2022
ms.custom: event-tier1-build-2022
---

# Integrate Azure Queue Storage with Service Connector

This page shows the supported authentication types and client types of Azure Queue Storage using Service Connector. You might still be able to connect to Azure Queue Storage in other programming languages without using Service Connector. This page also shows default environment variable names and values (or Spring Boot configuration) you get when you create the service connection. You can learn more about [Service Connector environment variable naming convention](concept-service-connector-internals.md).

## Supported compute service

- Azure App Service
- Azure Container Apps
- Azure Spring Apps

## Supported authentication types and client types

Supported authentication and clients for App Service, Container Apps and Azure Spring Apps:

### [Azure App Service](#tab/app-service)

| Client type        | System-assigned managed identity     | User-assigned managed identity       | Secret / connection string           | Service principal                    |
|--------------------|--------------------------------------|--------------------------------------|--------------------------------------|--------------------------------------|
| .NET               | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) |
| Java               | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) |
| Java - Spring Boot | ![yes icon](./media/green-check.png) |                                      |                                      |                                      |
| Node.js            | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) |
| Python             | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) |

### [Azure Container Apps](#tab/container-apps)

| Client type        | System-assigned managed identity     | User-assigned managed identity       | Secret / connection string           | Service principal                    |
|--------------------|--------------------------------------|--------------------------------------|--------------------------------------|--------------------------------------|
| .NET               | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) |
| Java               | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) |
| Java - Spring Boot | ![yes icon](./media/green-check.png) |                                      |                                      |                                      |
| Node.js            | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) |
| Python             | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) |

### [Azure Spring Apps](#tab/spring-apps)

| Client type        | System-assigned managed identity     | User-assigned managed identity | Secret / connection string           | Service principal                    |
|--------------------|--------------------------------------|--------------------------------|--------------------------------------|--------------------------------------|
| .NET               | ![yes icon](./media/green-check.png) |                                | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) |
| Java               | ![yes icon](./media/green-check.png) |                                | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) |
| Java - Spring Boot | ![yes icon](./media/green-check.png) |                                |                                      |                                      |
| Node.js            | ![yes icon](./media/green-check.png) |                                | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) |
| Python             | ![yes icon](./media/green-check.png) |                                | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) |

---

## Default environment variable names or application properties

Use the connection details below to connect compute services to Queue Storage. For each example below, replace the placeholder texts
`<account name>`, `<account-key>`, `<client-ID>`,  `<client-secret>`, `<tenant-ID>`, and `<storage-account-name>` with your own account name, account key, client ID, client secret, tenant ID and storage account name.

### .NET, Java, Node.JS, Python

#### Secret/ connection string

| Default environment variable name   | Description                     | Example value                                                                                                        |
|-------------------------------------|---------------------------------|----------------------------------------------------------------------------------------------------------------------|
| AZURE_STORAGEQUEUE_CONNECTIONSTRING | Queue storage connection string | `DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net` |

#### System-assigned managed identity

| Default environment variable name   | Description            | Example value                                            |
|-------------------------------------|------------------------|----------------------------------------------------------|
| AZURE_STORAGEQUEUE_RESOURCEENDPOINT | Queue storage endpoint | `https://<storage-account-name>.queue.core.windows.net/` |

#### User-assigned managed identity

| Default environment variable name   | Description            | Example value                                            |
|-------------------------------------|------------------------|----------------------------------------------------------|
| AZURE_STORAGEQUEUE_RESOURCEENDPOINT | Queue storage endpoint | `https://<storage-account-name>.queue.core.windows.net/` |
| AZURE_STORAGEQUEUE_CLIENTID         | Your client ID         | `<client-ID>`                                            |

#### Service principal

| Default environment variable name   | Description            | Example value                                            |
|-------------------------------------|------------------------|----------------------------------------------------------|
| AZURE_STORAGEQUEUE_RESOURCEENDPOINT | Queue storage endpoint | `https://<storage-account-name>.queue.core.windows.net/` |
| AZURE_STORAGEQUEUE_CLIENTID         | Your client ID         | `<client-ID>`                                            |
| AZURE_STORAGEQUEUE_CLIENTSECRET     | Your client secret     | `<client-secret>`                                        |
| AZURE_STORAGEQUEUE_TENANTID         | Your tenant ID         | `<tenant-ID>`                                            |

### Azure Spring Apps

#### Java - Spring Boot secret / connection string

| Application properties                 | Description                | Example value            |
|----------------------------------------|----------------------------|--------------------------|
| spring.cloud.azure.storage.account     | Queue storage account name | `<storage-account-name>` |
| spring.cloud.azure.storage.access-key  | Queue storage account key  | `<account-key>`          |

## Next steps

Follow the tutorials listed below to learn more about Service Connector.

> [!div class="nextstepaction"]
> [Learn about Service Connector concepts](./concept-service-connector-internals.md)
