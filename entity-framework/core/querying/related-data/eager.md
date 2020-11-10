---
title: 相關資料的積極式載入-EF Core
description: 使用 Entity Framework Core 積極載入相關資料
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/eager
ms.openlocfilehash: bd9c9045c1c2707d69ee4070bea59ad8066789f3
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430101"
---
# <a name="eager-loading-of-related-data"></a><span data-ttu-id="9a55b-103">相關資料的積極式載入</span><span class="sxs-lookup"><span data-stu-id="9a55b-103">Eager Loading of Related Data</span></span>

## <a name="eager-loading"></a><span data-ttu-id="9a55b-104">積極式載入</span><span class="sxs-lookup"><span data-stu-id="9a55b-104">Eager loading</span></span>

<span data-ttu-id="9a55b-105">您可以使用 `Include` 方法來指定要包含於查詢結果中的相關資料。</span><span class="sxs-lookup"><span data-stu-id="9a55b-105">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="9a55b-106">在下列範例中，於結果中所傳回部落格的 `Posts` 屬性將會填入相關文章。</span><span class="sxs-lookup"><span data-stu-id="9a55b-106">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleInclude)]

> [!TIP]
> <span data-ttu-id="9a55b-107">Entity Framework Core 會將導覽屬性自動修正為先前已載入至內容執行個體的任何其他實體。</span><span class="sxs-lookup"><span data-stu-id="9a55b-107">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="9a55b-108">因此，即使未明確包含導覽屬性的資料，如果先前已載入部分或所有相關的實體，則仍然可能會填入該屬性。</span><span class="sxs-lookup"><span data-stu-id="9a55b-108">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

<span data-ttu-id="9a55b-109">您可以將來自多個關聯性的相關資料包含至單一查詢。</span><span class="sxs-lookup"><span data-stu-id="9a55b-109">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleIncludes)]

> [!CAUTION]
> <span data-ttu-id="9a55b-110">積極載入單一查詢中的集合導覽可能會導致效能問題。</span><span class="sxs-lookup"><span data-stu-id="9a55b-110">Eager loading a collection navigation in a single query may cause performance issues.</span></span> <span data-ttu-id="9a55b-111">如需詳細資訊，請參閱 [單一與分割查詢](xref:core/querying/single-split-queries)。</span><span class="sxs-lookup"><span data-stu-id="9a55b-111">For more information, see [Single vs. split queries](xref:core/querying/single-split-queries).</span></span>

## <a name="including-multiple-levels"></a><span data-ttu-id="9a55b-112">包括多個層級</span><span class="sxs-lookup"><span data-stu-id="9a55b-112">Including multiple levels</span></span>

<span data-ttu-id="9a55b-113">您可以使用 `ThenInclude` 方法，透過關聯性向下切入以包含多個層級的相關資料。</span><span class="sxs-lookup"><span data-stu-id="9a55b-113">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="9a55b-114">下列範例會載入所有部落格、其相關文章，以及每篇文章的作者。</span><span class="sxs-lookup"><span data-stu-id="9a55b-114">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleThenInclude)]

<span data-ttu-id="9a55b-115">您可以將多個呼叫鏈結到 `ThenInclude`，以繼續包含更深層級的相關資料。</span><span class="sxs-lookup"><span data-stu-id="9a55b-115">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleThenIncludes)]

<span data-ttu-id="9a55b-116">您可以合併所有的呼叫，以包含來自多個層級的相關資料，以及相同查詢中的多個根。</span><span class="sxs-lookup"><span data-stu-id="9a55b-116">You can combine all of the calls to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#IncludeTree)]

<span data-ttu-id="9a55b-117">您可能會想要針對所包括的其中一個實體包含多個相關實體。</span><span class="sxs-lookup"><span data-stu-id="9a55b-117">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="9a55b-118">例如，查詢 `Blogs` 時，您包括了 `Posts`，接著想要同時包含 `Posts` 的 `Author` 和 `Tags`。</span><span class="sxs-lookup"><span data-stu-id="9a55b-118">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="9a55b-119">若要同時包含這兩個，您必須指定從根目錄開始的每個 include 路徑。</span><span class="sxs-lookup"><span data-stu-id="9a55b-119">To include both, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="9a55b-120">例如，`Blog -> Posts -> Author` 與 `Blog -> Posts -> Tags`。</span><span class="sxs-lookup"><span data-stu-id="9a55b-120">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="9a55b-121">這並不表示您將會收到多餘的聯結;在大部分情況下，EF 會在產生 SQL 時合併聯結。</span><span class="sxs-lookup"><span data-stu-id="9a55b-121">It doesn't mean you'll get redundant joins; in most cases, EF will combine the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludes)]

## <a name="filtered-include"></a><span data-ttu-id="9a55b-122">篩選的包含</span><span class="sxs-lookup"><span data-stu-id="9a55b-122">Filtered include</span></span>

> [!NOTE]
> <span data-ttu-id="9a55b-123">這項功能是在 EF Core 5.0 中引進。</span><span class="sxs-lookup"><span data-stu-id="9a55b-123">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="9a55b-124">將 Include 套用至載入相關資料時，您可以將某些可列舉的作業加入至包含的集合導覽，以允許篩選和排序結果。</span><span class="sxs-lookup"><span data-stu-id="9a55b-124">When applying Include to load related data, you can add certain enumerable operations to the included collection navigation, which allows for filtering and sorting of the results.</span></span>

<span data-ttu-id="9a55b-125">支援的作業為： `Where` 、 `OrderBy` 、、、、 `OrderByDescending` `ThenBy` `ThenByDescending` `Skip` 和 `Take` 。</span><span class="sxs-lookup"><span data-stu-id="9a55b-125">Supported operations are: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take`.</span></span>

<span data-ttu-id="9a55b-126">這類作業應該套用至傳遞給 Include 方法的 lambda 集合導覽，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="9a55b-126">Such operations should be applied on the collection navigation in the lambda passed to the Include method, as shown in example below:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#FilteredInclude)]

<span data-ttu-id="9a55b-127">每個包含的流覽都只允許一組唯一的篩選作業。</span><span class="sxs-lookup"><span data-stu-id="9a55b-127">Each included navigation allows only one unique set of filter operations.</span></span> <span data-ttu-id="9a55b-128">如果針對指定的集合導覽套用了多個 Include 作業 (`blog.Posts` 在下列範例) 中，只能在其中一個篩選作業上指定篩選作業：</span><span class="sxs-lookup"><span data-stu-id="9a55b-128">In cases where multiple Include operations are applied for a given collection navigation (`blog.Posts` in the examples below), filter operations can only be specified on one of them:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered1)]

<span data-ttu-id="9a55b-129">相反地，您可以針對每個包含多次的導覽套用相同的作業：</span><span class="sxs-lookup"><span data-stu-id="9a55b-129">Instead, identical operations can be applied for each navigation that is included multiple times:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> <span data-ttu-id="9a55b-130">在追蹤查詢的情況下，篩選的結果可能會因為 [流覽修復](xref:core/querying/tracking)而非預期的結果。</span><span class="sxs-lookup"><span data-stu-id="9a55b-130">In case of tracking queries, results of Filtered Include may be unexpected due to [navigation fixup](xref:core/querying/tracking).</span></span> <span data-ttu-id="9a55b-131">先前查詢並儲存在變更追蹤器中的所有相關實體都會出現在 [篩選的包含查詢] 的結果中，即使它們不符合篩選準則的需求。</span><span class="sxs-lookup"><span data-stu-id="9a55b-131">All relevant entities that have been queried for previously and have been stored in the Change Tracker will be present in the results of Filtered Include query, even if they don't meet the requirements of the filter.</span></span> <span data-ttu-id="9a55b-132">在 `NoTracking` 這些情況下使用篩選包含時，請考慮使用查詢或重新建立 DbCoNtext。</span><span class="sxs-lookup"><span data-stu-id="9a55b-132">Consider using `NoTracking` queries or re-create the DbContext when using Filtered Include in those situations.</span></span>

<span data-ttu-id="9a55b-133">範例：</span><span class="sxs-lookup"><span data-stu-id="9a55b-133">Example:</span></span>

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

> [!NOTE]
> <span data-ttu-id="9a55b-134">如果是追蹤查詢，則會將已套用篩選的包含的導覽視為已載入。</span><span class="sxs-lookup"><span data-stu-id="9a55b-134">In case of tracking queries, the navigation on which filtered include was applied is considered to be loaded.</span></span> <span data-ttu-id="9a55b-135">這表示 EF Core 不會嘗試使用 [明確載入](xref:core/querying/related-data/explicit) 或消極式 [載入](xref:core/querying/related-data/lazy)來重新載入它的值，即使某些元素仍然可能遺失也是一樣。</span><span class="sxs-lookup"><span data-stu-id="9a55b-135">This means that EF Core will not attempt to re-load it's values using [explicit loading](xref:core/querying/related-data/explicit) or [lazy loading](xref:core/querying/related-data/lazy), even though some elements could still be missing.</span></span>

## <a name="include-on-derived-types"></a><span data-ttu-id="9a55b-136">衍生類型中的 Include</span><span class="sxs-lookup"><span data-stu-id="9a55b-136">Include on derived types</span></span>

<span data-ttu-id="9a55b-137">您可以使用和，在衍生型別上包含僅針對衍生型別定義的導覽的相關資料 `Include` `ThenInclude` 。</span><span class="sxs-lookup"><span data-stu-id="9a55b-137">You can include related data from navigation defined only on a derived type using `Include` and `ThenInclude`.</span></span>

<span data-ttu-id="9a55b-138">假設有下列模型：</span><span class="sxs-lookup"><span data-stu-id="9a55b-138">Given the following model:</span></span>

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

<span data-ttu-id="9a55b-139">身分為學生之所有人員的 `School` 導覽內容可以使用多個模式進行積極式載入：</span><span class="sxs-lookup"><span data-stu-id="9a55b-139">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

* <span data-ttu-id="9a55b-140">使用 cast</span><span class="sxs-lookup"><span data-stu-id="9a55b-140">using cast</span></span>

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* <span data-ttu-id="9a55b-141">使用 `as` 運算子</span><span class="sxs-lookup"><span data-stu-id="9a55b-141">using `as` operator</span></span>

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* <span data-ttu-id="9a55b-142">使用 `Include` 的多載，其會接受類型 `string` 的參數</span><span class="sxs-lookup"><span data-stu-id="9a55b-142">using overload of `Include` that takes parameter of type `string`</span></span>

  ```csharp
  context.People.Include("School").ToList()
  ```
