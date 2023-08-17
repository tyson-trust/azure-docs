---
title: "Quickstart - Introduction to the sample app - Azure Spring Apps"
description: Describes the sample app used in this series of quickstarts for deployment to Azure Spring Apps.
author: KarlErickson
ms.author: karler
ms.service: spring-apps
ms.topic: quickstart
ms.date: 10/12/2021
ms.custom: devx-track-java, devx-track-extended-java, mode-other, event-tier1-build-2022
zone_pivot_groups: programming-languages-spring-apps
---

# Introduction to the sample app

> [!NOTE]
> The first 50 vCPU hours and 100 GB hours of memory are free each month. For more information, see [Price Reduction - Azure Spring Apps does more, costs less!](https://techcommunity.microsoft.com/t5/apps-on-azure-blog/price-reduction-azure-spring-apps-does-more-costs-less/ba-p/3614058) on the [Apps on Azure Blog](https://techcommunity.microsoft.com/t5/apps-on-azure-blog/bg-p/AppsonAzureBlog).

> [!NOTE]
> Azure Spring Apps is the new name for the Azure Spring Cloud service. Although the service has a new name, you'll see the old name in some places for a while as we work to update assets such as screenshots, videos, and diagrams.

**This article applies to:** ✔️ Basic/Standard ❌️ Enterprise

::: zone pivot="programming-language-csharp"

This series of quickstarts uses a sample app composed of two Spring apps to show how to deploy a .NET Core Steeltoe app to the Azure Spring Apps service. You use Azure Spring Apps capabilities such as service discovery, config server, logs, metrics, and distributed tracing.

## Functional services

The sample app is composed of two Spring apps:

* The `planet-weather-provider` service returns weather text in response to an HTTP request that specifies the planet name. For example, it may return "very warm" for planet Mercury. It gets the weather data from the Config server. The Config server gets the weather data from a YAML file in a Git repository, for example:

  ```yaml
  MercuryWeather: very warm
  VenusWeather: quite unpleasant
  MarsWeather: very cool
  SaturnWeather: a little bit sandy
  ```

* The `solar-system-weather` service returns data for four planets in response to an HTTP request. It gets the data by making four HTTP requests to `planet-weather-provider`. It uses the Eureka server discovery service to call `planet-weather-provider`. It returns JSON, for example:

  ```json
  [{
      "Key": "Mercury",
      "Value": "very warm"
  }, {
      "Key": "Venus",
      "Value": "quite unpleasant"
  }, {
      "Key": "Mars",
      "Value": "very cool"
  }, {
      "Key": "Saturn",
      "Value": "a little bit sandy"
  }]
  ```

The following diagram illustrates the sample app architecture:

:::image type="content" source="media/quickstart-sample-app-introduction/sample-app-diagram.png" alt-text="Diagram of sample app architecture.":::

> [!NOTE]
> When the application is hosted in Azure Spring Apps Enterprise plan, the managed Application Configuration Service for VMware Tanzu® assumes the role of Spring Cloud Config Server and the managed VMware Tanzu® Service Registry assumes the role of Eureka Service Discovery without any code changes to the application. For more information, see [Use Application Configuration Service for Tanzu](how-to-enterprise-application-configuration-service.md) and [Use Tanzu Service Registry](how-to-enterprise-service-registry.md).

## Code repository

The sample app is located in the [steeltoe-sample](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample) folder of the Azure-Samples/Azure-Spring-Cloud-Samples repository on GitHub.

The instructions in the following quickstarts refer to the source code as needed.

::: zone-end

::: zone pivot="programming-language-java"

In this quickstart, we use the well-known sample app [PetClinic](https://github.com/spring-petclinic/spring-petclinic-microservices) to show you how to deploy apps to the Azure Spring Apps service. The **Pet Clinic** sample demonstrates the microservice architecture pattern and highlights the services breakdown. You see how to deploy services to Azure with Azure Spring Apps capabilities such as service discovery, config server, logs, metrics, distributed tracing, and developer-friendly tooling support.

To follow the Azure Spring Apps deployment examples, you only need the location of the source code, which is provided as needed.

The following diagram shows the architecture of the PetClinic application.

![Architecture of PetClinic](media/build-and-deploy/microservices-architecture-diagram.jpg)

> [!NOTE]
> When the application is hosted in Azure Spring Apps Enterprise plan, the managed Application Configuration Service for VMware Tanzu® assumes the role of Spring Cloud Config Server and the managed VMware Tanzu® Service Registry assumes the role of Eureka Service Discovery without any code changes to the application. For more information, see the [Infrastructure services hosted by Azure Spring Apps](#infrastructure-services-hosted-by-azure-spring-apps) section later in this article.

## Functional services to be deployed

PetClinic is decomposed into four core Spring apps. All of them are independently deployable applications organized by business domains.

* **Customers service**: Contains general user input logic and validation including pets and owners information (Name, Address, City, Telephone).
* **Visits service**: Stores and shows visits information for each pet's comments.
* **Vets service**: Stores and shows Veterinarians' information, including names and specialties.
* **API Gateway**: The API Gateway is a single entry point into the system, used to handle requests and route them to an appropriate service or to invoke multiple services, and aggregate the results.  The three core services expose an external API to client. In real-world systems, the number of functions can grow quickly with system complexity. Hundreds of services might be involved in rendering one complex webpage.

## Infrastructure services hosted by Azure Spring Apps

There are several common patterns in distributed systems that support core services. Azure Spring Apps provides tools that enhance Spring Boot applications to implement the following patterns:

### [Basic/Standard plan](#tab/basic-standard-plan)

* **Config service**: Azure Spring Apps Config is a horizontally scalable centralized configuration service for distributed systems. It uses a pluggable repository that currently supports local storage, Git, and Subversion.
* **Service discovery**: It allows automatic detection of network locations for service instances, which could have dynamically assigned addresses because of autoscaling, failures, and upgrades.

### [Enterprise plan](#tab/enterprise-plan)

* **Application Configuration Service for Tanzu**: Application Configuration Service for Tanzu is one of the commercial VMware Tanzu components. It enables the management of Kubernetes-native ConfigMap resources that are populated from properties defined in one or more Git repositories.
* **Tanzu Service Registry**: Tanzu Service Registry is one of the commercial VMware Tanzu components. It provides your apps with an implementation of the Service Discovery pattern, one of the key tenets of a Spring-based architecture. Your apps can use the Service Registry to dynamically discover and call registered services.

---

## Database configuration

In its default configuration, **Pet Clinic** uses an in-memory database (HSQLDB) which is populated at startup with data. A similar setup is provided for MySQL if a persistent database configuration is needed. A dependency for Connector/J, the MySQL JDBC driver, is already included in the pom.xml files.

## Sample usage of PetClinic

For full implementation details, see our fork of [PetClinic](https://github.com/Azure-Samples/spring-petclinic-microservices). The samples reference the source code as needed.

::: zone-end

## Next steps

### [Basic/Standard plan](#tab/basic-standard-plan)

> [!div class="nextstepaction"]
> [Quickstart: Provision an Azure Spring Apps service instance](./quickstart-provision-service-instance.md)

### [Enterprise plan](#tab/enterprise-plan)

> [!div class="nextstepaction"]
> [Quickstart: Build and deploy apps to Azure Spring Apps using the Enterprise plan](quickstart-deploy-apps-enterprise.md)

---
