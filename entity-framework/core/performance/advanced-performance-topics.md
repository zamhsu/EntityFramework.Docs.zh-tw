---
title: Advanced Performance 主題
description: Entity Framework Core 的 Advanced 效能主題
author: rick-anderson
ms.author: riande
ms.date: 12/9/2020
uid: core/performance/advanced-performance-topics
ms.openlocfilehash: 3c0340e1b36cbbb96d23db0633cb2eebc04dd970
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657800"
---
# <a name="advanced-performance-topics"></a><span data-ttu-id="ee792-103">Advanced Performance 主題</span><span class="sxs-lookup"><span data-stu-id="ee792-103">Advanced Performance Topics</span></span>

## <a name="dbcontext-pooling"></a><span data-ttu-id="ee792-104">DbContext 共用</span><span class="sxs-lookup"><span data-stu-id="ee792-104">DbContext pooling</span></span>

<span data-ttu-id="ee792-105">`AddDbContextPool` 啟用實例的共用 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="ee792-105">`AddDbContextPool` enables pooling of `DbContext` instances.</span></span> <span data-ttu-id="ee792-106">內容共用可以藉由重複使用內容實例來增加高規模案例的輸送量，例如 web 伺服器，而不是為每個要求建立新的實例。</span><span class="sxs-lookup"><span data-stu-id="ee792-106">Context pooling can increase throughput in high-scale scenarios such as web servers by reusing context instances, rather than creating new instances for each request.</span></span>

<span data-ttu-id="ee792-107">使用 EF Core 的 ASP.NET Core 應用程式中的一般模式牽涉到將自訂類型註冊至相依性 <xref:Microsoft.EntityFrameworkCore.DbContext> [插入](/aspnet/core/fundamentals/dependency-injection) 容器，並透過控制器或 Razor Pages 中的函式參數取得該類型的實例。</span><span class="sxs-lookup"><span data-stu-id="ee792-107">The typical pattern in an ASP.NET Core app using EF Core involves registering a custom <xref:Microsoft.EntityFrameworkCore.DbContext> type into the [dependency injection](/aspnet/core/fundamentals/dependency-injection) container and obtaining instances of that type through constructor parameters in controllers or Razor Pages.</span></span> <span data-ttu-id="ee792-108">使用函式插入時，會為每個要求建立新的內容實例。</span><span class="sxs-lookup"><span data-stu-id="ee792-108">Using constructor injection, a new context instance is created for each request.</span></span>

<span data-ttu-id="ee792-109"><xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> 啟用可重複使用的內容實例集區。</span><span class="sxs-lookup"><span data-stu-id="ee792-109"><xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> enables a pool of reusable context instances.</span></span> <span data-ttu-id="ee792-110">若要使用內容共用，請使用 `AddDbContextPool` 方法，而不是 `AddDbContext` 在服務註冊期間：</span><span class="sxs-lookup"><span data-stu-id="ee792-110">To use context pooling, use the `AddDbContextPool` method instead of `AddDbContext` during service registration:</span></span>

```csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

<span data-ttu-id="ee792-111">`AddDbContextPool`使用時，在要求內容實例時，EF 會先檢查集區中是否有可用的實例。</span><span class="sxs-lookup"><span data-stu-id="ee792-111">When `AddDbContextPool` is used, at the time a context instance is requested, EF first checks if there is an instance available in the pool.</span></span> <span data-ttu-id="ee792-112">要求處理完成之後，會重設執行個體上的任何狀態，並將執行個體本身傳回到集區。</span><span class="sxs-lookup"><span data-stu-id="ee792-112">Once the request processing finalizes, any state on the instance is reset and the instance is itself returned to the pool.</span></span>

<span data-ttu-id="ee792-113">這在概念上類似于連接共用在 ADO.NET 提供者中的運作方式，而且具有節省部分初始化內容實例成本的優點。</span><span class="sxs-lookup"><span data-stu-id="ee792-113">This is conceptually similar to how connection pooling operates in ADO.NET providers and has the advantage of saving some of the cost of initialization of the context instance.</span></span>

<span data-ttu-id="ee792-114">的 `poolSize` 參數會 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> 設定集區保留的實例數目上限。</span><span class="sxs-lookup"><span data-stu-id="ee792-114">The `poolSize` parameter of <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> sets the maximum number of instances retained by the pool.</span></span> <span data-ttu-id="ee792-115">一旦 `poolSize` 超過此限制，就不會快取新的內容實例，而且 EF 會回到依需求建立實例的非共用行為。</span><span class="sxs-lookup"><span data-stu-id="ee792-115">Once `poolSize` is exceeded, new context instances are not cached and  EF falls back to the non-pooling behavior of creating instances on demand.</span></span>

### <a name="limitations"></a><span data-ttu-id="ee792-116">限制</span><span class="sxs-lookup"><span data-stu-id="ee792-116">Limitations</span></span>

<span data-ttu-id="ee792-117">應用程式應該進行程式碼剖析和測試，以顯示內容初始化是相當大的成本。</span><span class="sxs-lookup"><span data-stu-id="ee792-117">Apps should be profiled and tested to show that context initialization is a significant cost.</span></span>

<span data-ttu-id="ee792-118">`AddDbContextPool` 有幾項限制可在內容的方法中完成 `OnConfiguring` 。</span><span class="sxs-lookup"><span data-stu-id="ee792-118">`AddDbContextPool` has a few limitations on what can be done in the `OnConfiguring` method of the context.</span></span>

> [!WARNING]
> <span data-ttu-id="ee792-119">避免在維護狀態的應用程式中使用內容共用。</span><span class="sxs-lookup"><span data-stu-id="ee792-119">Avoid using context pooling in apps that maintain state.</span></span> <span data-ttu-id="ee792-120">例如，內容中不應該跨要求共用的私用欄位。</span><span class="sxs-lookup"><span data-stu-id="ee792-120">For example, private fields in the context that shouldn't be shared across requests.</span></span> <span data-ttu-id="ee792-121">EF Core 只會在將內容實例新增至集區之前，重設其感知的狀態。</span><span class="sxs-lookup"><span data-stu-id="ee792-121">EF Core only resets the state that it is aware of before adding a context instance to the pool.</span></span>

<span data-ttu-id="ee792-122">內容共用的運作方式是跨要求重複使用相同的內容實例。</span><span class="sxs-lookup"><span data-stu-id="ee792-122">Context pooling works by reusing the same context instance across requests.</span></span> <span data-ttu-id="ee792-123">這表示它實際上是以實例本身的 [Singleton](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) 來註冊，以便能夠保存。</span><span class="sxs-lookup"><span data-stu-id="ee792-123">This means that it's effectively registered as a [Singleton](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) in terms of the instance itself so that it's able to persist.</span></span>

<span data-ttu-id="ee792-124">內容共用適用于內容設定（包括已解決的服務）在要求之間固定的案例。</span><span class="sxs-lookup"><span data-stu-id="ee792-124">Context pooling is intended for scenarios where the context configuration, which includes services resolved, is fixed between requests.</span></span> <span data-ttu-id="ee792-125">針對需要 [範圍](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) 服務的情況，或需要變更設定時，請勿使用共用。</span><span class="sxs-lookup"><span data-stu-id="ee792-125">For cases where [Scoped](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) services are required, or configuration needs to be changed, don't use pooling.</span></span> <span data-ttu-id="ee792-126">除了在高度優化的案例中，來自共用的效能提升通常是可忽略的。</span><span class="sxs-lookup"><span data-stu-id="ee792-126">The performance gain from pooling is usually negligible except in highly optimized scenarios.</span></span>

## <a name="query-caching-and-parameterization"></a><span data-ttu-id="ee792-127">查詢快取和參數化</span><span class="sxs-lookup"><span data-stu-id="ee792-127">Query caching and parameterization</span></span>

<span data-ttu-id="ee792-128">當 EF 收到要執行的 LINQ 查詢樹狀結構時，它必須先將該樹狀結構「編譯」到 SQL 查詢中。</span><span class="sxs-lookup"><span data-stu-id="ee792-128">When EF receives a LINQ query tree for execution, it must first "compile" that tree into a SQL query.</span></span> <span data-ttu-id="ee792-129">因為這是相當繁重的程式，所以 EF 會根據查詢樹狀結構 *圖形* 來快取查詢：具有相同結構的查詢會在內部快取的編譯輸出中重複使用，並可略過重複的編譯。</span><span class="sxs-lookup"><span data-stu-id="ee792-129">Because this is a heavy process, EF caches queries by the query tree *shape*: queries with the same structure reuse internally-cached compilation outputs, and can skip repeated compilation.</span></span> <span data-ttu-id="ee792-130">不同的查詢仍可參考不同的 *值*，但只要這些值已正確地參數化，結構就會相同，而且快取會正常運作。</span><span class="sxs-lookup"><span data-stu-id="ee792-130">The different queries may still reference different *values*, but as long as these values are properly parameterized, the structure is the same and caching will function properly.</span></span>

<span data-ttu-id="ee792-131">請考慮下列兩個查詢：</span><span class="sxs-lookup"><span data-stu-id="ee792-131">Consider the following two queries:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#QueriesWithConstants)]

<span data-ttu-id="ee792-132">因為運算式樹狀架構包含不同的常數，所以運算式樹狀架構會有所不同，而且每個查詢都會由 EF Core 分別進行編譯。</span><span class="sxs-lookup"><span data-stu-id="ee792-132">Since the expression trees contains different constants, the expression tree differs and each of these queries will be compiled separately by EF Core.</span></span> <span data-ttu-id="ee792-133">此外，每個查詢都會產生稍微不同的 SQL 命令：</span><span class="sxs-lookup"><span data-stu-id="ee792-133">In addition, each query produces a slightly different SQL command:</span></span>

```sql
SELECT TOP(1) [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
WHERE [b].[Name] = N'blog1'

SELECT TOP(1) [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
WHERE [b].[Name] = N'blog2'
```

<span data-ttu-id="ee792-134">因為 SQL 不同，所以您的資料庫伺服器可能也需要針對這兩個查詢產生查詢計劃，而不是重複使用相同的計畫。</span><span class="sxs-lookup"><span data-stu-id="ee792-134">Because the SQL differs, your database server will likely also need to produce a query plan for both queries, rather than reusing the same plan.</span></span>

<span data-ttu-id="ee792-135">對您的查詢進行較小的修改可能會大幅改變事物：</span><span class="sxs-lookup"><span data-stu-id="ee792-135">A small modification to your queries can change things considerably:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#QueriesWithParameterization)]

<span data-ttu-id="ee792-136">因為現在會將 blog 名稱 *參數化*，所以兩個查詢都有相同的樹狀圖形，而且 EF 只需要編譯一次。</span><span class="sxs-lookup"><span data-stu-id="ee792-136">Since the blog name is now *parameterized*, both queries have the same tree shape, and EF only needs to be compiled once.</span></span> <span data-ttu-id="ee792-137">產生的 SQL 也會進行參數化，讓資料庫可以重複使用相同的查詢計劃：</span><span class="sxs-lookup"><span data-stu-id="ee792-137">The SQL produced is also parameterized, allowing the database to reuse the same query plan:</span></span>

```sql
SELECT TOP(1) [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
WHERE [b].[Name] = @__blogName_0
```

<span data-ttu-id="ee792-138">請注意，您不需要將每個查詢參數化：有一些常數的查詢都是不錯的，事實上，資料庫 (和 EF) 有時可以在查詢參數化時，對無法執行的常數執行某些優化。</span><span class="sxs-lookup"><span data-stu-id="ee792-138">Note that there is no need to parameterize each and every query: it's perfectly fine to have some queries with constants, and indeed, databases (and EF) can sometimes perform certain optimization around constants which aren't possible when the query is parameterized.</span></span> <span data-ttu-id="ee792-139">請參閱動態架構 [查詢](#dynamically-constructed-queries) 的一節，以取得適當的參數化很重要的範例。</span><span class="sxs-lookup"><span data-stu-id="ee792-139">See the section on [dynamically-constructed queries](#dynamically-constructed-queries) for an example where proper parameterization is crucial.</span></span>

> [!NOTE]
> <span data-ttu-id="ee792-140">EF Core 的 [事件計數器](xref:core/logging-events-diagnostics/event-counters) 會報告查詢快取點擊率。</span><span class="sxs-lookup"><span data-stu-id="ee792-140">EF Core's [event counters](xref:core/logging-events-diagnostics/event-counters) report the Query Cache Hit Rate.</span></span> <span data-ttu-id="ee792-141">在一般的應用程式中，此計數器會在程式啟動後立即達到100%，一旦大部分的查詢都至少執行一次。</span><span class="sxs-lookup"><span data-stu-id="ee792-141">In a normal application, this counter reaches 100% soon after program startup, once most queries have executed at least once.</span></span> <span data-ttu-id="ee792-142">如果此計數器維持穩定低於100%，則表示您的應用程式可能會執行一些與查詢快取不一致的情況，這是很好的想法。</span><span class="sxs-lookup"><span data-stu-id="ee792-142">If this counter remains stable below 100%, that is an indication that your application may be doing something which defeats the query cache - it's a good idea to investigate that.</span></span>

> [!NOTE]
> <span data-ttu-id="ee792-143">資料庫如何管理快取查詢計劃與資料庫相依。</span><span class="sxs-lookup"><span data-stu-id="ee792-143">How the database manages caches query plans is database-dependent.</span></span> <span data-ttu-id="ee792-144">例如，SQL Server 會隱含地維護 LRU 查詢計劃快取，而于 postgresql 不會 (但備妥的語句可能會產生非常類似的 end 效果) 。</span><span class="sxs-lookup"><span data-stu-id="ee792-144">For example, SQL Server implicitly maintains an LRU query plan cache, whereas PostgreSQL does not (but prepared statements can produce a very similar end effect).</span></span> <span data-ttu-id="ee792-145">如需詳細資訊，請參閱您的資料庫檔案。</span><span class="sxs-lookup"><span data-stu-id="ee792-145">Consult your database documentation for more details.</span></span>

## <a name="dynamically-constructed-queries"></a><span data-ttu-id="ee792-146">動態構建的查詢</span><span class="sxs-lookup"><span data-stu-id="ee792-146">Dynamically-constructed queries</span></span>

<span data-ttu-id="ee792-147">在某些情況下，必須以動態方式來建立 LINQ 查詢，而不是在原始程式碼中直接指定它們。</span><span class="sxs-lookup"><span data-stu-id="ee792-147">In some situations, it is necessary to dynamically construct LINQ queries rather than specifying them outright in source code.</span></span> <span data-ttu-id="ee792-148">例如，在從用戶端接收任意查詢詳細資料的網站中，有開放式查詢運算子 (排序、篩選、分頁 ... ) ，就會發生這種情況。基本上，如果正確地完成，以動態方式建立的查詢可以像一般的查詢一樣有效率 (不過，您無法使用已編譯的查詢優化來進行動態查詢) 。</span><span class="sxs-lookup"><span data-stu-id="ee792-148">This can happen, for example, in a website which receives arbitrary query details from a client, with open-ended query operators (sorting, filtering, paging...). In principle, if done correctly, dynamically-constructed queries can be just as efficient as regular ones (although it's not possible to use the compiled query optimization with dynamic queries).</span></span> <span data-ttu-id="ee792-149">不過在實務上，它們經常是效能問題的來源，因為很容易就會不小心地產生具有每次不同圖形的運算式樹狀架構。</span><span class="sxs-lookup"><span data-stu-id="ee792-149">In practice, however, they are frequently the source of performance issues, since it's easy to accidentally produce expression trees with shapes that differ every time.</span></span>

<span data-ttu-id="ee792-150">下列範例會使用兩種技術來動態地建立查詢; `Where` 只有當指定的參數不是 null 時，才會將運算子加入至查詢。</span><span class="sxs-lookup"><span data-stu-id="ee792-150">The following example uses two techniques to dynamically construct a query; we add a `Where` operator to the query only if the given parameter is not null.</span></span> <span data-ttu-id="ee792-151">請注意，這不是用來以動態方式建立查詢的好用案例，而是為了簡單起見而使用它：</span><span class="sxs-lookup"><span data-stu-id="ee792-151">Note that this isn't a good use case for dynamically constructing a query - but we're using it for simplicity:</span></span>

### <a name="with-constant"></a>[<span data-ttu-id="ee792-152">使用常數</span><span class="sxs-lookup"><span data-stu-id="ee792-152">With constant</span></span>](#tab/with-constant)

[!code-csharp[Main](../../../samples/core/Benchmarks/DynamicallyConstructedQueries.cs?name=WithConstant&highlight=14-24)]

### <a name="with-parameter"></a>[<span data-ttu-id="ee792-153">With 參數</span><span class="sxs-lookup"><span data-stu-id="ee792-153">With parameter</span></span>](#tab/with-parameter)

[!code-csharp[Main](../../../samples/core/Benchmarks/DynamicallyConstructedQueries.cs?name=WithParameter&highlight=14)]

***

<span data-ttu-id="ee792-154">這兩種技術的效能評定可提供下列結果：</span><span class="sxs-lookup"><span data-stu-id="ee792-154">Benchmarking these two techniques gives the following results:</span></span>

|        <span data-ttu-id="ee792-155">方法</span><span class="sxs-lookup"><span data-stu-id="ee792-155">Method</span></span> |       <span data-ttu-id="ee792-156">平均數</span><span class="sxs-lookup"><span data-stu-id="ee792-156">Mean</span></span> |    <span data-ttu-id="ee792-157">錯誤</span><span class="sxs-lookup"><span data-stu-id="ee792-157">Error</span></span> |    <span data-ttu-id="ee792-158">StdDev</span><span class="sxs-lookup"><span data-stu-id="ee792-158">StdDev</span></span> |   <span data-ttu-id="ee792-159">Gen 0</span><span class="sxs-lookup"><span data-stu-id="ee792-159">Gen 0</span></span> |  <span data-ttu-id="ee792-160">Gen 1</span><span class="sxs-lookup"><span data-stu-id="ee792-160">Gen 1</span></span> | <span data-ttu-id="ee792-161">Gen 2</span><span class="sxs-lookup"><span data-stu-id="ee792-161">Gen 2</span></span> | <span data-ttu-id="ee792-162">已配置</span><span class="sxs-lookup"><span data-stu-id="ee792-162">Allocated</span></span> |
|-------------- |-----------:|---------:|----------:|--------:|-------:|------:|----------:|
|  <span data-ttu-id="ee792-163">WithConstant</span><span class="sxs-lookup"><span data-stu-id="ee792-163">WithConstant</span></span> | <span data-ttu-id="ee792-164">1096.7 美國</span><span class="sxs-lookup"><span data-stu-id="ee792-164">1,096.7 us</span></span> | <span data-ttu-id="ee792-165">12.54 美國</span><span class="sxs-lookup"><span data-stu-id="ee792-165">12.54 us</span></span> |  <span data-ttu-id="ee792-166">11.12 美國</span><span class="sxs-lookup"><span data-stu-id="ee792-166">11.12 us</span></span> | <span data-ttu-id="ee792-167">13.6719</span><span class="sxs-lookup"><span data-stu-id="ee792-167">13.6719</span></span> | <span data-ttu-id="ee792-168">1.9531</span><span class="sxs-lookup"><span data-stu-id="ee792-168">1.9531</span></span> |     - |  <span data-ttu-id="ee792-169">83.91 KB</span><span class="sxs-lookup"><span data-stu-id="ee792-169">83.91 KB</span></span> |
| <span data-ttu-id="ee792-170">WithParameter</span><span class="sxs-lookup"><span data-stu-id="ee792-170">WithParameter</span></span> |   <span data-ttu-id="ee792-171">570.8 美國</span><span class="sxs-lookup"><span data-stu-id="ee792-171">570.8 us</span></span> | <span data-ttu-id="ee792-172">42.43 美國</span><span class="sxs-lookup"><span data-stu-id="ee792-172">42.43 us</span></span> | <span data-ttu-id="ee792-173">124.43 美國</span><span class="sxs-lookup"><span data-stu-id="ee792-173">124.43 us</span></span> |  <span data-ttu-id="ee792-174">5.8594</span><span class="sxs-lookup"><span data-stu-id="ee792-174">5.8594</span></span> |      - |     - |  <span data-ttu-id="ee792-175">37.16 KB</span><span class="sxs-lookup"><span data-stu-id="ee792-175">37.16 KB</span></span> |

<span data-ttu-id="ee792-176">即使毫秒差異看起來很小，但請記住，常數版本會持續干擾快取，並導致其他查詢重新編譯，同時使其變慢。</span><span class="sxs-lookup"><span data-stu-id="ee792-176">Even if the sub-millisecond difference seems small, keep in mind that the constant version continuously pollutes the cache and causes other queries to be re-compiled, slowing them down as well.</span></span>

> [!NOTE]
> <span data-ttu-id="ee792-177">除非您真的需要，否則請避免使用運算式樹狀架構 API 來建立查詢。</span><span class="sxs-lookup"><span data-stu-id="ee792-177">Avoid constructing queries with the expression tree API unless you really need to.</span></span> <span data-ttu-id="ee792-178">除了 API 的複雜度之外，在使用時，很容易就會不小心地造成顯著的效能問題。</span><span class="sxs-lookup"><span data-stu-id="ee792-178">Aside from the API's complexity, it's very easy to inadvertently cause significant performance issues when using them.</span></span>
