---
title: What's new in Azure OpenAI Service?
titleSuffix: Azure AI services
description: Learn about the latest news and features updates for Azure OpenAI
manager: nitinme
author: mrbullwinkle
ms.author: mbullwin
ms.service: cognitive-services
ms.subservice: openai
ms.topic: whats-new
ms.date: 08/08/2023
recommendations: false
keywords:  
---

# What's new in Azure OpenAI Service

## August 2023

- You can now deploy Azure OpenAI on your data to [Power Virtual Agents](/azure/ai-services/openai/concepts/use-your-data#deploying-the-model).
- [Azure OpenAI on your data](./concepts/use-your-data.md#virtual-network-support--private-network-support) now supports private endpoints.

## July 2023

### Support for function calling

- [Azure OpenAI now supports function calling](./how-to/function-calling.md) to enable you to work with functions in the chat completions API.

### Embedding input array increase

- Azure OpenAI now [supports arrays with up to 16 inputs](./how-to/switching-endpoints.md#azure-openai-embeddings-multiple-input-support) per API request with text-embedding-ada-002 Version 2.

### New Regions

- Azure OpenAI is now also available in the Canada East, East US 2, Japan East, and North Central US regions. Check the [models page](concepts/models.md), for the latest information on model availability in each region.  

## June 2023

### Use Azure OpenAI on your own data (preview)

- [Azure OpenAI on your data](./concepts/use-your-data.md) is now available in preview, enabling you to chat with OpenAI models such as GPT-35-Turbo and GPT-4 and receive responses based on your data. 

### New versions of gpt-35-turbo and gpt-4 models

- gpt-35-turbo (version 0613)
- gpt-35-turbo-16k (version 0613)
- gpt-4 (version 0613)
- gpt-4-32k (version 0613)

### UK South

- Azure OpenAI is now available in the UK South region. Check the [models page](concepts/models.md), for the latest information on model availability in each region.  

### Content filtering & annotations (Preview)

- How to [configure content filters](how-to/content-filters.md) with Azure OpenAI Service.
- [Enable annotations](concepts/content-filter.md) to view content filtering category and severity information as part of your GPT based Completion and Chat Completion calls.

### Quota

- Quota provides the flexibility to actively [manage the allocation of rate limits across the deployments](how-to/quota.md) within your subscription.

## May 2023

### Java & JavaScript SDK support

- NEW Azure OpenAI preview SDKs offering support for [JavaScript](quickstart.md?tabs=command-line&pivots=programming-language-javascript) and [Java](quickstart.md?tabs=command-line&pivots=programming-language-java).

### Azure OpenAI Chat Completion General Availability (GA)

- General availability support for:
  - Chat Completion API version `2023-05-15`.
  - GPT-35-Turbo models.
  - GPT-4 model series. Due to high demand access to this model series is currently only available by request. To request access, existing Azure OpenAI customers can [apply by filling out this form](https://aka.ms/oai/get-gpt4)
  
If you are currently using the `2023-03-15-preview` API, we recommend migrating to the GA `2023-05-15` API. If you are currently using API version `2022-12-01` this API remains GA, but does not include the latest Chat Completion capabilities.

> [!IMPORTANT]
> Using the current versions of the GPT-35-Turbo models with the completion endpoint remains in preview.
  
### France Central

- Azure OpenAI is now available in the France Central region. Check the [models page](concepts/models.md), for the latest information on model availability in each region.  

## April 2023

- **DALL-E 2 public preview**. Azure OpenAI Service now supports image generation APIs powered by OpenAI's DALL-E 2 model. Get AI-generated images based on the descriptive text you provide. To learn more, check out the [quickstart](./dall-e-quickstart.md). To request access, existing Azure OpenAI customers can [apply by filling out this form](https://aka.ms/oai/access).

- **Inactive deployments of customized models will now be deleted after 15 days; models will remain available for redeployment.** If a customized (fine-tuned) model is deployed for more than fifteen (15) days during which no completions or chat completions calls are made to it, the deployment will automatically be deleted (and no further hosting charges will be incurred for that deployment). The underlying customized model will remain available and can be redeployed at any time. To learn more check out the [how-to-article](./how-to/fine-tuning.md?pivots=programming-language-studio#deploy-a-customized-model).


## March 2023

- **GPT-4 series models are now available in preview on Azure OpenAI**. To request access, existing Azure OpenAI customers can [apply by filling out this form](https://aka.ms/oai/get-gpt4). These models are currently available in the East US and South Central US regions.

- **New Chat Completion API for GPT-35-Turbo and GPT-4 models released in preview on 3/21**. To learn more checkout the [updated quickstarts](./quickstart.md) and [how-to article](./how-to/chatgpt.md).

- **GPT-35-Turbo preview**. To learn more checkout the [how-to article](./how-to/chatgpt.md).

- Increased training limits for fine-tuning: The max training job size (tokens in training file) x (# of epochs) is 2 Billion tokens for all models. We have also increased the max training job from 120 to 720 hours. 
- Adding additional use cases to your existing access.  Previously, the process for adding new use cases required customers to reapply to the service. Now, we're releasing a new process that allows you to quickly add new use cases to your use of the service. This process follows the established Limited Access process within Azure AI services. [Existing customers can attest to any and all new use cases here](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUM003VEJPRjRSOTZBRVZBV1E5N1lWMk1XUyQlQCN0PWcu). Please note that this is required anytime you would like to use the service for a new use case you did not originally apply for.

## February 2023

### New Features

- .NET SDK(inference) [preview release](https://www.nuget.org/packages/Azure.AI.OpenAI/1.0.0-beta.3) | [Samples](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/openai/Azure.AI.OpenAI/tests/Samples)
- [Terraform SDK update](https://registry.terraform.io/providers/hashicorp/azurerm/3.37.0/docs/resources/cognitive_deployment) to support Azure OpenAI management operations.
- Inserting text at the end of a completion is now supported with the `suffix` parameter.

### Updates

- Content filtering is on by default.

New articles on:

- [Monitoring an Azure OpenAI Service](./how-to/monitoring.md)
- [Plan and manage costs for Azure OpenAI](./how-to/manage-costs.md)

New training course:

- [Intro to Azure OpenAI](/training/modules/explore-azure-openai/)


## January 2023

### New Features

* **Service GA**. Azure OpenAI Service is now generally available.​

* **New models**: Addition of the latest text model, text-davinci-003 (East US, West Europe), text-ada-embeddings-002 (East US, South Central US, West Europe)


## December 2022

### New features

* **The latest models from OpenAI.** Azure OpenAI provides access to all the latest models including the GPT-3.5 series​.

* **New API version (2022-12-01).** This update includes several requested enhancements including token usage information in the API response, improved error messages for files, alignment with OpenAI on fine-tuning creation data structure, and support for the suffix parameter to allow custom naming of fine-tuned jobs.  ​

* **Higher request per second limits.** 50 for non-Davinci models. 20 for Davinci models.​

* **Faster fine-tune deployments.** Deploy an Ada and Curie fine-tuned models in under 10 minutes.​

* **Higher training limits:** 40M training tokens for Ada, Babbage, and Curie. 10M for Davinci.​

* **Process for requesting modifications to the abuse & miss-use data logging & human review.** Today, the service logs request/response data for the purposes of abuse and misuse detection to ensure that these powerful models aren't abused. However, many customers have strict data privacy and security requirements that require greater control over their data. To support these use cases, we're releasing a new process for customers to modify the content filtering policies or turn off the abuse logging for low-risk use cases. This process follows the established Limited Access process within Azure AI services and [existing OpenAI customers can apply here](https://aka.ms/oai/modifiedaccess).​

* **Customer managed key (CMK) encryption.** CMK provides customers greater control over managing their data in Azure OpenAI by providing their own encryption keys used for storing training data and customized models. Customer-managed keys (CMK), also known as bring your own key (BYOK), offer greater flexibility to create, rotate, disable, and revoke access controls. You can also audit the encryption keys used to protect your data. [Learn more from our encryption at rest documentation](encrypt-data-at-rest.md).

* **Lockbox support**​

* **SOC-2 compliance**​

* **Logging and diagnostics** through Azure Resource Health, Cost Analysis, and Metrics & Diagnostic settings​.

* **Studio improvements.** Numerous usability improvements to the Studio workflow including Azure AD role support to control who in the team has access to create fine-tuned models and deploy.

### Changes (breaking)

**Fine-tuning** create API request has been updated to match OpenAI’s schema.

**Preview API versions:**

```json
{​
    "training_file": "file-XGinujblHPwGLSztz8cPS8XY",​
    "hyperparams": { ​
        "batch_size": 4,​
        "learning_rate_multiplier": 0.1,​
        "n_epochs": 4,​
        "prompt_loss_weight": 0.1,​
    }​
}
```

**API version 2022-12-01:**

```json
{​
    "training_file": "file-XGinujblHPwGLSztz8cPS8XY",​
    "batch_size": 4,​
    "learning_rate_multiplier": 0.1,​
    "n_epochs": 4,​
    "prompt_loss_weight": 0.1,​
}
```

**Content filtering is temporarily off** by default. Azure content moderation works differently than OpenAI. Azure OpenAI runs content filters during the generation call to detect harmful or abusive content and filters them from the response. [Learn More​](./concepts/content-filter.md)

​These models will be re-enabled in Q1 2023 and be on by default. ​

​**Customer actions**​

* [Contact Azure Support](https://portal.azure.com/#view/Microsoft_Azure_Support/HelpAndSupportBlade/~/overview) if you would like these turned on for your subscription​.
* [Apply for filtering modifications](https://aka.ms/oai/modifiedaccess), if you would like to have them remain off. (This option will be for low-risk use cases only.)​

## Next steps

Learn more about the [underlying models that power Azure OpenAI](./concepts/models.md).
