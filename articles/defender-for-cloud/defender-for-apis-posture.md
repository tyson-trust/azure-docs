---
title: Investigate your API security findings and posture
description: Learn how to analyze your API security alerts and posture in Microsoft Defender for Cloud
author: dcurwin
ms.author: dacurwin
ms.service: defender-for-cloud
ms.topic: conceptual
ms.date: 05/08/2023
---
# Investigate API findings, recommendations, and alerts

This article describes how to investigate API security findings, alerts, and security posture recommendations for APIs protected by [Microsoft Defender for APIs](defender-for-apis-introduction.md). Defender for APIs is currently in preview.

## Before you start

- [Onboard your API resources](defender-for-apis-deploy.md) to Defender for APIs.
- To explore security risks within your organization using Cloud Security Explorer, the Defender Cloud Security Posture Management (CSPM) plan must be enabled. [Learn more](concept-cloud-security-posture-management.md).

## View recommendations and runtime alerts

1. In the Defender for Cloud portal, select **Workload protections**.
1. Select **API security (Preview)**.
1. In the **API Security** dashboard, select an API collection.

    :::image type="content" source="media/defender-for-apis-posture/api-collection-details.png" alt-text="Screenshot that shows the onboarded API collections."lightbox="media/defender-for-apis-posture/api-collection-details.png":::

1. In the API collection page, to drill down into an API endpoint, select the ellipses (...) > **View resource**.

     :::image type="content" source="media/defender-for-apis-posture/view-resource.png" alt-text="Screenshot that shows API endpoint details." lightbox="media/defender-for-apis-posture/view-resource.png":::

1. In the **Resource health** page, review the endpoint settings.
1. In the **Recommendations** tab, review recommendation details and status.
1. In the **Alerts** tab, review security alerts for the endpoint. Defender for Endpoint monitors API traffic to and from endpoints, to provide runtime protection against suspicious behavior and malicious attacks.

    :::image type="content" source="media/defender-for-apis-posture/resource-health.png" alt-text="Screenshot that shows the health of an endpoint." lightbox="media/defender-for-apis-posture/resource-health.png":::

## Create sample security alerts

In Defender for Cloud you can use sample alerts to evaluate your Defender for Cloud plans, and validate your security configuration. [Follow these instructions](alert-validation.md#generate-sample-security-alerts) to set up sample alerts, and select the relevant APIs within your subscriptions.

## Simulate alerts

To see the alert process in action, you can simulate an action that triggers a Defender for APIs alert. [Follow the instructions in our Tech Community blog](https://techcommunity.microsoft.com/t5/microsoft-defender-for-cloud/validating-microsoft-defender-for-apis-alerts/ba-p/3803874) to do that.

## Build queries in Cloud Security Explorer

In Defender CSPM, [Cloud Security Graph](concept-attack-path.md) collects data to provide a map of assets and connections across organization, to expose security risks, vulnerabilities, and possible lateral movement paths.

When the Defender CSPM plan is enabled together with Defender for APIs, you can use Cloud Security Explorer to identify, review and analyze API security risks across your organization. 

1. In the Defender for Cloud portal, select **Cloud Security Explorer**.
1. In **What would you like to search?** select the **APIs** category. 
1. Review the search results so that you can review, prioritize, and fix any API issues.


## Next steps

[Manage](defender-for-apis-manage.md) your Defender for APIs deployment.



