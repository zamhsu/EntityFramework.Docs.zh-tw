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
# <a name="query-tags"></a>查詢標籤

查詢標記可協助您將程式碼中的 LINQ 查詢與在記錄中所產生的 SQL 查詢產生關聯。
您可使用新的 `TagWith()` 方法，標註 LINQ 查詢：

> [!TIP]
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Tags) \(英文\)。

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#BasicQueryTag)]

此 LINQ 查詢會轉譯為下列 SQL 陳述式：

```sql
-- This is my spatial query!

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

能夠在相同的查詢上多次呼叫 `TagWith()`。
查詢標籤是累計的。
例如，假設有下列方法：

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#QueryableMethods)]

下列查詢：

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#ChainedQueryTags)]

轉譯為：

```sql
-- GetNearestPeople

-- Limit

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

其也能夠將多行字串作為查詢標籤使用。
例如︰

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#MultilineQueryTag)]

產生下列 SQL：

```sql
-- GetNearestPeople

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a>已知的限制

**查詢標籤無法參數化：** EF Core 一律會將 LINQ 查詢中的查詢標籤，作為所產生之 SQL 中所包含的字串常值。
不允許編譯的查詢將查詢標籤作為參數使用。
