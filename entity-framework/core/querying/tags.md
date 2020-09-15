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
# <a name="query-tags"></a>查詢標籤

> [!NOTE]
> 此為 EF Core 2.2 中的新功能。

此功能可協助程式碼中 LINQ 查詢與記錄檔中所擷取而產生之 SQL 查詢的相互關聯。
您可使用新的 `TagWith()` 方法，標註 LINQ 查詢：

``` csharp
  var nearestFriends =
      (from f in context.Friends.TagWith("This is my spatial query!")
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

此 LINQ 查詢會轉譯為下列 SQL 陳述式：

``` sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

能夠在相同的查詢上多次呼叫 `TagWith()`。
查詢標籤是累計的。
例如，假設有下列方法：

``` csharp
IQueryable<Friend> GetNearestFriends(Point myLocation) =>
    from f in context.Friends.TagWith("GetNearestFriends")
    orderby f.Location.Distance(myLocation) descending
    select f;

IQueryable<T> Limit<T>(IQueryable<T> source, int limit) =>
    source.TagWith("Limit").Take(limit);
```

下列查詢：

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).ToList();
```

轉譯為：

``` sql
-- GetNearestFriends

-- Limit

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

其也能夠將多行字串作為查詢標籤使用。
例如：

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).TagWith(
@"This is a multi-line
string").ToList();
```

產生下列 SQL：

``` sql
-- GetNearestFriends

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a>已知限制

**查詢標籤無法參數化：** EF Core 一律會將 LINQ 查詢中的查詢標籤，作為所產生之 SQL 中所包含的字串常值。
不允許編譯的查詢將查詢標籤作為參數使用。
