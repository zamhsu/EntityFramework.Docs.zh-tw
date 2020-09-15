---
title: 相關資料的積極式載入-EF Core
description: 使用 Entity Framework Core 積極載入相關資料
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/eager
ms.openlocfilehash: f7e33895293e1d7b8b32a135d60b58dd1f050621
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078916"
---
# <a name="eager-loading-of-related-data"></a>相關資料的積極式載入

## <a name="eager-loading"></a>積極式載入

您可以使用 `Include` 方法來指定要包含於查詢結果中的相關資料。 在下列範例中，於結果中所傳回部落格的 `Posts` 屬性將會填入相關文章。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]
> Entity Framework Core 會將導覽屬性自動修正為先前已載入至內容執行個體的任何其他實體。 因此，即使未明確包含導覽屬性的資料，如果先前已載入部分或所有相關的實體，則仍然可能會填入該屬性。

您可以將來自多個關聯性的相關資料包含至單一查詢。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleIncludes)]

## <a name="including-multiple-levels"></a>包括多個層級

您可以使用 `ThenInclude` 方法，透過關聯性向下切入以包含多個層級的相關資料。 下列範例會載入所有部落格、其相關文章，以及每篇文章的作者。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#SingleThenInclude)]

您可以將多個呼叫鏈結到 `ThenInclude`，以繼續包含更深層級的相關資料。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

您可以合併所有的呼叫，以包含來自多個層級的相關資料，以及相同查詢中的多個根。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#IncludeTree)]

您可能會想要針對所包括的其中一個實體包含多個相關實體。 例如，查詢 `Blogs` 時，您包括了 `Posts`，接著想要同時包含 `Posts` 的 `Author` 和 `Tags`。 若要同時包含這兩個，您必須指定從根目錄開始的每個 include 路徑。 例如，`Blog -> Posts -> Author` 與 `Blog -> Posts -> Tags`。 這並不表示您將會收到多餘的聯結;在大部分情況下，EF 會在產生 SQL 時合併聯結。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

## <a name="single-and-split-queries"></a>單一和分割查詢

### <a name="single-queries"></a>單一查詢

在關係資料庫中，所有相關的實體預設都是藉由引進聯結來載入：

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

如果一般的 blog 有多個相關的文章，這些貼文的資料列將會複製 blog 的資訊，進而導致所謂的「笛卡兒爆炸」問題。 載入更多一對多關聯性時，重複的資料量可能會成長，而且會對應用程式的效能造成不良影響。 根據預設，如果偵測到載入集合的查詢可能會造成效能問題，EF Core 會發出警告。

### <a name="split-queries"></a>分割查詢

> [!NOTE]
> 這項功能是在 EF Core 5.0 中引進。

EF 可讓您指定應該將指定的 LINQ 查詢 *分割* 成多個 SQL 查詢。 分割查詢除了聯結之外，還會針對每個包含的一對多導覽執行額外的 SQL 查詢：

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs?name=AsSplitQuery&highlight=5)]

它會產生下列 SQL：

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
ORDER BY [b].[BlogId]

SELECT [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title], [b].[BlogId]
FROM [Blogs] AS [b]
INNER JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId]
```

> [!NOTE]
> 一對多相關實體一律會透過相同查詢中的聯結載入，因為這不會影響效能。

### <a name="enabling-split-queries-globally"></a>全域啟用分割查詢

您也可以將分割查詢設定為應用程式內容的預設值：

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

當分割查詢設定為預設值時，仍然可以將特定查詢設定為以單一查詢的形式執行：

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs?name=AsSplitQuery&highlight=5)]

如果未明確指定查詢分割模式，則全域或不是在查詢和 EF Core 會偵測到單一查詢會載入多個集合，併發出警告以吸引出可能產生的效能問題。 將查詢模式設定為 SingleQuery 會導致不產生警告。

### <a name="characteristics-of-split-queries"></a>分割查詢的特性

雖然分割查詢可避免與聯結和笛卡兒爆炸相關的效能問題，但它也有一些缺點：

* 雖然大部分資料庫保證單一查詢的資料一致性，但多個查詢沒有這類保證存在。 如果在執行查詢時同時更新資料庫，則產生的資料可能不一致。 您可以藉由將查詢包裝在可序列化或快照集交易中來加以減輕，雖然這樣做可能會產生自己的效能問題。 如需詳細資訊，請參閱資料庫檔案。
* 每個查詢目前暗示對您資料庫的額外網路往返。 多個網路往返可能會降低效能，特別是在資料庫的延遲很高 (例如雲端服務) 的情況下。
* 雖然某些資料庫允許同時使用多個查詢的結果 (使用 MARS 的 SQL Server，Sqlite) 大部分只允許在任何給定的時間點啟用單一查詢。 因此，必須先在應用程式的記憶體中緩衝處理舊版查詢的所有結果，然後再執行較新的查詢，這會導致記憶體需求增加。

可惜的是，載入適用于所有案例的相關實體沒有一個策略。 請仔細考慮單一和分割查詢的優缺點，然後選取符合您需求的查詢。

## <a name="filtered-include"></a>篩選的包含

> [!NOTE]
> 這項功能是在 EF Core 5.0 中引進。

將 Include 套用至載入相關資料時，您可以在包含的集合導覽上套用某些可列舉的作業，以篩選和排序結果。

支援的作業為： `Where` 、 `OrderBy` 、、、、 `OrderByDescending` `ThenBy` `ThenByDescending` `Skip` 和 `Take` 。

這類作業應該套用至傳遞給 Include 方法的 lambda 集合導覽，如下列範例所示：

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#FilteredInclude)]

每個包含的流覽都只允許一組唯一的篩選作業。 如果針對指定的集合導覽套用了多個 Include 作業 (`blog.Posts` 在下列範例) 中，只能在其中一個篩選作業上指定篩選作業：

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered1)]

相反地，您可以針對每個包含多次的導覽套用相同的作業：

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> 在追蹤查詢的情況下，篩選的結果可能會因為 [流覽修復](xref:core/querying/tracking)而非預期的結果。 先前查詢並儲存在變更追蹤器中的所有相關實體都會出現在 [篩選的包含查詢] 的結果中，即使它們不符合篩選準則的需求。 在 `NoTracking` 這些情況下使用篩選包含時，請考慮使用查詢或重新建立 DbCoNtext。

範例：

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

## <a name="include-on-derived-types"></a>衍生類型中的 Include

您可以使用和，在衍生型別上包含僅針對衍生型別定義的導覽的相關資料 `Include` `ThenInclude` 。

假設有下列模型：

```csharp
public class SchoolContext : DbContext
{
    public DbSet<Person> People { get; set; }
    public DbSet<School> Schools { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<School>().HasMany(s => s.Students).WithOne(s => s.School);
    }
}

public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Student : Person
{
    public School School { get; set; }
}

public class School
{
    public int Id { get; set; }
    public string Name { get; set; }

    public List<Student> Students { get; set; }
}
```

身分為學生之所有人員的 `School` 導覽內容可以使用多個模式進行積極式載入：

* 使用 cast

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* 使用 `as` 運算子

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* 使用 `Include` 的多載，其會接受類型 `string` 的參數

  ```csharp
  context.People.Include("School").ToList()
  ```
  