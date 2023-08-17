---
title: ACOS
titleSuffix: Azure Cosmos DB for NoSQL
description: An Azure Cosmos DB for NoSQL system function that returns the trigonometric arccosine of the specified angle.
author: jcodella
ms.author: jacodel
ms.reviewer: sidandrews
ms.service: cosmos-db
ms.subservice: nosql
ms.topic: reference
ms.date: 07/17/2023
ms.custom: query-reference
---

# ACOS (NoSQL query)

[!INCLUDE[NoSQL](../../includes/appliesto-nosql.md)]

Returns the trigonometric arccosine of the specified numeric value. The arccosine is the angle, in radians, whose cosine is the specified numeric expression.
  
## Syntax

```sql
ACOS(<numeric_expr>)  
```  

## Arguments

| | Description |
| --- | --- |
| **`numeric_expr`** | A numeric expression. |

## Return types

Returns a numeric expression.  

## Examples

The following example calculates the arccosine of the specified values using the function.

:::code language="sql" source="~/cosmos-db-nosql-query-samples/scripts/arccosine/query.sql" highlight="2":::

:::code language="json" source="~/cosmos-db-nosql-query-samples/scripts/arccosine/result.json":::

## Remarks

- This system function doesn't use the index.

## Next steps

- [System functions](system-functions.yml)
- [`COS`](cos.md)
