---
title: Relevance and scoring
titleSuffix: Azure Cognitive Search
description: Explains the concepts of relevance and scoring in Azure Cognitive Search, and what a developer can do to customize the scoring result.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/18/2023
---

# Relevance and scoring in Azure Cognitive Search

This article explains the relevance and the scoring algorithms used to compute search scores in Azure Cognitive Search. A relevance score is computed for each match found in a [full text search](search-lucene-query-architecture.md), where the strongest matches are assigned higher search scores. 

Relevance applies to full text search only. Filter queries, autocomplete and suggested queries, wildcard search or fuzzy search queries are not scored or ranked for relevance.

In Azure Cognitive Search, you can tune search relevance and boost search scores through these mechanisms:

+ Scoring algorithm configuration
+ Semantic ranking (in preview, described in [this article](semantic-ranking.md))
+ Scoring profiles
+ Custom scoring logic enabled through the *featuresMode* parameter

> [!NOTE]
> Matches are scored and ranked from high to low. The score is returned as "@search.score". By default, the top 50 are returned in the response, but you can use the **$top** parameter to return a smaller or larger number of items (up to 1000 in a single response), and **$skip** to get the next set of results.

## Relevance scoring

Relevance scoring refers to the computation of a search score that serves as an indicator of an item's relevance in the context of the current query. The higher the score, the more relevant the item. 

The search score is computed based on statistical properties of the string input and the query itself. Azure Cognitive Search finds documents that match on search terms (some or all, depending on [searchMode](/rest/api/searchservice/search-documents#query-parameters)), favoring documents that contain many instances of the search term. The search score goes up even higher if the term is rare across the data index, but common within the document. The basis for this approach to computing relevance is known as *TF-IDF or* term frequency-inverse document frequency.

Search scores can be repeated throughout a result set. When multiple hits have the same search score, the ordering of the same scored items is undefined and not stable. Run the query again, and you might see items shift position, especially if you are using the free service or a billable service with multiple replicas. Given two items with an identical score, there is no guarantee which one appears first.

If you want to break the tie among repeating scores, you can add an **$orderby** clause to first order by score, then order by another sortable field (for example, `$orderby=search.score() desc,Rating desc`). For more information, see [$orderby](search-query-odata-orderby.md).

> [!NOTE]
> A `@search.score = 1` indicates an un-scored or un-ranked result set. The score is uniform across all results. Un-scored results occur when the query form is fuzzy search, wildcard or regex queries, or an empty search (`search=*`, sometimes paired with filters, where the filter is the primary means for returning a match).

## Scoring algorithms in Search

Azure Cognitive Search provides the following scoring algorithms:

| Algorithm | Usage | Range |
|-----------|-------------|-------|
| BM25Similarity | Fixed algorithm on all search services created after July 2020. You can configure this algorithm, but you can't switch to an older one (classic). | Unbounded. |
|ClassicSimilarity | Present on older search services. You can [opt-in for BM25](index-ranking-similarity.md) and choose an algorithm on a per-index basis. | 0 < 1.00 |

Both BM25 and Classic are TF-IDF-like retrieval functions that use the term frequency (TF) and the inverse document frequency (IDF) as variables to calculate relevance scores for each document-query pair, which is then used for ranking results. While conceptually similar to classic, BM25 is rooted in probabilistic information retrieval that produces more intuitive matches, as measured by user research. 

BM25 offers advanced customization options, such as allowing the user to decide how the relevance score scales with the term frequency of matched terms. For more information, see [Configure the scoring  algorithm](index-ranking-similarity.md).

> [!NOTE]
> If you're using a search service that was created before July 2020, the scoring algorithm is most likely the previous default, `ClassicSimilarity`, which you can upgrade on a per-index basis. See [Enable BM25 scoring on older services](index-ranking-similarity.md#enable-bm25-scoring-on-older-services) for details.

The following video segment fast-forwards to an explanation of the generally available ranking algorithms used in Azure Cognitive Search. You can watch the full video for more background.

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=322&end=643]

## Score variation

Search scores convey general sense of relevance, reflecting the strength of match relative to other documents in the same result set. But scores aren't always consistent from one query to the next, so as you work with queries, you might notice small discrepancies in how search documents are ordered. There are several explanations for why this might occur.

| Cause | Description |
|-----------|-------------|
| Data volatility | Index content varies as you add, modify, or delete documents. Term frequencies will change as index updates are processed over time, affecting the search scores of matching documents. |
| Multiple replicas | For services using multiple replicas, queries are issued against each replica in parallel. The index statistics used to calculate a search score are calculated on a per-replica basis, with results merged and ordered in the query response. Replicas are mostly mirrors of each other, but statistics can differ due to small differences in state. For example, one replica might have deleted documents contributing to their statistics, which were merged out of other replicas. Typically, differences in per-replica statistics are more noticeable in smaller indexes. For more information about this condition, see [Concepts: search units, replicas, partitions, shards](search-capacity-planning.md#concepts-search-units-replicas-partitions-shards) in the capacity planning documentation. |
| Identical scores | If multiple documents have the same score, any one of them might appear first.  |

<a name="scoring-statistics"></a>

## Scoring statistics and sticky sessions

For scalability, Azure Cognitive Search distributes each index horizontally through a sharding process, which means that [portions of an index are physically separate](search-capacity-planning.md#concepts-search-units-replicas-partitions-shards).

By default, the score of a document is calculated based on statistical properties of the data *within a shard*. This approach is generally not a problem for a large corpus of data, and it provides better performance than having to calculate the score based on information across all shards. That said, using this performance optimization could cause two very similar documents (or even identical documents) to end up with different relevance scores if they end up in different shards.

If you prefer to compute the score based on the statistical properties across all shards, you can do so by adding *scoringStatistics=global* as a [query parameter](/rest/api/searchservice/search-documents) (or add *"scoringStatistics": "global"* as a body parameter of the [query request](/rest/api/searchservice/search-documents)).

```http
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2020-06-30
{
    "search": "<query string>",
    "scoringStatistics": "global"
}
```

Using scoringStatistics will ensure that all shards in the same replica provide the same results. That said, different replicas may be slightly different from one another as they are always getting updated with the latest changes to your index. In some scenarios, you may want your users to get more consistent results during a "query session". In such scenarios, you can provide a `sessionId` as part of your queries. The `sessionId` is a unique string that you create to refer to a unique user session.

```http
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2020-06-30
{
    "search": "<query string>",
    "sessionId": "<string>"
}
```

As long as the same `sessionId` is used, a best-effort attempt will be made to target the same replica, increasing the consistency of results your users will see. 

> [!NOTE]
> Reusing the same `sessionId` values repeatedly can interfere with the load balancing of the requests across replicas and adversely affect the performance of the search service. The value used as sessionId cannot start with a '_' character.

## Scoring profiles

You can customize the way different fields are ranked by defining a *scoring profile*. Scoring profiles provide criteria for boosting the search score of a match based on content characteristics. For example, you might want to boost matches based on their revenue potential, promote newer items, or perhaps boost items that have been in inventory too long. 

A scoring profile is part of the index definition, composed of weighted fields, functions, and parameters. For more information about defining one, see [Scoring Profiles](index-add-scoring-profiles.md).

<a name="featuresMode-param"></a>

## featuresMode parameter (preview)

[Search Documents](/rest/api/searchservice/preview-api/search-documents) requests have a new [featuresMode](/rest/api/searchservice/preview-api/search-documents#featuresmode) parameter that can provide additional detail about relevance at the field level. Whereas the `@searchScore` is calculated for the document all-up (how relevant is this document in the context of this query), through featuresMode you can get information about individual fields, as expressed in a `@search.features` structure. The structure contains all fields used in the query (either specific fields through **searchFields** in a query, or all fields attributed as **searchable** in an index). For each field, you get the following values:

+ Number of unique tokens found in the field
+ Similarity score, or a measure of how similar the content of the field is, relative to the query term
+ Term frequency, or the number of times the query term was found in the field

For a query that targets the "description" and "title" fields, a response that includes `@search.features` might look like this:

```json
"value": [
 {
    "@search.score": 5.1958685,
    "@search.features": {
        "description": {
            "uniqueTokenMatches": 1.0,
            "similarityScore": 0.29541412,
            "termFrequency" : 2
        },
        "title": {
            "uniqueTokenMatches": 3.0,
            "similarityScore": 1.75451557,
            "termFrequency" : 6
        }
```

You can consume these data points in [custom scoring solutions](https://github.com/Azure-Samples/search-ranking-tutorial) or use the information to debug search relevance problems.

## See also

+ [Scoring Profiles](index-add-scoring-profiles.md)
+ [REST API Reference](/rest/api/searchservice/)
+ [Search Documents API](/rest/api/searchservice/search-documents)
+ [Azure Cognitive Search .NET SDK](/dotnet/api/overview/azure/search)
