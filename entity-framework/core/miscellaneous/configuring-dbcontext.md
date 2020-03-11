---
title: 設定 DbCoNtext-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d7a22b5a-4c5b-4e3b-9897-4d7320fcd13f
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: 3ab90d46b7a4476044e5ea38eaf04f995708e7bf
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416666"
---
# <a name="configuring-a-dbcontext"></a><span data-ttu-id="b795d-102">設定 DbContext</span><span class="sxs-lookup"><span data-stu-id="b795d-102">Configuring a DbContext</span></span>

<span data-ttu-id="b795d-103">本文說明透過 `DbContextOptions` 設定 `DbContext` 的基本模式，以使用特定的 EF Core 提供者和選擇性的行為來連接到資料庫。</span><span class="sxs-lookup"><span data-stu-id="b795d-103">This article shows basic patterns for configuring a `DbContext` via a `DbContextOptions` to connect to a database using a specific EF Core provider and optional behaviors.</span></span>

## <a name="design-time-dbcontext-configuration"></a><span data-ttu-id="b795d-104">設計階段 DbCoNtext 設定</span><span class="sxs-lookup"><span data-stu-id="b795d-104">Design-time DbContext configuration</span></span>

<span data-ttu-id="b795d-105">EF Core 設計階段工具（例如[遷移](xref:core/managing-schemas/migrations/index)）必須能夠探索並建立 `DbContext` 類型的工作實例，才能收集應用程式實體類型的詳細資料，以及它們如何對應到資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="b795d-105">EF Core design-time tools such as [migrations](xref:core/managing-schemas/migrations/index) need to be able to discover and create a working instance of a `DbContext` type in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="b795d-106">此程式可以是自動的，只要工具可以輕鬆地建立 `DbContext`，就會以類似于執行時間設定的方式來進行設定。</span><span class="sxs-lookup"><span data-stu-id="b795d-106">This process can be automatic as long as the tool can easily create the `DbContext` in such a way that it will be configured similarly to how it would be configured at run-time.</span></span>

<span data-ttu-id="b795d-107">雖然提供必要設定資訊給 `DbContext` 的任何模式都可以在執行時間運作，但是在設計階段需要使用 `DbContext` 的工具只能使用有限數目的模式。</span><span class="sxs-lookup"><span data-stu-id="b795d-107">While any pattern that provides the necessary configuration information to the `DbContext` can work at run-time, tools that require using a `DbContext` at design-time can only work with a limited number of patterns.</span></span> <span data-ttu-id="b795d-108">在[設計階段內容建立](xref:core/miscellaneous/cli/dbcontext-creation)一節中，將會更詳細地說明這些功能。</span><span class="sxs-lookup"><span data-stu-id="b795d-108">These are covered in more detail in the [Design-Time Context Creation](xref:core/miscellaneous/cli/dbcontext-creation) section.</span></span>

## <a name="configuring-dbcontextoptions"></a><span data-ttu-id="b795d-109">設定 DbCoNtextOptions</span><span class="sxs-lookup"><span data-stu-id="b795d-109">Configuring DbContextOptions</span></span>

<span data-ttu-id="b795d-110">`DbContext` 必須有 `DbContextOptions` 的實例，才能執行任何工作。</span><span class="sxs-lookup"><span data-stu-id="b795d-110">`DbContext` must have an instance of `DbContextOptions` in order to perform any work.</span></span> <span data-ttu-id="b795d-111">`DbContextOptions` 實例會攜帶如下的設定資訊：</span><span class="sxs-lookup"><span data-stu-id="b795d-111">The `DbContextOptions` instance carries configuration information such as:</span></span>

- <span data-ttu-id="b795d-112">要使用的資料庫提供者，通常是藉由叫用方法（例如 `UseSqlServer` 或 `UseSqlite`）來選取。</span><span class="sxs-lookup"><span data-stu-id="b795d-112">The database provider to use, typically selected by invoking a method such as `UseSqlServer` or `UseSqlite`.</span></span> <span data-ttu-id="b795d-113">這些擴充方法需要對應的提供者封裝，例如 `Microsoft.EntityFrameworkCore.SqlServer` 或 `Microsoft.EntityFrameworkCore.Sqlite`。</span><span class="sxs-lookup"><span data-stu-id="b795d-113">These extension methods require the corresponding provider package, such as `Microsoft.EntityFrameworkCore.SqlServer` or `Microsoft.EntityFrameworkCore.Sqlite`.</span></span> <span data-ttu-id="b795d-114">這些方法會在 `Microsoft.EntityFrameworkCore` 命名空間中定義。</span><span class="sxs-lookup"><span data-stu-id="b795d-114">The methods are defined in the `Microsoft.EntityFrameworkCore` namespace.</span></span>
- <span data-ttu-id="b795d-115">資料庫實例的任何必要連接字串或識別碼，通常會當做引數傳遞給上述提供者選取方法</span><span class="sxs-lookup"><span data-stu-id="b795d-115">Any necessary connection string or identifier of the database instance, typically passed as an argument to the provider selection method mentioned above</span></span>
- <span data-ttu-id="b795d-116">任何提供者層級的選擇性行為選取器，通常也會在對提供者選取方法的呼叫內進行連結</span><span class="sxs-lookup"><span data-stu-id="b795d-116">Any provider-level optional behavior selectors, typically also chained inside the call to the provider selection method</span></span>
- <span data-ttu-id="b795d-117">任何一般 EF Core 行為選取器，通常會在提供者選取器方法之後或之前連結</span><span class="sxs-lookup"><span data-stu-id="b795d-117">Any general EF Core behavior selectors, typically chained after or before the provider selector method</span></span>

<span data-ttu-id="b795d-118">下列範例會將 `DbContextOptions` 設定為使用 SQL Server 提供者、`connectionString` 變數中包含的連接、提供者層級命令逾時，以及 EF Core 行為選取器，讓所有查詢都在預設情況下執行 `DbContext`[不追蹤](xref:core/querying/tracking#no-tracking-queries)：</span><span class="sxs-lookup"><span data-stu-id="b795d-118">The following example configures the `DbContextOptions` to use the SQL Server provider, a connection contained in the `connectionString` variable, a provider-level command timeout, and an EF Core behavior selector that makes all queries executed in the `DbContext` [no-tracking](xref:core/querying/tracking#no-tracking-queries) by default:</span></span>

``` csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]  
> <span data-ttu-id="b795d-119">上述提供者選取器方法和其他行為選取器方法是 `DbContextOptions` 或提供者特定選項類別上的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="b795d-119">Provider selector methods and other behavior selector methods mentioned above are extension methods on `DbContextOptions` or provider-specific option classes.</span></span> <span data-ttu-id="b795d-120">為了能夠存取這些擴充方法，您可能需要在範圍中具有命名空間（通常是 `Microsoft.EntityFrameworkCore`），並在專案中包含額外的封裝相依性。</span><span class="sxs-lookup"><span data-stu-id="b795d-120">In order to have access to these extension methods you may need to have a namespace (typically `Microsoft.EntityFrameworkCore`) in scope and include additional package dependencies in the project.</span></span>

<span data-ttu-id="b795d-121">`DbContextOptions` 可以藉由覆寫 `OnConfiguring` 方法，或從外部透過函式引數來提供給 `DbContext`。</span><span class="sxs-lookup"><span data-stu-id="b795d-121">The `DbContextOptions` can be supplied to the `DbContext` by overriding the `OnConfiguring` method or externally via a constructor argument.</span></span>

<span data-ttu-id="b795d-122">如果同時使用這兩種方法，就會最後套用 `OnConfiguring`，而且可以覆寫提供給此函式引數的選項。</span><span class="sxs-lookup"><span data-stu-id="b795d-122">If both are used, `OnConfiguring` is applied last and can overwrite options supplied to the constructor argument.</span></span>

### <a name="constructor-argument"></a><span data-ttu-id="b795d-123">函數引數</span><span class="sxs-lookup"><span data-stu-id="b795d-123">Constructor argument</span></span>

<span data-ttu-id="b795d-124">您的函式可以直接接受 `DbContextOptions`，如下所示：</span><span class="sxs-lookup"><span data-stu-id="b795d-124">Your constructor can simply accept a `DbContextOptions` as follows:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
        : base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

> [!TIP]  
> <span data-ttu-id="b795d-125">DbCoNtext 的基底函式也會接受非泛型版本的 `DbContextOptions`，但不建議針對具有多個內容類型的應用程式使用非泛型版本。</span><span class="sxs-lookup"><span data-stu-id="b795d-125">The base constructor of DbContext also accepts the non-generic version of `DbContextOptions`, but using the non-generic version is not recommended for applications with multiple context types.</span></span>

<span data-ttu-id="b795d-126">您的應用程式現在可以在具現化內容時傳遞 `DbContextOptions`，如下所示：</span><span class="sxs-lookup"><span data-stu-id="b795d-126">Your application can now pass the `DbContextOptions` when instantiating a context, as follows:</span></span>

``` csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a><span data-ttu-id="b795d-127">OnConfiguring</span><span class="sxs-lookup"><span data-stu-id="b795d-127">OnConfiguring</span></span>

<span data-ttu-id="b795d-128">您也可以在內容本身中初始化 `DbContextOptions`。</span><span class="sxs-lookup"><span data-stu-id="b795d-128">You can also initialize the `DbContextOptions` within the context itself.</span></span> <span data-ttu-id="b795d-129">雖然您可以使用這項技術來進行基本設定，但您通常還是需要從外部取得特定的設定詳細資料，例如資料庫連接字串。</span><span class="sxs-lookup"><span data-stu-id="b795d-129">While you can use this technique for basic configuration, you will typically still need to get certain configuration details from the outside, e.g. a database connection string.</span></span> <span data-ttu-id="b795d-130">這可以透過設定 API 或任何其他方式來完成。</span><span class="sxs-lookup"><span data-stu-id="b795d-130">This can be done with a configuration API or any other means.</span></span>

<span data-ttu-id="b795d-131">若要在內容中初始化 `DbContextOptions`，請覆寫 `OnConfiguring` 方法，並在提供的 `DbContextOptionsBuilder`上呼叫方法：</span><span class="sxs-lookup"><span data-stu-id="b795d-131">To initialize `DbContextOptions` within the context, override the `OnConfiguring` method and call the methods on the provided `DbContextOptionsBuilder`:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlite("Data Source=blog.db");
    }
}
```

<span data-ttu-id="b795d-132">應用程式可以直接具現化這類內容，而不需要將任何專案傳遞至其「其」</span><span class="sxs-lookup"><span data-stu-id="b795d-132">An application can simply instantiate such a context without passing anything to its constructor:</span></span>

``` csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> <span data-ttu-id="b795d-133">除非測試是以完整資料庫為目標，否則此方法不會使其本身進行測試。</span><span class="sxs-lookup"><span data-stu-id="b795d-133">This approach does not lend itself to testing, unless the tests target the full database.</span></span>

### <a name="using-dbcontext-with-dependency-injection"></a><span data-ttu-id="b795d-134">搭配使用 DbCoNtext 與相依性插入</span><span class="sxs-lookup"><span data-stu-id="b795d-134">Using DbContext with dependency injection</span></span>

<span data-ttu-id="b795d-135">EF Core 支援搭配使用 `DbContext` 與相依性插入容器。</span><span class="sxs-lookup"><span data-stu-id="b795d-135">EF Core supports using `DbContext` with a dependency injection container.</span></span> <span data-ttu-id="b795d-136">您可以使用 `AddDbContext<TContext>` 方法，將您的 DbCoNtext 類型新增至服務容器。</span><span class="sxs-lookup"><span data-stu-id="b795d-136">Your DbContext type can be added to the service container by using the `AddDbContext<TContext>` method.</span></span>

<span data-ttu-id="b795d-137">`AddDbContext<TContext>` 會讓您的 DbCoNtext 類型、`TContext`和對應的 `DbContextOptions<TContext>` 可從服務容器中進行插入。</span><span class="sxs-lookup"><span data-stu-id="b795d-137">`AddDbContext<TContext>` will make both your DbContext type, `TContext`, and the corresponding `DbContextOptions<TContext>` available for injection from the service container.</span></span>

<span data-ttu-id="b795d-138">如需有關相依性插入的詳細資訊，請參閱下方的進一步[閱讀](#more-reading)。</span><span class="sxs-lookup"><span data-stu-id="b795d-138">See [more reading](#more-reading) below for additional information on dependency injection.</span></span>

<span data-ttu-id="b795d-139">將 `DbContext` 加入至相依性插入：</span><span class="sxs-lookup"><span data-stu-id="b795d-139">Adding the `DbContext` to dependency injection:</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

<span data-ttu-id="b795d-140">這需要將 DbCoNtext 類型的函式[引數](#constructor-argument)加入接受 `DbContextOptions<TContext>`。</span><span class="sxs-lookup"><span data-stu-id="b795d-140">This requires adding a [constructor argument](#constructor-argument) to your DbContext type that accepts `DbContextOptions<TContext>`.</span></span>

<span data-ttu-id="b795d-141">內容代碼：</span><span class="sxs-lookup"><span data-stu-id="b795d-141">Context code:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

<span data-ttu-id="b795d-142">應用程式代碼（在 ASP.NET Core 中）：</span><span class="sxs-lookup"><span data-stu-id="b795d-142">Application code (in ASP.NET Core):</span></span>

``` csharp
public class MyController
{
    private readonly BloggingContext _context;

    public MyController(BloggingContext context)
    {
      _context = context;
    }

    ...
}
```

<span data-ttu-id="b795d-143">應用程式代碼（直接使用 ServiceProvider，較不常見）：</span><span class="sxs-lookup"><span data-stu-id="b795d-143">Application code (using ServiceProvider directly, less common):</span></span>

``` csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="avoiding-dbcontext-threading-issues"></a><span data-ttu-id="b795d-144">避免 DbCoNtext 執行緒問題</span><span class="sxs-lookup"><span data-stu-id="b795d-144">Avoiding DbContext threading issues</span></span>

<span data-ttu-id="b795d-145">Entity Framework Core 不支援在相同的 `DbContext` 實例上執行多個平行作業。</span><span class="sxs-lookup"><span data-stu-id="b795d-145">Entity Framework Core does not support multiple parallel operations being run on the same `DbContext` instance.</span></span> <span data-ttu-id="b795d-146">這包括平行執行非同步查詢，以及從多個執行緒進行任何明確的並行使用。</span><span class="sxs-lookup"><span data-stu-id="b795d-146">This includes both parallel execution of async queries and any explicit concurrent use from multiple threads.</span></span> <span data-ttu-id="b795d-147">因此，一律會立即 `await` 非同步呼叫，或針對平行執行的作業使用個別的 `DbContext` 實例。</span><span class="sxs-lookup"><span data-stu-id="b795d-147">Therefore, always `await` async calls immediately, or use separate `DbContext` instances for operations that execute in parallel.</span></span>

<span data-ttu-id="b795d-148">當 EF Core 偵測到同時嘗試使用 `DbContext` 實例時，您會看到一則訊息的 `InvalidOperationException`，如下所示：</span><span class="sxs-lookup"><span data-stu-id="b795d-148">When EF Core detects an attempt to use a `DbContext` instance concurrently, you'll see an `InvalidOperationException` with a message like this:</span></span>

> <span data-ttu-id="b795d-149">在先前的作業完成之前，已在此內容上啟動第二個作業。</span><span class="sxs-lookup"><span data-stu-id="b795d-149">A second operation started on this context before a previous operation completed.</span></span> <span data-ttu-id="b795d-150">這通常是因為不同的執行緒使用相同的 DbCoNtext 實例而造成，但是實例成員不一定是安全線程。</span><span class="sxs-lookup"><span data-stu-id="b795d-150">This is usually caused by different threads using the same instance of DbContext, however instance members are not guaranteed to be thread safe.</span></span>

<span data-ttu-id="b795d-151">未偵測到平行存取時，可能會導致未定義的行為、應用程式當機和資料損毀。</span><span class="sxs-lookup"><span data-stu-id="b795d-151">When concurrent access goes undetected, it can result in undefined behavior, application crashes and data corruption.</span></span>

<span data-ttu-id="b795d-152">在相同的 `DbContext` 實例上，可能會不小心造成平行存取的常見錯誤：</span><span class="sxs-lookup"><span data-stu-id="b795d-152">There are common mistakes that can inadvertently cause concurrent access on the same `DbContext` instance:</span></span>

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a><span data-ttu-id="b795d-153">忘了等待非同步作業完成，再于相同的 DbCoNtext 上啟動任何其他作業</span><span class="sxs-lookup"><span data-stu-id="b795d-153">Forgetting to await the completion of an asynchronous operation before starting any other operation on the same DbContext</span></span>

<span data-ttu-id="b795d-154">非同步方法可讓 EF Core 起始以非封鎖方式存取資料庫的作業。</span><span class="sxs-lookup"><span data-stu-id="b795d-154">Asynchronous methods enable EF Core to initiate operations that access the database in a non-blocking way.</span></span> <span data-ttu-id="b795d-155">但是，如果呼叫端不等待其中一種方法完成，並繼續在 `DbContext`上執行其他作業，則 `DbContext` 的狀態可以是（而且很可能會）損毀。</span><span class="sxs-lookup"><span data-stu-id="b795d-155">But if a caller does not await the completion of one of these methods, and proceeds to perform other operations on the `DbContext`, the state of the `DbContext` can be, (and very likely will be) corrupted.</span></span>

<span data-ttu-id="b795d-156">一律等待立即 EF Core 非同步方法。</span><span class="sxs-lookup"><span data-stu-id="b795d-156">Always await EF Core asynchronous methods immediately.</span></span>  

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a><span data-ttu-id="b795d-157">透過相依性插入，在多個執行緒之間隱含共用 DbCoNtext 實例</span><span class="sxs-lookup"><span data-stu-id="b795d-157">Implicitly sharing DbContext instances across multiple threads via dependency injection</span></span>

<span data-ttu-id="b795d-158">[`AddDbContext`](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)擴充方法預設會註冊具有[限定範圍存留期](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)的 `DbContext` 類型。</span><span class="sxs-lookup"><span data-stu-id="b795d-158">The [`AddDbContext`](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) extension method registers `DbContext` types with a [scoped lifetime](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes) by default.</span></span>

<span data-ttu-id="b795d-159">這是 ASP.NET Core 應用程式中的平行存取問題的安全，因為在指定的時間內只有一個執行緒執行每個用戶端要求，而且每個要求都會取得個別的相依性插入範圍（因此是個別的 `DbContext` 實例）。</span><span class="sxs-lookup"><span data-stu-id="b795d-159">This is safe from concurrent access issues in ASP.NET Core applications because there is only one thread executing each client request at a given time, and because each request gets a separate dependency injection scope (and therefore a separate `DbContext` instance).</span></span>

<span data-ttu-id="b795d-160">不過，任何明確地以平行方式執行多個執行緒的程式碼，都應該確保不會同時存取 `DbContext` 實例。</span><span class="sxs-lookup"><span data-stu-id="b795d-160">However any code that explicitly executes multiple threads in parallel should ensure that `DbContext` instances aren't ever accessed concurrently.</span></span>

<span data-ttu-id="b795d-161">使用相依性插入，您可以將內容註冊為已設定範圍，並建立每個執行緒的範圍（使用 `IServiceScopeFactory`），或將 `DbContext` 註冊為暫時性（使用接受 `ServiceLifetime` 參數的 `AddDbContext` 多載），藉此達成此目的。</span><span class="sxs-lookup"><span data-stu-id="b795d-161">Using dependency injection, this can be achieved by either registering the context as scoped and creating scopes (using `IServiceScopeFactory`) for each thread, or by registering the `DbContext` as transient (using the overload of `AddDbContext` which takes a `ServiceLifetime` parameter).</span></span>

## <a name="more-reading"></a><span data-ttu-id="b795d-162">閱讀更多</span><span class="sxs-lookup"><span data-stu-id="b795d-162">More reading</span></span>

- <span data-ttu-id="b795d-163">若要深入瞭解如何使用 DI，請參閱相依性[插入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="b795d-163">Read [Dependency Injection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) to learn more about using DI.</span></span>
- <span data-ttu-id="b795d-164">如需詳細資訊，請參閱[測試](testing/index.md)。</span><span class="sxs-lookup"><span data-stu-id="b795d-164">Read [Testing](testing/index.md) for more information.</span></span>
