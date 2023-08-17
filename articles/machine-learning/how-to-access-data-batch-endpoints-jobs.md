---
title: "Create jobs and input data for batch endpoints"
titleSuffix: Azure Machine Learning
description: Learn how to access data from different sources in batch endpoints jobs.
services: machine-learning
ms.service: machine-learning
ms.subservice: inferencing
ms.topic: how-to
author: santiagxf
ms.author: fasantia
ms.date: 5/01/2023
ms.reviewer: larryfr
ms.custom: devplatv2
---

# Create jobs and input data for batch endpoints

Batch endpoints can be used to perform long batch operations over large amounts of data. Such data can be placed in different places. Some type of batch endpoints can also receive literal parameters as inputs. In this tutorial we'll cover how you can specify those inputs, and the different types or locations supported.

## Prerequisites

* This example assumes that you've created a batch endpoint with at least one deployment. To create an endpoint, follow the steps at [How to use batch endpoints for production workloads](how-to-use-batch-endpoints.md).

* You would need permissions to run a batch endpoint deployment. Read [Authorization on batch endpoints](how-to-authenticate-batch-endpoint.md) for details.

## Understanding inputs and outputs

Batch endpoints provide a durable API that consumers can use to create batch jobs. The same interface can be used to indicate the inputs and the outputs your deployment expects. Use inputs to pass any information your endpoint needs to perform the job. 

:::image type="content" source="./media/concept-endpoints/batch-endpoint-inputs-outputs.png" alt-text="Diagram showing how inputs and outputs are used in batch endpoints.":::

The number and type of inputs and outputs depend on the [type of batch deployment](concept-endpoints-batch.md#batch-deployments). Model deployments always require 1 data input and produce 1 data output. However, pipeline component deployments provide a more general construct to build endpoints. You can indicate any number of inputs and outputs.

The following table summarizes it:

| Deployment type | Input's number | Supported input's types | Output's number | Supported output's types |
|--|--|--|--|--|
| [Model deployment](concept-endpoints-batch.md#model-deployments) | 1 | [Data inputs](#data-inputs) | 1 | [Data outputs](#data-inputs) |
| [Pipeline component deployment (preview)](concept-endpoints-batch.md#pipeline-component-deployment-preview) | [0..N] | [Data inputs](#data-inputs) and [literal inputs](#literal-inputs) | [0..N] | [Data outputs](#data-outputs) |



> [!TIP]
> Inputs and outputs are always named. Those names serve as keys to indentify them and pass the actual value during invocation. For model deployments, since they always require 1 input and output, the name is ignored during invocation. You can assign the name its best describe your use case, like "salest_estimations".

## Data inputs

Data inputs refer to inputs that point to a location where data is placed. Since batch endpoints usually consume large amounts of data, you can't pass the input data as part of the invocation request. Instead, you indicate the location where the batch endpoint should go to look for the data. Input data is mounted and streamed on the target compute to improve performance. 

Batch endpoints support reading files located in the following storage options:

* [Azure Machine Learning Data Assets](#input-data-from-a-data-asset). The following types are supported:
    * Data assets of type Folder (`uri_folder`).
    * Data assets of type File (`uri_file`).
    * Datasets of type `FileDataset` (Deprecated).
* [Azure Machine Learning Data Stores](#input-data-from-data-stores). The following stores are supported:
    * Azure Blob Storage
    * Azure Data Lake Storage Gen1
    * Azure Data Lake Storage Gen2
* [Azure Storage Accounts](#input-data-from-azure-storage-accounts). The following storage containers are supported:
    * Azure Data Lake Storage Gen1
    * Azure Data Lake Storage Gen2
    * Azure Blob Storage

> [!TIP]
> Local data folders/files can be used when executing batch endpoints from the Azure Machine Learning CLI or Azure Machine Learning SDK for Python. However, that operation will result in the local data to be uploaded to the default Azure Machine Learning Data Store of the workspace you are working on.

> [!IMPORTANT]
> __Deprecation notice__: Datasets of type `FileDataset` (V1) are deprecated and will be retired in the future. Existing batch endpoints relying on this functionality will continue to work but batch endpoints created with GA CLIv2 (2.4.0 and newer) or GA REST API (2022-05-01 and newer) will not support V1 dataset.


#### Input data from a data asset

Azure Machine Learning data assets (formerly known as datasets) are supported as inputs for jobs. Follow these steps to run a batch endpoint job using data stored in a registered data asset in Azure Machine Learning:

> [!WARNING]
> Data assets of type Table (`MLTable`) aren't currently supported.

1. Let's create the data asset first. This data asset consists of a folder with multiple CSV files that we want to process in parallel using batch endpoints. You can skip this step is your data is already registered as a data asset.

    # [Azure CLI](#tab/cli)
   
    Create a data asset definition in `YAML`:
   
    __heart-dataset-unlabeled.yml__
    ```yaml
    $schema: https://azuremlschemas.azureedge.net/latest/data.schema.json
    name: heart-dataset-unlabeled
    description: An unlabeled dataset for heart classification.
    type: uri_folder
    path: heart-classifier-mlflow/data
    ```
   
    Then, create the data asset:
   
    ```bash
    az ml data create -f heart-dataset-unlabeled.yml
    ```
   
    # [Python](#tab/sdk)
   
    ```python
    data_path = "heart-classifier-mlflow/data"
    dataset_name = "heart-dataset-unlabeled"
 
    heart_dataset_unlabeled = Data(
        path=data_path,
        type=AssetTypes.URI_FOLDER,
        description="An unlabeled dataset for heart classification",
        name=dataset_name,
    )
    ```
    
    Then, create the data asset:
    
    ```python
    ml_client.data.create_or_update(heart_dataset_unlabeled)
    ```
    
    To get the newly created data asset, use:
    
    ```python
    heart_dataset_unlabeled = ml_client.data.get(name=dataset_name, label="latest")
    ```

    # [REST](#tab/rest)

    Use the Azure Machine Learning CLI, Azure Machine Learning SDK for Python, or Studio to get the location (region), workspace, and data asset name and version. You need them later.


1. Create a data input:

    # [Azure CLI](#tab/cli)
    
    ```azurecli
    DATASET_ID=$(az ml data show -n heart-dataset-unlabeled --label latest | jq -r .id)
    ```

    # [Python](#tab/sdk)

    ```python
    input = Input(path=heart_dataset_unlabeled.id)
    ```

    # [REST](#tab/rest)

    __Body__

    ```json
    {
        "properties": {
            "InputData": {
                "heart_dataset": {
                    "JobInputType" : "UriFolder",
                    "Uri": "azureml://locations/<location>/workspaces/<workspace>/data/<dataset_name>/versions/labels/latest"
                }
            }
        }
    }
    ```
    ---

    > [!NOTE]
    > Data assets ID would look like `/subscriptions/<subscription>/resourcegroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace>/data/<data-asset>/versions/<version>`. You can also use `azureml:/<datasset_name>@latest` as a way to indicate the input.


1. Run the deployment:

    # [Azure CLI](#tab/cli)
   
    Use the argument `--set` to indicate the input:

    ```azurecli
    az ml batch-endpoint invoke --name $ENDPOINT_NAME \
        --set inputs.heart_dataset.type uri_folder inputs.heart_dataset.path $DATASET_ID
    ```

    If your endpoint serves a model deployment, you can use the short form which supports only 1 input:

    ```azurecli
    az ml batch-endpoint invoke --name $ENDPOINT_NAME --input $DATASET_ID
    ```
    
    The argument `--set` tends to produce long commands when multiple inputs are indicated. On those cases, place your inputs in a `YAML` file and use `--file` to indicate the inputs you need for your endpoint invocation.

    __inputs.yml__
    
    ```yml
    inputs:
      heart_dataset: azureml:/<datasset_name>@latest
    ```
    
    ```azurecli
    az ml batch-endpoint invoke --name $ENDPOINT_NAME --file inputs.yml
    ```

    # [Python](#tab/sdk)

    ```python
    job = ml_client.batch_endpoints.invoke(
        endpoint_name=endpoint.name,
        inputs={
            "heart_dataset": input,
        }
    )
    ```

    If your endpoint serves a model deployment, you can use the short form which supports only 1 input:
   
    ```python
    job = ml_client.batch_endpoints.invoke(
       endpoint_name=endpoint.name,
       input=input,
    )
    ```

   # [REST](#tab/rest)

   __Request__
    
    ```http
    POST jobs HTTP/1.1
    Host: <ENDPOINT_URI>
    Authorization: Bearer <TOKEN>
    Content-Type: application/json
    ```

#### Input data from data stores

Data from Azure Machine Learning registered data stores can be directly referenced by batch deployments jobs. In this example, we're going to first upload some data to the default data store in the Azure Machine Learning workspace and then run a batch deployment on it. Follow these steps to run a batch endpoint job using data stored in a data store:

1. Let's get access to the default data store in the Azure Machine Learning workspace. If your data is in a different store, you can use that store instead. There's no requirement of using the default data store. 

    # [Azure CLI](#tab/cli)

    ```azurecli
    DATASTORE_ID=$(az ml datastore show -n workspaceblobstore | jq -r '.id')
    ```
    
    > [!NOTE]
    > Data stores ID would look like `/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace>/datastores/<data-store>`.

    # [Python](#tab/sdk)

    ```python
    default_ds = ml_client.datastores.get_default()
    ```

    # [REST](#tab/rest)

    Use the Azure Machine Learning CLI, Azure Machine Learning SDK for Python, or Studio to get the data store information.
    
    ---
    
    > [!TIP]
    > The default blob data store in a workspace is called __workspaceblobstore__. You can skip this step if you already know the resource ID of the default data store in your workspace.

1. We'll need to upload some sample data to it. This example assumes you've uploaded the sample data included in the repo in the folder `sdk/python/endpoints/batch/deploy-models/heart-classifier-mlflow/data` in the folder `heart-disease-uci-unlabeled` in the blob storage account. Ensure you have done that before moving forward.

1. Create a data input:

    # [Azure CLI](#tab/cli)
    
    Let's place the file path in the following variable:

    ```azurecli
    DATA_PATH="heart-disease-uci-unlabeled"
    INPUT_PATH="$DATASTORE_ID/paths/$DATA_PATH"
    ```

    # [Python](#tab/sdk)

    ```python
    data_path = "heart-disease-uci-unlabeled"
    input = Input(type=AssetTypes.URI_FOLDER, path=f"{default_ds.id}/paths/{data_path})
    ```

    If your data is a file, change `type=AssetTypes.URI_FILE`. 

    # [REST](#tab/rest)

    __Body__

    ```json
    {
        "properties": {
            "InputData": {
                "heart_dataset": {
                    "JobInputType" : "UriFolder",
                    "Uri": "azureml:/subscriptions/<subscription>/resourceGroups/<resource-group/providers/Microsoft.MachineLearningServices/workspaces/<workspace>/datastores/<data-store>/paths/<data-path>"
                }
            }
        }
    }
    ```

    If your data is a file, use `UriFile` as type instead. 

    ---
    
    > [!NOTE]
    > See how the path `paths` is appended to the resource id of the data store to indicate that what follows is a path inside of it.

    > [!TIP]
    > You can also use `azureml://datastores/<data-store>/paths/<data-path>` as a way to indicate the input.

1. Run the deployment:

    # [Azure CLI](#tab/cli)
   
    Use the argument `--set` to indicate the input:

    ```azurecli
    az ml batch-endpoint invoke --name $ENDPOINT_NAME \
        --set inputs.heart_dataset.type uri_folder inputs.heart_dataset.path $INPUT_PATH
    ```

    If your endpoint serves a model deployment, you can use the short form which supports only 1 input:

    ```azurecli
    az ml batch-endpoint invoke --name $ENDPOINT_NAME --input $INPUT_PATH --input-type uri_folder
    ```
    
    The argument `--set` tends to produce long commands when multiple inputs are indicated. On those cases, place your inputs in a `YAML` file and use `--file` to indicate the inputs you need for your endpoint invocation.

    __inputs.yml__
    
    ```yml
    inputs:
      heart_dataset:
        type: uri_folder
        path: azureml://datastores/<data-store>/paths/<data-path>
    ```
    
    ```azurecli
    az ml batch-endpoint invoke --name $ENDPOINT_NAME --file inputs.yml
    ```

    If your data is a file, use `uri_file` as type instead. 
   
    # [Python](#tab/sdk)

    ```python
    job = ml_client.batch_endpoints.invoke(
        endpoint_name=endpoint.name,
        inputs={
            "heart_dataset": input,
        }
    )
    ```

    If your endpoint serves a model deployment, you can use the short form which supports only 1 input:
   
    ```python
    job = ml_client.batch_endpoints.invoke(
       endpoint_name=endpoint.name,
       input=input,
    )
    ```

    # [REST](#tab/rest)

    __Request__
    
    ```http
    POST jobs HTTP/1.1
    Host: <ENDPOINT_URI>
    Authorization: Bearer <TOKEN>
    Content-Type: application/json
    ```

#### Input data from Azure Storage Accounts

Azure Machine Learning batch endpoints can read data from cloud locations in Azure Storage Accounts, both public and private. Use the following steps to run a batch endpoint job using data stored in a storage account:

> [!NOTE]
> Check the section [Security considerations when reading data](#security-considerations-when-reading-data) for learn more about additional configuration required to successfully read data from storage accoutns.

1. Create a data input:

    # [Azure CLI](#tab/cli)

    ```azurecli
    INPUT_DATA = "https://azuremlexampledata.blob.core.windows.net/data/heart-disease-uci/data"
    ```

    If your data is a file:

    ```azurecli
    INPUT_DATA = "https://azuremlexampledata.blob.core.windows.net/data/heart-disease-uci/data/heart.csv"
    ```

    # [Python](#tab/sdk)

    ```python
    input = Input(
        type=AssetTypes.URI_FOLDER, 
        path="https://azuremlexampledata.blob.core.windows.net/data/heart-disease-uci/data"
    )
    ```

    If your data is a file, change `type=AssetTypes.URI_FILE`:

    ```python
    input = Input(
        type=AssetTypes.URI_FILE,
        path="https://azuremlexampledata.blob.core.windows.net/data/heart-disease-uci/data/heart.csv"
    )
    ```

    # [REST](#tab/rest)

    __Body__

   ```json
   {
       "properties": {
           "InputData": {
               "heart_dataset": {
                   "JobInputType" : "UriFolder",
                   "Uri": "https://azuremlexampledata.blob.core.windows.net/data/heart-disease-uci/data"
               }
           }
       }
   }
   ```

   If your data is a file, change `JobInputType`:

   __Body__

   ```json
   {
       "properties": {
           "InputData": {
               "heart_dataset": {
                   "JobInputType" : "UriFile",
                   "Uri": "https://azuremlexampledata.blob.core.windows.net/data/heart-disease-uci/data/heart.csv"
               }
           }
       }
   }
   ```

1. Run the deployment:

    # [Azure CLI](#tab/cli)
    
    Use the argument `--set` to indicate the input:

    ```azurecli
    az ml batch-endpoint invoke --name $ENDPOINT_NAME \
        --set inputs.heart_dataset.type uri_folder inputs.heart_dataset.path $INPUT_DATA
    ```

    If your endpoint serves a model deployment, you can use the short form which supports only 1 input:

    ```azurecli
    az ml batch-endpoint invoke --name $ENDPOINT_NAME --input $INPUT_DATA --input-type uri_folder
    ```
    
    The argument `--set` tends to produce long commands when multiple inputs are indicated. On those cases, place your inputs in a `YAML` file and use `--file` to indicate the inputs you need for your endpoint invocation.

    __inputs.yml__
    
    ```yml
    inputs:
      heart_dataset:
        type: uri_folder
        path: https://azuremlexampledata.blob.core.windows.net/data/heart-disease-uci/data
    ```
    
    ```azurecli
    az ml batch-endpoint invoke --name $ENDPOINT_NAME --file inputs.yml
    ```

    If your data is a file, use `uri_file` as type instead. 

    # [Python](#tab/sdk)

    ```python
    job = ml_client.batch_endpoints.invoke(
        endpoint_name=endpoint.name,
        inputs={
            "heart_dataset": input,
        }
    )
    ```

    If your endpoint serves a model deployment, you can use the short form which supports only 1 input:
   
    ```python
    job = ml_client.batch_endpoints.invoke(
       endpoint_name=endpoint.name,
       input=input,
    )
    ```

    # [REST](#tab/rest)

    __Request__
    
    ```http
    POST jobs HTTP/1.1
    Host: <ENDPOINT_URI>
    Authorization: Bearer <TOKEN>
    Content-Type: application/json
    ```
    

### Security considerations when reading data

Batch endpoints ensure that only authorized users are able to invoke batch deployments and generate jobs. However, depending on how the input data is configured, other credentials may be used to read the underlying data. Use the following table to understand which credentials are used:

| Data input type              | Credential in store             | Credentials used                                              | Access granted by |
|------------------------------|---------------------------------|---------------------------------------------------------------|-------------------|
| Data store                   | Yes                             | Data store's credentials in the workspace                     | Credentials       |
| Data store                   | No                              | Identity of the job                                           | Depends on type   |
| Data asset                   | Yes                             | Data store's credentials in the workspace                     | Credentials       |
| Data asset                   | No                              | Identity of the job                                           | Depends on store  |
| Azure Blob Storage           | Not apply                       | Identity of the job + Managed identity of the compute cluster | RBAC              |
| Azure Data Lake Storage Gen1 | Not apply                       | Identity of the job + Managed identity of the compute cluster | POSIX             |
| Azure Data Lake Storage Gen2 | Not apply                       | Identity of the job + Managed identity of the compute cluster | POSIX and RBAC    |

The managed identity of the compute cluster is used for mounting and configuring external data storage accounts. However, the identity of the job is still used to read the underlying data allowing you to achieve granular access control. That means that in order to successfully read data from external storage services, the managed identity of the compute cluster where the deployment is running must have at least [Storage Blob Data Reader](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) access to the storage account. Only storage account owners can [change your access level via the Azure portal](../storage/blobs/assign-azure-role-data-access.md).

> [!NOTE]
> To assign an identity to the compute used by a batch deployment, follow the instructions at [Set up authentication between Azure Machine Learning and other services](how-to-identity-based-service-authentication.md#compute-cluster). Configure the identity on the compute cluster associated with the deployment. Notice that all the jobs running on such compute are affected by this change. However, different deployments (even under the same deployment) can be configured to run under different clusters so you can administer the permissions accordingly depending on your requirements.


## Literal inputs

Literal inputs refer to inputs that can be represented and resolved at invocation time, like strings, numbers, and boolean values. You typically use literal inputs to pass parameters to your endpoint as part of a pipeline component deployment.

Batch endpoints support the following literal types:

- `string`
- `boolean`
- `float`
- `integer`

The following example shows how to indicate an input named `score_mode`, of type `string`, with a value of `append`:

# [Azure CLI](#tab/cli)

Place your inputs in a `YAML` file and use `--file` to indicate the inputs you need for your endpoint invocation.

__inputs.yml__

```yml
inputs:
  score_mode:
    type: string
    default: append
```

```azurecli
az ml batch-endpoint invoke --name $ENDPOINT_NAME --file inputs.yml
```

You can also use the argument `--set` to indicate the value. However, it tends to produce long commands when multiple inputs are indicated:

```azurecli
az ml batch-endpoint invoke --name $ENDPOINT_NAME \
    --set inputs.score_mode.type string inputs.score_mode.default append
```

# [Python](#tab/sdk)

```python
job = ml_client.batch_endpoints.invoke(
    endpoint_name=endpoint.name, 
    inputs = { 
        'score_mode': Input(type="string", default="append")
        }
)
```

# [REST](#tab/rest)

__Body__

```json
{
    "properties": {
        "InputData": {
            "score_mode": {
                "JobInputType" : "Literal",
                "Value": "append"
            }
        }
    }
}
```

__Request__

```http
POST jobs HTTP/1.1
Host: <ENDPOINT_URI>
Authorization: Bearer <TOKEN>
Content-Type: application/json
```
---

## Data outputs

Data outputs refer to the location where the results of a batch job should be placed. Outputs are identified by name and Azure Machine Learning automatically assign a unique path to each named output. However, you can indicate another path if required. Batch Endpoints only support writing outputs in blob Azure Machine Learning data stores. 

The following example shows how to change the location where an output named `score` is placed. For completeness, these examples also configure an input named `heart_dataset`.

1. Let's use the default data store in the Azure Machine Learning workspace to save the outputs. You can use any other data store in your workspace as long as it's a blob storage account. 

    # [Azure CLI](#tab/cli)

    ```azurecli
    DATASTORE_ID=$(az ml datastore show -n workspaceblobstore | jq -r '.id')
    ```
    
    > [!NOTE]
    > Data stores ID would look like `/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace>/datastores/<data-store>`.

    # [Python](#tab/sdk)

    ```python
    default_ds = ml_client.datastores.get_default()
    ```

    # [REST](#tab/rest)

    Use the Azure Machine Learning CLI, Azure Machine Learning SDK for Python, or Studio to get the data store information.
    

1. Create a data output:

    # [Azure CLI](#tab/cli)
    
    ```azurecli
    DATA_PATH="batch-jobs/my-unique-path"
    OUTPUT_PATH="$DATASTORE_ID/paths/$DATA_PATH"
    ```

    For completeness, let's also create a data input:

    ```azurecli
    INPUT_PATH="https://azuremlexampledata.blob.core.windows.net/data/heart-disease-uci/data"
    ```

    # [Python](#tab/sdk)

    ```python
    data_path = "batch-jobs/my-unique-path"
    output = Output(type=AssetTypes.URI_FOLDER, path=f"{default_ds.id}/paths/{data_path})
    ```

    For completeness, let's also create a data input:

    ```python
    input="https://azuremlexampledata.blob.core.windows.net/data/heart-disease-uci/data"
    ```

    # [REST](#tab/rest)

    __Body__

    ```json
    {
        "properties": {
            "InputData": {
               "heart_dataset": {
                   "JobInputType" : "UriFolder",
                   "Uri": "https://azuremlexampledata.blob.core.windows.net/data/heart-disease-uci/data"
               }
            },
            "OutputData": {
                "score": {
                    "JobOutputType" : "UriFolder",
                    "Uri": "azureml:/subscriptions/<subscription>/resourceGroups/<resource-group/providers/Microsoft.MachineLearningServices/workspaces/<workspace>/datastores/<data-store>/paths/<data-path>"
                }
            }
        }
    }
    ```
    ---
    
    > [!NOTE]
    > See how the path `paths` is appended to the resource id of the data store to indicate that what follows is a path inside of it.

1. Run the deployment:

    # [Azure CLI](#tab/cli)
   
    Use the argument `--set` to indicate the input:

    ```azurecli
    az ml batch-endpoint invoke --name $ENDPOINT_NAME \
        --set inputs.heart_dataset.path $INPUT_PATH \
        --set outputs.score.path $OUTPUT_PATH
    ```
   
    # [Python](#tab/sdk)
   
    ```python
    job = ml_client.batch_endpoints.invoke(
       endpoint_name=endpoint.name,
       inputs={ "heart_dataset": input },
       outputs={ "score": output }
    )
    ```

    # [REST](#tab/rest)

    __Request__
    
    ```http
    POST jobs HTTP/1.1
    Host: <ENDPOINT_URI>
    Authorization: Bearer <TOKEN>
    Content-Type: application/json
    ```


## Next steps

* [Troubleshooting batch endpoints](how-to-troubleshoot-batch-endpoints.md).
* [Customize outputs in batch deployments](how-to-deploy-model-custom-output.md).
* [Invoking batch endpoints from Azure Data Factory](how-to-use-batch-azure-data-factory.md).
