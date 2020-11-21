---
title: 相關資料的積極式載入-EF Core
description: 使用 Entity Framework Core 積極載入相關資料
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/eager
ms.openlocfilehash: 66956fcd85bb21a08c69fa93b93c12382bbfc8eb
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003571"
---
# <a name="eager-loading-of-related-data"></a>相關資料的積極式載入

## <a name="eager-loading"></a>積極式載入

您可以使用 `Include` 方法來指定要包含於查詢結果中的相關資料。 在下列範例中，於結果中所傳回部落格的 `Posts` 屬性將會填入相關文章。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleInclude)]

> [!TIP]
> Entity Framework Core 會將導覽屬性自動修正為先前已載入至內容執行個體的任何其他實體。 因此，即使未明確包含導覽屬性的資料，如果先前已載入部分或所有相關的實體，則仍然可能會填入該屬性。

您可以將來自多個關聯性的相關資料包含至單一查詢。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleIncludes)]

> [!CAUTION]
> 積極載入單一查詢中的集合導覽可能會導致效能問題。 如需詳細資訊，請參閱 [單一與分割查詢](xref:core/querying/single-split-queries)。

## <a name="including-multiple-levels"></a>包括多個層級

您可以使用 `ThenInclude` 方法，透過關聯性向下切入以包含多個層級的相關資料。 下列範例會載入所有部落格、其相關文章，以及每篇文章的作者。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleThenInclude)]

您可以將多個呼叫鏈結到 `ThenInclude`，以繼續包含更深層級的相關資料。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleThenIncludes)]

您可以合併所有的呼叫，以包含來自多個層級的相關資料，以及相同查詢中的多個根。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#IncludeTree)]

您可能會想要針對所包括的其中一個實體包含多個相關實體。 例如，查詢 `Blogs` 時，您包括了 `Posts`，接著想要同時包含 `Posts` 的 `Author` 和 `Tags`。 若要同時包含這兩個，您必須指定從根目錄開始的每個 include 路徑。 例如，`Blog -> Posts -> Author` 與 `Blog -> Posts -> Tags`。 這並不表示您將會收到多餘的聯結;在大部分情況下，EF 會在產生 SQL 時合併聯結。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludes)]

> [!TIP]
> 您也可以使用單一方法來載入多個導覽 `Include` 。 這可能適用于所有參考的導覽「鏈」，或是以單一集合結尾。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#IncludeMultipleNavigationsWithSingleInclude)]

## <a name="filtered-include"></a>篩選的包含

> [!NOTE]
> 這項功能是在 EF Core 5.0 中引進。

將 Include 套用至載入相關資料時，您可以將某些可列舉的作業加入至包含的集合導覽，以允許篩選和排序結果。

支援的作業為： `Where` 、 `OrderBy` 、、、、 `OrderByDescending` `ThenBy` `ThenByDescending` `Skip` 和 `Take` 。

這類作業應該套用至傳遞給 Include 方法的 lambda 集合導覽，如下列範例所示：

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#FilteredInclude)]

每個包含的流覽都只允許一組唯一的篩選作業。 如果針對指定的集合導覽套用了多個 Include 作業 (`blog.Posts` 在下列範例) 中，只能在其中一個篩選作業上指定篩選作業：

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered1)]

相反地，您可以針對每個包含多次的導覽套用相同的作業：

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> 在追蹤查詢的情況下，篩選的結果可能會因為 [流覽修復](xref:core/querying/tracking)而非預期的結果。 先前查詢並儲存在變更追蹤器中的所有相關實體都會出現在 [篩選的包含查詢] 的結果中，即使它們不符合篩選準則的需求。 在 `NoTracking` 這些情況下使用篩選包含時，請考慮使用查詢或重新建立 DbCoNtext。

範例：

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

> [!NOTE]
> 如果是追蹤查詢，則會將已套用篩選的包含的導覽視為已載入。 這表示 EF Core 不會嘗試使用 [明確載入](xref:core/querying/related-data/explicit) 或消極式 [載入](xref:core/querying/related-data/lazy)來重新載入它的值，即使某些元素仍然可能遺失也是一樣。

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
