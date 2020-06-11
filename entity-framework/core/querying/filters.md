---
title: 全域查詢篩選條件 - EF Core
author: anpete
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: f6c59bcbab31edcbed22079a1320c060ce08c6f7
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664126"
---
# <a name="global-query-filters"></a>全域查詢篩選條件

> [!NOTE]
> 此功能是在 EF Core 2.0 中引入。

全域查詢篩選條件是中繼資料模型 (通常位於 *OnModelCreating*) 中套用到實體類型的 LINQ 查詢述詞 (布林運算式通常會傳遞到 LINQ *Where* 查詢運算子)。 這類篩選會自動套用至任何涉及這些「實體類型」(包含間接參考的「實體類型」) 的 LINQ 查詢 (例如，使用 Include 或直接導覽屬性參考)。 此功能的一些常見應用如下：

* **虛刪除**：實體類型會定義 *IsDeleted* 屬性。
* **多**租使用者-實體類型會定義*TenantId*屬性。

## <a name="example"></a>範例

下列範例示範如何使用全域查詢篩選條件，在簡單的部落格模型中實作虛刪除和多租用戶查詢行為。

> [!TIP]
> 您可以使用 GitHub 上的流覽來查看多租使用者[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters)和[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations)。 

首先，定義實體：

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Entities)]

請注意 _Blog_ 實體上 _tenantId_ 欄位的宣告。 這會用來將每個 Blog 執行個體與特定租用戶產生關聯。 此外，還定義了 _Post_ 實體類型上的 _IsDeleted_ 屬性。 這會用來追蹤是否已將 _Post_ 執行個體「虛刪除」。 亦即，將執行個體標示為已刪除，而不實際移除底層資料。

接下來，使用 `HasQueryFilter` API，在 _OnModelCreating_ 中設定查詢篩選條件。

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Configuration)]

傳遞到 _HasQueryFilter_ 呼叫的述詞運算式，現在將針對那些類型自動套用到任何 LINQ 查詢。

> [!TIP]
> 請注意 DbContext 執行個體層級欄位的使用方式：`_tenantId` 用來設定目前的租用戶。 模型層級篩選將會使用正確內容執行個體 (亦即，執行查詢的執行個體) 中的值。

> [!NOTE]
> 目前不能在相同的實體上定義多個查詢篩選準則-只會套用最後一個。 不過，您可以使用邏輯_AND_運算子（[ `&&` c # 中](https://docs.microsoft.com/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)的）定義具有多個條件的單一篩選。

## <a name="use-of-navigations"></a>導覽的使用

在定義全域查詢篩選器時，可以使用導覽。 它們會以遞迴方式套用-在查詢篩選中使用的導覽轉譯時，也會套用在參考實體上定義的查詢篩選，可能會加入更多的導覽。

> [!NOTE]
> 目前 EF Core 不會偵測全域查詢篩選定義中的迴圈，因此在定義時，您應該小心。 如果指定錯誤，這可能會在查詢轉譯期間導致無限迴圈。

## <a name="accessing-entity-with-query-filter-using-reqiured-navigation"></a>使用 reqiured 導覽來存取具有查詢篩選準則的實體

> [!CAUTION]
> 使用必要的流覽來存取已定義全域查詢篩選器的實體，可能會導致非預期的結果。 

必要的導覽會預期相關實體一律存在。 如果要求的相關實體已由查詢篩選器篩選掉，父實體最後可能會處於非預期的狀態。 這可能會導致傳回的元素數目少於預期。 

為了說明這個問題，我們可以使用 `Blog` `Post` 上面指定的和實體，以及下列_OnModelCreating_方法：

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

此模型可以使用下列資料植入：

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#SeedData)]

執行兩個查詢時，可能會發現此問題：

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#Queries)]

使用此設定時，第一個查詢會傳回所有 6 `Post` 秒，但第二個查詢只會傳回3。 發生這種情況是因為第二個查詢中的_Include_方法會載入相關的 `Blog` 實體。 因為需要和之間的導覽 `Blog` `Post` ，EF Core `INNER JOIN` 在建立查詢時使用：

```SQL
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Post] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE CHARINDEX(N'fish', [b].[Url]) > 0
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

使用 `INNER JOIN` 篩選掉所有已被 `Post` `Blog` 全域查詢篩選器移除其相關的。 

您可以使用選擇性的導覽，而不是必要的來解決此問題。 如此一來，第一個查詢會維持不變，但第二個查詢現在會產生 `LEFT JOIN` 並傳回6個結果。

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired(false);
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

替代方法是在和實體上指定一致的篩選準則 `Blog` `Post` 。
這種比對篩選準則適用于 `Blog` 和 `Post` 。 `Post`最後會移除處於非預期狀態的，而且這兩個查詢都會傳回3個結果。 

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
    modelBuilder.Entity<Post>().HasQueryFilter(p => p.Blog.Url.Contains("fish"));
}
```

## <a name="disabling-filters"></a>停用篩選條件

可能會使用 `IgnoreQueryFilters()` 運算子來停用個別 LINQ 查詢的篩選條件。

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a>限制

全域查詢篩選條件具有下列限制：

* 只能針對繼承階層的根實體類型定義篩選條件。
