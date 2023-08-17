---
title: Integrate Azure Table Storage with Service Connector
description: Integrate Azure Table Storage into your application with Service Connector
author: mcleanbyron
ms.author: mcleans
ms.service: service-connector
ms.topic: how-to
ms.date: 08/11/2022
ms.custom: event-tier1-build-2022
---

# Integrate Azure Table Storage with Service Connector

This page shows the supported authentication types and client types of Azure Table Storage using Service Connector. You might still be able to connect to Azure Table Storage in other programming languages without using Service Connector. This page also shows default environment variable names and values (or Spring Boot configuration) you get when you create the service connection. You can learn more about [Service Connector environment variable naming convention](concept-service-connector-internals.md).

## Supported compute service

- Azure App Service
- Azure Container Apps
- Azure Spring Apps

Supported authentication and clients for App Service, Container Apps and Azure Spring Apps:

| Client type | System-assigned managed identity | User-assigned managed identity | Secret / connection string           | Service principal |
|-------------|----------------------------------|--------------------------------|--------------------------------------|-------------------|
| .NET        |                                  |                                | ![yes icon](./media/green-check.png) |                   |
| Java        |                                  |                                | ![yes icon](./media/green-check.png) |                   |
| Node.js     |                                  |                                | ![yes icon](./media/green-check.png) |                   |
| Python      |                                  |                                | ![yes icon](./media/green-check.png) |                   |

## Default environment variable names or application properties

Use the connection details below to connect compute services to Azure Table Storage. For each example below, replace the placeholder texts `<account-name>` and `<account-key>` with your own account name and account key.

### .NET, Java, Node.JS and Python secret / connection string

| Default environment variable name   | Description                     | Example value                                                                                                        |
|-------------------------------------|---------------------------------|----------------------------------------------------------------------------------------------------------------------|
| AZURE_STORAGETABLE_CONNECTIONSTRING | Table storage connection string | `DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net` |

## Next steps

Follow the tutorials listed below to learn more about Service Connector.

> [!div class="nextstepaction"]
> [Learn about Service Connector concepts](./concept-service-connector-internals.md)
