---
title: 'How to use content filters (preview) with Azure OpenAI Service'
titleSuffix: Azure OpenAI
description: Learn how to use content filters (preview) with Azure OpenAI Service
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: openai
ms.topic: how-to
ms.date: 6/5/2023
author: mrbullwinkle
ms.author: mbullwin
recommendations: false
keywords: 
---

# How to configure content filters with Azure OpenAI Service

> [!NOTE]
> All customers have the ability to modify the content filters to be stricter (for example, to filter content at lower severity levels than the default). Approval is required for full content filtering control, including (i) configuring content filters at severity level high only (ii) or turning the content filters off. Managed customers only may apply for full content filtering control via this form: [Azure OpenAI Limited Access Review: Modified Content Filters and Abuse Monitoring (microsoft.com)](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xURE01NDY1OUhBRzQ3MkQxMUhZSE1ZUlJKTiQlQCN0PWcu).

The content filtering system integrated into Azure OpenAI Service runs alongside the core models and uses an ensemble of multi-class classification models to detect four categories of harmful content (violence, hate, sexual, and self-harm) at four severity levels respectively (safe, low, medium, and high). The default content filtering configuration is set to filter at the medium severity threshold for all four content harms categories for both prompts and completions. That means that content that is detected at severity level medium or high is filtered, while content detected at severity level low or safe  is not filtered by the content filters. Learn more about content categories, severity levels, and the behavior of the content filtering system [here](../concepts/content-filter.md).

Content filters can be configured at resource level. Once a new configuration is created, it can be associated with one or more deployments. For more information about model deployment, see the [resource deployment guide](create-resource.md).

The configurability feature is available in preview and allows customers to adjust the settings, separately for prompts and completions, to filter content for each content category at different severity levels as described in the table below. Content detected at the 'safe' severity level is labeled in annotations but is not subject to filtering and is not configurable.

| Severity filtered | Configurable for prompts | Configurable for completions | Descriptions |
|-------------------|--------------------------|------------------------------|--------------|
| Low, medium, high | Yes | Yes | Strictest filtering configuration. Content detected at severity levels low, medium and high is filtered.|
| Medium, high      | Yes | Yes | Default setting. Content detected at severity level low is not filtered, content at medium and high is filtered.|
| High              | If approved<sup>\*</sup>| If approved<sup>\*</sup> | Content detected at severity levels low and medium is not filtered. Only content at severity level high is filtered. Requires approval<sup>\*</sup>.|
| No filters | If approved<sup>\*</sup>| If approved<sup>\*</sup>| No content is filtered regardless of severity level detected. Requires approval<sup>\*</sup>.|

<sup>\*</sup> Only approved customers have full content filtering control, including configuring content filters at severity level high only or turning the content filters off. Managed customers only can apply for full content filtering control via this form: [Azure OpenAI Limited Access Review: Modified Content Filters and Abuse Monitoring (microsoft.com)](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xURE01NDY1OUhBRzQ3MkQxMUhZSE1ZUlJKTiQlQCN0PWcu)

## Configuring content filters via Azure OpenAI Studio (preview)

The following steps show how to set up a customized content filtering configuration for your resource.

1. Go to Azure OpenAI Studio and navigate to the Content Filters tab (in the bottom left navigation, as designated by the red box below).

    :::image type="content" source="../media/content-filters/studio.png" alt-text="Screenshot of the AI Studio UI with Content Filters highlighted" lightbox="../media/content-filters/studio.png":::

2. Create a new customized content filtering configuration.

   :::image type="content" source="../media/content-filters/create-filter.png" alt-text="Screenshot of the content filtering configuration UI with create selected" lightbox="../media/content-filters/create-filter.png":::

    This leads to the following configuration view, where you can choose a name for the custom content filtering configuration.

    :::image type="content" source="../media/content-filters/filter-view.png" alt-text="Screenshot of the content filtering configuration UI" lightbox="../media/content-filters/filter-view.png":::

3. This is the view of the default content filtering configuration, where content is filtered at medium and high severity levels for all categories. You can modify the content filtering severity level for both prompts and completions separately (configuration for prompts is in the left column and configuration for completions is in the right column, as designated with the blue boxes below) for each of the four content categories (content categories are listed on the left side of the screen, as designated with the green box below). There are three severity levels for each category that are partially or fully configurable: Low, medium, and high (labeled at the top of each column, as designated with the red box below).

    :::image type="content" source="../media/content-filters/severity-level.png" alt-text="Screenshot of the content filtering configuration UI with user prompts and model completions highlighted" lightbox="../media/content-filters/severity-level.png":::

4. If you determine that your application or usage scenario requires  stricter filtering for some or all content categories, you can configure the settings, separately for prompts and completions, to filter at more severity levels than the default setting. An example is shown in the image below, where the filtering level for user prompts is set to the strictest configuration for hate and sexual, with low severity content filtered along with content classified as medium and high severity (outlined in the red box below). In the example, the filtering levels for model completions are set at the strictest configuration for all content categories (blue box below). With this modified filtering configuration in place, low, medium, and high severity content will be filtered for the hate and sexual categories in user prompts; medium and high severity content will be filtered for the self-harm and violence categories in user prompts; and low, medium, and high severity content will be filtered for all content categories in model completions.

    :::image type="content" source="../media/content-filters/settings.png" alt-text="Screenshot of the content filtering configuration with low, medium, high, highlighted." lightbox="../media/content-filters/settings.png":::

5. If your use case was approved for modified content filters as outlined above, you will receive full control over content filtering configurations. With full control, you can choose to turn filtering off, or filter only at severity level high, while accepting low and medium severity content. In the image below, filtering for the categories of self-harm and violence is turned off for user prompts (red box below), while default configurations are retained for other categories for user prompts. For model completions, only high severity content is filtered for the category self-harm (blue box below), and filtering is turned off for violence (green box below), while default configurations are retained for other categories.

    :::image type="content" source="../media/content-filters/off.png" alt-text="Screenshot of the content filtering configuration with self harm and violence set to off." lightbox="../media/content-filters/off.png":::

    You can create multiple content filtering configurations as per your requirements.

    :::image type="content" source="../media/content-filters/multiple.png" alt-text="Screenshot of the content filtering configuration with multiple content filters configured." lightbox="../media/content-filters/multiple.png":::

6. Next, to make a custom content filtering configuration operational, assign a configuration to one or more deployments in your resource. To do this, go to the **Deployments** tab and select **Edit deployment** (outlined near the top of the screen in a red box below).

    :::image type="content" source="../media/content-filters/edit-deployment.png" alt-text="Screenshot of the content filtering configuration with edit deployment highlighted." lightbox="../media/content-filters/edit-deployment.png":::

7. Go to advanced options (outlined in the blue box below) select the content filter configuration suitable for that deployment from the **Content Filter** dropdown (outlined near the bottom of the dialog box in the red box below).

    :::image type="content" source="../media/content-filters/advanced.png" alt-text="Screenshot of edit deployment configuration with advanced options selected." lightbox="../media/content-filters/select-filter.png":::

8. Select **Save and close** to apply the selected configuration to the deployment.

    :::image type="content" source="../media/content-filters/select-filter.png" alt-text="Screenshot of edit deployment configuration with content filter selected." lightbox="../media/content-filters/select-filter.png":::

9. You can also edit and delete a content filter configuration if required. To do this, navigate to the content filters tab and select the desired action (options outlined near the top of the screen in the red box below). You can edit/delete only one filtering configuration at a time.  

    :::image type="content" source="../media/content-filters/delete.png" alt-text="Screenshot of content filter configuration with edit and delete highlighted." lightbox="../media/content-filters/delete.png":::

    > [!NOTE]
    > Before deleting a content filtering configuration, you will need to unassign it from any deployment in the Deployments tab.

## Best practices

We recommend informing your content filtering configuration decisions through an iterative identification (for example, red team testing, stress-testing, and analysis) and measurement process to address the potential harms that are relevant for a specific model, application, and deployment scenario. After implementing mitigations such as content filtering, repeat measurement to test effectiveness. Recommendations and best practices for Responsible AI for Azure OpenAI, grounded in the [Microsoft Responsible AI Standard](https://aka.ms/RAI) can be found in the [Responsible AI Overview for Azure OpenAI](/legal/cognitive-services/openai/overview?context=/azure/ai-services/openai/context/context).

## Next steps

- Learn more about Responsible AI practices for Azure OpenAI: [Overview of Responsible AI practices for Azure OpenAI models](/legal/cognitive-services/openai/overview?context=/azure/ai-services/openai/context/context).
- Read more about [content filtering categories and severity levels](../concepts/content-filter.md) with Azure OpenAI Service.
- Learn more about red teaming from our: [Introduction to red teaming large language models (LLMs) article](../concepts/red-teaming.md).
