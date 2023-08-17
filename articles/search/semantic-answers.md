---
title: Return a semantic answer
titleSuffix: Azure Cognitive Search
description: Describes the composition of a semantic answer and how to obtain answers from a result set.

manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/14/2023
---

# Return a semantic answer in Azure Cognitive Search

> [!IMPORTANT]
> Semantic search is in public preview under [supplemental terms of use](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). It's available through the Azure portal, preview REST API, and beta SDKs. This feature is billable (see [Availability and pricing](semantic-search-overview.md#availability-and-pricing)).

When invoking [semantic ranking and captions](semantic-how-to-query-request.md), you can optionally extract content from the top-matching documents that "answers" the query directly. One or more answers can be included in the response, which you can then render on a search page to improve the user experience of your app.

In this article, learn how to request a semantic answer, unpack the response, and what content characteristics are most conducive to producing high-quality answers.

## Prerequisites

All prerequisites that apply to [semantic queries](semantic-how-to-query-request.md#prerequisites) also apply to answers, including [service tier and region](semantic-search-overview.md#availability-and-pricing).

+ Query logic must include the semantic query parameters "queryType=semantic", plus the "answers" parameter. Required parameters are discussed in this article.

+ Query strings entered by the user must be recognizable as a question (what, where, when, how).

+ Search documents in the index must contain text having the characteristics of an answer, and that text must exist in one of the fields listed in the [semantic configuration](semantic-how-to-query-request.md#2---create-a-semantic-configuration). For example, given a query "what is a hash table", if none of the fields in the semantic configuration contain passages that include "A hash table is ...", then it's unlikely an answer will be returned.

## What is a semantic answer?

A semantic answer is a substructure of a [semantic query response](semantic-how-to-query-request.md). It consists of one or more verbatim passages from a search document, formulated as an answer to a query that looks like a question. To return an answer, phrases or sentences must exist in a search document that have the language characteristics of an answer, and the query itself must be posed as a question.

Cognitive Search uses a machine reading comprehension model to pick the best answer. The model produces a set of potential answers from the available content, and when it reaches a high enough confidence level, it will propose one as an answer.

Answers are returned as an independent, top-level object in the query response payload that you can choose to render on  search pages, along side search results. Structurally, it's an array element within the response consisting of text, a document key, and a confidence score.

<a name="query-params"></a>

## Formulate a REST query for "answers"

The approach for listing fields in priority order has changed, with "semanticConfiguration" replacing "searchFields". If you're currently using "searchFields", update your code to the 2021-04-30-Preview API version and use "semanticConfiguration" instead.

### [**Semantic Configuration (recommended)**](#tab/semanticConfiguration)

To return a semantic answer, the query must have the semantic "queryType", "queryLanguage", "semanticConfiguration", and the "answers" parameters. Specifying these parameters doesn't guarantee an answer, but the request must include them for answer processing to occur.

The "semanticConfiguration" parameter is required. It's defined in a search index, and then referenced in a query, as shown below. 

```json
{
    "search": "how do clouds form",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "semanticConfiguration": "my-semantic-config",
    "answers": "extractive|count-3",
    "captions": "extractive|highlight-true",
    "count": "true"
}
```

+ A query string must not be null and should be formulated as question.

+ "queryType" must be set to "semantic.

+ "queryLanguage" must be one of the values from the [supported languages list (REST API)](/rest/api/searchservice/preview-api/search-documents#queryLanguage).

+ A "semanticConfiguration" determines which string fields provide tokens to the extraction model. The same fields that produce captions also produce answers. See [Create a semantic configuration](semantic-how-to-query-request.md#2---create-a-semantic-configuration) for details.

+ For "answers", parameter construction is `"answers": "extractive"`, where the default number of answers returned is one. You can increase the number of answers by adding a `count` as shown in the above example, up to a maximum of 10.  Whether you need more than one answer depends on the user experience of your app, and how you want to render results.

### [**searchFields**](#tab/searchFields)

To return a semantic answer, the query must have the semantic "queryType", "queryLanguage", "searchFields", and the "answers" parameter. Specifying the "answers" parameter doesn't guarantee that you'll get an answer, but the request must include this parameter if answer processing is to be invoked at all.

The "searchFields" parameter is crucial to returning a high-quality answer, both in terms of content and order (see below). 

```json
{
    "search": "how do clouds form",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "title,locations,content",
    "answers": "extractive|count-3",
    "count": "true"
}
```

+ A query string must not be null and should be formulated as question.

+ "queryType" must be set to "semantic.

+ "queryLanguage" must be one of the values from the [supported languages list (REST API)](/rest/api/searchservice/preview-api/search-documents#queryLanguage).

+ "searchFields" determines which string fields provide tokens to the extraction model. The same fields that produce captions also produce answers. See [Set searchFields](semantic-how-to-query-request.md#2b---use-searchfields-for-field-prioritization) for details. 

+ For "answers", parameter construction is `"answers": "extractive"`, where the default number of answers returned is one. You can increase the number of answers by adding a `count` as shown in the above example, up to a maximum of 10.  Whether you need more than one answer depends on the user experience of your app, and how you want to render results.

---

## Unpack an "answer" from the response

Answers are provided in the `"@search.answers"` array, which appears first in the query response. Each answer in the array will include:

+ Document key
+ Text or content of the answer, in plain text or with formatting
+ Confidence score

If an answer is indeterminate, the response will show up as `"@search.answers": []`. The answers array is followed by the value array, which is the standard response in a semantic query.

Given the query "how do clouds form", the following example illustrates an answer:

```json
{
    "@search.answers": [
        {
            "key": "4123",
            "text": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form where air is ascending (over land in this case),   but not where it is descending (over the river).",
            "highlights": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form<em> where air is ascending</em> (over land in this case),   but not where it is<em> descending</em> (over the river).",
            "score": 0.94639826
        }
    ],
    "value": [
        {
            "@search.score": 0.5479723,
            "@search.rerankerScore": 1.0321671911515296,
            "@search.captions": [
                {
                    "text": "Like all clouds, it forms when the air reaches its dew point—the temperature at which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley fog, which is common in the Pacific Northwest of North America.",
                    "highlights": "Like all<em> clouds</em>, it<em> forms</em> when the air reaches its dew point—the temperature at    which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley<em> fog</em>, which is common in the Pacific Northwest of North America."
                }
            ],
            "title": "Earth Atmosphere",
            "content": "Fog is essentially a cloud lying on the ground. Like all clouds, it forms when the air reaches its dew point—the temperature at  \n\nwhich an air mass is cool enough for its water vapor to condense into liquid droplets.\n\nThis false-color image shows valley fog, which is common in the Pacific Northwest of North America. On clear winter nights, the \n\nground and overlying air cool off rapidly, especially at high elevations. Cold air is denser than warm air, and it sinks down into the \n\nvalleys. The moist air in the valleys gets chilled to its dew point, and fog forms. If undisturbed by winds, such fog may persist for \n\ndays. The Terra satellite captured this image of foggy valleys northeast of Vancouver in February 2010.\n\n\n",
            "locations": [
                "Pacific Northwest",
                "North America",
                "Vancouver"
            ]
        }
    ]
}

```

When designing a search results page that includes answers, be sure to handle cases where answers are not found.

Within @search.answers:

+ **"key"** is the document key or ID of the match. Given a document key, you can use [Lookup Document](/rest/api/searchservice/lookup-document) API to retrieve any or all parts of the search document to include on the search page or a detail page.

+ **"text"** and **"highlights"** provide identical content, in both plain text and with highlights. 

  By default, highlights are styled as `<em>`, which you can override using the existing highlightPreTag and highlightPostTag parameters. As noted elsewhere, the substance of an answer is verbatim content from a search document. The extraction model looks for characteristics of an answer to find the appropriate content, but doesn't compose new language in the response.

+ **"score"** is a confidence score that reflects the strength of the answer. If there are multiple answers in the response, this score is used to determine the order. Top answers and top captions can be derived from different search documents, where the top answer originates from one document, and the top caption from another, but in general you will see the same documents in the top positions within each array.

Answers are followed by the **"value"** array, which always includes scores, captions, and any fields that are retrievable by default. If you specified the select parameter, the "value" array is limited to the fields that you specified. See [Configure semantic ranking](semantic-how-to-query-request.md) for details.

## Tips for producing high-quality answers

For best results, return semantic answers on a document corpus having the following characteristics:

+ The "semanticConfiguration" must include fields that offer sufficient text in which an answer is likely to be found. Fields more likely to contain answers should be listed first in "prioritizedContentFields". Only verbatim text from a document can appear as an answer.

+ Query strings must not be null (search=`*`) and the string should have the characteristics of a question, such as "what is" or "how to", as opposed to a keyword search consisting of terms or phrases in arbitrary order. If the query string doesn't appear to be a question, answer processing is skipped, even if the request specifies "answers" as a query parameter.

+ Semantic extraction and summarization have limits over how many tokens per document can be analyzed in a timely fashion. In practical terms, if you have large documents that run into hundreds of pages, try to break up the content into smaller documents first.

## Next steps

+ [Semantic search overview](semantic-search-overview.md)
+ [Semantic ranking algorithm](semantic-ranking.md)
+ [Similarity ranking algorithm](index-ranking-similarity.md)
+ [Configure semantic ranking](semantic-how-to-query-request.md)
