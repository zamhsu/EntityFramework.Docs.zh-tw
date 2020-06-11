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
# <a name="global-query-filters"></a><span data-ttu-id="85d86-102">全域查詢篩選條件</span><span class="sxs-lookup"><span data-stu-id="85d86-102">Global Query Filters</span></span>

> [!NOTE]
> <span data-ttu-id="85d86-103">此功能是在 EF Core 2.0 中引入。</span><span class="sxs-lookup"><span data-stu-id="85d86-103">This feature was introduced in EF Core 2.0.</span></span>

<span data-ttu-id="85d86-104">全域查詢篩選條件是中繼資料模型 (通常位於 *OnModelCreating*) 中套用到實體類型的 LINQ 查詢述詞 (布林運算式通常會傳遞到 LINQ *Where* 查詢運算子)。</span><span class="sxs-lookup"><span data-stu-id="85d86-104">Global query filters are LINQ query predicates (a boolean expression typically passed to the LINQ *Where* query operator) applied to Entity Types in the metadata model (usually in *OnModelCreating*).</span></span> <span data-ttu-id="85d86-105">這類篩選會自動套用至任何涉及這些「實體類型」(包含間接參考的「實體類型」) 的 LINQ 查詢 (例如，使用 Include 或直接導覽屬性參考)。</span><span class="sxs-lookup"><span data-stu-id="85d86-105">Such filters are automatically applied to any LINQ queries involving those Entity Types, including Entity Types referenced indirectly, such as through the use of Include or direct navigation property references.</span></span> <span data-ttu-id="85d86-106">此功能的一些常見應用如下：</span><span class="sxs-lookup"><span data-stu-id="85d86-106">Some common applications of this feature are:</span></span>

* <span data-ttu-id="85d86-107">**虛刪除**：實體類型會定義 *IsDeleted* 屬性。</span><span class="sxs-lookup"><span data-stu-id="85d86-107">**Soft delete** - An Entity Type defines an *IsDeleted* property.</span></span>
* <span data-ttu-id="85d86-108">**多**租使用者-實體類型會定義*TenantId*屬性。</span><span class="sxs-lookup"><span data-stu-id="85d86-108">**Multi-tenancy** - An Entity Type defines a *TenantId* property.</span></span>

## <a name="example"></a><span data-ttu-id="85d86-109">範例</span><span class="sxs-lookup"><span data-stu-id="85d86-109">Example</span></span>

<span data-ttu-id="85d86-110">下列範例示範如何使用全域查詢篩選條件，在簡單的部落格模型中實作虛刪除和多租用戶查詢行為。</span><span class="sxs-lookup"><span data-stu-id="85d86-110">The following example shows how to use Global Query Filters to implement soft-delete and multi-tenancy query behaviors in a simple blogging model.</span></span>

> [!TIP]
> <span data-ttu-id="85d86-111">您可以使用 GitHub 上的流覽來查看多租使用者[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters)和[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations)。</span><span class="sxs-lookup"><span data-stu-id="85d86-111">You can view a [multi-tenancy sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) and [samples using navigations](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) on GitHub.</span></span> 

<span data-ttu-id="85d86-112">首先，定義實體：</span><span class="sxs-lookup"><span data-stu-id="85d86-112">First, define the entities:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Entities)]

<span data-ttu-id="85d86-113">請注意 _Blog_ 實體上 _tenantId_ 欄位的宣告。</span><span class="sxs-lookup"><span data-stu-id="85d86-113">Note the declaration of a _tenantId_ field on the _Blog_ entity.</span></span> <span data-ttu-id="85d86-114">這會用來將每個 Blog 執行個體與特定租用戶產生關聯。</span><span class="sxs-lookup"><span data-stu-id="85d86-114">This will be used to associate each Blog instance with a specific tenant.</span></span> <span data-ttu-id="85d86-115">此外，還定義了 _Post_ 實體類型上的 _IsDeleted_ 屬性。</span><span class="sxs-lookup"><span data-stu-id="85d86-115">Also defined is an _IsDeleted_ property on the _Post_ entity type.</span></span> <span data-ttu-id="85d86-116">這會用來追蹤是否已將 _Post_ 執行個體「虛刪除」。</span><span class="sxs-lookup"><span data-stu-id="85d86-116">This is used to keep track of whether a _Post_ instance has been "soft-deleted".</span></span> <span data-ttu-id="85d86-117">亦即，將執行個體標示為已刪除，而不實際移除底層資料。</span><span class="sxs-lookup"><span data-stu-id="85d86-117">That is, the instance is marked as deleted without physically removing the underlying data.</span></span>

<span data-ttu-id="85d86-118">接下來，使用 `HasQueryFilter` API，在 _OnModelCreating_ 中設定查詢篩選條件。</span><span class="sxs-lookup"><span data-stu-id="85d86-118">Next, configure the query filters in _OnModelCreating_ using the `HasQueryFilter` API.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Configuration)]

<span data-ttu-id="85d86-119">傳遞到 _HasQueryFilter_ 呼叫的述詞運算式，現在將針對那些類型自動套用到任何 LINQ 查詢。</span><span class="sxs-lookup"><span data-stu-id="85d86-119">The predicate expressions passed to the _HasQueryFilter_ calls will now automatically be applied to any LINQ queries for those types.</span></span>

> [!TIP]
> <span data-ttu-id="85d86-120">請注意 DbContext 執行個體層級欄位的使用方式：`_tenantId` 用來設定目前的租用戶。</span><span class="sxs-lookup"><span data-stu-id="85d86-120">Note the use of a DbContext instance level field: `_tenantId` used to set the current tenant.</span></span> <span data-ttu-id="85d86-121">模型層級篩選將會使用正確內容執行個體 (亦即，執行查詢的執行個體) 中的值。</span><span class="sxs-lookup"><span data-stu-id="85d86-121">Model-level filters will use the value from the correct context instance (that is, the instance that is executing the query).</span></span>

> [!NOTE]
> <span data-ttu-id="85d86-122">目前不能在相同的實體上定義多個查詢篩選準則-只會套用最後一個。</span><span class="sxs-lookup"><span data-stu-id="85d86-122">It is currently not possible to define multiple query filters on the same entity - only the last one will be applied.</span></span> <span data-ttu-id="85d86-123">不過，您可以使用邏輯_AND_運算子（[ `&&` c # 中](https://docs.microsoft.com/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)的）定義具有多個條件的單一篩選。</span><span class="sxs-lookup"><span data-stu-id="85d86-123">However, you can define a single filter with multiple conditions using the logical _AND_ operator ([`&&` in C#](https://docs.microsoft.com/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span></span>

## <a name="use-of-navigations"></a><span data-ttu-id="85d86-124">導覽的使用</span><span class="sxs-lookup"><span data-stu-id="85d86-124">Use of navigations</span></span>

<span data-ttu-id="85d86-125">在定義全域查詢篩選器時，可以使用導覽。</span><span class="sxs-lookup"><span data-stu-id="85d86-125">Navigations can be used when defining global query filters.</span></span> <span data-ttu-id="85d86-126">它們會以遞迴方式套用-在查詢篩選中使用的導覽轉譯時，也會套用在參考實體上定義的查詢篩選，可能會加入更多的導覽。</span><span class="sxs-lookup"><span data-stu-id="85d86-126">They are applied recursively - when navigations used in query filters are translated, query filters defined on referenced entities are also applied, potentially adding more navigations.</span></span>

> [!NOTE]
> <span data-ttu-id="85d86-127">目前 EF Core 不會偵測全域查詢篩選定義中的迴圈，因此在定義時，您應該小心。</span><span class="sxs-lookup"><span data-stu-id="85d86-127">Currently EF Core does not detect cycles in global query filter definitions, so you should be careful when defining them.</span></span> <span data-ttu-id="85d86-128">如果指定錯誤，這可能會在查詢轉譯期間導致無限迴圈。</span><span class="sxs-lookup"><span data-stu-id="85d86-128">If specified incorrectly, this could lead to infinite loops during query translation.</span></span>

## <a name="accessing-entity-with-query-filter-using-reqiured-navigation"></a><span data-ttu-id="85d86-129">使用 reqiured 導覽來存取具有查詢篩選準則的實體</span><span class="sxs-lookup"><span data-stu-id="85d86-129">Accessing entity with query filter using reqiured navigation</span></span>

> [!CAUTION]
> <span data-ttu-id="85d86-130">使用必要的流覽來存取已定義全域查詢篩選器的實體，可能會導致非預期的結果。</span><span class="sxs-lookup"><span data-stu-id="85d86-130">Using required navigation to access entity which has global query filter defined may lead to unexpected results.</span></span> 

<span data-ttu-id="85d86-131">必要的導覽會預期相關實體一律存在。</span><span class="sxs-lookup"><span data-stu-id="85d86-131">Required navigation expects the related entity to always be present.</span></span> <span data-ttu-id="85d86-132">如果要求的相關實體已由查詢篩選器篩選掉，父實體最後可能會處於非預期的狀態。</span><span class="sxs-lookup"><span data-stu-id="85d86-132">If required related entity is filtered out by the query filter, the parent entity could end up in unexpected state.</span></span> <span data-ttu-id="85d86-133">這可能會導致傳回的元素數目少於預期。</span><span class="sxs-lookup"><span data-stu-id="85d86-133">This may result in returning fewer elements than expected.</span></span> 

<span data-ttu-id="85d86-134">為了說明這個問題，我們可以使用 `Blog` `Post` 上面指定的和實體，以及下列_OnModelCreating_方法：</span><span class="sxs-lookup"><span data-stu-id="85d86-134">To illustrate the problem, we can use the `Blog` and `Post` entities specified above and the following _OnModelCreating_ method:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="85d86-135">此模型可以使用下列資料植入：</span><span class="sxs-lookup"><span data-stu-id="85d86-135">The model can be seeded with the following data:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#SeedData)]

<span data-ttu-id="85d86-136">執行兩個查詢時，可能會發現此問題：</span><span class="sxs-lookup"><span data-stu-id="85d86-136">The problem can be observed when executing two queries:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#Queries)]

<span data-ttu-id="85d86-137">使用此設定時，第一個查詢會傳回所有 6 `Post` 秒，但第二個查詢只會傳回3。</span><span class="sxs-lookup"><span data-stu-id="85d86-137">With this setup, the first query returns all 6 `Post`s, however the second query only returns 3.</span></span> <span data-ttu-id="85d86-138">發生這種情況是因為第二個查詢中的_Include_方法會載入相關的 `Blog` 實體。</span><span class="sxs-lookup"><span data-stu-id="85d86-138">This happens because _Include_ method in the second query loads the related `Blog` entities.</span></span> <span data-ttu-id="85d86-139">因為需要和之間的導覽 `Blog` `Post` ，EF Core `INNER JOIN` 在建立查詢時使用：</span><span class="sxs-lookup"><span data-stu-id="85d86-139">Since the navigation between `Blog` and `Post` is required, EF Core uses `INNER JOIN` when constructing the query:</span></span>

```SQL
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Post] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE CHARINDEX(N'fish', [b].[Url]) > 0
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

<span data-ttu-id="85d86-140">使用 `INNER JOIN` 篩選掉所有已被 `Post` `Blog` 全域查詢篩選器移除其相關的。</span><span class="sxs-lookup"><span data-stu-id="85d86-140">Use of the `INNER JOIN` filters out all `Post`s whose related `Blog`s have been removed by a global query filter.</span></span> 

<span data-ttu-id="85d86-141">您可以使用選擇性的導覽，而不是必要的來解決此問題。</span><span class="sxs-lookup"><span data-stu-id="85d86-141">It can be addressed by using optional navigation instead of required.</span></span> <span data-ttu-id="85d86-142">如此一來，第一個查詢會維持不變，但第二個查詢現在會產生 `LEFT JOIN` 並傳回6個結果。</span><span class="sxs-lookup"><span data-stu-id="85d86-142">This way the first query stays the same as before, however the second query will now generate `LEFT JOIN` and return 6 results.</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired(false);
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="85d86-143">替代方法是在和實體上指定一致的篩選準則 `Blog` `Post` 。</span><span class="sxs-lookup"><span data-stu-id="85d86-143">Alternative approach is to specify consistent filters on both `Blog` and `Post` entities.</span></span>
<span data-ttu-id="85d86-144">這種比對篩選準則適用于 `Blog` 和 `Post` 。</span><span class="sxs-lookup"><span data-stu-id="85d86-144">This way matching filters are applied to both `Blog` and `Post`.</span></span> <span data-ttu-id="85d86-145">`Post`最後會移除處於非預期狀態的，而且這兩個查詢都會傳回3個結果。</span><span class="sxs-lookup"><span data-stu-id="85d86-145">`Post`s that could end up in unexpected state are removed and both queries return 3 results.</span></span> 

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
    modelBuilder.Entity<Post>().HasQueryFilter(p => p.Blog.Url.Contains("fish"));
}
```

## <a name="disabling-filters"></a><span data-ttu-id="85d86-146">停用篩選條件</span><span class="sxs-lookup"><span data-stu-id="85d86-146">Disabling Filters</span></span>

<span data-ttu-id="85d86-147">可能會使用 `IgnoreQueryFilters()` 運算子來停用個別 LINQ 查詢的篩選條件。</span><span class="sxs-lookup"><span data-stu-id="85d86-147">Filters may be disabled for individual LINQ queries by using the `IgnoreQueryFilters()` operator.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a><span data-ttu-id="85d86-148">限制</span><span class="sxs-lookup"><span data-stu-id="85d86-148">Limitations</span></span>

<span data-ttu-id="85d86-149">全域查詢篩選條件具有下列限制：</span><span class="sxs-lookup"><span data-stu-id="85d86-149">Global query filters have the following limitations:</span></span>

* <span data-ttu-id="85d86-150">只能針對繼承階層的根實體類型定義篩選條件。</span><span class="sxs-lookup"><span data-stu-id="85d86-150">Filters can only be defined for the root Entity Type of an inheritance hierarchy.</span></span>
