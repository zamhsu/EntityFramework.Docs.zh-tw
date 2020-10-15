---
title: 查詢標籤 - EF Core
description: 使用查詢標記協助識別 Entity Framework Core 所發出記錄訊息中的特定查詢
author: smitpatel
ms.date: 11/14/2018
uid: core/querying/tags
ms.openlocfilehash: f7cd3558682b1c19e03fc6d04957c7112e870734
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065728"
---
# <a name="query-tags"></a><span data-ttu-id="6e682-103">查詢標籤</span><span class="sxs-lookup"><span data-stu-id="6e682-103">Query tags</span></span>

<span data-ttu-id="6e682-104">查詢標記可協助您將程式碼中的 LINQ 查詢與在記錄中所產生的 SQL 查詢產生關聯。</span><span class="sxs-lookup"><span data-stu-id="6e682-104">Query tags help correlate LINQ queries in code with generated SQL queries captured in logs.</span></span>
<span data-ttu-id="6e682-105">您可使用新的 `TagWith()` 方法，標註 LINQ 查詢：</span><span class="sxs-lookup"><span data-stu-id="6e682-105">You annotate a LINQ query using the new `TagWith()` method:</span></span>

> [!TIP]
> <span data-ttu-id="6e682-106">您可以在 GitHub 上檢視此文章的[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Tags) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="6e682-106">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Tags) on GitHub.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#BasicQueryTag)]

<span data-ttu-id="6e682-107">此 LINQ 查詢會轉譯為下列 SQL 陳述式：</span><span class="sxs-lookup"><span data-stu-id="6e682-107">This LINQ query is translated to the following SQL statement:</span></span>

```sql
-- This is my spatial query!

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="6e682-108">能夠在相同的查詢上多次呼叫 `TagWith()`。</span><span class="sxs-lookup"><span data-stu-id="6e682-108">It's possible to call `TagWith()` many times on the same query.</span></span>
<span data-ttu-id="6e682-109">查詢標籤是累計的。</span><span class="sxs-lookup"><span data-stu-id="6e682-109">Query tags are cumulative.</span></span>
<span data-ttu-id="6e682-110">例如，假設有下列方法：</span><span class="sxs-lookup"><span data-stu-id="6e682-110">For example, given the following methods:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#QueryableMethods)]

<span data-ttu-id="6e682-111">下列查詢：</span><span class="sxs-lookup"><span data-stu-id="6e682-111">The following query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#ChainedQueryTags)]

<span data-ttu-id="6e682-112">轉譯為：</span><span class="sxs-lookup"><span data-stu-id="6e682-112">Translates to:</span></span>

```sql
-- GetNearestPeople

-- Limit

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="6e682-113">其也能夠將多行字串作為查詢標籤使用。</span><span class="sxs-lookup"><span data-stu-id="6e682-113">It's also possible to use multi-line strings as query tags.</span></span>
<span data-ttu-id="6e682-114">例如︰</span><span class="sxs-lookup"><span data-stu-id="6e682-114">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#MultilineQueryTag)]

<span data-ttu-id="6e682-115">產生下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="6e682-115">Produces the following SQL:</span></span>

```sql
-- GetNearestPeople

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a><span data-ttu-id="6e682-116">已知的限制</span><span class="sxs-lookup"><span data-stu-id="6e682-116">Known limitations</span></span>

<span data-ttu-id="6e682-117">**查詢標籤無法參數化：** EF Core 一律會將 LINQ 查詢中的查詢標籤，作為所產生之 SQL 中所包含的字串常值。</span><span class="sxs-lookup"><span data-stu-id="6e682-117">**Query tags aren't parameterizable:** EF Core always treats query tags in the LINQ query as string literals that are included in the generated SQL.</span></span>
<span data-ttu-id="6e682-118">不允許編譯的查詢將查詢標籤作為參數使用。</span><span class="sxs-lookup"><span data-stu-id="6e682-118">Compiled queries that take query tags as parameters aren't allowed.</span></span>
