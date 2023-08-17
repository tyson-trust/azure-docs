---
title: Get started in Prompt flow (preview)
titleSuffix: Azure Machine Learning
description: Learn how to use Prompt flow in Azure Machine Learning studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: ishinzhang
ms.author: yijunzhang
ms.reviewer: lagayhar
ms.date: 06/30/2023
---

# Get started with Prompt flow (preview)

This article walks you through the main user journey of using Prompt flow in Azure Machine Learning studio. You'll learn how to enable Prompt flow in your Azure Machine Learning workspace, create and develop your first prompt flow, test and evaluate it, then deploy it to production.

A quick video tutorial can be found here: [Prompt flow get started video tutorial](https://www.youtube.com/watch?v=kYqRtjDBci8).

> [!IMPORTANT]
> Prompt flow is currently in public preview. This preview is provided without a service-level agreement, and are not recommended for production workloads. Certain features might not be supported or might have constrained capabilities.
> For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## Prerequisites: Enable Prompt flow in your Azure Machine Learning workspace

> [!IMPORTANT]
> Prompt flow is **not supported** in the workspace which has data isolation enabled. The enableDataIsolation flag can only be set at the workspace creation phase and can't be updated.
>
>Prompt flow is **not supported** in the project workspace which was created with a workspace hub. The workspace hub is a private preview feature.
>
>Prompt flow is **not supported** in workspaces that enable managed VNet. Managed VNet is a private preview feature.
>
>Prompt flow is **not supported** if you secure your Azure AI services account(Azure openAI, Azure cognitive search, Azure content safety) with virtual networks. If you want to use these as connection in prompt flow please allow access from all networks.

In your Azure Machine Learning workspace, you can enable Prompt flow by turning on **Build AI solutions with Prompt flow** in the **Manage preview features** panel.

:::image type="content" source="./media/get-started-prompt-flow/preview-panel.png" alt-text="Screenshot of manage preview features highlighting build AI solutions with Prompt flow button." lightbox ="./media/get-started-prompt-flow/preview-panel.png":::

## Setup

First you need to set up connection and runtime.

### Connection

Connection helps securely store and manage secret keys or other sensitive credentials required for interacting with LLM (Large Language Models) and other external tools, for example,  Azure Content Safety.

Navigate to the Prompt flow homepage, select **Connections** tab. Connection is a shared resource to all members in the workspace. So, if you already see a connection whose provider is AzureOpenAI, you can skip this step, go to create runtime.

If you aren't already connected to AzureOpenAI, select the **Create** button then *AzureOpenAI* from the drop-down.

:::image type="content" source="./media/get-started-prompt-flow/connection-creation-entry-point.png" alt-text="Screenshot of the connections tab with create highlighted." lightbox = "./media/get-started-prompt-flow/connection-creation-entry-point.png":::

Then a right-hand panel will appear. Here, you'll need to provide the connection name, API key, API base, API type, and API version before selecting the **Save** button.

To obtain the API key, base, type, and version, you can navigate to the [chat playground](https://oai.azure.com/portal/chat) in the Azure OpenAI portal and select the **View code** button. From here, you can copy the necessary information and paste it into the connection creation panel.

:::image type="content" source="./media/get-started-prompt-flow/create-aoai-connection.png" alt-text="Screenshot of the chat playground after selecting the view code  button that displays a popup with sample code, highlighting the API key." lightbox = "./media/get-started-prompt-flow/create-aoai-connection.png":::

After inputting the required fields, select **Save** to create the runtime.

### Runtime

Runtime serves as the computing resources required for the application to run, including a Docker image that contains all necessary dependency packages. It's a must-have for flow execution. So, we suggest before starting flow authoring, you should set up your runtime.

In this article, we recommend creating a runtime from Compute Instance. If you're a subscription owner or resource group owner, you have all the permissions needed. If not, first go [ask your subscription owner or resource group owner to grant you permissions](how-to-create-manage-runtime.md#grant-sufficient-permissions-to-use-the-runtime).

Meanwhile check if you have a Compute Instance assigned to you in the workspace. If not, follow [How to create a managed compute instance](../how-to-create-compute-instance.md) to create one. A memory optimized compute is recommended.

Once you have your Compute Instance running, you can start to create a runtime. Go to **Runtime** tab, select **Create**.

We support 2 types of runtimes, for this tutorial use **Compute Instance**. Then in the runtime creation right panel, specify a name, select your running compute instance, select **Authenticate** (if you see the warning message as shown below), and use the default environment, then **Create**.

:::image type="content" source="./media/get-started-prompt-flow/create-runtime.png" alt-text="Screenshot of add compute instance runtime tab. " lightbox = "./media/get-started-prompt-flow/create-runtime.png":::

If you want to learn more about runtime type, how to customize conda packages in runtime, limitations, etc., see [how to create and manage runtime](how-to-create-manage-runtime.md).

## Create and develop your Prompt flow

In **Flows** tab of Prompt flow home page, select **Create** to create your first Prompt flow. You can create a flow by cloning the samples in the gallery.

### Clone from sample

The built-in samples are shown in the gallery.

In this guide, we'll use **Web Classification** sample to walk you through the main user journey, so select **View detail** on Web Classification tile to preview the sample.

:::image type="content" source="./media/get-started-prompt-flow/sample-in-gallery.png" alt-text="Screenshot of create from galley highlighting web classification. " lightbox = "./media/get-started-prompt-flow/sample-in-gallery.png":::

Then a preview window is popped up. You can browse the sample introduction to see if the sample is similar to your scenario. You can select Clone to clone the sample, then check the flow, test it, modify it.

### Authoring page

After selecting **Clone**, You'll enter the authoring page.

At the left, it's the flatten view, the main working area where you can author the flow, for example add a new node, edit the prompt, select the flow input data, etc.

:::image type="content" source="./media/get-started-prompt-flow/flatten-view.png" alt-text="Screenshot of web classification highlighting the main working area." lightbox = "./media/get-started-prompt-flow/flatten-view.png":::

At the right, it's the graph view for visualization only. You can zoom in, zoom out, auto layout, etc.

:::image type="content" source="./media/get-started-prompt-flow/graph-view.png" alt-text="Screenshot of web classification highlighting graph view area." lightbox = "./media/get-started-prompt-flow/graph-view.png":::

In this guide, we use **Web Classification** sample to walk you through the main user journey. Web Classification is a flow demonstrating multi-class classification with LLM. Given a URL, it will classify the URL into a web category with just a few shots, simple summarization and classification prompts. For example, given \"https://www.imdb.com/\", it will classify this URL into \"Movie\".

In the graph view, you can see how the sample flow looks like. The input is a URL to classify, then it uses a Python script to fetch text content from the URL, use LLM to summarize the text content within 100 words, then classify based on the URL and summarized text content, last use Python script to convert LLM output into a dictionary. The prepare_examples node is to feed few-shot examples to classification node's prompt.

### Select runtime

Before you start authoring, you should first select a runtime.

:::image type="content" source="./media/get-started-prompt-flow/select-a-runtime.png" alt-text="Screenshot of Web classification highlighting the runtime selection drop-down." lightbox = "./media/get-started-prompt-flow/select-a-runtime.png":::

### Flow input data

When unfolding **Inputs** section, you can create and view inputs. For Web Classification sample as shown the screenshot below, the flow input is a URL of string type.

:::image type="content" source="./media/get-started-prompt-flow/flow-input.png" alt-text="Screenshot of Web classification highlighting the inputs." lightbox = "./media/get-started-prompt-flow/flow-input.png":::

The input schema (name: url; type: string) and value are already set when cloning samples. You can change to another value manually, for example \"https://www.imdb.com/\".

### Set up LLM nodes

For each LLM node, you need to select a connection to set your LLM API keys.

:::image type="content" source="./media/get-started-prompt-flow/select-a-connection.png" alt-text="Screenshot of Web classification showing the connection drop-down." lightbox = "./media/get-started-prompt-flow/select-a-connection.png":::

For this example, the API type should be **completion**.

Then depending on the connection type you selected, you need to select a deployment  or a model. If you use AzureOpenAI connection, you need to select a deployment in drop-down (If you don't have a deployment, create one in AzureOpenAI portal by following [Create a resource and deploy a model using Azure OpenAI](../../cognitive-services/openai/how-to/create-resource.md?pivots=web-portal#deploy-a-model)). If you use OpenAI connection, you need to select a model.

We have two LLM nodes (summarize_text_content and classify_with_llm) in the flow, so you need to set up for each respectively.

### Run single node

To test and debug a single node, select the **Run** icon on node in flatten view. Run status is shown at the very top, once running completed, check output in node output section.

:::image type="content" source="./media/get-started-prompt-flow/run-single-node.png" alt-text="Screenshot of Web classification showing first you run the python node then check the output, next you run the LLM node then check its output." lightbox = "./media/get-started-prompt-flow/run-single-node.png":::

Run fetch_text_content_from_url then summarize_text_content, check if the flow can successfully fetch content from web, and summarize the web content.

The single node status is shown in the graph view as well. You can also change the flow input URL to test the node behavior for different URLs.

### Run the whole flow

To test and debug the whole flow, select the **Run** button at the right top.

:::image type="content" source="./media/get-started-prompt-flow/run-flow.png" alt-text="Screenshot of Web classification showing a whole run and highlighting the run button." lightbox = "./media/get-started-prompt-flow/run-flow.png":::

Then you can check the run status and output of each node. The node statuses are shown in the graph view as well. Similarly, you can change the flow input URL to test how the flow behaves for different URLs.

### Set and check flow output

When the flow is complicated, instead of checking outputs on each node, you can set flow output and check outputs of multiple nodes in one place. Moreover, flow output helps:

- Check bulk test results in one single table
- Define evaluation interface mapping
- Set deployment response schema

When you clone the sample, the flow outputs (category and evidence) are already set. You can select **View outputs** to check the outputs in a table.

:::image type="content" source="./media/get-started-prompt-flow/view-outputs-entry-point.png" alt-text="Screenshot of Web classification showing the view outputs button." lightbox = "./media/get-started-prompt-flow/view-outputs-entry-point.png":::

:::image type="content" source="./media/get-started-prompt-flow/view-outputs.png" alt-text="Screenshot of Web classification showing outputs tab." lightbox = "./media/get-started-prompt-flow/view-outputs.png":::

## Test and evaluation

After the flow run successfully with a single row of data, you might want to test if it performs well in large set of data, you can run a bulk test and choose some evaluation methods then check the metrics.

### Prepare data

You need to prepare test data first. We support csv and txt file for now.

Go to [GitHub](https://aka.ms/web-classification-data) to download raw file for Web Classification sample.

### Bulk test

Select **Bulk test** button, then a right panel pops up. It's a wizard that guides you to submit a bulk test and to select the evaluation method (optional).​​​​​​​

:::image type="content" source="./media/get-started-prompt-flow/bulk-test-entry-point.png" alt-text="Screenshot of Web classification showing the bulk test button." lightbox = "./media/get-started-prompt-flow/bulk-test-entry-point.png":::

You need to set a bulk test name, description, then select a runtime first.

Then select **Upload new data** to upload the data you just downloaded. After uploading the data or if your colleagues in the workspace already created a dataset, you can choose the dataset from the drop-down and preview first 50 rows.

:::image type="content" source="./media/get-started-prompt-flow/upload-new-data-bulk-test.png" alt-text="Screenshot of Bulk test and evaluate, highlighting upload new data." lightbox = "./media/get-started-prompt-flow/upload-new-data-bulk-test.png":::

The dataset selection drop down supports search and autosuggestion.

### Evaluate

Select **Next**, then you can use an evaluation method to evaluate your flow. The evaluation methods are also flows that use Python or LLM etc., to calculate metrics like accuracy, relevance score. The built-in evaluation flows and customized ones are listed in the drop-down.

:::image type="content" source="./media/get-started-prompt-flow/accuracy.png" alt-text="Screenshot of Web classification showing the bulk test and evaluate on the evaluation settings." lightbox = "./media/get-started-prompt-flow/accuracy.png":::

Since Web classification is a classification scenario, it's suitable to select the **Classification Accuracy Evaluation** to evaluate.

If you're interested in how the metrics are defined for built-in evaluation methods, you can preview the evaluation flows by selecting **View details**.

After selecting **Classification Accuracy Evaluation** as evaluation method, you can set interface mapping to map the ground truth to flow input and category to flow output.

Then select **Submit** to submit a bulk test and the selected evaluation.

### Check evaluation results

When completed, select the link, go to bulk test detail page.

:::image type="content" source="./media/get-started-prompt-flow/bulk-test-status.png" alt-text="Screenshot of Web classification showing a successful bulk run and link to detail page." lightbox = "./media/get-started-prompt-flow/bulk-test-status.png":::

Select **Refresh** until the evaluation run is completed.

:::image type="content" source="./media/get-started-prompt-flow/refresh-until-the-evaluation-run-is-completed.png" alt-text="Screenshot of Web classification bulk test detail page." lightbox = "./media//get-started-prompt-flow/refresh-until-the-evaluation-run-is-completed.png":::

Then go to the **Metrics** tab, check accuracy.

:::image type="content" source="./media/get-started-prompt-flow/check-metrics.png" alt-text="Screenshot of Web classification bulk test detail page on the metrics tab." lightbox = "./media/get-started-prompt-flow/check-metrics.png":::

To understand in which case the flow classifies incorrectly, you need to see the evaluation results for each row of data. Go to **Outputs** tab, select the evaluation run, you can see in the table below for most cases the flow classifies correctly except for few rows.

:::image type="content" source="./media/get-started-prompt-flow/check-outputs-for-each-row-of-data.png" alt-text="Screenshot of Web classification bulk test detail page on the output tab." lightbox = "./media/get-started-prompt-flow/check-outputs-for-each-row-of-data.png":::

You can adjust column width, hide/unhide columns, and export table to csv file for further investigation. 

As you might know, accuracy isn't the only metric that can evaluate a classification task, for example you can also use recall to evaluate. In this case, you can select **New evaluation**, choose other evaluation methods to evaluate.

## Deployment

After you build a flow and test it properly, you may want to deploy it as an endpoint so that you can invoke the endpoint for real-time inference.

### Configure the endpoint

When you are in the bulk test **Overview** tab, select bulk test link.

:::image type="content" source="./media/get-started-prompt-flow/bulk-test-run.png" alt-text="Screenshot of Web classification bulk test detail page highlighting the bulk test link." lightbox = "./media/get-started-prompt-flow/bulk-test-run.png":::

Then you're directed to the bulk test detail page, select **Deploy**. A wizard pops up to allow you to configure the endpoint. Specify an endpoint name, use the default settings, set connections, and select a virtual machine, select **Deploy** to start the deployment.

:::image type="content" source="./media/get-started-prompt-flow/endpoint-creation.png" alt-text="Screenshot of endpoint configuration wizard." lightbox = "./media/get-started-prompt-flow/endpoint-creation.png":::

If you're a Workspace Owner or Subscription Owner, see [Deploy a flow as a managed online endpoint for real-time inference](how-to-deploy-for-real-time-inference.md#grant-permissions-to-the-endpoint) to grant permissions to the endpoint. If not, go ask your Workspace Owner or Subscription Owner to it for you.

### Test the endpoint

It takes several minutes to deploy the endpoint. After the endpoint is deployed successfully, you can test it in the **Test** tab. 

Copy following sample input data, paste to the input box, and select **Test**, then you'll see the result predicted by your endpoint.

```json
{
  "url": "https://learn.microsoft.com/en-us/azure/ai-services/openai/"
}
```

:::image type="content" source="./media/get-started-prompt-flow/test-endpoint.png" alt-text="Screenshot of the endpoint on the test tab." lightbox = "./media/get-started-prompt-flow/test-endpoint.png":::

## Clean up resources

If you plan to continue now to how-to guides and would like to use the resources you created here, skip to [Next steps](#next-steps).

### Stop compute instance

If you're not going to use it now, stop the compute instance:

1. In the studio, in the left navigation area, select **Compute**.
1. In the top tabs, select **Compute instances**
1. Select the compute instance in the list.
1. On the top toolbar, select **Stop**.

### Delete all resources

If you don't plan to use any of the resources that you created, delete them so you don't incur any charges:

1. In the Azure portal, select **Resource groups** on the far left.
1. From the list, select the resource group that you created.
1. Select **Delete resource group**.

## Next steps

Now that you have an idea of what's involved in flow developing, testing, evaluating and deploying, learn more about the process in these tutorials:

- [Create and manage runtimes](how-to-create-manage-runtime.md)
- [Develop a standard flow](how-to-develop-a-standard-flow.md)
- [Submit bulk test and evaluate a flow](how-to-develop-a-standard-flow.md)
- [Tune prompts using variants](how-to-tune-prompts-using-variants.md)
- [Deploy a flow](how-to-deploy-for-real-time-inference.md)
