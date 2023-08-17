---
title: Azure Stream Analytics - Writing to Delta Lake table (Public Preview)

description: This article describes how to write data to a delta lake table stored in Azure Data Lake Storage Gen2.
author: an-emma    
ms.author: raan
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/12/2022
ms.custom: seodec18, ignite-2022, build-2023
---

# Azure Stream Analytics - write to Delta Lake table 


Delta Lake is an open format that brings reliability, quality and performance to data lakes. Azure Stream Analytics allows you to directly write streaming data to your delta lake tables without writing a single line of code.

A stream analytics job can be configured to write through a native delta lake output connector, either to a new or a pre-created Delta table in an Azure Data Lake Storage Gen2 account. This connector is optimized for high-speed ingestion to delta tables in append mode and also provides exactly once semantics, which guarantees that no data is lost or duplicated. Ingesting real-time data streams from Azure Event Hubs into Delta tables allows you to perform ad-hoc interactive or batch analytics.  

## Delta Lake configuration


To write data in Delta Lake, you need to connect to an Azure Data Lake Storage Gen2 account. The below table lists the properties related to Delta Lake configuration.

|Property Name  |Description  |
|----------|-----------|
|Event Serialization Format|Serialization format for output data. JSON, CSV, AVRO, Parquet are supported. Delta Lake is listed as an option here. The data will be in Parquet format if Delta Lake is selected.  |
|Delta path name| The path that is used to write your delta lake table within the specified container. It includes the table name. More details in the section below |
|Partition Column |Optional. The {field} name from your output data to partition. Only one partition column is supported. The column's value must be of string type |  

To see the full list of ADLS Gen2 configuration, see [ALDS Gen2 Overview](blob-storage-azure-data-lake-gen2-output.md).

### Delta Path name


The Delta Path Name is used to specify the location and name of your Delta Lake table stored in Azure Data Lake Storage Gen2.

You can choose to use one or more path segments to define the path to the delta table and the delta table name. A path segment is the string between consecutive delimiter characters (for example, the forward slash `/`) that corresponds to the name of a virtual directory.

The segment name is alphanumeric and can include spaces, hyphens, and underscores. The last path segment will be used as the table name.

Restrictions on Delta Path name include the following ones:

- Field names aren't case-sensitive. For example, the service can't differentiate between column "ID" and "id".
- No dynamic {field} name is allowed. For example, {ID} will be treated as text {ID}.
- The number of path segments comprising the name can't exceed 254.

### Examples

Examples for Delta path name:

- Example 1: WestUS/CA/factory1/device-table
- Example 2: Test/demo
- Example 3: mytable

Example output files:

1. Under the chosen container, directory path would be `WestEurope/CA/factory1`, delta table folder name would be **device-table**.
2. Under the chosen container, directory path would be `Test`, delta table folder name would be **demo**.
3. Under the chosen container, delta table folder name would be **mytable**.

## Writing to the table

To create a new Delta Lake table, you need to specify a Delta Path Name that doesn't lead to any existing tables. If there's already a Delta Lake table existing with the same name and in the location specified by the Delta Path name, by default, Azure Stream Analytics writes new records to the existing table.

### Exactly once delivery


The transaction log enables Delta Lake to guarantee exactly once processing. Azure Stream Analytics also provides exactly once delivery when output data to Azure Data Lake Storage Gen2 during one single job run.

### Schema enforcement


Schema enforcement means that all new writes to a table are enforced to be compatible with the target table's schema at write time, to ensure data quality.

All records of output data are projected to the schema of the existing table. If the output is being written to a new delta table, the table schema will be created with the first record. If the incoming data has one extra column compared to the existing table schema, it will be written in the table without the extra column. If the incoming data is missing one column compared to the existing table schema, it will be written in the table with the column being null.


At the failure of schema conversion, the job behavior will follow the [output data error handing policy](stream-analytics-output-error-policy.md) configured at the job level.

## Limitations

- Dynamic partition key isn't supported.
- Writing to Delta lake is append only.
- Schema checking in query testing isn't available.
- Checkpoints for delta lake aren't taken by Stream Analytics.

## Next steps

* [Create a Stream Analytics job writing to Delta Lake Table in ADLS Gen2](write-to-delta-lake.md)
