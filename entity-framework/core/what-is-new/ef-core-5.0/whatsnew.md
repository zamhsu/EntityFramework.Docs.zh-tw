---
title: 5.0 EF Core 的新功能
description: EF Core 5.0 中的新功能總覽
author: ajcvickers
ms.date: 07/20/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: d42b2811d07516e9febedbc51fcb206000d38371
ms.sourcegitcommit: 51148929e3889c48227d96c95c4e310d53a3d2c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/21/2020
ms.locfileid: "86873379"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="0ffc8-103">5.0 EF Core 的新功能</span><span class="sxs-lookup"><span data-stu-id="0ffc8-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="0ffc8-104">EF Core 5.0 目前正在開發中。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-104">EF Core 5.0 is currently in development.</span></span> <span data-ttu-id="0ffc8-105">此頁面將包含每個預覽中所引進之有趣變更的總覽。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="0ffc8-106">此頁面不會複製[EF Core 5.0 的計畫](xref:core/what-is-new/ef-core-5.0/plan)。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-106">This page does not duplicate the [plan for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span></span> <span data-ttu-id="0ffc8-107">此計畫描述 EF Core 5.0 的整體主題，包括我們打算在交付最終版本之前包含的所有專案。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="0ffc8-108">我們會將這裡的連結新增至正式檔，因為它已發佈。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-7"></a><span data-ttu-id="0ffc8-109">Preview 7</span><span class="sxs-lookup"><span data-stu-id="0ffc8-109">Preview 7</span></span>

### <a name="dbcontextfactory"></a><span data-ttu-id="0ffc8-110">DbCoNtextFactory</span><span class="sxs-lookup"><span data-stu-id="0ffc8-110">DbContextFactory</span></span>

<span data-ttu-id="0ffc8-111">EF Core 5.0 引進 `AddDbContextFactory` 並 `AddPooledDbContextFactory` 註冊 factory，以便在應用程式的相依性插入（D.I.）容器中建立 DbCoNtext 實例。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-111">EF Core 5.0 introduces `AddDbContextFactory` and `AddPooledDbContextFactory` to register a factory for creating DbContext instances in the application's dependency injection (D.I.) container.</span></span> <span data-ttu-id="0ffc8-112">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-112">For example:</span></span>

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

<span data-ttu-id="0ffc8-113">然後，ASP.NET Core 控制器之類的應用程式服務就可以依賴 `IDbContextFactory<TContext>` 服務的函式。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-113">Application services such as ASP.NET Core controllers can then depend on `IDbContextFactory<TContext>` in the service constructor.</span></span> <span data-ttu-id="0ffc8-114">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-114">For example:</span></span>

```csharp
public class MyController
{
    private readonly IDbContextFactory<SomeDbContext> _contextFactory;

    public MyController(IDbContextFactory<SomeDbContext> contextFactory)
    {
        _contextFactory = contextFactory;
    }
}
```

<span data-ttu-id="0ffc8-115">然後可以視需要建立和使用 DbCoNtext 實例。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-115">DbContext instances can then be created and used as needed.</span></span> <span data-ttu-id="0ffc8-116">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-116">For example:</span></span>

```csharp
public void DoSomehing()
{
    using (var context = _contextFactory.CreateDbContext())
    {
        // ...            
    }
}
```

<span data-ttu-id="0ffc8-117">請注意，以這種方式建立的 DbCoNtext 實例_不_會由應用程式的服務提供者管理，因此必須由應用程式處置。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-117">Note that the DbContext instances created in this way are _not_ managed by the application's service provider and therefore must be disposed by the application.</span></span> <span data-ttu-id="0ffc8-118">這種分離功能對於 Blazor 應用程式非常有用，其中 `IDbContextFactory` 建議使用，但在其他情況下也很有用。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-118">This decoupling is very useful for Blazor applications, where using `IDbContextFactory` is recommended, but may also be useful in other scenarios.</span></span>

<span data-ttu-id="0ffc8-119">DbCoNtext 實例可以藉由呼叫來集區 `AddPooledDbContextFactory` 。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-119">DbContext instances can be pooled by calling `AddPooledDbContextFactory`.</span></span> <span data-ttu-id="0ffc8-120">這個共用的運作方式與相同 `AddDbContextPool` ，而且也具有相同的限制。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-120">This pooling works the same way as for `AddDbContextPool`, and also has the same limitations.</span></span>

<span data-ttu-id="0ffc8-121">檔是由問題[#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-121">Documentation is tracked by issue [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).</span></span>

### <a name="reset-dbcontext-state"></a><span data-ttu-id="0ffc8-122">重設 DbCoNtext 狀態</span><span class="sxs-lookup"><span data-stu-id="0ffc8-122">Reset DbContext state</span></span>

<span data-ttu-id="0ffc8-123">EF Core 5.0 引進了 `ChangeTracker.Clear()` ，其會清除所有追蹤實體的 DbCoNtext。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-123">EF Core 5.0 introduces `ChangeTracker.Clear()` which clears the DbContext of all tracked entities.</span></span> <span data-ttu-id="0ffc8-124">當您使用最佳作法來建立每個工作單位的新短期內容實例時，通常不需要這樣做。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-124">This should usually not be needed when using the best practice of creating a new, short-lived context instance for each unit-of-work.</span></span> <span data-ttu-id="0ffc8-125">不過，如果需要重設 DbCoNtext 實例的狀態，則使用新的 `Clear()` 方法比大量卸離所有實體更具效能且穩定。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-125">However, if there is a need to reset the state of a DbContext instance, then using the new `Clear()` method is more performant and robust than mass-detaching all entities.</span></span>  

<span data-ttu-id="0ffc8-126">檔是由問題[#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-126">Documentation is tracked by issue [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span></span>

### <a name="new-pattern-for-store-generated-defaults"></a><span data-ttu-id="0ffc8-127">存放區產生之預設值的新模式</span><span class="sxs-lookup"><span data-stu-id="0ffc8-127">New pattern for store-generated defaults</span></span>

<span data-ttu-id="0ffc8-128">EF Core 允許針對可能也有預設值條件約束的資料行設定明確的值。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-128">EF Core allows an explicit value to be set for a column that may also have default value constraint.</span></span> <span data-ttu-id="0ffc8-129">EF Core 使用 type 屬性類型的 CLR 預設值做為此的 sentinel;如果該值不是 CLR 預設值，則會插入它，否則會使用資料庫預設。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-129">EF Core uses the CLR default of type property type as a sentinel for this; if the value is not the CLR default, then it is inserted, otherwise the database default is used.</span></span>

<span data-ttu-id="0ffc8-130">這會為 CLR 預設不是良好 sentinel 的類型（最值得注意的屬性）產生問題 `bool` 。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-130">This creates problems for types where the CLR default is not a good sentinel--most notably, `bool` properties.</span></span> <span data-ttu-id="0ffc8-131">EF Core 5.0 現在允許在這類情況下，支援欄位可為 null。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-131">EF Core 5.0 now allows the backing field to be nullable for cases like this.</span></span> <span data-ttu-id="0ffc8-132">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-132">For example:</span></span>

```csharp
public class Blog
{
    private bool? _isValid;

    public bool IsValid
    {
        get => _isValid ?? false;
        set => _isValid = value;
    }
}
```

<span data-ttu-id="0ffc8-133">請注意，支援欄位可為 null，但公開的屬性不是。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-133">Note that the backing field is nullable, but the publicly exposed property is not.</span></span> <span data-ttu-id="0ffc8-134">這可讓 sentinel 值不會 `null` 影響實體類型的公用介面。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-134">This allows the sentinel value to be `null` without impacting the public surface of the entity type.</span></span> <span data-ttu-id="0ffc8-135">在此情況下，如果 `IsValid` 從未設定，則會使用資料庫預設值，因為支援欄位仍然是 null。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-135">In this case, if the `IsValid` is never set, then the database default will be used since the backing field remains null.</span></span> <span data-ttu-id="0ffc8-136">如果 `true` `false` 設定了或，則此值會明確儲存到資料庫中。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-136">If either `true` or `false` are set, then this value is saved explicitly to the database.</span></span>

<span data-ttu-id="0ffc8-137">檔是由問題[#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-137">Documentation is tracked by issue [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="0ffc8-138">Cosmos 分割區索引鍵</span><span class="sxs-lookup"><span data-stu-id="0ffc8-138">Cosmos partition keys</span></span>

<span data-ttu-id="0ffc8-139">EF Core 允許在 EF 模型中包含 Cosmos 分割區索引鍵。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-139">EF Core allows the Cosmos partition key is included in the EF model.</span></span> <span data-ttu-id="0ffc8-140">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-140">For example:</span></span>

```csharp
modelBuilder.Entity<Customer>().HasPartitionKey(b => b.AlternateKey)
```

<span data-ttu-id="0ffc8-141">從 preview 7 開始，資料分割索引鍵會包含在實體類型的 PK 中，並在某些查詢中用來改善效能。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-141">Starting with preview 7, the partition key is included in the entity type's PK and is used to improved performance in some queries.</span></span>

<span data-ttu-id="0ffc8-142">檔是由問題[#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-142">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="cosmos-configuration"></a><span data-ttu-id="0ffc8-143">Cosmos 設定</span><span class="sxs-lookup"><span data-stu-id="0ffc8-143">Cosmos configuration</span></span>

<span data-ttu-id="0ffc8-144">EF Core 5.0 會改善 Cosmos 和 Cosmos 連接的設定。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-144">EF Core 5.0 improves configuration of Cosmos and Cosmos connections.</span></span>

<span data-ttu-id="0ffc8-145">在過去，EF Core 需要在連接到 Cosmos 資料庫時明確指定端點和金鑰。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-145">Previously, EF Core required the end-point and key to be specified explicitly when connecting to a Cosmos database.</span></span> <span data-ttu-id="0ffc8-146">EF Core 5.0 允許改用連接字串。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-146">EF Core 5.0 allows use of a connection string instead.</span></span> <span data-ttu-id="0ffc8-147">此外，EF Core 5.0 允許明確設定 WebProxy 實例。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-147">In addition, EF Core 5.0 allows the WebProxy instance to be explicitly set.</span></span> <span data-ttu-id="0ffc8-148">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-148">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.WebProxy(myProxyInstance);
            });
```

<span data-ttu-id="0ffc8-149">現在也可以設定許多其他的超時值、限制等等。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-149">Many other timeout values, limits, etc. can now also be configured.</span></span> <span data-ttu-id="0ffc8-150">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-150">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.LimitToEndpoint();
                cosmosOptionsBuilder.RequestTimeout(requestTimeout);
                cosmosOptionsBuilder.OpenTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.IdleTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.GatewayModeMaxConnectionLimit(connectionLimit);
                cosmosOptionsBuilder.MaxTcpConnectionsPerEndpoint(connectionLimit);
                cosmosOptionsBuilder.MaxRequestsPerTcpConnection(requestLimit);
            });
```

<span data-ttu-id="0ffc8-151">最後，預設的連接模式現在是 `ConnectionMode.Gateway` 較相容的。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-151">Finally, the default connection mode is now `ConnectionMode.Gateway`, which is generally more compatible.</span></span>

<span data-ttu-id="0ffc8-152">檔是由問題[#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-152">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="scaffold-dbcontext-now-singularizes"></a><span data-ttu-id="0ffc8-153">Scaffold-DbCoNtext now singularizes</span><span class="sxs-lookup"><span data-stu-id="0ffc8-153">Scaffold-DbContext now singularizes</span></span>

<span data-ttu-id="0ffc8-154">先前，當樣板從現有的資料庫 DbCoNtext 時，EF Core 將會建立符合資料庫中資料表名稱的實體類型名稱。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-154">Previously when scaffolding a DbContext from an existing database, EF Core will create entity type names that match the table names in the database.</span></span> <span data-ttu-id="0ffc8-155">例如，資料表 `People` 和會 `Addresses` 產生名為和的實體類型 `People` `Addresses` 。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-155">For example, tables `People` and `Addresses` resulted in entity types named `People` and `Addresses`.</span></span>

<span data-ttu-id="0ffc8-156">在先前的版本中，此行為可透過註冊複數表示服務來設定。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-156">In previous releases, this behavior was configurable through registration of a pluralization service.</span></span> <span data-ttu-id="0ffc8-157">現在在 EF Core 5.0 中，會使用[Humanizer](https://www.nuget.org/packages/Humanizer.Core/)套件做為預設複數表示服務。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-157">Now in EF Core 5.0, the [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) package is used as a default pluralization service.</span></span> <span data-ttu-id="0ffc8-158">這表示資料表 `People` 和 `Addresses` 現在會針對名為和的實體類型進行反向工程 `Person` `Address` 。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-158">This means tables `People` and `Addresses` will now be reverse engineered to entity types named `Person` and `Address`.</span></span>

### <a name="savepoints"></a><span data-ttu-id="0ffc8-159">保存</span><span class="sxs-lookup"><span data-stu-id="0ffc8-159">Savepoints</span></span>

<span data-ttu-id="0ffc8-160">EF Core 現在支援儲存[點](/SQL/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15#remarks)，以進一步控制執行多項作業的交易。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-160">EF Core now supports [savepoints](/SQL/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15#remarks) for greater control over transactions that execute multiple operations.</span></span>

<span data-ttu-id="0ffc8-161">儲存點可以手動建立、釋放和復原。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-161">Savepoints can be manually created, released, and rolled back.</span></span> <span data-ttu-id="0ffc8-162">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-162">For example:</span></span>

```csharp
context.Database.CreateSavepoint("MySavePoint"); 
```

<span data-ttu-id="0ffc8-163">此外，在執行失敗時，EF Core 現在會回復為最後一個儲存點 `SaveChanges` 。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-163">In addition, EF Core will now roll back to the last savepoint when executing `SaveChanges` fails.</span></span> <span data-ttu-id="0ffc8-164">這可讓 SaveChanges 重新嘗試，而不需要重新嘗試整個交易。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-164">This allows SaveChanges to be re-tried without re-trying the entire transaction.</span></span>

<span data-ttu-id="0ffc8-165">檔是由問題[#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-165">Documentation is tracked by issue [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span></span>

## <a name="preview-6"></a><span data-ttu-id="0ffc8-166">Preview 6</span><span class="sxs-lookup"><span data-stu-id="0ffc8-166">Preview 6</span></span>

### <a name="split-queries-for-related-collections"></a><span data-ttu-id="0ffc8-167">相關集合的分割查詢</span><span class="sxs-lookup"><span data-stu-id="0ffc8-167">Split queries for related collections</span></span>

<span data-ttu-id="0ffc8-168">從 EF Core 3.0 開始，EF Core 一律會針對每個 LINQ 查詢產生單一 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-168">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="0ffc8-169">這可確保在使用中交易模式的條件約束內傳回的資料一致。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-169">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="0ffc8-170">不過，當查詢使用 `Include` 或預測來重新顯示多個相關的集合時，這可能會變得非常緩慢。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-170">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="0ffc8-171">EF Core 5.0 現在允許單一 LINQ 查詢，包括要分割成多個 SQL 查詢的相關集合。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-171">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="0ffc8-172">這可以大幅提升效能，但如果兩個查詢之間的資料有所變更，可能會導致傳回的結果不一致。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-172">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="0ffc8-173">可序列化或快照集交易可以用來減輕這個問題，並利用分割查詢達成一致性，但這可能會帶來其他效能成本和行為上的差異。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-173">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

#### <a name="split-queries-with-include"></a><span data-ttu-id="0ffc8-174">使用 Include 分割查詢</span><span class="sxs-lookup"><span data-stu-id="0ffc8-174">Split queries with Include</span></span>

<span data-ttu-id="0ffc8-175">例如，假設有一個查詢會使用提取兩個層級的相關集合 `Include` ：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-175">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```CSharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="0ffc8-176">根據預設，當使用 SQLite 提供者時，EF Core 將會產生下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-176">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name", "t0"."Id", "t0"."ArtistId", "t0"."Title", "t0"."Id0", "t0"."AlbumId", "t0"."Name"
FROM "Artists" AS "a"
LEFT JOIN (
    SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "t"."Id" AS "Id0", "t"."AlbumId", "t"."Name"
    FROM "Album" AS "a0"
    LEFT JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
) AS "t0" ON "a"."Id" = "t0"."ArtistId"
ORDER BY "a"."Id", "t0"."Id", "t0"."Id0"
```

<span data-ttu-id="0ffc8-177">新的 `AsSplitQuery` API 可用於變更此行為。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-177">The new `AsSplitQuery` API can be used to change this behavior.</span></span> <span data-ttu-id="0ffc8-178">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-178">For example:</span></span>

```CSharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="0ffc8-179">AsSplitQuery 適用于所有關系資料庫提供者，而且可以在查詢中的任何位置使用，就像 AsNoTracking 一樣。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-179">AsSplitQuery is available for all relational database providers and can be used anywhere in the query, just like AsNoTracking.</span></span> <span data-ttu-id="0ffc8-180">EF Core 現在會產生下列三個 SQL 查詢：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-180">EF Core will now generate the following three SQL queries:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id", "a0"."Id"

SELECT "t"."Id", "t"."AlbumId", "t"."Name", "a"."Id", "a0"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
INNER JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
ORDER BY "a"."Id", "a0"."Id"
```

<span data-ttu-id="0ffc8-181">支援查詢根目錄上的所有作業。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-181">All operations on the query root are supported.</span></span> <span data-ttu-id="0ffc8-182">這包括 OrderBy/Skip/Take、Join 作業、FirstOrDefault 和類似的單一結果選取作業。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-182">This includes OrderBy/Skip/Take, Join operations, FirstOrDefault and similar single result selecting operations.</span></span>

<span data-ttu-id="0ffc8-183">請注意，preview 6 不支援具有 OrderBy/Skip/Take 的篩選包含，但在每日組建中都有提供，而且將包含在 preview 7 中。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-183">Note that filtered Includes with OrderBy/Skip/Take are not supported in preview 6, but are available in the daily builds and will be included in preview 7.</span></span>

#### <a name="split-queries-with-collection-projections"></a><span data-ttu-id="0ffc8-184">使用集合投射分割查詢</span><span class="sxs-lookup"><span data-stu-id="0ffc8-184">Split queries with collection projections</span></span>

<span data-ttu-id="0ffc8-185">`AsSplitQuery`在投影中載入集合時，也可以使用。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-185">`AsSplitQuery` can also be used when collections are loaded in projections.</span></span> <span data-ttu-id="0ffc8-186">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-186">For example:</span></span>

```CSharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

<span data-ttu-id="0ffc8-187">使用 SQLite 提供者時，此 LINQ 查詢會產生下列兩個 SQL 查詢：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-187">This LINQ query generates the following two SQL queries when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

<span data-ttu-id="0ffc8-188">請注意，只支援集合的具體化。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-188">Note that only materialization of the collection is supported.</span></span> <span data-ttu-id="0ffc8-189">`e.Albums`在上述情況下的任何組合都不會產生分割查詢。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-189">Any composition after `e.Albums` in above case won't result in a split query.</span></span> <span data-ttu-id="0ffc8-190">此區域中的改進功能會由[#21234](https://github.com/dotnet/efcore/issues/21234)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-190">Improvements in this area are tracked by [#21234](https://github.com/dotnet/efcore/issues/21234).</span></span>

### <a name="indexattribute"></a><span data-ttu-id="0ffc8-191">IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="0ffc8-191">IndexAttribute</span></span>

<span data-ttu-id="0ffc8-192">新的 IndexAttribute 可以放在實體類型上，以指定單一資料行的索引。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-192">The new IndexAttribute can be placed on an entity type to specify an index for a single column.</span></span> <span data-ttu-id="0ffc8-193">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-193">For example:</span></span>

```CSharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }
    
    [MaxLength(128)]
    public string FullName { get; set; }
}
```

<span data-ttu-id="0ffc8-194">針對 SQL Server，遷移將會產生下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-194">For SQL Server, Migrations will then generate the following SQL:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

<span data-ttu-id="0ffc8-195">IndexAttribute 也可以用來指定跨越多個資料行的索引。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-195">IndexAttribute can also be used to specify an index spanning multiple columns.</span></span> <span data-ttu-id="0ffc8-196">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-196">For example:</span></span>

```CSharp
[Index(nameof(FirstName), nameof(LastName), IsUnique = true)]
public class User
{
    public int Id { get; set; }
    
    [MaxLength(64)]
    public string FirstName { get; set; }

    [MaxLength(64)]
    public string LastName { get; set; }
}
```

<span data-ttu-id="0ffc8-197">針對 SQL Server，這會導致：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-197">For SQL Server, this results in:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

<span data-ttu-id="0ffc8-198">檔是由問題[#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-198">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="improved-query-translation-exceptions"></a><span data-ttu-id="0ffc8-199">改善的查詢轉譯例外狀況</span><span class="sxs-lookup"><span data-stu-id="0ffc8-199">Improved query translation exceptions</span></span>

<span data-ttu-id="0ffc8-200">我們會繼續改善查詢轉譯失敗時所產生的例外狀況訊息。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-200">We are continuing to improve the exception messages generated when query translation fails.</span></span> <span data-ttu-id="0ffc8-201">例如，此查詢會使用未對應的屬性 `IsSigned` ：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-201">For example, this query uses the unmapped property `IsSigned`:</span></span>

```CSharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

<span data-ttu-id="0ffc8-202">EF Core 將會擲回下列例外狀況，指出轉譯失敗，因為未 `IsSigned` 對應：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-202">EF Core will throw the following exception indicating that translation failed because `IsSigned` is not mapped:</span></span>

> <span data-ttu-id="0ffc8-203">未處理的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-203">Unhandled exception.</span></span> <span data-ttu-id="0ffc8-204">InvalidOperationException： LINQ 運算式的 DbSet <Artist> （）。Where （a => IsSigned） ' 無法轉譯。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-204">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.IsSigned)' could not be translated.</span></span> <span data-ttu-id="0ffc8-205">其他資訊：實體類型 ' 演出者 ' 上成員 ' IsSigned ' 的轉譯失敗。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-205">Additional information: Translation of member 'IsSigned' on entity type 'Artist' failed.</span></span> <span data-ttu-id="0ffc8-206">可能是指定的成員未對應。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-206">Possibly the specified member is not mapped.</span></span> <span data-ttu-id="0ffc8-207">請以可翻譯的形式重寫查詢，或將呼叫插入 Enumerable.asenumerable （）、AsAsyncEnumerable （）、ToList （）或 ToListAsync （），以明確地切換至用戶端評估。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-207">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="0ffc8-208">如需相關資訊，請參閱 https://go.microsoft.com/fwlink/?linkid=2101038 。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-208">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

<span data-ttu-id="0ffc8-209">同樣地，當嘗試使用與文化特性相關的語義來轉譯字串比較時，現在會產生更好的例外狀況訊息。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-209">Similarly, better exception messages are now generated when attempting to translate string comparisons with culture-dependent semantics.</span></span> <span data-ttu-id="0ffc8-210">例如，此查詢會嘗試使用 `StringComparison.CurrentCulture` ：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-210">For example, this query attempts to use `StringComparison.CurrentCulture`:</span></span>

```CSharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

<span data-ttu-id="0ffc8-211">EF Core 現在會擲回下列例外狀況：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-211">EF Core will now throw the following exception:</span></span>

> <span data-ttu-id="0ffc8-212">未處理的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-212">Unhandled exception.</span></span> <span data-ttu-id="0ffc8-213">InvalidOperationException： LINQ 運算式的 DbSet <Artist> （）。其中（a =>. Name. Equals （值： "the 獨角獸"，comparisonType： CurrentCulture）） ' 無法轉譯。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-213">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))' could not be translated.</span></span> <span data-ttu-id="0ffc8-214">其他資訊： ' string ' 的轉譯。不支援採用 ' StringComparison ' 引數的 Equals ' 方法。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-214">Additional information: Translation of 'string.Equals' method which takes 'StringComparison' argument is not supported.</span></span> <span data-ttu-id="0ffc8-215">如需相關資訊，請參閱 https://go.microsoft.com/fwlink/?linkid=2129535 。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-215">See https://go.microsoft.com/fwlink/?linkid=2129535 for more information.</span></span> <span data-ttu-id="0ffc8-216">請以可翻譯的形式重寫查詢，或將呼叫插入 Enumerable.asenumerable （）、AsAsyncEnumerable （）、ToList （）或 ToListAsync （），以明確地切換至用戶端評估。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-216">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="0ffc8-217">如需相關資訊，請參閱 https://go.microsoft.com/fwlink/?linkid=2101038 。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-217">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

### <a name="specify-transaction-id"></a><span data-ttu-id="0ffc8-218">指定交易識別碼</span><span class="sxs-lookup"><span data-stu-id="0ffc8-218">Specify transaction ID</span></span>

<span data-ttu-id="0ffc8-219">這項功能是由的「社區」提供 [@Marusyk](https://github.com/Marusyk) 。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-219">This feature was contributed from the community by [@Marusyk](https://github.com/Marusyk).</span></span> <span data-ttu-id="0ffc8-220">感謝您的貢獻！</span><span class="sxs-lookup"><span data-stu-id="0ffc8-220">Many thanks for the contribution!</span></span>

<span data-ttu-id="0ffc8-221">EF Core 會公開交易識別碼，以便在呼叫之間相互關聯。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-221">EF Core exposes a transaction ID for correlation of transactions across calls.</span></span> <span data-ttu-id="0ffc8-222">此識別碼通常會在交易啟動時由 EF Core 設定。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-222">This ID typically set by EF Core when a transaction is started.</span></span> <span data-ttu-id="0ffc8-223">如果應用程式改為啟動交易，則此功能可讓應用程式明確設定交易識別碼，讓它在使用的任何地方都能正確地相互關聯。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-223">If the application starts the transaction instead, then this feature allows the application to explicitly set the transaction ID so it is correlated correctly everywhere it is used.</span></span> <span data-ttu-id="0ffc8-224">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-224">For example:</span></span>

```CSharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a><span data-ttu-id="0ffc8-225">IPAddress 對應</span><span class="sxs-lookup"><span data-stu-id="0ffc8-225">IPAddress mapping</span></span>

<span data-ttu-id="0ffc8-226">這項功能是由的「社區」提供 [@ralmsdeveloper](https://github.com/ralmsdeveloper) 。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-226">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="0ffc8-227">感謝您的貢獻！</span><span class="sxs-lookup"><span data-stu-id="0ffc8-227">Many thanks for the contribution!</span></span>

<span data-ttu-id="0ffc8-228">標準 .NET [IPAddress 類別](/dotnet/api/system.net.ipaddress)現在會自動對應至尚未具備原生支援之資料庫的字串資料行。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-228">The standard .NET [IPAddress class](/dotnet/api/system.net.ipaddress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="0ffc8-229">例如，請考慮對應此實體類型：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-229">For example, consider mapping this entity type:</span></span>

```CSharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

<span data-ttu-id="0ffc8-230">在 SQL Server 上，這會導致遷移建立下列資料表：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-230">On SQL Server, this will result in Migrations creating the following table:</span></span>

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
``` 

<span data-ttu-id="0ffc8-231">然後，實體可以透過正常方式新增：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-231">Entities can then be added in the normal way:</span></span>

```CSharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
``` 

<span data-ttu-id="0ffc8-232">而產生的 SQL 會插入正規化的 IPv4 或 IPv6 位址：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-232">And the resulting SQL will insert the normalized IPv4 or IPv6 address:</span></span>

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a><span data-ttu-id="0ffc8-233">在架構時排除 OnConfiguring</span><span class="sxs-lookup"><span data-stu-id="0ffc8-233">Exclude OnConfiguring when scaffolding</span></span>

<span data-ttu-id="0ffc8-234">從現有的資料庫 scaffold DbCoNtext 時，EF Core 預設會使用連接字串建立 OnConfiguring 多載，以便立即使用內容。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-234">When a DbContext is scaffolded from an existing database, EF Core by default creates an OnConfiguring overload with a connection string so that the context is immediately usable.</span></span> <span data-ttu-id="0ffc8-235">不過，如果您已經有部分類別具有 OnConfiguring，或如果您要以其他方式設定內容，這就不會很有用。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-235">However, this is not useful if you already have a partial class with OnConfiguring, or if you are configuring the context some other way.</span></span>

<span data-ttu-id="0ffc8-236">為了解決這個情況，現在可以指示樣板命令省略產生 OnConfiguring。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-236">To address this, the scaffolding commands can now be instructed to omit generation of OnConfiguring.</span></span> <span data-ttu-id="0ffc8-237">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-237">For example:</span></span>

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

<span data-ttu-id="0ffc8-238">或在 [套件管理員主控台] 中：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-238">Or in the Package Manager Console:</span></span>

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring 
``` 

<span data-ttu-id="0ffc8-239">請注意，我們建議使用[已命名的連接字串，並保護像是使用者秘密的儲存體](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets)。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-239">Note that we recommend using [a named connection string and secure storage like User Secrets](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).</span></span>

### <a name="translations-for-firstordefault-on-strings"></a><span data-ttu-id="0ffc8-240">字串上 FirstOrDefault 的翻譯</span><span class="sxs-lookup"><span data-stu-id="0ffc8-240">Translations for FirstOrDefault on strings</span></span>

<span data-ttu-id="0ffc8-241">這項功能是由的「社區」提供 [@dvoreckyaa](https://github.com/dvoreckyaa) 。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-241">This feature was contributed from the community by [@dvoreckyaa](https://github.com/dvoreckyaa).</span></span> <span data-ttu-id="0ffc8-242">感謝您的貢獻！</span><span class="sxs-lookup"><span data-stu-id="0ffc8-242">Many thanks for the contribution!</span></span>

<span data-ttu-id="0ffc8-243">現在會轉譯字串中字元的 FirstOrDefault 和類似運算子。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-243">FirstOrDefault and similar operators for characters in strings are now translated.</span></span> <span data-ttu-id="0ffc8-244">例如，此 LINQ 查詢：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-244">For example, this LINQ query:</span></span>

```CSharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

<span data-ttu-id="0ffc8-245">使用 SQL Server 時，將會轉譯為下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-245">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a><span data-ttu-id="0ffc8-246">簡化案例區塊</span><span class="sxs-lookup"><span data-stu-id="0ffc8-246">Simplify case blocks</span></span>

<span data-ttu-id="0ffc8-247">EF Core 現在會以案例區塊產生更佳的查詢。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-247">EF Core now generates better queries with CASE blocks.</span></span> <span data-ttu-id="0ffc8-248">例如，此 LINQ 查詢：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-248">For example, this LINQ query:</span></span> 

```CSharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

<span data-ttu-id="0ffc8-249">Was SQL Server 正式轉譯為：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-249">Was on SQL Server formally translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN (CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END = 0) AND CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
``` 

<span data-ttu-id="0ffc8-250">但現在已轉譯為：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-250">But is now translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
``` 

## <a name="preview-5"></a><span data-ttu-id="0ffc8-251">Preview 5</span><span class="sxs-lookup"><span data-stu-id="0ffc8-251">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="0ffc8-252">資料庫定序</span><span class="sxs-lookup"><span data-stu-id="0ffc8-252">Database collations</span></span>

<span data-ttu-id="0ffc8-253">現在，您可以在 EF 模型中指定資料庫的預設定序。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-253">The default collation for a database can now be specified in the EF model.</span></span> <span data-ttu-id="0ffc8-254">這會傳送至產生的遷移，以便在建立資料庫時設定定序。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-254">This will flow through to generated migrations to set the collation when the database is created.</span></span> <span data-ttu-id="0ffc8-255">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-255">For example:</span></span>

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="0ffc8-256">接著，遷移會產生下列內容，以在 SQL Server 上建立資料庫：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-256">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="0ffc8-257">您也可以指定用於特定資料庫資料行的定序。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-257">The collation to use for specific database columns can also be specified.</span></span> <span data-ttu-id="0ffc8-258">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-258">For example:</span></span>

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="0ffc8-259">對於未使用遷移的人員，定序現在會在 DbCoNtext 架構時，從資料庫進行反向工程。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-259">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="0ffc8-260">最後， `EF.Functions.Collate()` 允許使用不同定序的特定查詢。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-260">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span> <span data-ttu-id="0ffc8-261">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-261">For example:</span></span>

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="0ffc8-262">這會產生 SQL Server 的下列查詢：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-262">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="0ffc8-263">請注意，特定定序應謹慎使用，因為它們可能會對資料庫效能造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-263">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="0ffc8-264">檔是由問題[#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-264">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="0ffc8-265">IDesignTimeDbCoNtextFactory 中的流程引數</span><span class="sxs-lookup"><span data-stu-id="0ffc8-265">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="0ffc8-266">引數現在會從命令列流動到 `CreateDbContext` [IDesignTimeDbCoNtextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1)的方法中。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-266">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span></span> <span data-ttu-id="0ffc8-267">例如，若要指出這是開發組建， `dev` 可以在命令列上傳遞自訂引數（例如）：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-267">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can be passed on the command line:</span></span>

```
dotnet ef migrations add two --verbose --dev
``` 

<span data-ttu-id="0ffc8-268">這個引數接著會流入處理站，它可以用來控制內容的建立和初始化方式。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-268">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span> <span data-ttu-id="0ffc8-269">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-269">For example:</span></span>

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args) 
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="0ffc8-270">檔是由問題[#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-270">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="0ffc8-271">沒有識別解析的追蹤查詢</span><span class="sxs-lookup"><span data-stu-id="0ffc8-271">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="0ffc8-272">現在沒有任何追蹤查詢可以設定為執行識別解析。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-272">No-tracking queries can now be configured to perform identity resolution.</span></span> <span data-ttu-id="0ffc8-273">例如，下列查詢會針對每個貼文建立新的 Blog 實例，即使每個 Blog 都有相同的主要金鑰。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-273">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span> 

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="0ffc8-274">不過，通常會稍微變慢，而且一定會使用更多的記憶體，因此可以變更此查詢，以確保只會建立單一的 Blog 實例：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-274">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="0ffc8-275">請注意，這只適用于沒有追蹤的查詢，因為所有追蹤查詢都已經呈現此行為。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-275">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="0ffc8-276">此外，在遵循 API 審查之後， `PerformIdentityResolution` 語法將會變更。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-276">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span> <span data-ttu-id="0ffc8-277">請參閱[#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073)。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-277">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="0ffc8-278">檔是由問題[#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-278">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="0ffc8-279">儲存的（保存）計算資料行</span><span class="sxs-lookup"><span data-stu-id="0ffc8-279">Stored (persisted) computed columns</span></span>

<span data-ttu-id="0ffc8-280">大部分的資料庫都允許在計算後儲存計算的資料行值。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-280">Most databases allow computed column values to be stored after computation.</span></span> <span data-ttu-id="0ffc8-281">雖然這會佔用磁碟空間，但計算的資料行只會在 update 上計算一次，而不是每次抓取它的值。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-281">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span> <span data-ttu-id="0ffc8-282">這也可讓某些資料庫的資料行編制索引。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-282">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="0ffc8-283">EF Core 5.0 允許將計算資料行設定為已儲存。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-283">EF Core 5.0 allows computed columns to be configured as stored.</span></span> <span data-ttu-id="0ffc8-284">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-284">For example:</span></span>
 
```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="0ffc8-285">SQLite 計算資料行</span><span class="sxs-lookup"><span data-stu-id="0ffc8-285">SQLite computed columns</span></span>

<span data-ttu-id="0ffc8-286">EF Core 現在支援 SQLite 資料庫中的計算資料行。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-286">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="0ffc8-287">Preview 4</span><span class="sxs-lookup"><span data-stu-id="0ffc8-287">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="0ffc8-288">在模型中設定資料庫精確度/規模</span><span class="sxs-lookup"><span data-stu-id="0ffc8-288">Configure database precision/scale in model</span></span>

<span data-ttu-id="0ffc8-289">屬性的有效位數和小數位數現在可以使用模型產生器來指定。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-289">Precision and scale for a property can now be specified using the model builder.</span></span> <span data-ttu-id="0ffc8-290">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-290">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="0ffc8-291">精確度和小數位數仍然可以透過完整資料庫類型來設定，例如 "decimal （16，4）"。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-291">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span> 

<span data-ttu-id="0ffc8-292">檔是由問題[#527](https://github.com/dotnet/EntityFramework.Docs/issues/527)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-292">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="0ffc8-293">指定 SQL Server 索引填滿因數</span><span class="sxs-lookup"><span data-stu-id="0ffc8-293">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="0ffc8-294">在 SQL Server 上建立索引時，現在可以指定填滿因數。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-294">The fill factor can now be specified when creating an index on SQL Server.</span></span> <span data-ttu-id="0ffc8-295">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-295">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="0ffc8-296">Preview 3</span><span class="sxs-lookup"><span data-stu-id="0ffc8-296">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="0ffc8-297">篩選的包含</span><span class="sxs-lookup"><span data-stu-id="0ffc8-297">Filtered Include</span></span>

<span data-ttu-id="0ffc8-298">Include 方法現在支援篩選包含的實體。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-298">The Include method now supports filtering of the entities included.</span></span> <span data-ttu-id="0ffc8-299">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-299">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="0ffc8-300">此查詢將會連同每個相關聯的貼文一起傳回 blog，但只有在貼文標題包含「乳酪」時。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-300">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="0ffc8-301">Skip 和 Take 也可以用來減少包含的實體數目。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-301">Skip and Take can also be used to reduce the number of included entities.</span></span> <span data-ttu-id="0ffc8-302">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-302">For example:</span></span>
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="0ffc8-303">此查詢將會傳回包含每個 blog 中最多五篇文章的 blog。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-303">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="0ffc8-304">如需完整詳細資料，請參閱[包含檔](xref:core/querying/related-data#filtered-include)。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-304">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="0ffc8-305">導覽屬性的新 ModelBuilder API</span><span class="sxs-lookup"><span data-stu-id="0ffc8-305">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="0ffc8-306">導覽屬性主要是在[定義關聯](xref:core/modeling/relationships)性時設定。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-306">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span> <span data-ttu-id="0ffc8-307">不過，在 `Navigation` 導覽屬性需要其他設定的情況下，可以使用新的方法。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-307">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span> <span data-ttu-id="0ffc8-308">例如，當依照慣例找不到欄位時，設定導覽的支援欄位：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-308">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="0ffc8-309">請注意， `Navigation` API 不會取代關聯性設定。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-309">Note that the `Navigation` API does not replace relationship configuration.</span></span> <span data-ttu-id="0ffc8-310">相反地，它允許在已經探索或定義的關聯性中，額外設定導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-310">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="0ffc8-311">請參閱設定[導覽屬性檔](xref:core/modeling/relationships#configuring-navigation-properties)。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-311">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="0ffc8-312">命名空間和連接字串的新命令列參數</span><span class="sxs-lookup"><span data-stu-id="0ffc8-312">New command-line parameters for namespaces and connection strings</span></span> 

<span data-ttu-id="0ffc8-313">現在，遷移和架構可讓您在命令列上指定命名空間。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-313">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span> <span data-ttu-id="0ffc8-314">例如，若要對資料庫進行反向工程，將內容和模型類別放在不同的命名空間中：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-314">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span> 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="0ffc8-315">如需完整詳細資料，請參閱[遷移](xref:core/managing-schemas/migrations/index#namespaces)和[還原工程](xref:core/managing-schemas/scaffolding#directories-and-namespaces)檔。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-315">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="0ffc8-316">此外，連接字串現在可以傳遞至 `database-update` 命令：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-316">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="0ffc8-317">如需完整詳細資料，請參閱[工具檔](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update)。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-317">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="0ffc8-318">對等的參數也已新增至 VS 套件管理員主控台中使用的 PowerShell 命令。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-318">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="0ffc8-319">EnableDetailedErrors 已傳回</span><span class="sxs-lookup"><span data-stu-id="0ffc8-319">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="0ffc8-320">基於效能考慮，從資料庫讀取值時，EF 不會執行額外的 null 檢查。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-320">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span> <span data-ttu-id="0ffc8-321">當遇到未預期的 null 時，這可能會導致難以造成根本原因的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-321">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="0ffc8-322">使用 `EnableDetailedErrors` 會在查詢中加入額外的 null 檢查，如此一來，在小型的效能額外負荷下，這些錯誤就會更容易追蹤到根本原因。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-322">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>  

<span data-ttu-id="0ffc8-323">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-323">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="0ffc8-324">檔是由問題[#955](https://github.com/dotnet/EntityFramework.Docs/issues/955)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-324">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="0ffc8-325">Cosmos 分割區索引鍵</span><span class="sxs-lookup"><span data-stu-id="0ffc8-325">Cosmos partition keys</span></span>

<span data-ttu-id="0ffc8-326">您現在可以在查詢中指定要用於給定查詢的分割區索引鍵。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-326">The partition key to use for a given query can now be specified in the query.</span></span> <span data-ttu-id="0ffc8-327">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-327">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="0ffc8-328">檔是由問題[#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-328">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="0ffc8-329">支援 SQL Server DATALENGTH 函數</span><span class="sxs-lookup"><span data-stu-id="0ffc8-329">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="0ffc8-330">這可使用新的方法來存取 `EF.Functions.DataLength` 。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-330">This can be accessed using the new `EF.Functions.DataLength` method.</span></span> <span data-ttu-id="0ffc8-331">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-331">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a><span data-ttu-id="0ffc8-332">Preview 2</span><span class="sxs-lookup"><span data-stu-id="0ffc8-332">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="0ffc8-333">使用 c # 屬性指定屬性支援欄位</span><span class="sxs-lookup"><span data-stu-id="0ffc8-333">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="0ffc8-334">C # 屬性現在可以用來指定屬性的支援欄位。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-334">A C# attribute can now be used to specify the backing field for a property.</span></span> <span data-ttu-id="0ffc8-335">即使無法自動找到支援欄位，此屬性仍然可以讓 EF Core 仍會如往常般寫入和讀取支援欄位。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-335">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span> <span data-ttu-id="0ffc8-336">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-336">For example:</span></span>

```CSharp
public class Blog
{
    private string _mainTitle;

    public int Id { get; set; }

    [BackingField(nameof(_mainTitle))]
    public string Title
    {
        get => _mainTitle;
        set => _mainTitle = value;
    }
}
```

<span data-ttu-id="0ffc8-337">檔是由問題[#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-337">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="0ffc8-338">完成鑒別子對應</span><span class="sxs-lookup"><span data-stu-id="0ffc8-338">Complete discriminator mapping</span></span>

<span data-ttu-id="0ffc8-339">EF Core 針對[繼承階層的 TPH 對應](/ef/core/modeling/inheritance)使用鑒別子資料行。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-339">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span> <span data-ttu-id="0ffc8-340">只要 EF Core 知道鑒別子的所有可能值，就可能會有一些效能增強功能。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-340">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span> <span data-ttu-id="0ffc8-341">EF Core 5.0 現在會實行這些增強功能。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-341">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="0ffc8-342">例如，舊版的 EF Core 一律會針對傳回階層中所有類型的查詢產生此 SQL：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-342">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="0ffc8-343">當設定完整的鑒別子對應時，EF Core 5.0 現在會產生下列內容：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-343">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="0ffc8-344">這會是從 preview 3 開始的預設行為。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-344">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="0ffc8-345">Microsoft 中的效能改進。 Sqlite</span><span class="sxs-lookup"><span data-stu-id="0ffc8-345">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="0ffc8-346">我們對 SQLIte 進行了兩項效能改善：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-346">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="0ffc8-347">藉由使用 SqliteBlob 和資料流程，以 GetBytes、GetChars 和 GetTextReader 來抓取二進位和字串資料現在更有效率。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-347">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="0ffc8-348">SqliteConnection 的初始化現在是延遲的。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-348">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="0ffc8-349">這些改良功能位於 ADO.NET 的 Microsoft. Sqlite 提供者，因此也會改善 EF Core 之外的效能。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-349">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="0ffc8-350">Preview 1</span><span class="sxs-lookup"><span data-stu-id="0ffc8-350">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="0ffc8-351">簡單記錄</span><span class="sxs-lookup"><span data-stu-id="0ffc8-351">Simple logging</span></span>

<span data-ttu-id="0ffc8-352">這項功能會 `Database.Log` 在 EF6 中新增類似的功能。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-352">This feature adds functionality similar to `Database.Log` in EF6.</span></span> <span data-ttu-id="0ffc8-353">也就是說，它提供簡單的方法，讓您不需要設定任何類型的外部記錄架構，即可從 EF Core 取得記錄。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-353">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="0ffc8-354">[2019 年12月5日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)會包含初稿檔。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-354">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="0ffc8-355">其他檔則是由問題[#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-355">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="0ffc8-356">取得產生之 SQL 的簡單方式</span><span class="sxs-lookup"><span data-stu-id="0ffc8-356">Simple way to get generated SQL</span></span>

<span data-ttu-id="0ffc8-357">EF Core 5.0 引進了 `ToQueryString` 擴充方法，這會傳回在執行 LINQ 查詢時，EF Core 將產生的 SQL。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-357">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="0ffc8-358">初稿檔包含在[2020 年1月9日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-358">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="0ffc8-359">其他檔則是由問題[#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-359">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="0ffc8-360">使用 c # 屬性來表示實體沒有索引鍵</span><span class="sxs-lookup"><span data-stu-id="0ffc8-360">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="0ffc8-361">現在可以使用新的，將實體類型設定為沒有任何索引鍵 `KeylessAttribute` 。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-361">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span> <span data-ttu-id="0ffc8-362">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-362">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="0ffc8-363">檔是由問題[#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-363">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="0ffc8-364">連接或連接字串可以在初始化的 DbCoNtext 上變更</span><span class="sxs-lookup"><span data-stu-id="0ffc8-364">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="0ffc8-365">現在您可以更輕鬆地建立 DbCoNtext 實例，而不需要任何連接或連接字串。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-365">It is now easier to create a DbContext instance without any connection or connection string.</span></span> <span data-ttu-id="0ffc8-366">此外，連接或連接字串現在可以在內容實例上進行變化。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-366">Also, the connection or connection string can now be mutated on the context instance.</span></span> <span data-ttu-id="0ffc8-367">這項功能可讓相同的內容實例動態連接到不同的資料庫。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-367">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="0ffc8-368">檔是由問題[#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-368">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="0ffc8-369">變更追蹤 proxy</span><span class="sxs-lookup"><span data-stu-id="0ffc8-369">Change-tracking proxies</span></span>

<span data-ttu-id="0ffc8-370">EF Core 現在可以產生自動執行[INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1)和[INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1)的執行時間 proxy。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-370">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span> <span data-ttu-id="0ffc8-371">這些之後，實體屬性上的這些值會直接變更為 EF Core，避免需要掃描變更。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-371">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span> <span data-ttu-id="0ffc8-372">不過，proxy 會有自己的一組限制，因此不適合所有人。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-372">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="0ffc8-373">檔是由問題[#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-373">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="0ffc8-374">增強的調試視圖</span><span class="sxs-lookup"><span data-stu-id="0ffc8-374">Enhanced debug views</span></span>

<span data-ttu-id="0ffc8-375">「偵錯工具」是一種簡單的方式，可在偵錯工具發生問題時查看 EF Core 的內部。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-375">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span> <span data-ttu-id="0ffc8-376">模型的「偵錯工具」視圖已在一段時間前完成。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-376">A debug view for the Model was implemented some time ago.</span></span> <span data-ttu-id="0ffc8-377">針對 EF Core 5.0，我們讓模型視圖更容易閱讀，並在狀態管理員中為追蹤的實體新增新的 [偵錯工具]。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-377">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="0ffc8-378">[2019 年12月12日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)會包含初稿檔。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-378">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="0ffc8-379">其他檔則是由問題[#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-379">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="0ffc8-380">改善資料庫 null 語義的處理</span><span class="sxs-lookup"><span data-stu-id="0ffc8-380">Improved handling of database null semantics</span></span>

<span data-ttu-id="0ffc8-381">關係資料庫通常會將 Null 視為未知的值，因此不等於任何其他 Null。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-381">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span> <span data-ttu-id="0ffc8-382">雖然 c # 會將 null 視為已定義的值，但其比較會等於任何其他 null。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-382">While C# treats null as a defined value, which compares equal to any other null.</span></span> <span data-ttu-id="0ffc8-383">EF Core 預設會轉譯查詢，使其使用 c # null 語義。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-383">EF Core by default translates queries so that they use C# null semantics.</span></span> <span data-ttu-id="0ffc8-384">EF Core 5.0 大幅提升了這些翻譯的效率。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-384">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="0ffc8-385">檔是由問題[#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-385">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="0ffc8-386">索引子屬性</span><span class="sxs-lookup"><span data-stu-id="0ffc8-386">Indexer properties</span></span>

<span data-ttu-id="0ffc8-387">EF Core 5.0 支援 c # 索引子屬性的對應。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-387">EF Core 5.0 supports mapping of C# indexer properties.</span></span> <span data-ttu-id="0ffc8-388">這些屬性可讓實體作為屬性包，其中的資料行會對應到包中的命名屬性。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-388">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="0ffc8-389">檔是由問題[#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-389">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="0ffc8-390">列舉對應的檢查條件約束產生</span><span class="sxs-lookup"><span data-stu-id="0ffc8-390">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="0ffc8-391">EF Core 5.0 遷移現在可以產生 enum 屬性對應的檢查條件約束。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-391">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span> <span data-ttu-id="0ffc8-392">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-392">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="0ffc8-393">檔是由問題[#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-393">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="0ffc8-394">IsRelational</span><span class="sxs-lookup"><span data-stu-id="0ffc8-394">IsRelational</span></span>

<span data-ttu-id="0ffc8-395">`IsRelational`除了現有的、和之外，還加入了新的方法 `IsSqlServer` `IsSqlite` `IsInMemory` 。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-395">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span> <span data-ttu-id="0ffc8-396">這個方法可以用來測試 DbCoNtext 是否使用任何關係資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-396">This method can be used to test if the DbContext is using any relational database provider.</span></span> <span data-ttu-id="0ffc8-397">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-397">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="0ffc8-398">檔是由問題[#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-398">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="0ffc8-399">使用 Etag Cosmos 開放式平行存取</span><span class="sxs-lookup"><span data-stu-id="0ffc8-399">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="0ffc8-400">Azure Cosmos DB 資料庫提供者現在支援使用 Etag 的開放式平行存取。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-400">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span> <span data-ttu-id="0ffc8-401">使用 OnModelCreating 中的模型產生器來設定 ETag：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-401">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="0ffc8-402">接著，SaveChanges 會擲回 `DbUpdateConcurrencyException` 並行衝突，以[處理](https://docs.microsoft.com/ef/core/saving/concurrency)以執行重試等。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-402">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](https://docs.microsoft.com/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="0ffc8-403">檔是由問題[#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-403">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="0ffc8-404">更多日期時間結構的查詢翻譯</span><span class="sxs-lookup"><span data-stu-id="0ffc8-404">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="0ffc8-405">包含新日期時間結構的查詢現在已轉譯。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-405">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="0ffc8-406">此外，現在已對應下列 SQL Server 函式：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-406">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="0ffc8-407">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="0ffc8-407">DateDiffWeek</span></span>
* <span data-ttu-id="0ffc8-408">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="0ffc8-408">DateFromParts</span></span>

<span data-ttu-id="0ffc8-409">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-409">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="0ffc8-410">檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-410">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="0ffc8-411">更多位元組陣列結構的查詢翻譯</span><span class="sxs-lookup"><span data-stu-id="0ffc8-411">Query translations for more byte array constructs</span></span>

<span data-ttu-id="0ffc8-412">在 byte [] 屬性上使用 Contains、Length、Enumerable.sequenceequal 等的查詢現在會轉譯為 SQL。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-412">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="0ffc8-413">[2019 年12月5日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)會包含初稿檔。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-413">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="0ffc8-414">其他檔則是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-414">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="0ffc8-415">反向的查詢轉譯</span><span class="sxs-lookup"><span data-stu-id="0ffc8-415">Query translation for Reverse</span></span>

<span data-ttu-id="0ffc8-416">使用 `Reverse` 的查詢現在已轉譯。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-416">Queries using `Reverse` are now translated.</span></span> <span data-ttu-id="0ffc8-417">例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-417">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="0ffc8-418">檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-418">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="0ffc8-419">位運算子的查詢轉譯</span><span class="sxs-lookup"><span data-stu-id="0ffc8-419">Query translation for bitwise operators</span></span>

<span data-ttu-id="0ffc8-420">使用位運算子的查詢現在會在更多情況下轉譯，例如：</span><span class="sxs-lookup"><span data-stu-id="0ffc8-420">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="0ffc8-421">檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-421">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="0ffc8-422">Cosmos 上字串的查詢轉譯</span><span class="sxs-lookup"><span data-stu-id="0ffc8-422">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="0ffc8-423">使用 Azure Cosmos DB 提供者時，現在會轉譯使用字串方法 Contains、StartsWith 和 EndsWith 的查詢。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-423">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="0ffc8-424">檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="0ffc8-424">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
