---
title: 全域查詢篩選條件 - EF Core
description: 使用全域查詢篩選準則來篩選 Entity Framework Core 的結果
author: maumar
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: 8a9eabd7e86864c9ebb4b1dc4a06bf7fc207d496
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062603"
---
# <a name="global-query-filters"></a><span data-ttu-id="39583-103">全域查詢篩選條件</span><span class="sxs-lookup"><span data-stu-id="39583-103">Global Query Filters</span></span>

<span data-ttu-id="39583-104">全域查詢篩選準則是套用至元資料模型中實體類型的 LINQ 查詢述詞， (通常是 `OnModelCreating`) 。</span><span class="sxs-lookup"><span data-stu-id="39583-104">Global query filters are LINQ query predicates applied to Entity Types in the metadata model (usually in `OnModelCreating`).</span></span> <span data-ttu-id="39583-105">查詢述詞是通常傳遞給 LINQ 查詢運算子的布林運算式 `Where` 。</span><span class="sxs-lookup"><span data-stu-id="39583-105">A query predicate is a boolean expression typically passed to the LINQ `Where` query operator.</span></span>  <span data-ttu-id="39583-106">EF Core 會自動將這類篩選套用至任何涉及這些實體類型的 LINQ 查詢。</span><span class="sxs-lookup"><span data-stu-id="39583-106">EF Core applies such filters automatically to any LINQ queries involving those Entity Types.</span></span>  <span data-ttu-id="39583-107">EF Core 也會將它們套用至實體類型，使用 Include 或導覽屬性間接參考。</span><span class="sxs-lookup"><span data-stu-id="39583-107">EF Core also applies them to Entity Types, referenced indirectly through use of Include or navigation property.</span></span> <span data-ttu-id="39583-108">此功能的一些常見應用如下：</span><span class="sxs-lookup"><span data-stu-id="39583-108">Some common applications of this feature are:</span></span>

* <span data-ttu-id="39583-109">虛**刪除**-實體類型會定義 `IsDeleted` 屬性。</span><span class="sxs-lookup"><span data-stu-id="39583-109">**Soft delete** - An Entity Type defines an `IsDeleted` property.</span></span>
* <span data-ttu-id="39583-110">**多** 租使用者-實體類型會定義 `TenantId` 屬性。</span><span class="sxs-lookup"><span data-stu-id="39583-110">**Multi-tenancy** - An Entity Type defines a `TenantId` property.</span></span>

## <a name="example"></a><span data-ttu-id="39583-111">範例</span><span class="sxs-lookup"><span data-stu-id="39583-111">Example</span></span>

<span data-ttu-id="39583-112">下列範例將示範如何使用全域查詢篩選器，在簡單的電子博客模型中執行多租使用者和虛刪除查詢行為。</span><span class="sxs-lookup"><span data-stu-id="39583-112">The following example shows how to use Global Query Filters to implement multi-tenancy and soft-delete query behaviors in a simple blogging model.</span></span>

> [!TIP]
> <span data-ttu-id="39583-113">您可以在 GitHub 上檢視此文章的[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/QueryFilters) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="39583-113">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/QueryFilters) on GitHub.</span></span>

<span data-ttu-id="39583-114">首先，定義實體：</span><span class="sxs-lookup"><span data-stu-id="39583-114">First, define the entities:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Entities.cs#Entities)]

<span data-ttu-id="39583-115">請注意 `_tenantId` 實體上的欄位宣告 `Blog` 。</span><span class="sxs-lookup"><span data-stu-id="39583-115">Note the declaration of a `_tenantId` field on the `Blog` entity.</span></span> <span data-ttu-id="39583-116">此欄位將用來將每個 Blog 實例與特定租使用者建立關聯。</span><span class="sxs-lookup"><span data-stu-id="39583-116">This field will be used to associate each Blog instance with a specific tenant.</span></span> <span data-ttu-id="39583-117">此外，也會定義 `IsDeleted` 實體類型上的屬性 `Post` 。</span><span class="sxs-lookup"><span data-stu-id="39583-117">Also defined is an `IsDeleted` property on the `Post` entity type.</span></span> <span data-ttu-id="39583-118">這個屬性是用來追蹤 post 實例是否已「虛刪除」。</span><span class="sxs-lookup"><span data-stu-id="39583-118">This property is used to keep track of whether a post instance has been "soft-deleted".</span></span> <span data-ttu-id="39583-119">亦即，將執行個體標示為已刪除，而不實際移除底層資料。</span><span class="sxs-lookup"><span data-stu-id="39583-119">That is, the instance is marked as deleted without physically removing the underlying data.</span></span>

<span data-ttu-id="39583-120">接下來， `OnModelCreating` 使用 API 來設定查詢篩選 `HasQueryFilter` 。</span><span class="sxs-lookup"><span data-stu-id="39583-120">Next, configure the query filters in `OnModelCreating` using the `HasQueryFilter` API.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/BloggingContext.cs#FilterConfiguration)]

<span data-ttu-id="39583-121">傳遞至呼叫的述詞運算式 `HasQueryFilter` 現在會自動套用至這些類型的任何 LINQ 查詢。</span><span class="sxs-lookup"><span data-stu-id="39583-121">The predicate expressions passed to the `HasQueryFilter` calls will now automatically be applied to any LINQ queries for those types.</span></span>

> [!TIP]
> <span data-ttu-id="39583-122">請注意 DbContext 執行個體層級欄位的使用方式：`_tenantId` 用來設定目前的租用戶。</span><span class="sxs-lookup"><span data-stu-id="39583-122">Note the use of a DbContext instance level field: `_tenantId` used to set the current tenant.</span></span> <span data-ttu-id="39583-123">模型層級篩選將會使用正確內容執行個體 (亦即，執行查詢的執行個體) 中的值。</span><span class="sxs-lookup"><span data-stu-id="39583-123">Model-level filters will use the value from the correct context instance (that is, the instance that is executing the query).</span></span>

> [!NOTE]
> <span data-ttu-id="39583-124">目前無法在相同的實體上定義多個查詢篩選-只會套用最後一個篩選。</span><span class="sxs-lookup"><span data-stu-id="39583-124">It is currently not possible to define multiple query filters on the same entity - only the last one will be applied.</span></span> <span data-ttu-id="39583-125">不過，您可以使用 `AND` [ `&&` c #) 中](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)的邏輯運算子 (來定義具有多個條件的單一篩選。</span><span class="sxs-lookup"><span data-stu-id="39583-125">However, you can define a single filter with multiple conditions using the logical `AND` operator ([`&&` in C#](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span></span>

## <a name="use-of-navigations"></a><span data-ttu-id="39583-126">使用導覽</span><span class="sxs-lookup"><span data-stu-id="39583-126">Use of navigations</span></span>

<span data-ttu-id="39583-127">您也可以使用導覽來定義全域查詢篩選準則。</span><span class="sxs-lookup"><span data-stu-id="39583-127">You can also use navigations in defining global query filters.</span></span> <span data-ttu-id="39583-128">使用查詢篩選器中的 [導覽]，將會以遞迴方式套用查詢篩選。</span><span class="sxs-lookup"><span data-stu-id="39583-128">Using navigations in query filter will cause query filters to be applied recursively.</span></span> <span data-ttu-id="39583-129">當 EF Core 展開查詢篩選中使用的導覽時，也會套用在參考實體上定義的查詢篩選。</span><span class="sxs-lookup"><span data-stu-id="39583-129">When EF Core expands navigations used in query filters, it will also apply query filters defined on referenced entities.</span></span>

> [!NOTE]
> <span data-ttu-id="39583-130">目前 EF Core 不會偵測到全域查詢篩選定義中的迴圈，因此在定義時，您應該小心。</span><span class="sxs-lookup"><span data-stu-id="39583-130">Currently EF Core does not detect cycles in global query filter definitions, so you should be careful when defining them.</span></span> <span data-ttu-id="39583-131">如果指定不正確，迴圈可能會在查詢轉譯期間導致無限迴圈。</span><span class="sxs-lookup"><span data-stu-id="39583-131">If specified incorrectly, cycles could lead to infinite loops during query translation.</span></span>

## <a name="accessing-entity-with-query-filter-using-required-navigation"></a><span data-ttu-id="39583-132">使用必要的導覽存取具有查詢篩選的實體</span><span class="sxs-lookup"><span data-stu-id="39583-132">Accessing entity with query filter using required navigation</span></span>

> [!CAUTION]
> <span data-ttu-id="39583-133">使用所需的流覽來存取已定義全域查詢篩選器的實體，可能會導致非預期的結果。</span><span class="sxs-lookup"><span data-stu-id="39583-133">Using required navigation to access entity which has global query filter defined may lead to unexpected results.</span></span>

<span data-ttu-id="39583-134">必要的導覽需要相關的實體一律存在。</span><span class="sxs-lookup"><span data-stu-id="39583-134">Required navigation expects the related entity to always be present.</span></span> <span data-ttu-id="39583-135">如果查詢篩選器會篩選出必要的相關實體，父實體將不會產生任何結果。</span><span class="sxs-lookup"><span data-stu-id="39583-135">If necessary related entity is filtered out by the query filter, the parent entity wouldn't be in result either.</span></span> <span data-ttu-id="39583-136">因此，您可能會得到比預期更少的元素。</span><span class="sxs-lookup"><span data-stu-id="39583-136">So you may get fewer elements than expected in result.</span></span>

<span data-ttu-id="39583-137">為了說明這個問題，我們可以使用 `Blog` `Post` 上面指定的和實體，以及下列 `OnModelCreating` 方法：</span><span class="sxs-lookup"><span data-stu-id="39583-137">To illustrate the problem, we can use the `Blog` and `Post` entities specified above and the following `OnModelCreating` method:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#IncorrectFilter)]

<span data-ttu-id="39583-138">您可以使用下列資料來植入模型：</span><span class="sxs-lookup"><span data-stu-id="39583-138">The model can be seeded with the following data:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#SeedData)]

<span data-ttu-id="39583-139">執行兩個查詢時，可能會觀察到此問題：</span><span class="sxs-lookup"><span data-stu-id="39583-139">The problem can be observed when executing two queries:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#Queries)]

<span data-ttu-id="39583-140">在上述設定中，第一個查詢會傳回所有 6 `Post` 秒，但是第二個查詢只會傳回3個。</span><span class="sxs-lookup"><span data-stu-id="39583-140">With above setup, the first query returns all 6 `Post`s, however the second query only returns 3.</span></span> <span data-ttu-id="39583-141">因為 `Include` 第二個查詢中的方法會載入相關實體，所以會發生這種不符的情況 `Blog` 。</span><span class="sxs-lookup"><span data-stu-id="39583-141">This mismatch happens because `Include` method in the second query loads the related `Blog` entities.</span></span> <span data-ttu-id="39583-142">由於和之間的 `Blog` 導覽 `Post` 是必要的，EF Core `INNER JOIN` 在建立查詢時使用：</span><span class="sxs-lookup"><span data-stu-id="39583-142">Since the navigation between `Blog` and `Post` is required, EF Core uses `INNER JOIN` when constructing the query:</span></span>

```sql
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Posts] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE [b].[Url] LIKE N'%fish%'
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

<span data-ttu-id="39583-143">使用 `INNER JOIN` 篩選出所有已被 `Post` `Blog` 全域查詢篩選器移除其相關的。</span><span class="sxs-lookup"><span data-stu-id="39583-143">Use of the `INNER JOIN` filters out all `Post`s whose related `Blog`s have been removed by a global query filter.</span></span>

<span data-ttu-id="39583-144">您可以使用選擇性流覽而非必要來解決此問題。</span><span class="sxs-lookup"><span data-stu-id="39583-144">It can be addressed by using optional navigation instead of required.</span></span>
<span data-ttu-id="39583-145">如此一來，第一個查詢會維持與之前相同，但第二個查詢現在會產生 `LEFT JOIN` 並傳回6個結果。</span><span class="sxs-lookup"><span data-stu-id="39583-145">This way the first query stays the same as before, however the second query will now generate `LEFT JOIN` and return 6 results.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#OptionalNavigation)]

<span data-ttu-id="39583-146">替代方法是在和實體上同時指定一致的篩選 `Blog` `Post` 。</span><span class="sxs-lookup"><span data-stu-id="39583-146">Alternative approach is to specify consistent filters on both `Blog` and `Post` entities.</span></span>
<span data-ttu-id="39583-147">這種比對篩選準則會套用至 `Blog` 和 `Post` 。</span><span class="sxs-lookup"><span data-stu-id="39583-147">This way matching filters are applied to both `Blog` and `Post`.</span></span> <span data-ttu-id="39583-148">`Post`最後會移除可能處於非預期狀態的，而且這兩個查詢都會傳回3個結果。</span><span class="sxs-lookup"><span data-stu-id="39583-148">`Post`s that could end up in unexpected state are removed and both queries return 3 results.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#MatchingFilters)]

## <a name="disabling-filters"></a><span data-ttu-id="39583-149">停用篩選條件</span><span class="sxs-lookup"><span data-stu-id="39583-149">Disabling Filters</span></span>

<span data-ttu-id="39583-150">可能會使用 <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.IgnoreQueryFilters%2A> 運算子來停用個別 LINQ 查詢的篩選條件。</span><span class="sxs-lookup"><span data-stu-id="39583-150">Filters may be disabled for individual LINQ queries by using the <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.IgnoreQueryFilters%2A> operator.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a><span data-ttu-id="39583-151">限制</span><span class="sxs-lookup"><span data-stu-id="39583-151">Limitations</span></span>

<span data-ttu-id="39583-152">全域查詢篩選條件具有下列限制：</span><span class="sxs-lookup"><span data-stu-id="39583-152">Global query filters have the following limitations:</span></span>

* <span data-ttu-id="39583-153">只能針對繼承階層的根實體類型定義篩選條件。</span><span class="sxs-lookup"><span data-stu-id="39583-153">Filters can only be defined for the root Entity Type of an inheritance hierarchy.</span></span>
