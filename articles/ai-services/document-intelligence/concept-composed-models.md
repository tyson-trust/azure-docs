---
title: Composed custom models - Document Intelligence (formerly Form Recognizer)
titleSuffix: Azure AI services
description: Compose several custom models into a single model for easier data extraction from groups of distinct form types.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/18/2023
ms.author: lajanuar
monikerRange: '<=doc-intel-3.1.0'
---

# Document Intelligence composed custom models

::: moniker range=">=doc-intel-3.0.0"
[!INCLUDE [applies to v3.1 and v3.0](includes/applies-to-v3-1-v3-0.md)]
::: moniker-end

::: moniker range="doc-intel-2.1.0"
[!INCLUDE [applies to v2.1](includes/applies-to-v2-1.md)]
::: moniker-end

::: moniker range=">=doc-intel-2.1.0"

**Composed models**. A composed model is created by taking a collection of custom models and assigning them to a single model built from your form types. When a document is submitted for analysis using a composed model, the service performs a classification to decide which custom model best represents the submitted document.

With composed models, you can assign multiple custom models to a composed model called with a single model ID. It's useful when you've trained several models and want to group them to analyze similar form types. For example, your composed model might include custom models trained to analyze your supply, equipment, and furniture purchase orders. Instead of manually trying to select the appropriate model, you can use a composed model to determine the appropriate custom model for each analysis and extraction.

* ```Custom form``` and ```Custom template``` models can be composed together into a single composed model.

* With the model compose operation, you can assign up to 200 trained custom models to a single composed model. To analyze a document with a composed model, Document Intelligence first classifies the submitted form, chooses the best-matching assigned model, and returns results.

* For **_custom template models_**, the composed model can be created using variations of a custom template or different form types. This operation is useful when incoming forms may belong to one of several templates.

* The response includes a ```docType``` property to indicate which of the composed models was used to analyze the document.

* For ```Custom neural``` models the best practice is to add all the different variations of a single document type into a single training dataset and train on custom neural model. Model compose is best suited for scenarios when you have documents of different types being submitted for analysis.

::: moniker-end

::: moniker range=">=doc-intel-3.0.0"

With the introduction of [**custom classification models**](./concept-custom-classifier.md), you can choose to use a [**composed model**](./concept-composed-models.md) or [**classification model**](concept-custom-classifier.md) as an explicit step before analysis. For a deeper understanding of when to use a classification or composed model, _see_ [**Custom classification models**](concept-custom-classifier.md#compare-custom-classification-and-composed-models).

## Compose model limits

> [!NOTE]
> With the addition of **_custom neural model_** , there are a few limits to the compatibility of models that can be composed together.

### Composed model compatibility

|Custom model type|Models trained with v2.1 and v2.0 | Custom template models v3.0 |Custom neural models v3.0 (preview) |Custom neural models 3.0 (GA)|
|--|--|--|--|--|
|**Models trained with version 2.1 and v2.0** |Supported|Supported|Not Supported|Not Supported|
|**Custom template models v3.0** |Supported|Supported|Not Supported|NotSupported|
|**Custom template models v3.0 (GA)** |Not Supported|Not Supported|Supported|Not Supported|
|**Custom neural models v3.0 (preview)**|Not Supported|Not Supported|Supported|Not Supported|
|**Custom Neural models v3.0 (GA)**|Not Supported|Not Supported|Not Supported|Supported|

* To compose a model trained with a prior version of the API (v2.1 or earlier), train a model with the v3.0 API using the same labeled dataset. That addition ensures that the v2.1 model can be composed with other models.

* Models composed with v2.1 of the API continues to be supported, requiring no updates.

* The limit for maximum number of custom models that can be composed is 100.

::: moniker-end

## Development options

::: moniker range=">=doc-intel-3.0.0"
The following resources are supported by Document Intelligence **v3.0** :

| Feature | Resources |
|----------|-------------|
|_**Custom model**_| <ul><li>[Document Intelligence Studio](https://formrecognizer.appliedai.azure.com/studio/custommodel/projects)</li><li>[REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-2023-07-31/operations/AnalyzeDocument)</li><li>[C# SDK](quickstarts/get-started-sdks-rest-api.md?view=doc-intel-3.0.0&preserve-view=true)</li><li>[Java SDK](quickstarts/get-started-sdks-rest-api.md?view=doc-intel-3.0.0&preserve-view=true)</li><li>[JavaScript SDK](quickstarts/get-started-sdks-rest-api.md?view=doc-intel-3.0.0&preserve-view=true)</li><li>[Python SDK](quickstarts/get-started-sdks-rest-api.md?view=doc-intel-3.0.0&preserve-view=true)</li></ul>|
| _**Composed model**_| <ul><li>[Document Intelligence Studio](https://formrecognizer.appliedai.azure.com/studio/custommodel/projects)</li><li>[REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-2023-07-31/operations/ComposeDocumentModel)</li><li>[C# SDK](/dotnet/api/azure.ai.formrecognizer.training.formtrainingclient.startcreatecomposedmodel)</li><li>[Java SDK](/java/api/com.azure.ai.formrecognizer.training.formtrainingclient.begincreatecomposedmodel)</li><li>[JavaScript SDK](/javascript/api/@azure/ai-form-recognizer/documentmodeladministrationclient?view=azure-node-latest#@azure-ai-form-recognizer-documentmodeladministrationclient-begincomposemodel&preserve-view=true)</li><li>[Python SDK](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formtrainingclient?view=azure-python#azure-ai-formrecognizer-formtrainingclient-begin-create-composed-model&preserve-view=true)</li></ul>|
::: moniker-end

::: moniker range="doc-intel-2.1.0"

Document Intelligence v2.1 supports the following resources:

| Feature | Resources |
|----------|-------------------------|
|_**Custom model**_| <ul><li>[Document Intelligence labeling tool](https://fott-2-1.azurewebsites.net)</li><li>[REST API](how-to-guides/compose-custom-models.md?view=doc-intel-2.1.0&tabs=rest&preserve-view=true)</li><li>[Client library SDK](~/articles/ai-services/document-intelligence/how-to-guides/use-sdk-rest-api.md?view=doc-intel-2.1.0&preserve-view=true)</li><li>[Document Intelligence Docker container](containers/install-run.md?tabs=custom#run-the-container-with-the-docker-compose-up-command)</li></ul>|
| _**Composed model**_ |<ul><li>[Document Intelligence labeling tool](https://fott-2-1.azurewebsites.net/)</li><li>[REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/Compose)</li><li>[C# SDK](/dotnet/api/azure.ai.formrecognizer.training.createcomposedmodeloperation?view=azure-dotnet&preserve-view=true)</li><li>[Java SDK](/java/api/com.azure.ai.formrecognizer.models.createcomposedmodeloptions?view=azure-java-stable&preserve-view=true)</li><li>JavaScript SDK</li><li>[Python SDK](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formtrainingclient?view=azure-python#azure-ai-formrecognizer-formtrainingclient-begin-create-composed-model&preserve-view=true)</li></ul>|
::: moniker-end

## Next steps

Learn to create and compose custom models:

> [!div class="nextstepaction"]
> [**Build a custom model**](how-to-guides/build-a-custom-model.md)
> [**Compose custom models**](how-to-guides/compose-custom-models.md)
> 
