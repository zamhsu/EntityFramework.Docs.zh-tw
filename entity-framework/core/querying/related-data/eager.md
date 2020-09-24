---
title: 相關資料的積極式載入-EF Core
description: 使用 Entity Framework Core 積極載入相關資料
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/eager
ms.openlocfilehash: 5ac15a85b28f21588639f34cbaa9ef76f366f7b5
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2020
ms.locfileid: "91210463"
---
# <a name="eager-loading-of-related-data"></a><span data-ttu-id="07e50-103">相關資料的積極式載入</span><span class="sxs-lookup"><span data-stu-id="07e50-103">Eager Loading of Related Data</span></span>

## <a name="eager-loading"></a><span data-ttu-id="07e50-104">積極式載入</span><span class="sxs-lookup"><span data-stu-id="07e50-104">Eager loading</span></span>

<span data-ttu-id="07e50-105">您可以使用 `Include` 方法來指定要包含於查詢結果中的相關資料。</span><span class="sxs-lookup"><span data-stu-id="07e50-105">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="07e50-106">在下列範例中，於結果中所傳回部落格的 `Posts` 屬性將會填入相關文章。</span><span class="sxs-lookup"><span data-stu-id="07e50-106">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]
> <span data-ttu-id="07e50-107">Entity Framework Core 會將導覽屬性自動修正為先前已載入至內容執行個體的任何其他實體。</span><span class="sxs-lookup"><span data-stu-id="07e50-107">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="07e50-108">因此，即使未明確包含導覽屬性的資料，如果先前已載入部分或所有相關的實體，則仍然可能會填入該屬性。</span><span class="sxs-lookup"><span data-stu-id="07e50-108">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

<span data-ttu-id="07e50-109">您可以將來自多個關聯性的相關資料包含至單一查詢。</span><span class="sxs-lookup"><span data-stu-id="07e50-109">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleIncludes)]

## <a name="including-multiple-levels"></a><span data-ttu-id="07e50-110">包括多個層級</span><span class="sxs-lookup"><span data-stu-id="07e50-110">Including multiple levels</span></span>

<span data-ttu-id="07e50-111">您可以使用 `ThenInclude` 方法，透過關聯性向下切入以包含多個層級的相關資料。</span><span class="sxs-lookup"><span data-stu-id="07e50-111">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="07e50-112">下列範例會載入所有部落格、其相關文章，以及每篇文章的作者。</span><span class="sxs-lookup"><span data-stu-id="07e50-112">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#SingleThenInclude)]

<span data-ttu-id="07e50-113">您可以將多個呼叫鏈結到 `ThenInclude`，以繼續包含更深層級的相關資料。</span><span class="sxs-lookup"><span data-stu-id="07e50-113">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

<span data-ttu-id="07e50-114">您可以合併所有的呼叫，以包含來自多個層級的相關資料，以及相同查詢中的多個根。</span><span class="sxs-lookup"><span data-stu-id="07e50-114">You can combine all of the calls to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#IncludeTree)]

<span data-ttu-id="07e50-115">您可能會想要針對所包括的其中一個實體包含多個相關實體。</span><span class="sxs-lookup"><span data-stu-id="07e50-115">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="07e50-116">例如，查詢 `Blogs` 時，您包括了 `Posts`，接著想要同時包含 `Posts` 的 `Author` 和 `Tags`。</span><span class="sxs-lookup"><span data-stu-id="07e50-116">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="07e50-117">若要同時包含這兩個，您必須指定從根目錄開始的每個 include 路徑。</span><span class="sxs-lookup"><span data-stu-id="07e50-117">To include both, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="07e50-118">例如，`Blog -> Posts -> Author` 與 `Blog -> Posts -> Tags`。</span><span class="sxs-lookup"><span data-stu-id="07e50-118">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="07e50-119">這並不表示您將會收到多餘的聯結;在大部分情況下，EF 會在產生 SQL 時合併聯結。</span><span class="sxs-lookup"><span data-stu-id="07e50-119">It doesn't mean you'll get redundant joins; in most cases, EF will combine the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

## <a name="single-and-split-queries"></a><span data-ttu-id="07e50-120">單一和分割查詢</span><span class="sxs-lookup"><span data-stu-id="07e50-120">Single and split queries</span></span>

### <a name="single-queries"></a><span data-ttu-id="07e50-121">單一查詢</span><span class="sxs-lookup"><span data-stu-id="07e50-121">Single queries</span></span>

<span data-ttu-id="07e50-122">在關係資料庫中，所有相關的實體預設都是藉由引進聯結來載入：</span><span class="sxs-lookup"><span data-stu-id="07e50-122">In relational databases, all related entities are by default loaded by introducing JOINs:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

<span data-ttu-id="07e50-123">如果一般的 blog 有多個相關的文章，這些貼文的資料列將會複製 blog 的資訊，進而導致所謂的「笛卡兒爆炸」問題。</span><span class="sxs-lookup"><span data-stu-id="07e50-123">If a typical blog has multiple related posts, rows for these posts will duplicate the blog's information, leading to the so-called "cartesian explosion" problem.</span></span> <span data-ttu-id="07e50-124">載入更多一對多關聯性時，重複的資料量可能會成長，而且會對應用程式的效能造成不良影響。</span><span class="sxs-lookup"><span data-stu-id="07e50-124">As more one-to-many relationships are loaded, the amount of duplicated data may grow and adversely affect the performance of your application.</span></span> <span data-ttu-id="07e50-125">根據預設，如果偵測到載入集合的查詢可能會造成效能問題，EF Core 會發出警告。</span><span class="sxs-lookup"><span data-stu-id="07e50-125">By default, EF Core emits a warning if it detects queries loading collection includes that may cause performance issues.</span></span>

### <a name="split-queries"></a><span data-ttu-id="07e50-126">分割查詢</span><span class="sxs-lookup"><span data-stu-id="07e50-126">Split queries</span></span>

> [!NOTE]
> <span data-ttu-id="07e50-127">這項功能是在 EF Core 5.0 中引進。</span><span class="sxs-lookup"><span data-stu-id="07e50-127">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="07e50-128">EF 可讓您指定應該將指定的 LINQ 查詢 *分割* 成多個 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="07e50-128">EF allows you to specify that a given LINQ query should be *split* into multiple SQL queries.</span></span> <span data-ttu-id="07e50-129">分割查詢除了聯結之外，還會針對每個包含的一對多導覽執行額外的 SQL 查詢：</span><span class="sxs-lookup"><span data-stu-id="07e50-129">Instead of JOINs, split queries perform an additional SQL query for each included one-to-many navigation:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs?name=AsSplitQuery&highlight=5)]

<span data-ttu-id="07e50-130">它會產生下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="07e50-130">It will produce the following SQL:</span></span>

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
> <span data-ttu-id="07e50-131">一對多相關實體一律會透過相同查詢中的聯結載入，因為這不會影響效能。</span><span class="sxs-lookup"><span data-stu-id="07e50-131">One-to-one related entities are always loaded via JOINs in the same query, as this has no performance impact.</span></span>

### <a name="enabling-split-queries-globally"></a><span data-ttu-id="07e50-132">全域啟用分割查詢</span><span class="sxs-lookup"><span data-stu-id="07e50-132">Enabling split queries globally</span></span>

<span data-ttu-id="07e50-133">您也可以將分割查詢設定為應用程式內容的預設值：</span><span class="sxs-lookup"><span data-stu-id="07e50-133">You can also configure split queries as the default for your application's context:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

<span data-ttu-id="07e50-134">當分割查詢設定為預設值時，仍然可以將特定查詢設定為以單一查詢的形式執行：</span><span class="sxs-lookup"><span data-stu-id="07e50-134">When split queries are configured as the default, it is still possible to configure specific queries to execute as single queries:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs?name=AsSingleQuery&highlight=5)]

<span data-ttu-id="07e50-135">如果未明確指定查詢分割模式，則全域或不是在查詢和 EF Core 會偵測到單一查詢會載入多個集合，併發出警告以吸引出可能產生的效能問題。</span><span class="sxs-lookup"><span data-stu-id="07e50-135">If the query splitting mode isn't explicitly specified - neither globally nor on the query - and EF Core detects that a single query loads multiple collection includes, a warning is emitted to draw attention to the potential resulting performance issues.</span></span> <span data-ttu-id="07e50-136">將查詢模式設定為 SingleQuery 會導致不產生警告。</span><span class="sxs-lookup"><span data-stu-id="07e50-136">Setting the query mode to SingleQuery will cause the warning not to be generated.</span></span>

### <a name="characteristics-of-split-queries"></a><span data-ttu-id="07e50-137">分割查詢的特性</span><span class="sxs-lookup"><span data-stu-id="07e50-137">Characteristics of split queries</span></span>

<span data-ttu-id="07e50-138">雖然分割查詢可避免與聯結和笛卡兒爆炸相關的效能問題，但它也有一些缺點：</span><span class="sxs-lookup"><span data-stu-id="07e50-138">While split query avoids the performance issues associated with JOINs and cartesian explosion, it also has some drawbacks:</span></span>

* <span data-ttu-id="07e50-139">雖然大部分資料庫保證單一查詢的資料一致性，但多個查詢沒有這類保證存在。</span><span class="sxs-lookup"><span data-stu-id="07e50-139">While most databases guarantee data consistency for single queries, no such guarantees exist for multiple queries.</span></span> <span data-ttu-id="07e50-140">如果在執行查詢時同時更新資料庫，則產生的資料可能不一致。</span><span class="sxs-lookup"><span data-stu-id="07e50-140">If the database is updated concurrently when executing your queries, resulting data may not be consistent.</span></span> <span data-ttu-id="07e50-141">您可以藉由將查詢包裝在可序列化或快照集交易中來加以減輕，雖然這樣做可能會產生自己的效能問題。</span><span class="sxs-lookup"><span data-stu-id="07e50-141">You can mitigate it by wrapping the queries in a serializable or snapshot transaction, although doing so may create performance issues of its own.</span></span> <span data-ttu-id="07e50-142">如需詳細資訊，請參閱資料庫檔案。</span><span class="sxs-lookup"><span data-stu-id="07e50-142">For more information, see your database's documentation.</span></span>
* <span data-ttu-id="07e50-143">每個查詢目前暗示對您資料庫的額外網路往返。</span><span class="sxs-lookup"><span data-stu-id="07e50-143">Each query currently implies an additional network roundtrip to your database.</span></span> <span data-ttu-id="07e50-144">多個網路往返可能會降低效能，特別是在資料庫的延遲很高 (例如雲端服務) 的情況下。</span><span class="sxs-lookup"><span data-stu-id="07e50-144">Multiple network roundtrip can degrade performance, especially where latency to the database is high (for example, cloud services).</span></span>
* <span data-ttu-id="07e50-145">雖然某些資料庫允許同時使用多個查詢的結果 (使用 MARS 的 SQL Server，Sqlite) 大部分只允許在任何給定的時間點啟用單一查詢。</span><span class="sxs-lookup"><span data-stu-id="07e50-145">While some databases allow consuming the results of multiple queries at the same time (SQL Server with MARS, Sqlite), most allow only a single query to be active at any given point.</span></span> <span data-ttu-id="07e50-146">因此，必須先在應用程式的記憶體中緩衝處理舊版查詢的所有結果，然後再執行較新的查詢，這會導致記憶體需求增加。</span><span class="sxs-lookup"><span data-stu-id="07e50-146">So all results from earlier queries must be buffered in your application's memory before executing later queries, which leads to increased memory requirements.</span></span>

<span data-ttu-id="07e50-147">可惜的是，載入適用于所有案例的相關實體沒有一個策略。</span><span class="sxs-lookup"><span data-stu-id="07e50-147">Unfortunately, there isn't one strategy for loading related entities that fits all scenarios.</span></span> <span data-ttu-id="07e50-148">請仔細考慮單一和分割查詢的優缺點，然後選取符合您需求的查詢。</span><span class="sxs-lookup"><span data-stu-id="07e50-148">Carefully consider the advantages and disadvantages of single and split queries, and select the one that fits your needs.</span></span>

## <a name="filtered-include"></a><span data-ttu-id="07e50-149">篩選的包含</span><span class="sxs-lookup"><span data-stu-id="07e50-149">Filtered include</span></span>

> [!NOTE]
> <span data-ttu-id="07e50-150">這項功能是在 EF Core 5.0 中引進。</span><span class="sxs-lookup"><span data-stu-id="07e50-150">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="07e50-151">將 Include 套用至載入相關資料時，您可以在包含的集合導覽上套用某些可列舉的作業，以篩選和排序結果。</span><span class="sxs-lookup"><span data-stu-id="07e50-151">When applying Include to load related data, you can apply certain enumerable operations on the included collection navigation, which allows for filtering and sorting of the results.</span></span>

<span data-ttu-id="07e50-152">支援的作業為： `Where` 、 `OrderBy` 、、、、 `OrderByDescending` `ThenBy` `ThenByDescending` `Skip` 和 `Take` 。</span><span class="sxs-lookup"><span data-stu-id="07e50-152">Supported operations are: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take`.</span></span>

<span data-ttu-id="07e50-153">這類作業應該套用至傳遞給 Include 方法的 lambda 集合導覽，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="07e50-153">Such operations should be applied on the collection navigation in the lambda passed to the Include method, as shown in example below:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#FilteredInclude)]

<span data-ttu-id="07e50-154">每個包含的流覽都只允許一組唯一的篩選作業。</span><span class="sxs-lookup"><span data-stu-id="07e50-154">Each included navigation allows only one unique set of filter operations.</span></span> <span data-ttu-id="07e50-155">如果針對指定的集合導覽套用了多個 Include 作業 (`blog.Posts` 在下列範例) 中，只能在其中一個篩選作業上指定篩選作業：</span><span class="sxs-lookup"><span data-stu-id="07e50-155">In cases where multiple Include operations are applied for a given collection navigation (`blog.Posts` in the examples below), filter operations can only be specified on one of them:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered1)]

<span data-ttu-id="07e50-156">相反地，您可以針對每個包含多次的導覽套用相同的作業：</span><span class="sxs-lookup"><span data-stu-id="07e50-156">Instead, identical operations can be applied for each navigation that is included multiple times:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> <span data-ttu-id="07e50-157">在追蹤查詢的情況下，篩選的結果可能會因為 [流覽修復](xref:core/querying/tracking)而非預期的結果。</span><span class="sxs-lookup"><span data-stu-id="07e50-157">In case of tracking queries, results of Filtered Include may be unexpected due to [navigation fixup](xref:core/querying/tracking).</span></span> <span data-ttu-id="07e50-158">先前查詢並儲存在變更追蹤器中的所有相關實體都會出現在 [篩選的包含查詢] 的結果中，即使它們不符合篩選準則的需求。</span><span class="sxs-lookup"><span data-stu-id="07e50-158">All relevant entities that have been queried for previously and have been stored in the Change Tracker will be present in the results of Filtered Include query, even if they don't meet the requirements of the filter.</span></span> <span data-ttu-id="07e50-159">在 `NoTracking` 這些情況下使用篩選包含時，請考慮使用查詢或重新建立 DbCoNtext。</span><span class="sxs-lookup"><span data-stu-id="07e50-159">Consider using `NoTracking` queries or re-create the DbContext when using Filtered Include in those situations.</span></span>

<span data-ttu-id="07e50-160">範例：</span><span class="sxs-lookup"><span data-stu-id="07e50-160">Example:</span></span>

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

## <a name="include-on-derived-types"></a><span data-ttu-id="07e50-161">衍生類型中的 Include</span><span class="sxs-lookup"><span data-stu-id="07e50-161">Include on derived types</span></span>

<span data-ttu-id="07e50-162">您可以使用和，在衍生型別上包含僅針對衍生型別定義的導覽的相關資料 `Include` `ThenInclude` 。</span><span class="sxs-lookup"><span data-stu-id="07e50-162">You can include related data from navigation defined only on a derived type using `Include` and `ThenInclude`.</span></span>

<span data-ttu-id="07e50-163">假設有下列模型：</span><span class="sxs-lookup"><span data-stu-id="07e50-163">Given the following model:</span></span>

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

<span data-ttu-id="07e50-164">身分為學生之所有人員的 `School` 導覽內容可以使用多個模式進行積極式載入：</span><span class="sxs-lookup"><span data-stu-id="07e50-164">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

* <span data-ttu-id="07e50-165">使用 cast</span><span class="sxs-lookup"><span data-stu-id="07e50-165">using cast</span></span>

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* <span data-ttu-id="07e50-166">使用 `as` 運算子</span><span class="sxs-lookup"><span data-stu-id="07e50-166">using `as` operator</span></span>

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* <span data-ttu-id="07e50-167">使用 `Include` 的多載，其會接受類型 `string` 的參數</span><span class="sxs-lookup"><span data-stu-id="07e50-167">using overload of `Include` that takes parameter of type `string`</span></span>

  ```csharp
  context.People.Include("School").ToList()
  ```
  
