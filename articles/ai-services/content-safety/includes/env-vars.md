---
title: "Create environment variables"
description: Create environemnt variables for Content Safety
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-safety
ms.custom: build-2023
ms.topic: include
ms.date: 05/03/2023
ms.author: pafarley
---

## Create environment variables 

In this example, you'll write your credentials to environment variables on the local machine running the application.

> [!TIP]
> Don't include the key directly in your code, and never post it publicly. See the Azure AI services [security](../../security-features.md) article for more authentication options like [Azure Key Vault](../../use-key-vault.md). 

To set the environment variable for your key and endpoint, open a console window and follow the instructions for your operating system and development environment.

1. To set the `CONTENT_SAFETY_KEY` environment variable, replace `your-key` with one of the keys for your resource.
2. To set the `CONTENT_SAFETY_ENDPOINT` environment variable, replace `your-endpoint` with the endpoint for your resource.

#### [Windows](#tab/windows)

```console
setx CONTENT_SAFETY_KEY your-key
```

```console
setx CONTENT_SAFETY_ENDPOINT your-endpoint
```

After you add the environment variables, you may need to restart any running programs that will read the environment variables, including the console window.

#### [Linux](#tab/linux)

```bash
export CONTENT_SAFETY_KEY=your-key
```

```bash
export CONTENT_SAFETY_ENDPOINT=your-endpoint
```

After you add the environment variables, run `source ~/.bashrc` from your console window to make the changes effective.

---
