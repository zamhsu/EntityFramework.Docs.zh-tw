---
title: 載入相關資料 - EF Core
description: 使用 Entity Framework Core 載入相關資料的不同策略
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: 43b8cbac1e36a37bc85c953319407b3814d7d327
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618909"
---
# <a name="loading-related-data"></a><span data-ttu-id="8d9b3-103">載入相關資料</span><span class="sxs-lookup"><span data-stu-id="8d9b3-103">Loading Related Data</span></span>

<span data-ttu-id="8d9b3-104">Entity Framework Core 可讓您在模型中使用導覽屬性來載入相關實體。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-104">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="8d9b3-105">有三種常見的 O/RM 模式可用來載入相關資料。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-105">There are three common O/RM patterns used to load related data.</span></span>

* <span data-ttu-id="8d9b3-106">**積極式載入**表示會從資料庫以初始查詢一部分的方式載入相關資料。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-106">**Eager loading** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="8d9b3-107">**明確式載入**表示會從資料庫於稍後以明確方式載入相關資料。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-107">**Explicit loading** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="8d9b3-108">**消極式載入**表示會於存取導覽屬性時從資料庫以透明的方式載入相關資料。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-108">**Lazy loading** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span>

> [!TIP]
> <span data-ttu-id="8d9b3-109">您可以在 GitHub 上查看這篇文章的 [範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) 。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-109">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="8d9b3-110">積極式載入</span><span class="sxs-lookup"><span data-stu-id="8d9b3-110">Eager loading</span></span>

<span data-ttu-id="8d9b3-111">您可以使用 `Include` 方法來指定要包含於查詢結果中的相關資料。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-111">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="8d9b3-112">在下列範例中，於結果中所傳回部落格的 `Posts` 屬性將會填入相關文章。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-112">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]
> <span data-ttu-id="8d9b3-113">Entity Framework Core 會將導覽屬性自動修正為先前已載入至內容執行個體的任何其他實體。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-113">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="8d9b3-114">因此，即使未明確包含導覽屬性的資料，如果先前已載入部分或所有相關的實體，則仍然可能會填入該屬性。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-114">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

<span data-ttu-id="8d9b3-115">您可以將來自多個關聯性的相關資料包含至單一查詢。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-115">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a><span data-ttu-id="8d9b3-116">包括多個層級</span><span class="sxs-lookup"><span data-stu-id="8d9b3-116">Including multiple levels</span></span>

<span data-ttu-id="8d9b3-117">您可以使用 `ThenInclude` 方法，透過關聯性向下切入以包含多個層級的相關資料。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-117">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="8d9b3-118">下列範例會載入所有部落格、其相關文章，以及每篇文章的作者。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-118">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleThenInclude)]

<span data-ttu-id="8d9b3-119">您可以將多個呼叫鏈結到 `ThenInclude`，以繼續包含更深層級的相關資料。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-119">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

<span data-ttu-id="8d9b3-120">您可以合併所有的呼叫，以包含來自多個層級的相關資料，以及相同查詢中的多個根。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-120">You can combine all of the calls to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#IncludeTree)]

<span data-ttu-id="8d9b3-121">您可能會想要針對所包括的其中一個實體包含多個相關實體。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-121">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="8d9b3-122">例如，查詢 `Blogs` 時，您包括了 `Posts`，接著想要同時包含 `Posts` 的 `Author` 和 `Tags`。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-122">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="8d9b3-123">若要同時包含這兩個，您必須指定從根目錄開始的每個 include 路徑。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-123">To include both, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="8d9b3-124">例如 `Blog -> Posts -> Author` 和 `Blog -> Posts -> Tags`。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-124">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="8d9b3-125">這並不表示您將會收到多餘的聯結;在大部分情況下，EF 會在產生 SQL 時合併聯結。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-125">It doesn't mean you'll get redundant joins; in most cases, EF will combine the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

### <a name="single-and-split-queries"></a><span data-ttu-id="8d9b3-126">單一和分割查詢</span><span class="sxs-lookup"><span data-stu-id="8d9b3-126">Single and split queries</span></span>

#### <a name="single-queries"></a><span data-ttu-id="8d9b3-127">單一查詢</span><span class="sxs-lookup"><span data-stu-id="8d9b3-127">Single queries</span></span>

<span data-ttu-id="8d9b3-128">在關係資料庫中，所有相關的實體預設都是藉由引進聯結來載入：</span><span class="sxs-lookup"><span data-stu-id="8d9b3-128">In relational databases, all related entities are by default loaded by introducing JOINs:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

<span data-ttu-id="8d9b3-129">如果一般的 blog 有多個相關的文章，這些貼文的資料列將會複製 blog 的資訊，進而導致所謂的「笛卡兒爆炸」問題。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-129">If a typical blog has multiple related posts, rows for these posts will duplicate the blog's information, leading to the so-called "cartesian explosion" problem.</span></span> <span data-ttu-id="8d9b3-130">載入更多一對多關聯性時，重複的資料量可能會成長，而且會對應用程式的效能造成不良影響。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-130">As more one-to-many relationships are loaded, the amount of duplicated data may grow and adversely affect the performance of your application.</span></span> <span data-ttu-id="8d9b3-131">根據預設，如果偵測到載入集合的查詢可能會造成效能問題，EF Core 會發出警告。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-131">By default, EF Core emits a warning if it detects queries loading collection includes that may cause performance issues.</span></span>

#### <a name="split-queries"></a><span data-ttu-id="8d9b3-132">分割查詢</span><span class="sxs-lookup"><span data-stu-id="8d9b3-132">Split queries</span></span>

> [!NOTE]
> <span data-ttu-id="8d9b3-133">這項功能是在 EF Core 5.0 中引進。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-133">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="8d9b3-134">EF 可讓您指定應該將指定的 LINQ 查詢 *分割* 成多個 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-134">EF allows you to specify that a given LINQ query should be *split* into multiple SQL queries.</span></span> <span data-ttu-id="8d9b3-135">分割查詢除了聯結之外，還會針對每個包含的一對多導覽執行額外的 SQL 查詢：</span><span class="sxs-lookup"><span data-stu-id="8d9b3-135">Instead of JOINs, split queries perform an additional SQL query for each included one-to-many navigation:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs?name=AsSplitQuery&highlight=5)]

<span data-ttu-id="8d9b3-136">它會產生下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="8d9b3-136">It will produce the following SQL:</span></span>

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
> <span data-ttu-id="8d9b3-137">一對多相關實體一律會透過相同查詢中的聯結載入，因為這不會影響效能。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-137">One-to-one related entities are always loaded via JOINs in the same query, as this has no performance impact.</span></span>

#### <a name="enabling-split-queries-globally"></a><span data-ttu-id="8d9b3-138">全域啟用分割查詢</span><span class="sxs-lookup"><span data-stu-id="8d9b3-138">Enabling split queries globally</span></span>

<span data-ttu-id="8d9b3-139">您也可以將分割查詢設定為應用程式內容的預設值：</span><span class="sxs-lookup"><span data-stu-id="8d9b3-139">You can also configure split queries as the default for your application's context:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

<span data-ttu-id="8d9b3-140">當分割查詢設定為預設值時，仍然可以將特定查詢設定為以單一查詢的形式執行：</span><span class="sxs-lookup"><span data-stu-id="8d9b3-140">When split queries are configured as the default, it is still possible to configure specific queries to execute as single queries:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs?name=AsSplitQuery&highlight=5)]

<span data-ttu-id="8d9b3-141">如果未明確指定查詢分割模式，則全域或不是在查詢和 EF Core 會偵測到單一查詢會載入多個集合，併發出警告以吸引出可能產生的效能問題。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-141">If the query splitting mode isn't explicitly specified - neither globally nor on the query - and EF Core detects that a single query loads multiple collection includes, a warning is emitted to draw attention to the potential resulting performance issues.</span></span> <span data-ttu-id="8d9b3-142">將查詢模式設定為 SingleQuery 會導致不產生警告。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-142">Setting the query mode to SingleQuery will cause the warning not to be generated.</span></span>

#### <a name="characteristics-of-split-queries"></a><span data-ttu-id="8d9b3-143">分割查詢的特性</span><span class="sxs-lookup"><span data-stu-id="8d9b3-143">Characteristics of split queries</span></span>

<span data-ttu-id="8d9b3-144">雖然分割查詢可避免與聯結和笛卡兒爆炸相關的效能問題，但它也有一些缺點：</span><span class="sxs-lookup"><span data-stu-id="8d9b3-144">While split query avoids the performance issues associated with JOINs and cartesian explosion, it also has some drawbacks:</span></span>

* <span data-ttu-id="8d9b3-145">雖然大部分資料庫保證單一查詢的資料一致性，但多個查詢沒有這類保證存在。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-145">While most databases guarantee data consistency for single queries, no such guarantees exist for multiple queries.</span></span> <span data-ttu-id="8d9b3-146">如果在執行查詢時同時更新資料庫，則產生的資料可能不一致。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-146">If the database is updated concurrently when executing your queries, resulting data may not be consistent.</span></span> <span data-ttu-id="8d9b3-147">您可以藉由將查詢包裝在可序列化或快照集交易中來加以減輕，雖然這樣做可能會產生自己的效能問題。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-147">You can mitigate it by wrapping the queries in a serializable or snapshot transaction, although doing so may create performance issues of its own.</span></span> <span data-ttu-id="8d9b3-148">如需詳細資訊，請參閱資料庫檔案。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-148">For more information, see your database's documentation.</span></span>
* <span data-ttu-id="8d9b3-149">每個查詢目前暗示對您資料庫的額外網路往返。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-149">Each query currently implies an additional network roundtrip to your database.</span></span> <span data-ttu-id="8d9b3-150">多個網路往返可能會降低效能，特別是在資料庫的延遲很高 (例如雲端服務) 的情況下。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-150">Multiple network roundtrip can degrade performance, especially where latency to the database is high (for example, cloud services).</span></span>
* <span data-ttu-id="8d9b3-151">雖然某些資料庫允許同時使用多個查詢的結果 (使用 MARS 的 SQL Server，Sqlite) 大部分只允許在任何給定的時間點啟用單一查詢。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-151">While some databases allow consuming the results of multiple queries at the same time (SQL Server with MARS, Sqlite), most allow only a single query to be active at any given point.</span></span> <span data-ttu-id="8d9b3-152">因此，必須先在應用程式的記憶體中緩衝處理舊版查詢的所有結果，然後再執行較新的查詢，這會導致記憶體需求增加。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-152">So all results from earlier queries must be buffered in your application's memory before executing later queries, which leads to increased memory requirements.</span></span>

<span data-ttu-id="8d9b3-153">可惜的是，載入適用于所有案例的相關實體沒有一個策略。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-153">Unfortunately, there isn't one strategy for loading related entities that fits all scenarios.</span></span> <span data-ttu-id="8d9b3-154">請仔細考慮單一和分割查詢的優缺點，然後選取符合您需求的查詢。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-154">Carefully consider the advantages and disadvantages of single and split queries, and select the one that fits your needs.</span></span>

### <a name="filtered-include"></a><span data-ttu-id="8d9b3-155">篩選的包含</span><span class="sxs-lookup"><span data-stu-id="8d9b3-155">Filtered include</span></span>

> [!NOTE]
> <span data-ttu-id="8d9b3-156">這項功能是在 EF Core 5.0 中引進。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-156">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="8d9b3-157">將 Include 套用至載入相關資料時，您可以在包含的集合導覽上套用某些可列舉的作業，以篩選和排序結果。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-157">When applying Include to load related data, you can apply certain enumerable operations on the included collection navigation, which allows for filtering and sorting of the results.</span></span>

<span data-ttu-id="8d9b3-158">支援的作業為： `Where` 、 `OrderBy` 、、、、 `OrderByDescending` `ThenBy` `ThenByDescending` `Skip` 和 `Take` 。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-158">Supported operations are: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take`.</span></span>

<span data-ttu-id="8d9b3-159">這類作業應該套用至傳遞給 Include 方法的 lambda 集合導覽，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="8d9b3-159">Such operations should be applied on the collection navigation in the lambda passed to the Include method, as shown in example below:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#FilteredInclude)]

<span data-ttu-id="8d9b3-160">每個包含的流覽都只允許一組唯一的篩選作業。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-160">Each included navigation allows only one unique set of filter operations.</span></span> <span data-ttu-id="8d9b3-161">如果針對指定的集合導覽套用了多個 Include 作業 (`blog.Posts` 在下列範例) 中，只能在其中一個篩選作業上指定篩選作業：</span><span class="sxs-lookup"><span data-stu-id="8d9b3-161">In cases where multiple Include operations are applied for a given collection navigation (`blog.Posts` in the examples below), filter operations can only be specified on one of them:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered1)]

<span data-ttu-id="8d9b3-162">相反地，您可以針對每個包含多次的導覽套用相同的作業：</span><span class="sxs-lookup"><span data-stu-id="8d9b3-162">Instead, identical operations can be applied for each navigation that is included multiple times:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> <span data-ttu-id="8d9b3-163">在追蹤查詢的情況下，篩選的結果可能會因為 [流覽修復](xref:core/querying/tracking)而非預期的結果。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-163">In case of tracking queries, results of Filtered Include may be unexpected due to [navigation fixup](xref:core/querying/tracking).</span></span> <span data-ttu-id="8d9b3-164">先前查詢並儲存在變更追蹤器中的所有相關實體都會出現在 [篩選的包含查詢] 的結果中，即使它們不符合篩選準則的需求。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-164">All relevant entities that have been queried for previously and have been stored in the Change Tracker will be present in the results of Filtered Include query, even if they don't meet the requirements of the filter.</span></span> <span data-ttu-id="8d9b3-165">在 `NoTracking` 這些情況下使用篩選包含時，請考慮使用查詢或重新建立 DbCoNtext。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-165">Consider using `NoTracking` queries or re-create the DbContext when using Filtered Include in those situations.</span></span>

<span data-ttu-id="8d9b3-166">範例：</span><span class="sxs-lookup"><span data-stu-id="8d9b3-166">Example:</span></span>

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

### <a name="include-on-derived-types"></a><span data-ttu-id="8d9b3-167">衍生類型中的 Include</span><span class="sxs-lookup"><span data-stu-id="8d9b3-167">Include on derived types</span></span>

<span data-ttu-id="8d9b3-168">您可以使用和，在衍生型別上包含僅針對衍生型別定義的導覽的相關資料 `Include` `ThenInclude` 。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-168">You can include related data from navigation defined only on a derived type using `Include` and `ThenInclude`.</span></span>

<span data-ttu-id="8d9b3-169">假設有下列模型：</span><span class="sxs-lookup"><span data-stu-id="8d9b3-169">Given the following model:</span></span>

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

<span data-ttu-id="8d9b3-170">身分為學生之所有人員的 `School` 導覽內容可以使用多個模式進行積極式載入：</span><span class="sxs-lookup"><span data-stu-id="8d9b3-170">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

* <span data-ttu-id="8d9b3-171">使用 cast</span><span class="sxs-lookup"><span data-stu-id="8d9b3-171">using cast</span></span>

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* <span data-ttu-id="8d9b3-172">使用 `as` 運算子</span><span class="sxs-lookup"><span data-stu-id="8d9b3-172">using `as` operator</span></span>

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* <span data-ttu-id="8d9b3-173">使用 `Include` 的多載，其會接受類型 `string` 的參數</span><span class="sxs-lookup"><span data-stu-id="8d9b3-173">using overload of `Include` that takes parameter of type `string`</span></span>

  ```csharp
  context.People.Include("School").ToList()
  ```

## <a name="explicit-loading"></a><span data-ttu-id="8d9b3-174">明確式載入</span><span class="sxs-lookup"><span data-stu-id="8d9b3-174">Explicit loading</span></span>

<span data-ttu-id="8d9b3-175">您可以透過 `DbContext.Entry(...)` API 來明確地載入導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-175">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

<span data-ttu-id="8d9b3-176">您也可以透過執行會傳回相關實體的個別查詢，來明確地載入導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-176">You can also explicitly load a navigation property by executing a separate query that returns the related entities.</span></span> <span data-ttu-id="8d9b3-177">如果已啟用變更追蹤，則當查詢具體化實體時，EF Core 會自動將新載入之實體的導覽屬性設定為參考已載入的任何實體，並將已載入之實體的導覽屬性設定為參考新載入的實體。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-177">If change tracking is enabled, then when query materializes an entity, EF Core will automatically set the navigation properties of the newly loaded entity to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly loaded entity.</span></span>

### <a name="querying-related-entities"></a><span data-ttu-id="8d9b3-178">查詢相關實體</span><span class="sxs-lookup"><span data-stu-id="8d9b3-178">Querying related entities</span></span>

<span data-ttu-id="8d9b3-179">您也可以取得表示導覽屬性內容的 LINQ 查詢。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-179">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="8d9b3-180">它可讓您在查詢上套用其他運算子。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-180">It allows you to apply additional operators over the query.</span></span> <span data-ttu-id="8d9b3-181">下限範例：在相關實體上套用匯總運算子，而不將其載入記憶體中。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-181">Foe example applying an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

<span data-ttu-id="8d9b3-182">您也可以篩選要將哪些相關實體載入至記憶體。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-182">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a><span data-ttu-id="8d9b3-183">消極式載入</span><span class="sxs-lookup"><span data-stu-id="8d9b3-183">Lazy loading</span></span>

<span data-ttu-id="8d9b3-184">使用消極式載入的最簡單方式是安裝 [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) \(英文\) 套件，並呼叫 `UseLazyLoadingProxies` 來啟用它。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-184">The simplest way to use lazy-loading is by installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package and enabling it with a call to `UseLazyLoadingProxies`.</span></span> <span data-ttu-id="8d9b3-185">例如：</span><span class="sxs-lookup"><span data-stu-id="8d9b3-185">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```

<span data-ttu-id="8d9b3-186">或在使用 AddDbContext 時：</span><span class="sxs-lookup"><span data-stu-id="8d9b3-186">Or when using AddDbContext:</span></span>

```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```

<span data-ttu-id="8d9b3-187">EF Core 接著將針對可覆寫的所有導覽屬性 (也就是說，它必須是 `virtual` 並位於可繼承的類別上) 啟用消極式載入。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-187">EF Core will then enable lazy loading for any navigation property that can be overridden--that is, it must be `virtual` and on a class that can be inherited from.</span></span> <span data-ttu-id="8d9b3-188">例如，在下列實體中，系統將會對 `Post.Blog` 和 `Blog.Posts` 導覽屬性進行消極式載入。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-188">For example, in the following entities, the `Post.Blog` and `Blog.Posts` navigation properties will be lazy-loaded.</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public virtual Blog Blog { get; set; }
}
```

### <a name="lazy-loading-without-proxies"></a><span data-ttu-id="8d9b3-189">沒有 Proxy 的消極式載入</span><span class="sxs-lookup"><span data-stu-id="8d9b3-189">Lazy loading without proxies</span></span>

<span data-ttu-id="8d9b3-190">消極式載入 Proxy 的運作方式是將 `ILazyLoader` 服務插入至實體，如[實體類型建構函式](xref:core/modeling/constructors)中所述。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-190">Lazy-loading proxies work by injecting the `ILazyLoader` service into an entity, as described in [Entity Type Constructors](xref:core/modeling/constructors).</span></span> <span data-ttu-id="8d9b3-191">例如：</span><span class="sxs-lookup"><span data-stu-id="8d9b3-191">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="8d9b3-192">這個方法不需要繼承自或導覽屬性為虛擬的實體類型，而且可讓建立的實體實例在 `new` 附加至內容之後，延遲載入。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-192">This method doesn't require entity types to be inherited from or navigation properties to be virtual, and allows entity instances created with `new` to lazy-load once attached to a context.</span></span> <span data-ttu-id="8d9b3-193">不過，它需要對 `ILazyLoader` 服務的參考，這在 [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) 套件中定義。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-193">However, it requires a reference to the `ILazyLoader` service, which is defined in the [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) package.</span></span> <span data-ttu-id="8d9b3-194">此封裝包含一組基本的類型，因此不會對其產生很大的影響。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-194">This package contains a minimal set of types so that there is little impact in depending on it.</span></span> <span data-ttu-id="8d9b3-195">不過，若要完全避免根據實體類型中的任何 EF Core 套件，可以將 `ILazyLoader.Load` 方法插入為委派。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-195">However, to completely avoid depending on any EF Core packages in the entity types, it's possible to inject the `ILazyLoader.Load` method as a delegate.</span></span> <span data-ttu-id="8d9b3-196">例如：</span><span class="sxs-lookup"><span data-stu-id="8d9b3-196">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="8d9b3-197">上述程式碼會使用 `Load` 擴充方法，來更簡潔地使用委派：</span><span class="sxs-lookup"><span data-stu-id="8d9b3-197">The code above uses a `Load` extension method to make using the delegate a bit cleaner:</span></span>

```csharp
public static class PocoLoadingExtensions
{
    public static TRelated Load<TRelated>(
        this Action<object, string> loader,
        object entity,
        ref TRelated navigationField,
        [CallerMemberName] string navigationName = null)
        where TRelated : class
    {
        loader?.Invoke(entity, navigationName);

        return navigationField;
    }
}
```

> [!NOTE]
> <span data-ttu-id="8d9b3-198">消極式載入委派的建構函式參數必須稱為 "lazyLoader"。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-198">The constructor parameter for the lazy-loading delegate must be called "lazyLoader".</span></span> <span data-ttu-id="8d9b3-199">預計將於未來版本中推出使用與此不同之名稱的設定。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-199">Configuration to use a different name than this is planned for a future release.</span></span>

## <a name="related-data-and-serialization"></a><span data-ttu-id="8d9b3-200">相關資料和序列化</span><span class="sxs-lookup"><span data-stu-id="8d9b3-200">Related data and serialization</span></span>

<span data-ttu-id="8d9b3-201">因為 EF Core 會自動執行導覽屬性的修正，所以您的物件圖形中會有迴圈。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-201">Because EF Core automatically does fix-up of navigation properties, you can end up with cycles in your object graph.</span></span> <span data-ttu-id="8d9b3-202">例如，載入部落格與其相關文章將會產生參考文章集合的部落格物件。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-202">For example, loading a blog and its related posts will result in a blog object that references a collection of posts.</span></span> <span data-ttu-id="8d9b3-203">那些文章都會具有針對該部落格的參考。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-203">Each of those posts will have a reference back to the blog.</span></span>

<span data-ttu-id="8d9b3-204">某些序列化架構不允許這種迴圈。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-204">Some serialization frameworks don't allow such cycles.</span></span> <span data-ttu-id="8d9b3-205">例如，如果找到迴圈，Json.NET 將會擲回下列例外狀況。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-205">For example, Json.NET will throw the following exception if a cycle is found.</span></span>

> <span data-ttu-id="8d9b3-206">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog' (針對具有類型 'MyApplication.Models.Blog' 的屬性 'Blog' 偵測到自我參考迴圈)。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-206">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span></span>

<span data-ttu-id="8d9b3-207">如果您使用 ASP.NET Core，可以將 Json.NET 設定為忽略它在物件圖形中找到的迴圈。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-207">If you're using ASP.NET Core, you can configure Json.NET to ignore cycles that it finds in the object graph.</span></span> <span data-ttu-id="8d9b3-208">這項設定是在的 `ConfigureServices(...)` 方法中完成 `Startup.cs` 。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-208">This configuration is done in the `ConfigureServices(...)` method in `Startup.cs`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddMvc()
        .AddJsonOptions(
            options => options.SerializerSettings.ReferenceLoopHandling = Newtonsoft.Json.ReferenceLoopHandling.Ignore
        );

    ...
}
```

<span data-ttu-id="8d9b3-209">另一個替代方式為使用 `[JsonIgnore]` 屬性裝飾導覽屬性的其中一個，它會指示 Json.NET 序列化時不要周遊該導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="8d9b3-209">Another alternative is to decorate one of the navigation properties with the `[JsonIgnore]` attribute, which instructs Json.NET to not traverse that navigation property while serializing.</span></span>
