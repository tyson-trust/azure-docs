---
title: "Reference: Document Intelligence (formerly Form Recognizer) Errors"
titleSuffix: Azure AI services
description: Learn how errors are represented in Document Intelligence and find a list of possible errors returned by the service.
author: paulhsu
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/18/2023
ms.author: paulhsu
monikerRange: '>=doc-intel-3.0.0'
---


# Document Intelligence error guide v3.0 and v3.1

Document Intelligence uses a unified design to represent all errors encountered in the REST APIs.  Whenever an API operation returns a 4xx or 5xx status code, additional information about the error is returned in the response JSON body as follows:

```json
{
  "error": {
    "code": "InvalidRequest",
    "message": "Invalid request.",
    "innererror": {
      "code": "InvalidContent",
      "message": "The file format is unsupported or corrupted. Refer to documentation for the list of supported formats."
    }
  }
}
```

For long-running operations where multiple errors may be encountered, the top-level error code is set to the most severe error, with the individual errors listed under the *error.details* property.  In such scenarios, the *target* property of each individual error specifies the trigger of the error.

```json
{
    "status": "failed",
    "createdDateTime": "2021-07-14T10:17:51Z",
    "lastUpdatedDateTime": "2021-07-14T10:17:51Z",
    "error": {
        "code": "InternalServerError",
        "message": "An unexpected error occurred.",
        "details": [
            {
                "code": "InternalServerError",
                "message": "An unexpected error occurred."
            },
            {
                "code": "InvalidContentDimensions",
                "message": "The input image dimensions are out of range. Refer to documentation for supported image dimensions.",
                "target": "2"
            }
        ]
    }
}
```

The top-level *error.code* property can be one of the following error code messages:

| Error Code           | Message                                                | Http Status |
| -------------------- | ------------------------------------------------------ | ----------- |
| InvalidRequest       | Invalid request.                                       | 400         |
| InvalidArgument      | Invalid argument.                                      | 400         |
| Forbidden            | Access forbidden due to policy or other configuration. | 403         |
| NotFound             | Resource not found.                                    | 404         |
| MethodNotAllowed     | The requested HTTP method is not allowed.              | 405         |
| Conflict             | The request could not be completed due to a conflict.  | 409         |
| UnsupportedMediaType | Request content type is not supported.                 | 415         |
| InternalServerError  | An unexpected error occurred.                          | 500         |
| ServiceUnavailable   | A transient error has occurred. Try again.      | 503         |

When possible, more details are specified in the *inner error* property.

| Top Error Code | Inner Error Code | Message |
| -------------- | ---------------- | ------- |
| Conflict | ModelExists | A model with the provided name already exists. |
| Forbidden | AuthorizationFailed | Authorization failed: {details} |
| Forbidden | InvalidDataProtectionKey | Data protection key is invalid: {details} |
| Forbidden | OutboundAccessForbidden | The request contains a domain name that is not allowed by the current access control policy. |
| InternalServerError | Unknown | Unknown error. |
| InvalidArgument | InvalidContentSourceFormat | Invalid content source: {details} |
| InvalidArgument | InvalidParameter | The parameter {parameterName} is invalid: {details} |
| InvalidArgument | InvalidParameterLength | Parameter {parameterName} length must not exceed {maxChars} characters. |
| InvalidArgument | InvalidSasToken | The shared access signature (SAS) is invalid: {details} |
| InvalidArgument | ParameterMissing | The parameter {parameterName} is required. |
| InvalidRequest | ContentSourceNotAccessible | Content is not accessible: {details} |
| InvalidRequest | ContentSourceTimeout | Timeout while receiving the file from client. |
| InvalidRequest | DocumentModelLimit | Account cannot create more than {maximumModels} models. |
| InvalidRequest | DocumentModelLimitNeural | Account cannot create more than 10 custom neural models per month. Please contact support to request additional capacity. |
| InvalidRequest | DocumentModelLimitComposed | Account cannot create a model with more than {details} component models. |
| InvalidRequest | InvalidContent | The file is corrupted or format is unsupported. Refer to documentation for the list of supported formats. |
| InvalidRequest | InvalidContentDimensions | The input image dimensions are out of range. Refer to documentation for supported image dimensions. |
| InvalidRequest | InvalidContentLength | The input image is too large. Refer to documentation for the maximum file size. |
| InvalidRequest | InvalidFieldsDefinition | Invalid fields: {details} |
| InvalidRequest | InvalidTrainingContentLength | Training content contains {bytes} bytes. Training is limited to {maxBytes} bytes. |
| InvalidRequest | InvalidTrainingContentPageCount | Training content contains {pages} pages. Training is limited to {pages} pages. |
| InvalidRequest | ModelAnalyzeError | Could not analyze using a custom model: {details} |
| InvalidRequest | ModelBuildError | Could not build the model: {details} |
| InvalidRequest | ModelComposeError | Could not compose the model: {details} |
| InvalidRequest | ModelNotReady | Model is not ready for the requested operation. Wait for training to complete or check for operation errors. |
| InvalidRequest | ModelReadOnly | The requested model is read-only. |
| InvalidRequest | NotSupportedApiVersion | The requested operation requires {minimumApiVersion} or later. |
| InvalidRequest | OperationNotCancellable | The operation can no longer be canceled. |
| InvalidRequest | TrainingContentMissing | Training data is missing: {details} |
| InvalidRequest | UnsupportedContent | Content is not supported: {details} |
| NotFound | ModelNotFound | The requested model was not found. It may have been deleted or is still building. |
| NotFound | OperationNotFound | The requested operation was not found. The identifier may be invalid or the operation may have expired. |
