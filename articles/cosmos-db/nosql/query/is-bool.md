---
title: IS_BOOL
titleSuffix: Azure Cosmos DB for NoSQL
description: An Azure Cosmos DB for NoSQL system function that returns a boolean indicating whether an expression is a boolean.
author: jcodella
ms.author: jacodel
ms.reviewer: sidandrews
ms.service: cosmos-db
ms.subservice: nosql
ms.topic: reference
ms.date: 07/20/2023
ms.custom: query-reference
---

# IS_BOOL (NoSQL query)

[!INCLUDE[NoSQL](../../includes/appliesto-nosql.md)]

Returns a boolean value indicating if the type of the specified expression is a boolean.  
  
## Syntax
  
```sql
IS_BOOL(<expr>)  
```  
  
## Arguments

| | Description |
| --- | --- |
| **`expr`** | Any expression. |
  
## Return types
  
Returns a boolean expression.  
  
## Examples
  
The following example checks objects of various types using the function.

:::code language="sql" source="~/cosmos-db-nosql-query-samples/scripts/is-bool/query.sql" highlight="2-10":::

:::code language="json" source="~/cosmos-db-nosql-query-samples/scripts/is-bool/result.json":::

## Remarks

- This function benefits from a [range index](../../index-policy.md#includeexclude-strategy).

## Next steps

- [System functions](system-functions.yml)
- [`IS_NUMBER`](is-number.md)
