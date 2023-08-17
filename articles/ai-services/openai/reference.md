---
title: Azure OpenAI Service REST API reference
titleSuffix: Azure OpenAI
description: Learn how to use Azure OpenAI's REST API. In this article, you'll learn about authorization options,  how to structure a request and receive a response.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: openai
ms.topic: conceptual
ms.date: 08/15/2023
author: mrbullwinkle
ms.author: mbullwin
recommendations: false
ms.custom:
---

# Azure OpenAI Service REST API reference

This article provides details on the inference REST API endpoints for Azure OpenAI.

## Authentication

Azure OpenAI provides two methods for authentication. you can use  either API Keys or Azure Active Directory.

- **API Key authentication**: For this type of authentication, all API requests must include the API Key in the ```api-key``` HTTP header. The [Quickstart](./quickstart.md) provides guidance for how to make calls with this type of authentication.

- **Azure Active Directory authentication**: You can authenticate an API call using an Azure Active Directory token. Authentication tokens are included in a request as the ```Authorization``` header. The token provided must be preceded by ```Bearer```, for example ```Bearer YOUR_AUTH_TOKEN```. You can read our how-to guide on [authenticating with Azure Active Directory](./how-to/managed-identity.md).

### REST API versioning

The service APIs are versioned using the ```api-version``` query parameter. All versions follow the YYYY-MM-DD date structure. For example:

```http
POST https://YOUR_RESOURCE_NAME.openai.azure.com/openai/deployments/YOUR_DEPLOYMENT_NAME/completions?api-version=2023-05-15
```

## Completions

With the Completions operation, the model will generate one or more predicted completions based on a provided prompt. The service can also return the probabilities of alternative tokens at each position.

**Create a completion**

```http
POST https://{your-resource-name}.openai.azure.com/openai/deployments/{deployment-id}/completions?api-version={api-version}
```

**Path parameters**

| Parameter | Type | Required? |  Description |
|--|--|--|--|
| ```your-resource-name``` | string |  Required | The name of your Azure OpenAI Resource. |
| ```deployment-id``` | string | Required | The deployment name you chose when you deployed the model.  |
| ```api-version``` | string | Required |The API version to use for this operation. This follows the YYYY-MM-DD format.  |

**Supported versions**

- `2023-03-15-preview` [Swagger spec](https://github.com/Azure/azure-rest-api-specs/blob/main/specification/cognitiveservices/data-plane/AzureOpenAI/inference/preview/2023-03-15-preview/inference.json)
- `2022-12-01` [Swagger spec](https://github.com/Azure/azure-rest-api-specs/blob/main/specification/cognitiveservices/data-plane/AzureOpenAI/inference/stable/2022-12-01/inference.json)
- `2023-05-15` [Swagger spec](https://github.com/Azure/azure-rest-api-specs/blob/main/specification/cognitiveservices/data-plane/AzureOpenAI/inference/stable/2023-05-15/inference.json)
- `2023-06-01-preview` [Swagger spec](https://github.com/Azure/azure-rest-api-specs/blob/main/specification/cognitiveservices/data-plane/AzureOpenAI/inference/preview/2023-06-01-preview/inference.json)

**Request body**

| Parameter | Type | Required? | Default | Description |
|--|--|--|--|--|
| ```prompt``` | string or array | Optional | ```<\|endoftext\|>``` | The prompt(s) to generate completions for, encoded as a string, or array of strings. Note that ```<\|endoftext\|>``` is the document separator that the model sees during training, so if a prompt isn't specified the model will generate as if from the beginning of a new document. |
| ```max_tokens``` | integer | Optional | 16 | The maximum number of tokens to generate in the completion. The token count of your prompt plus max_tokens can't exceed the model's context length. Most models have a context length of 2048 tokens (except for the newest models, which support 4096). |
| ```temperature``` | number | Optional | 1 | What sampling temperature to use, between 0 and 2. Higher values means the model will take more risks. Try 0.9 for more creative applications, and 0 (`argmax sampling`) for ones with a well-defined answer. We generally recommend altering this or top_p but not both. |
| ```top_p``` | number | Optional | 1 | An alternative to sampling with temperature, called nucleus sampling, where the model considers the results of the tokens with top_p probability mass. So 0.1 means only the tokens comprising the top 10% probability mass are considered. We generally recommend altering this or temperature but not both. |
| ```logit_bias``` | map | Optional | null | Modify the likelihood of specified tokens appearing in the completion. Accepts a json object that maps tokens (specified by their token ID in the GPT tokenizer) to an associated bias value from -100 to 100. You can use this tokenizer tool (which works for both GPT-2 and GPT-3) to convert text to token IDs. Mathematically, the bias is added to the logits generated by the model prior to sampling. The exact effect will vary per model, but values between -1 and 1 should decrease or increase likelihood of selection; values like -100 or 100 should result in a ban or exclusive selection of the relevant token. As an example, you can pass {"50256": -100} to prevent the <\|endoftext\|> token from being generated. |
| ```user``` | string | Optional | | A unique identifier representing your end-user, which can help monitoring and detecting abuse |
| ```n``` | integer | Optional |  1 | How many completions to generate for each prompt. Note: Because this parameter generates many completions, it can quickly consume your token quota. Use carefully and ensure that you have reasonable settings for max_tokens and stop. |
| ```stream``` | boolean | Optional | False | Whether to stream back partial progress. If set, tokens will be sent as data-only server-sent events as they become available, with the stream terminated by a data: [DONE] message.| 
| ```logprobs``` | integer | Optional | null | Include the log probabilities on the logprobs most likely tokens, as well the chosen tokens. For example, if logprobs is 10, the API will return a list of the 10 most likely tokens. the API will always return the logprob of the sampled token, so there may be up to logprobs+1 elements in the response. This parameter cannot be used with `gpt-35-turbo`. |
| ```suffix```| string | Optional | null | The suffix that comes after a completion of inserted text. |
| ```echo``` | boolean | Optional | False | Echo back the prompt in addition to the completion. This parameter cannot be used with `gpt-35-turbo`. |
| ```stop``` | string or array | Optional | null | Up to four sequences where the API will stop generating further tokens. The returned text won't contain the stop sequence. |
| ```presence_penalty``` | number | Optional | 0 | Number between -2.0 and 2.0. Positive values penalize new tokens based on whether they appear in the text so far, increasing the model's likelihood to talk about new topics. |
| ```frequency_penalty``` | number | Optional | 0 | Number between -2.0 and 2.0. Positive values penalize new tokens based on their existing frequency in the text so far, decreasing the model's likelihood to repeat the same line verbatim. |
| ```best_of``` | integer | Optional | 1 | Generates best_of completions server-side and returns the "best" (the one with the lowest log probability per token). Results can't be streamed. When used with n, best_of controls the number of candidate completions and n specifies how many to return – best_of must be greater than n. Note: Because this parameter generates many completions, it can quickly consume your token quota. Use carefully and ensure that you have reasonable settings for max_tokens and stop. This parameter cannot be used with `gpt-35-turbo`. |

#### Example request

```console
curl https://YOUR_RESOURCE_NAME.openai.azure.com/openai/deployments/YOUR_DEPLOYMENT_NAME/completions?api-version=2023-05-15\
  -H "Content-Type: application/json" \
  -H "api-key: YOUR_API_KEY" \
  -d "{
  \"prompt\": \"Once upon a time\",
  \"max_tokens\": 5
}"
```

#### Example response

```json
{
    "id": "cmpl-4kGh7iXtjW4lc9eGhff6Hp8C7btdQ",
    "object": "text_completion",
    "created": 1646932609,
    "model": "ada",
    "choices": [
        {
            "text": ", a dark line crossed",
            "index": 0,
            "logprobs": null,
            "finish_reason": "length"
        }
    ]
}
```

In the example response, `finish_reason` equals `stop`. If `finish_reason` equals `content_filter` consult our [content filtering guide](./concepts/content-filter.md) to understand why this is occurring.

## Embeddings
Get a vector representation of a given input that can be easily consumed by machine learning models and other algorithms.

> [!NOTE]
> OpenAI currently allows a larger number of array inputs with `text-embedding-ada-002`. Azure OpenAI currently supports input arrays up to 16 for `text-embedding-ada-002 (Version 2)`. Both require the max input token limit per API request to remain under 8191 for this model.

**Create an embedding**

```http
POST https://{your-resource-name}.openai.azure.com/openai/deployments/{deployment-id}/embeddings?api-version={api-version}
```

**Path parameters**

| Parameter | Type | Required? |  Description |
|--|--|--|--|
| ```your-resource-name``` | string |  Required | The name of your Azure OpenAI Resource. |
| ```deployment-id``` | string | Required | The name of your model deployment. You're required to first deploy a model before you can make calls |
| ```api-version``` | string | Required |The API version to use for this operation. This follows the YYYY-MM-DD format.  |

**Supported versions**

- `2023-03-15-preview` [Swagger spec](https://github.com/Azure/azure-rest-api-specs/blob/main/specification/cognitiveservices/data-plane/AzureOpenAI/inference/preview/2023-03-15-preview/inference.json)
- `2022-12-01` [Swagger spec](https://github.com/Azure/azure-rest-api-specs/blob/main/specification/cognitiveservices/data-plane/AzureOpenAI/inference/stable/2022-12-01/inference.json)
- `2023-05-15` [Swagger spec](https://github.com/Azure/azure-rest-api-specs/blob/main/specification/cognitiveservices/data-plane/AzureOpenAI/inference/stable/2023-05-15/inference.json)

**Request body**

| Parameter | Type | Required? | Default | Description |
|--|--|--|--|--|
| ```input```| string or array | Yes | N/A | Input text to get embeddings for, encoded as an array or string. The number of input tokens varies depending on what [model you are using](./concepts/models.md). Only `text-embedding-ada-002 (Version 2)` supports array input.|
| ```user``` | string | No | Null | A unique identifier representing your end-user. This will help Azure OpenAI monitor and detect abuse. **Do not pass PII identifiers instead use pseudoanonymized values such as GUIDs** |

#### Example request

```console
curl https://YOUR_RESOURCE_NAME.openai.azure.com/openai/deployments/YOUR_DEPLOYMENT_NAME/embeddings?api-version=2023-05-15 \
  -H "Content-Type: application/json" \
  -H "api-key: YOUR_API_KEY" \
  -d "{\"input\": \"The food was delicious and the waiter...\"}"
```

#### Example response

```json
{
  "object": "list",
  "data": [
    {
      "object": "embedding",
      "embedding": [
        0.018990106880664825,
        -0.0073809814639389515,
        .... (1024 floats total for ada)
        0.021276434883475304,
      ],
      "index": 0
    }
  ],
  "model": "text-similarity-babbage:001"
}
```

## Chat completions

Create completions for chat messages with the GPT-35-Turbo and GPT-4  models. 

**Create chat completions**

```http
POST https://{your-resource-name}.openai.azure.com/openai/deployments/{deployment-id}/chat/completions?api-version={api-version}
```

**Path parameters**

| Parameter | Type | Required? |  Description |
|--|--|--|--|
| ```your-resource-name``` | string |  Required | The name of your Azure OpenAI Resource. |
| ```deployment-id``` | string | Required | The name of your model deployment. You're required to first deploy a model before you can make calls |
| ```api-version``` | string | Required |The API version to use for this operation. This follows the YYYY-MM-DD format.  |

**Supported versions**

- `2023-03-15-preview` [Swagger spec](https://github.com/Azure/azure-rest-api-specs/blob/main/specification/cognitiveservices/data-plane/AzureOpenAI/inference/preview/2023-03-15-preview/inference.json)
- `2023-05-15` [Swagger spec](https://github.com/Azure/azure-rest-api-specs/blob/main/specification/cognitiveservices/data-plane/AzureOpenAI/inference/stable/2023-05-15/inference.json)
- `2023-06-01-preview` [Swagger spec](https://github.com/Azure/azure-rest-api-specs/blob/main/specification/cognitiveservices/data-plane/AzureOpenAI/inference/preview/2023-06-01-preview/inference.json)
- `2023-07-01-preview` [Swagger spec](https://github.com/Azure/azure-rest-api-specs/blob/main/specification/cognitiveservices/data-plane/AzureOpenAI/inference/preview/2023-07-01-preview/generated.json)

#### Example request

```console
curl https://YOUR_RESOURCE_NAME.openai.azure.com/openai/deployments/YOUR_DEPLOYMENT_NAME/chat/completions?api-version=2023-05-15 \
  -H "Content-Type: application/json" \
  -H "api-key: YOUR_API_KEY" \
  -d '{"messages":[{"role": "system", "content": "You are a helpful assistant."},{"role": "user", "content": "Does Azure OpenAI support customer managed keys?"},{"role": "assistant", "content": "Yes, customer managed keys are supported by Azure OpenAI."},{"role": "user", "content": "Do other Azure AI services support this too?"}]}'

```

#### Example response

```console
{"id":"chatcmpl-6v7mkQj980V1yBec6ETrKPRqFjNw9",
"object":"chat.completion","created":1679072642,
"model":"gpt-35-turbo",
"usage":{"prompt_tokens":58,
"completion_tokens":68,
"total_tokens":126},
"choices":[{"message":{"role":"assistant",
"content":"Yes, other Azure AI services also support customer managed keys. Azure AI services offer multiple options for customers to manage keys, such as using Azure Key Vault, customer-managed keys in Azure Key Vault or customer-managed keys through Azure Storage service. This helps customers ensure that their data is secure and access to their services is controlled."},"finish_reason":"stop","index":0}]}
```

In the example response, `finish_reason` equals `stop`. If `finish_reason` equals `content_filter` consult our [content filtering guide](./concepts/content-filter.md) to understand why this is occurring.

Output formatting adjusted for ease of reading, actual output is a single block of text without line breaks.

| Parameter | Type | Required? | Default | Description |
|--|--|--|--|--|
| ```messages``` | array | Required |  | The collection of context messages associated with this chat completions request. Typical usage begins with a [chat message](#chatmessage) for the System role that provides instructions for the behavior of the assistant, followed by alternating messages between the User and Assistant roles.|
| ```temperature```| number | Optional | 1 | What sampling temperature to use, between 0 and 2. Higher values like 0.8 will make the output more random, while lower values like 0.2 will make it more focused and deterministic.\nWe generally recommend altering this or `top_p` but not both. |
| ```n``` | integer | Optional | 1 | How many chat completion choices to generate for each input message.  |
| ```stream``` | boolean | Optional | false | If set, partial message deltas will be sent, like in ChatGPT. Tokens will be sent as data-only server-sent events as they become available, with the stream terminated by a `data: [DONE]` message." |
| ```stop``` | string or array | Optional | null | Up to 4 sequences where the API will stop generating further tokens.|
| ```max_tokens``` | integer | Optional | inf | The maximum number of tokens allowed for the generated answer. By default, the number of tokens the model can return will be (4096 - prompt tokens).|
| ```presence_penalty``` | number | Optional | 0 | Number between -2.0 and 2.0. Positive values penalize new tokens based on whether they appear in the text so far, increasing the model's likelihood to talk about new topics.|
| ```frequency_penalty``` | number | Optional | 0 | Number between -2.0 and 2.0. Positive values penalize new tokens based on their existing frequency in the text so far, decreasing the model's likelihood to repeat the same line verbatim.|
| ```logit_bias``` | object | Optional | null | Modify the likelihood of specified tokens appearing in the completion. Accepts a json object that maps tokens (specified by their token ID in the tokenizer) to an associated bias value from -100 to 100. Mathematically, the bias is added to the logits generated by the model prior to sampling. The exact effect will vary per model, but values between -1 and 1 should decrease or increase likelihood of selection; values like -100 or 100 should result in a ban or exclusive selection of the relevant token.|
| ```user``` | string | Optional | | A unique identifier representing your end-user, which can help Azure OpenAI to monitor and detect abuse.|
|```function_call```|  | Optional | | Controls how the model responds to function calls. "none" means the model does not call a function, and responds to the end-user. "auto" means the model can pick between an end-user or calling a function. Specifying a particular function via {"name": "my_function"} forces the model to call that function. "none" is the default when no functions are present. "auto" is the default if functions are present. This parameter requires API version [`2023-07-01-preview`](https://github.com/Azure/azure-rest-api-specs/blob/main/specification/cognitiveservices/data-plane/AzureOpenAI/inference/preview/2023-07-01-preview/generated.json) |
|```functions``` | [`FunctionDefinition[]`](#functiondefinition) | Optional | | A list of functions the model may generate JSON inputs for. This parameter requires API version [`2023-07-01-preview`](https://github.com/Azure/azure-rest-api-specs/blob/main/specification/cognitiveservices/data-plane/AzureOpenAI/inference/preview/2023-07-01-preview/generated.json)|

### ChatMessage

A single, role-attributed message within a chat completion interaction.

| Name | Type | Description |
|---|---|---|
| content | string | The text associated with this message payload.|
| function_call | [FunctionCall](#functioncall)| The name and arguments of a function that should be called, as generated by the model. |
| name | string | The `name` of the author of this message. `name` is required if role is `function`, and it should be the name of the function whose response is in the `content`. May contain a-z, A-Z, 0-9, and underscores, with a maximum length of 64 characters.|
|role | [ChatRole](#chatrole) | The role associated with this message payload |

### ChatRole

A description of the intended purpose of a message within a chat completions interaction.

|Name | Type | Description |
|---|---|---|
| assistant | string | The role that provides responses to system-instructed, user-prompted input. |
| function | string | The role that provides function results for chat completions. |
| system | string | The role that instructs or sets the behavior of the assistant. |
| user | string | The role that provides input for chat completions. |

### FunctionCall

The name and arguments of a function that should be called, as generated by the model. This requires API version [`2023-07-01-preview`](https://github.com/Azure/azure-rest-api-specs/blob/main/specification/cognitiveservices/data-plane/AzureOpenAI/inference/preview/2023-07-01-preview/generated.json)

| Name | Type | Description|
|---|---|---|
| arguments | string | The arguments to call the function with, as generated by the model in JSON format. Note that the model does not always generate valid JSON, and may fabricate parameters not defined by your function schema. Validate the arguments in your code before calling your function. |
| name | string | The name of the function to call.|

### FunctionDefinition

The definition of a caller-specified function that chat completions may invoke in response to matching user input. This requires API version [`2023-07-01-preview`](https://github.com/Azure/azure-rest-api-specs/blob/main/specification/cognitiveservices/data-plane/AzureOpenAI/inference/preview/2023-07-01-preview/generated.json)

|Name | Type| Description|
|---|---|---|
| description | string | A description of what the function does. The model will use this description when selecting the function and interpreting its parameters. |
| name | string | The name of the function to be called. |
| parameters | | The parameters the functions accepts, described as a [JSON Schema](https://json-schema.org/understanding-json-schema/) object.|

## Completions extensions

Extensions for chat completions, for example Azure OpenAI on your data.

**Use chat completions extensions**

```http
POST {your-resource-name}/openai/deployments/{deployment-id}/extensions/chat/completions?api-version={api-version}
```

**Path parameters**

| Parameter | Type | Required? |  Description |
|--|--|--|--|
| ```your-resource-name``` | string |  Required | The name of your Azure OpenAI Resource. |
| ```deployment-id``` | string | Required | The name of your model deployment. You're required to first deploy a model before you can make calls |
| ```api-version``` | string | Required |The API version to use for this operation. This follows the YYYY-MM-DD format.  |

**Supported versions**
- `2023-06-01-preview` [Swagger spec](https://github.com/Azure/azure-rest-api-specs/blob/main/specification/cognitiveservices/data-plane/AzureOpenAI/inference/preview/2023-06-01-preview/inference.json)

#### Example request

```Console
curl -i -X POST YOUR_RESOURCE_NAME/openai/deployments/YOUR_DEPLOYMENT_NAME/extensions/chat/completions?api-version=2023-06-01-preview \
-H "Content-Type: application/json" \
-H "api-key: YOUR_API_KEY" \
-d \
'
{
    "temperature": 0,
    "max_tokens": 1000,
    "top_p": 1.0,
    "dataSources": [
        {
            "type": "AzureCognitiveSearch",
            "parameters": {
                "endpoint": "'YOUR_AZURE_COGNITIVE_SEARCH_ENDPOINT'",
                "key": "'YOUR_AZURE_COGNITIVE_SEARCH_KEY'",
                "indexName": "'YOUR_AZURE_COGNITIVE_SEARCH_INDEX_NAME'"
            }
        }
    ],
    "messages": [
        {
            "role": "user",
            "content": "What are the differences between Azure Machine Learning and Azure AI services?"
        }
    ]
}
'
```

#### Example response

```json
{
    "id": "12345678-1a2b-3c4e5f-a123-12345678abcd",
    "model": "",
    "created": 1684304924,
    "object": "chat.completion",
    "choices": [
        {
            "index": 0,
            "messages": [
                {
                    "role": "tool",
                    "content": "{\"citations\": [{\"content\": \"\\nAzure AI services are cloud-based artificial intelligence (AI) services...\", \"id\": null, \"title\": \"What is Azure AI services\", \"filepath\": null, \"url\": null, \"metadata\": {\"chunking\": \"orignal document size=250. Scores=0.4314117431640625 and 1.72564697265625.Org Highlight count=4.\"}, \"chunk_id\": \"0\"}], \"intent\": \"[\\\"Learn about Azure AI services.\\\"]\"}",
                    "end_turn": false
                },
                {
                    "role": "assistant",
                    "content": " \nAzure AI services are cloud-based artificial intelligence (AI) services that help developers build cognitive intelligence into applications without having direct AI or data science skills or knowledge. [doc1]. Azure Machine Learning is a cloud service for accelerating and managing the machine learning project lifecycle. [doc1].",
                    "end_turn": true
                }
            ]
        }
    ]
}
```

| Parameters | Type | Required? | Default | Description |
|--|--|--|--|--|
| `messages` | array | Required | null | The messages to generate chat completions for, in the chat format. |
| `dataSources` | array | Required |  | The data sources to be used for the Azure OpenAI on your data feature. |
| `temperature` | number | Optional | 0 | 	What sampling temperature to use, between 0 and 2. Higher values like 0.8 will make the output more random, while lower values like 0.2 will make it more focused and deterministic. We generally recommend altering this or `top_p` but not both. |
| `top_p` | number | Optional | 1 |An alternative to sampling with temperature, called nucleus sampling, where the model considers the results of the tokens with `top_p` probability mass. So 0.1 means only the tokens comprising the top 10% probability mass are considered. We generally recommend altering this or temperature but not both.|
| `stream` | boolean | Optional | false | If set, partial message deltas will be sent, like in ChatGPT. Tokens will be sent as data-only server-sent events as they become available, with the stream terminated by a message `"messages": [{"delta": {"content": "[DONE]"}, "index": 2, "end_turn": true}]`  |
| `stop` | string or array | Optional | null | Up to 2 sequences where the API will stop generating further tokens. |
| `max_tokens` | integer | Optional | 1000 | 	The maximum number of tokens allowed for the generated answer. By default, the number of tokens the model can return is `4096 - prompt_tokens`.  |

The following parameters can be used inside of the `parameters` field inside of `dataSources`.

|  Parameters | Type | Required? | Default | Description |
|--|--|--|--|--|
| `type` | string | Required | null | The data source to be used for the Azure OpenAI on your data feature. For Azure Cognitive search the value is `AzureCognitiveSearch`. |
| `endpoint` | string | Required | null | The data source endpoint. |
| `key` | string | Required | null | One of the Azure Cognitive Search admin keys for your service. |
| `indexName` | string | Required | null | The search index to be used. |
| `fieldsMapping` | dictionary | Optional | null | Index data column mapping.   |
| `inScope` | boolean | Optional | true | If set, this value will limit responses specific to the grounding data content.  |
| `topNDocuments` | number | Optional | 5 | Number of documents that need to be fetched for document augmentation.  |
| `queryType` | string | Optional | simple |  Indicates which query option will be used for Azure Cognitive Search. |
| `semanticConfiguration` | string | Optional | null |  The semantic search configuration. Only available when `queryType` is set to `semantic`.  |
| `roleInformation` | string | Optional | null |  Gives the model instructions about how it should behave and the context it should reference when generating a response. Corresponds to the “System Message” in Azure OpenAI Studio. <!--See [Using your data](./concepts/use-your-data.md#system-message) for more information.--> There’s a 100 token limit, which counts towards the overall token limit.|

## Image generation

### Request a generated image

Generate a batch of images from a text caption. Image generation is currently only available with `api-version=2023-06-01-preview`.

```http
POST https://{your-resource-name}.openai.azure.com/openai/images/generations:submit?api-version={api-version}
```

**Path parameters**

| Parameter | Type | Required? |  Description |
|--|--|--|--|
| ```your-resource-name``` | string |  Required | The name of your Azure OpenAI Resource. |
| ```api-version``` | string | Required |The API version to use for this operation. This follows the YYYY-MM-DD format.  |

**Supported versions**

- `2023-06-01-preview`

**Request body**

| Parameter | Type | Required? | Default | Description |
|--|--|--|--|--|
| ```prompt``` | string | Required |  | A text description of the desired image(s). The maximum length is 1000 characters. |
| ```n``` | integer | Optional | 1 | The number of images to generate. Must be between 1 and 5. |
| ```size``` | string | Optional | 1024x1024 | The size of the generated images. Must be one of `256x256`, `512x512`, or `1024x1024`. |

#### Example request

```console
curl -X POST https://YOUR_RESOURCE_NAME.openai.azure.com/openai/images/generations:submit?api-version=2023-06-01-preview \
  -H "Content-Type: application/json" \
  -H "api-key: YOUR_API_KEY" \
  -d '{
"prompt": "An avocado chair",
"size": "512x512",
"n": 3
}'
```

#### Example response

The operation returns a `202` status code and an `GenerateImagesResponse` JSON object containing the ID and status of the operation.

```json
{
  "id": "f508bcf2-e651-4b4b-85a7-58ad77981ffa",
  "status": "notRunning"
}
```

### Get a generated image result


Use this API to retrieve the results of an image generation operation. Image generation is currently only available with `api-version=2023-06-01-preview`.

```http
GET https://{your-resource-name}.openai.azure.com/openai/operations/images/{operation-id}?api-version={api-version}
```


**Path parameters**

| Parameter | Type | Required? |  Description |
|--|--|--|--|
| ```your-resource-name``` | string |  Required | The name of your Azure OpenAI Resource. |
| ```operation-id``` | string |  Required | The GUID that identifies the original image generation request. |

**Supported versions**

- `2023-06-01-preview`

#### Example request

```console
curl -X GET "https://{your-resource-name}.openai.azure.com/openai/operations/images/{operation-id}?api-version=2023-06-01-preview"
-H "Content-Type: application/json"
-H "Api-Key: {api key}"
```

#### Example response

Upon success the operation returns a `200` status code and an `OperationResponse` JSON object. The `status` field can be `"notRunning"` (task is queued but hasn't started yet), `"running"`, `"succeeded"`, `"canceled"` (task has timed out), `"failed"`, or `"deleted"`. A `succeeded` status indicates that the generated image is available for download at the given URL. If multiple images were generated, their URLs are all returned in the `result.data` field.

```json
{
  "created": 1685064331,
  "expires": 1685150737,
  "id": "4b755937-3173-4b49-bf3f-da6702a3971a",
  "result": {
    "data": [
      {
        "url": "<URL_TO_IMAGE>"
      },
      {
        "url": "<URL_TO_NEXT_IMAGE>"
      },
      ...
    ]
  },
  "status": "succeeded"
}
```

### Delete a generated image from the server

You can use the operation ID returned by the request to delete the corresponding image from the Azure server. Generated images are automatically deleted after 24 hours by default, but you can trigger the deletion earlier if you want to.

```http
DELETE https://{your-resource-name}.openai.azure.com/openai/operations/images/{operation-id}?api-version={api-version}
```

**Path parameters**

| Parameter | Type | Required? |  Description |
|--|--|--|--|
| ```your-resource-name``` | string |  Required | The name of your Azure OpenAI Resource. |
| ```operation-id``` | string |  Required | The GUID that identifies the original image generation request. |

**Supported versions**

- `2023-06-01-preview`

#### Example request

```console
curl -X DELETE "https://{your-resource-name}.openai.azure.com/openai/operations/images/{operation-id}?api-version=2023-06-01-preview"
-H "Content-Type: application/json"
-H "Api-Key: {api key}"
```

#### Response

The operation returns a `204` status code if successful. This API only succeeds if the operation is in an end state (not `running`).

## Management APIs

Azure OpenAI is deployed as a part of the Azure AI services. All Azure AI services rely on the same set of management APIs for creation, update and delete operations. The management APIs are also used for deploying models within an OpenAI resource.

[**Management APIs reference documentation**](/rest/api/cognitiveservices/)

## Next steps

Learn about [ Models, and fine-tuning with the REST API](/rest/api/cognitiveservices/azureopenaistable/files).
Learn more about the [underlying models that power Azure OpenAI](./concepts/models.md).
