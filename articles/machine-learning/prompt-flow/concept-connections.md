---
title: Connections in Azure Machine Learning prompt flow (preview)
titleSuffix: Azure Machine Learning
description: Learn about how in Azure Machine Learning prompt flow, you can utilize connections to effectively manage credentials or secrets for APIs and data sources.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: lagayhar
ms.date: 06/30/2023
---

# Connections in Prompt flow (preview)

In Azure Machine Learning prompt flow, you can utilize connections to effectively manage credentials or secrets for APIs and data sources.

> [!IMPORTANT]
> Prompt flow is currently in public preview. This preview is provided without a service-level agreement, and is not recommended for production workloads. Certain features might not be supported or might have constrained capabilities.
> For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## Connections

Connections in Prompt flow play a crucial role in establishing connections to remote APIs or data sources. They encapsulate essential information such as endpoints and secrets, ensuring secure and reliable communication.

In the Azure Machine Learning workspace, connections can be configured to be shared across the entire workspace or limited to the creator. Secrets associated with connections are securely persisted in the corresponding Azure Key Vault, adhering to robust security and compliance standards.

Prompt flow provides various prebuilt connections, including Azure Open AI, Open AI, and Azure Content Safety. These prebuilt connections enable seamless integration with these resources within the built-in tools. Additionally, users have the flexibility to create custom connection types using key-value pairs, empowering them to tailor the connections to their specific requirements, particularly in Python tools.

| Connection type                                              | Built-in tools                  |
| ------------------------------------------------------------ | ------------------------------- |
| [Azure Open AI](https://azure.microsoft.com/products/cognitive-services/openai-service) | LLM or Python                   |
| [Open AI](https://openai.com/)                               | LLM or Python                   |
| [Azure Content Safety](https://aka.ms/acs-doc)               | Content Safety (Text) or Python |
| [Cognitive Search](https://azure.microsoft.com/products/search) | Vector DB Lookup or Python      |
| [Serp](https://serpapi.com/)                                 | Serp API or Python              |
| [Custom](./tools-reference/python-tool.md#how-to-consume-custom-connection-in-python-tool)                                                       | Python                          |

By leveraging connections in Prompt flow, users can easily establish and manage connections to external APIs and data sources, facilitating efficient data exchange and interaction within their AI applications.

## Next steps

- [Get started with Prompt flow](get-started-prompt-flow.md)
- [Consume custom connection in Python Tool](./tools-reference/python-tool.md#how-to-consume-custom-connection-in-python-tool)