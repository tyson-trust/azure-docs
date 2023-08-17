---
title: What are tools in Azure Machine Learning prompt flow (preview)
titleSuffix: Azure Machine Learning
description: Learn about how tools are the fundamental building blocks of a flow in Azure Machine Learning prompt flow.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: lagayhar
ms.date: 06/30/2023
---

# Tools in Prompt flow (preview)?

Tools are the fundamental building blocks of a flow in Azure Machine Learning prompt flow.

Each tool is a simple, executable unit with a specific function, allowing users to perform various tasks.
By combining different tools, users can create a flow that accomplishes a wide range of goals.

One of the key benefit of Prompt flow tools is their seamless integration with third-party APIs and python open source packages.
This not only improves the functionality of large language models but also makes the development process more efficient for developers.

> [!IMPORTANT]
> Prompt flow is currently in public preview. This preview is provided without a service-level agreement, and is not recommended for production workloads. Certain features might not be supported or might have constrained capabilities.
> For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## Types of tools

Prompt flow provides different kinds of tools:
- LLM tool: The LLM tool allows you to write custom prompts and leverage large language models to achieve specific goals, such as summarizing articles, generating customer support responses, and more.
- Python tool: The Python tool enables you to write custom Python functions to perform various tasks, such as fetching web pages, processing intermediate data, calling third-party APIs, and more.
- Prompt tool: The Prompt tool allows you to prepare a prompt as a string for more complex use cases or for use in conjunction with other prompt tools or python tools.

## Next steps

For more information on the tools and their usage, visit the following resources:

- [Prompt tool](tools-reference/prompt-tool.md)
- [LLM tool](tools-reference/llm-tool.md)
- [Python tool](tools-reference/python-tool.md)