---
title: 全域查詢篩選條件 - EF Core
description: 使用全域查詢篩選器來篩選 Entity Framework Core 的結果
author: maumar
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: d28f34cd3846203675b5a03343e0211460797b0a
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023740"
---
# <a name="global-query-filters"></a>全域查詢篩選條件

全域查詢篩選準則是套用至元資料模型中實體類型的 LINQ 查詢述詞， (通常是 `OnModelCreating`) 。 查詢述詞是通常傳遞給 LINQ 查詢運算子的布林運算式 `Where` 。  EF Core 會自動將這類篩選準則套用至任何涉及這些實體類型的 LINQ 查詢。  EF Core 也會將它們套用至實體類型，使用 Include 或導覽屬性間接參考。 此功能的一些常見應用如下：

* 虛 **刪除**-實體類型會定義 `IsDeleted` 屬性。
* **多** 租使用者-實體類型會定義 `TenantId` 屬性。

## <a name="example"></a>範例

下列範例將示範如何使用全域查詢篩選器，在簡單的電子博客模型中執行多租使用者和虛刪除查詢行為。

> [!TIP]
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Querying/QueryFilters) \(英文\)。

首先，定義實體：

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Entities.cs#Entities)]

請注意 `_tenantId` 實體上的欄位宣告 `Blog` 。 此欄位將用來將每個 Blog 實例與特定租使用者建立關聯。 此外，也會定義 `IsDeleted` 實體類型上的屬性 `Post` 。 這個屬性是用來追蹤 post 實例是否已「虛刪除」。 亦即，將執行個體標示為已刪除，而不實際移除底層資料。

接下來， `OnModelCreating` 使用 API 來設定查詢篩選 `HasQueryFilter` 。

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/BloggingContext.cs#FilterConfiguration)]

傳遞至呼叫的述詞運算式 `HasQueryFilter` 現在會自動套用至這些類型的任何 LINQ 查詢。

> [!TIP]
> 請注意 DbContext 執行個體層級欄位的使用方式：`_tenantId` 用來設定目前的租用戶。 模型層級篩選將會使用正確內容執行個體 (亦即，執行查詢的執行個體) 中的值。

> [!NOTE]
> 目前無法在相同的實體上定義多個查詢篩選-只會套用最後一個篩選。 不過，您可以使用 `AND` [ `&&` c #) 中](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)的邏輯運算子 (來定義具有多個條件的單一篩選。

## <a name="use-of-navigations"></a>使用導覽

您也可以使用導覽來定義全域查詢篩選準則。 使用查詢篩選器中的 [導覽]，將會以遞迴方式套用查詢篩選。 當 EF Core 展開查詢篩選中使用的導覽時，也會套用在參考實體上定義的查詢篩選。

若要以下列方式說明這項設定查詢篩選 `OnModelCreating` ： [!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#NavigationInFilter)]

接下來，查詢所有 `Blog` 實體： [!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#QueriesNavigation)]

此查詢會產生下列 SQL，這會套用針對和實體定義的查詢篩選 `Blog` `Post` ：

```sql
SELECT [b].[BlogId], [b].[Name], [b].[Url]
FROM [Blogs] AS [b]
WHERE (
    SELECT COUNT(*)
    FROM [Posts] AS [p]
    WHERE ([p].[Title] LIKE N'%fish%') AND ([b].[BlogId] = [p].[BlogId])) > 0
```

> [!NOTE]
> 目前 EF Core 不會偵測全域查詢篩選定義中的迴圈，因此在定義時，您應該小心。 如果指定不正確，迴圈可能會在查詢轉譯期間導致無限迴圈。

## <a name="accessing-entity-with-query-filter-using-required-navigation"></a>使用必要的導覽存取具有查詢篩選的實體

> [!CAUTION]
> 使用所需的流覽來存取已定義全域查詢篩選器的實體，可能會導致非預期的結果。

必要的導覽需要相關的實體一律存在。 如果查詢篩選器會篩選出必要的相關實體，父實體將不會產生任何結果。 因此，您可能會得到比預期更少的元素。

為了說明這個問題，我們可以使用 `Blog` `Post` 上面指定的和實體，以及下列 `OnModelCreating` 方法：

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#IncorrectFilter)]

您可以使用下列資料來植入模型：

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#SeedData)]

執行兩個查詢時，可能會觀察到此問題：

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#Queries)]

在上述設定中，第一個查詢會傳回所有 6 `Post` 秒，但是第二個查詢只會傳回3個。 因為 `Include` 第二個查詢中的方法會載入相關實體，所以會發生這種不符的情況 `Blog` 。 由於和之間的 `Blog` 導覽 `Post` 是必要的，因此 EF Core 會 `INNER JOIN` 在建立查詢時使用：

```sql
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Posts] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE [b].[Url] LIKE N'%fish%'
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

使用 `INNER JOIN` 篩選出所有已被 `Post` `Blog` 全域查詢篩選器移除其相關的。

您可以使用選擇性流覽而非必要來解決此問題。
如此一來，第一個查詢會維持與之前相同，但第二個查詢現在會產生 `LEFT JOIN` 並傳回6個結果。

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#OptionalNavigation)]

替代方法是在和實體上同時指定一致的篩選 `Blog` `Post` 。
這種比對篩選準則會套用至 `Blog` 和 `Post` 。 `Post`最後會移除可能處於非預期狀態的，而且這兩個查詢都會傳回3個結果。

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#MatchingFilters)]

## <a name="disabling-filters"></a>停用篩選條件

可能會使用 <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.IgnoreQueryFilters%2A> 運算子來停用個別 LINQ 查詢的篩選條件。

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a>限制

全域查詢篩選條件具有下列限制：

* 只能針對繼承階層的根實體類型定義篩選條件。
