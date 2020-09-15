---
title: 查詢標籤 - EF Core
description: 使用查詢標記協助識別 Entity Framework Core 所發出記錄訊息中的特定查詢
author: divega
ms.date: 11/14/2018
uid: core/querying/tags
ms.openlocfilehash: 27f757f4159a36bec324cce56d74b7860e1c3741
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070987"
---
# <a name="query-tags"></a><span data-ttu-id="79b01-103">查詢標籤</span><span class="sxs-lookup"><span data-stu-id="79b01-103">Query tags</span></span>

> [!NOTE]
> <span data-ttu-id="79b01-104">此為 EF Core 2.2 中的新功能。</span><span class="sxs-lookup"><span data-stu-id="79b01-104">This feature is new in EF Core 2.2.</span></span>

<span data-ttu-id="79b01-105">此功能可協助程式碼中 LINQ 查詢與記錄檔中所擷取而產生之 SQL 查詢的相互關聯。</span><span class="sxs-lookup"><span data-stu-id="79b01-105">This feature helps correlate LINQ queries in code with generated SQL queries captured in logs.</span></span>
<span data-ttu-id="79b01-106">您可使用新的 `TagWith()` 方法，標註 LINQ 查詢：</span><span class="sxs-lookup"><span data-stu-id="79b01-106">You annotate a LINQ query using the new `TagWith()` method:</span></span>

``` csharp
  var nearestFriends =
      (from f in context.Friends.TagWith("This is my spatial query!")
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

<span data-ttu-id="79b01-107">此 LINQ 查詢會轉譯為下列 SQL 陳述式：</span><span class="sxs-lookup"><span data-stu-id="79b01-107">This LINQ query is translated to the following SQL statement:</span></span>

``` sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="79b01-108">能夠在相同的查詢上多次呼叫 `TagWith()`。</span><span class="sxs-lookup"><span data-stu-id="79b01-108">It's possible to call `TagWith()` many times on the same query.</span></span>
<span data-ttu-id="79b01-109">查詢標籤是累計的。</span><span class="sxs-lookup"><span data-stu-id="79b01-109">Query tags are cumulative.</span></span>
<span data-ttu-id="79b01-110">例如，假設有下列方法：</span><span class="sxs-lookup"><span data-stu-id="79b01-110">For example, given the following methods:</span></span>

``` csharp
IQueryable<Friend> GetNearestFriends(Point myLocation) =>
    from f in context.Friends.TagWith("GetNearestFriends")
    orderby f.Location.Distance(myLocation) descending
    select f;

IQueryable<T> Limit<T>(IQueryable<T> source, int limit) =>
    source.TagWith("Limit").Take(limit);
```

<span data-ttu-id="79b01-111">下列查詢：</span><span class="sxs-lookup"><span data-stu-id="79b01-111">The following query:</span></span>

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).ToList();
```

<span data-ttu-id="79b01-112">轉譯為：</span><span class="sxs-lookup"><span data-stu-id="79b01-112">Translates to:</span></span>

``` sql
-- GetNearestFriends

-- Limit

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="79b01-113">其也能夠將多行字串作為查詢標籤使用。</span><span class="sxs-lookup"><span data-stu-id="79b01-113">It's also possible to use multi-line strings as query tags.</span></span>
<span data-ttu-id="79b01-114">例如：</span><span class="sxs-lookup"><span data-stu-id="79b01-114">For example:</span></span>

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).TagWith(
@"This is a multi-line
string").ToList();
```

<span data-ttu-id="79b01-115">產生下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="79b01-115">Produces the following SQL:</span></span>

``` sql
-- GetNearestFriends

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a><span data-ttu-id="79b01-116">已知限制</span><span class="sxs-lookup"><span data-stu-id="79b01-116">Known limitations</span></span>

<span data-ttu-id="79b01-117">**查詢標籤無法參數化：** EF Core 一律會將 LINQ 查詢中的查詢標籤，作為所產生之 SQL 中所包含的字串常值。</span><span class="sxs-lookup"><span data-stu-id="79b01-117">**Query tags aren't parameterizable:** EF Core always treats query tags in the LINQ query as string literals that are included in the generated SQL.</span></span>
<span data-ttu-id="79b01-118">不允許編譯的查詢將查詢標籤作為參數使用。</span><span class="sxs-lookup"><span data-stu-id="79b01-118">Compiled queries that take query tags as parameters aren't allowed.</span></span>
