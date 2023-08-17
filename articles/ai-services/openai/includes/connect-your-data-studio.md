---
titleSuffix: Azure OpenAI
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: openai
ms.topic: include
author: aahill
ms.author: aahi
ms.date: 08/11/2023
recommendations: false
---

Navigate to [Azure OpenAI Studio](https://oai.azure.com/) and sign-in with credentials that have access to your Azure OpenAI resource. During or after the sign-in workflow, select the appropriate directory, Azure subscription, and Azure OpenAI resource.

1. Select the **Chat playground** tile.

    :::image type="content" source="../media/quickstarts/chat-playground-card.png" alt-text="A screenshot of the Azure OpenAI Studio landing page." lightbox="../media/quickstarts/chat-playground-card.png":::

1. On the **Assistant setup** tile, select **Add your data (preview)** > **+ Add a data source**.

    :::image type="content" source="../media/quickstarts/chatgpt-playground-add-your-data.png" alt-text="A screenshot showing the button for adding your data in Azure OpenAI Studio." lightbox="../media/quickstarts/chatgpt-playground-add-your-data.png":::

1. In the pane that appears, select **Upload files** under **Select data source**. Select **Upload files**. Azure OpenAI needs both a storage resource and a search resource to access and index your data.

    > [!TIP]
    > * See the following resource for more information:
    >    * [Data source options](../concepts/use-your-data.md#data-source-options)
    >    * [supported file types and formats](../concepts/use-your-data.md#data-formats-and-file-types)
    > *  For documents and datasets with long text, we recommend using the available [data preparation script](../concepts/use-your-data.md#ingesting-your-data-into-azure-cognitive-search). 

    1. For Azure OpenAI to access your storage account, you will need to turn on [Cross-origin resource sharing (CORS)](https://go.microsoft.com/fwlink/?linkid=2237228). If CORS isn't already turned on for the Azure Blob storage resource, select **Turn on CORS**. 

    1. Select your Azure Cognitive Search resource, and select the acknowledgment that connecting it will incur usage on your account. Then select **Next**.

    :::image type="content" source="../media/quickstarts/add-your-data-source.png" alt-text="A screenshot showing options for selecting a data source in Azure OpenAI Studio." lightbox="../media/quickstarts/add-your-data-source.png":::


1. On the **Upload files** pane, select **Browse for a file** and select the files you want to upload. Then select **Upload files**. Then select **Next**.

1. Review the details you entered, and select **Save and close**. You can now chat with the model and it will use information from your data to construct the response.

> [!div class="nextstepaction"]
> [I ran into an issue adding my data.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=STUDIO&Pillar=AOAI&Product=ownData&Page=quickstart&Section=Adding-data)
