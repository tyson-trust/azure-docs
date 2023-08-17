---
title: Quickstart - Deploy event-driven application to Azure Spring Apps
description: Learn how to deploy an event-driven application to Azure Spring Apps.
author: KarlErickson
ms.service: spring-apps
ms.topic: quickstart
ms.date: 07/19/2023
ms.author: rujche
ms.custom: devx-track-java, devx-track-extended-java, devx-track-azurecli, mode-other, event-tier1-build-2022, engagement-fy23
zone_pivot_groups: spring-apps-plan-selection
---

# Quickstart: Deploy an event-driven application to Azure Spring Apps

> [!NOTE]
> The first 50 vCPU hours and 100 GB hours of memory are free each month. For more information, see [Price Reduction - Azure Spring Apps does more, costs less!](https://techcommunity.microsoft.com/t5/apps-on-azure-blog/price-reduction-azure-spring-apps-does-more-costs-less/ba-p/3614058) on the [Apps on Azure Blog](https://techcommunity.microsoft.com/t5/apps-on-azure-blog/bg-p/AppsonAzureBlog).

> [!NOTE]
> Azure Spring Apps is the new name for the Azure Spring Cloud service. Although the service has a new name, you'll see the old name in some places for a while as we work to update assets such as screenshots, videos, and diagrams.

This article explains how to deploy a Spring Boot event-driven application to Azure Spring Apps.

The sample project is an event-driven application that subscribes to a [Service Bus queue](../service-bus-messaging/service-bus-queues-topics-subscriptions.md#queues) named `lower-case`, and then handles the message and sends another message to another queue named `upper-case`. To make the app simple, message processing just converts the message to uppercase. The following diagram depicts this process:

:::image type="content" source="media/quickstart-deploy-event-driven-app/diagram.png" alt-text="Diagram showing the Azure Spring Apps event-driven app architecture." lightbox="media/quickstart-deploy-event-driven-app/diagram.png" border="false":::

::: zone pivot="sc-standard"

[!INCLUDE [quickstart-tool-introduction](includes/quickstart-deploy-event-driven-app/quickstart-tool-introduction.md)]

::: zone-end

## 1. Prerequisites

::: zone pivot="sc-consumption-plan"

- An Azure subscription. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
- [Git](https://git-scm.com/downloads).
- [Java Development Kit (JDK)](/java/azure/jdk/), version 17.
- [Azure CLI](/cli/azure/install-azure-cli) version 2.45.0 or higher.

::: zone-end

::: zone pivot="sc-standard"

### [Azure portal](#tab/Azure-portal)

- An Azure subscription. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
- [Git](https://git-scm.com/downloads).
- [Java Development Kit (JDK)](/java/azure/jdk/), version 17.

### [Azure Developer CLI](#tab/Azure-Developer-CLI)

- An Azure subscription. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
- [Git](https://git-scm.com/downloads).
- [Java Development Kit (JDK)](/java/azure/jdk/), version 17.
- [Azure Developer CLI (AZD)](https://aka.ms/azd-install), version 1.0.2 or higher.

---

::: zone-end

::: zone pivot="sc-enterprise"

- An Azure subscription. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
- If you're deploying an Azure Spring Apps Enterprise plan instance for the first time in the target subscription, see the [Requirements](./how-to-enterprise-marketplace-offer.md#requirements) section of [View Azure Spring Apps Enterprise tier offering in Azure Marketplace](./how-to-enterprise-marketplace-offer.md).
- [Git](https://git-scm.com/downloads).
- [Java Development Kit (JDK)](/java/azure/jdk/), version 17.
- [Azure CLI](/cli/azure/install-azure-cli) version 2.45.0 or higher.

::: zone-end

::: zone pivot="sc-consumption-plan"

[!INCLUDE [deploy-event-driven-app-with-standard-consumption-plan](includes/quickstart-deploy-event-driven-app/deploy-event-driven-app-standard-consumption-plan.md)]

::: zone-end

::: zone pivot="sc-standard"

[!INCLUDE [deploy-event-driven-app-with-basic-standard-plan](includes/quickstart-deploy-event-driven-app/deploy-event-driven-app-basic-standard-plan.md)]

::: zone-end

::: zone pivot="sc-enterprise"

[!INCLUDE [deploy-event-driven-app-with-enterprise-plan](includes/quickstart-deploy-event-driven-app/deploy-event-driven-app-enterprise-plan.md)]

::: zone-end

## 5. Validate the app

Use the following steps to confirm that the event-driven app works correctly. You can validate the app by sending a message to the `lower-case` queue, then confirm that there's a message in the `upper-case` queue.

1. Send a message to the `lower-case` queue with Service Bus Explorer. For more information, see the [Send a message to a queue or topic](../service-bus-messaging/explorer.md#send-a-message-to-a-queue-or-topic) section of [Use Service Bus Explorer to run data operations on Service Bus](../service-bus-messaging/explorer.md).

1. Confirm that there's a new message sent to the `upper-case` queue. For more information, see the [Peek a message](../service-bus-messaging/explorer.md#peek-a-message) section of [Use Service Bus Explorer to run data operations on Service Bus](../service-bus-messaging/explorer.md).

::: zone pivot="sc-consumption-plan,sc-enterprise"

3. Use the following command to check the app's log to investigate any deployment issue:

   ```azurecli
   az spring app logs \
       --service ${AZURE_SPRING_APPS_INSTANCE} \
       --name ${APP_NAME}
   ```

::: zone-end

::: zone pivot="sc-standard"

3. From the navigation pane of the Azure Spring Apps instance overview page, select **Logs** to check the app's logs.

   :::image type="content" source="media/quickstart-deploy-event-driven-app/logs.png" alt-text="Screenshot of the Azure portal showing the Azure Spring Apps Logs page." lightbox="media/quickstart-deploy-event-driven-app/logs.png":::

::: zone-end

[!INCLUDE [clean-up-resources-portal-or-azd](includes/quickstart-deploy-event-driven-app/clean-up-resources.md)]

## 7. Next steps

> [!div class="nextstepaction"]
> [Structured application log for Azure Spring Apps](./structured-app-log.md)

> [!div class="nextstepaction"]
> [Map an existing custom domain to Azure Spring Apps](./tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [Set up Azure Spring Apps CI/CD with GitHub Actions](./how-to-github-actions.md)

> [!div class="nextstepaction"]
> [Set up Azure Spring Apps CI/CD with Azure DevOps](./how-to-cicd.md)

> [!div class="nextstepaction"]
> [Use managed identities for applications in Azure Spring Apps](./how-to-use-managed-identities.md)

> [!div class="nextstepaction"]
> [Create a service connection in Azure Spring Apps with the Azure CLI](../service-connector/quickstart-cli-spring-cloud-connection.md)

::: zone pivot="sc-standard, sc-consumption-plan"

> [!div class="nextstepaction"]
> [Run microservice apps(Pet Clinic)](./quickstart-sample-app-introduction.md)

::: zone-end

::: zone pivot="sc-enterprise"

> [!div class="nextstepaction"]
> [Run polyglot apps on Enterprise plan(ACME Fitness Store)](./quickstart-sample-app-acme-fitness-store-introduction.md)

::: zone-end

For more information, see the following articles:

- [Azure Spring Apps Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
- [Spring on Azure](/azure/developer/java/spring/)
- [Spring Cloud Azure](/azure/developer/java/spring-framework/)
