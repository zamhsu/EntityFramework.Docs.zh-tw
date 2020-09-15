---
title: 設定 DbCoNtext-EF Core
description: 使用 Entity Framework Core 設定 DbcoNtext 的策略
author: rowanmiller
ms.date: 10/27/2016
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: 95b855c01b4b0b721eb91d53e0257295527ea44e
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071689"
---
# <a name="configuring-a-dbcontext"></a><span data-ttu-id="6c6dc-103">設定 DbContext</span><span class="sxs-lookup"><span data-stu-id="6c6dc-103">Configuring a DbContext</span></span>

<span data-ttu-id="6c6dc-104">本文說明 `DbContext` `DbContextOptions` 使用特定 EF Core 提供者和選擇性行為來設定 via 的基本模式，以連接到資料庫。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-104">This article shows basic patterns for configuring a `DbContext` via a `DbContextOptions` to connect to a database using a specific EF Core provider and optional behaviors.</span></span>

## <a name="design-time-dbcontext-configuration"></a><span data-ttu-id="6c6dc-105">設計階段 DbCoNtext 設定</span><span class="sxs-lookup"><span data-stu-id="6c6dc-105">Design-time DbContext configuration</span></span>

<span data-ttu-id="6c6dc-106">EF Core 的設計階段工具（例如 [遷移](xref:core/managing-schemas/migrations/index) ）必須能夠探索和建立型別的工作實例，才能 `DbContext` 收集有關應用程式實體類型的詳細資料，以及它們如何對應到資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-106">EF Core design-time tools such as [migrations](xref:core/managing-schemas/migrations/index) need to be able to discover and create a working instance of a `DbContext` type in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="6c6dc-107">這項程式可以是自動的，只要工具可以輕鬆地建立，就會以 `DbContext` 類似于執行時間設定的方式來設定。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-107">This process can be automatic as long as the tool can easily create the `DbContext` in such a way that it will be configured similarly to how it would be configured at run-time.</span></span>

<span data-ttu-id="6c6dc-108">雖然提供必要設定資訊的任何模式 `DbContext` 都可以在執行時間運作，但是在設計階段需要使用的工具只能使用 `DbContext` 有限數目的模式。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-108">While any pattern that provides the necessary configuration information to the `DbContext` can work at run-time, tools that require using a `DbContext` at design-time can only work with a limited number of patterns.</span></span> <span data-ttu-id="6c6dc-109">這些在 [設計階段內容建立](xref:core/miscellaneous/cli/dbcontext-creation) 一節中會更詳細地討論。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-109">These are covered in more detail in the [Design-Time Context Creation](xref:core/miscellaneous/cli/dbcontext-creation) section.</span></span>

## <a name="configuring-dbcontextoptions"></a><span data-ttu-id="6c6dc-110">設定 DbCoNtextOptions</span><span class="sxs-lookup"><span data-stu-id="6c6dc-110">Configuring DbContextOptions</span></span>

<span data-ttu-id="6c6dc-111">`DbContext` 必須具有的實例 `DbContextOptions` ，才能執行任何工作。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-111">`DbContext` must have an instance of `DbContextOptions` in order to perform any work.</span></span> <span data-ttu-id="6c6dc-112">此 `DbContextOptions` 實例會攜帶設定資訊，例如：</span><span class="sxs-lookup"><span data-stu-id="6c6dc-112">The `DbContextOptions` instance carries configuration information such as:</span></span>

- <span data-ttu-id="6c6dc-113">要使用的資料庫提供者，通常是透過叫用方法（例如或）來選取 `UseSqlServer` `UseSqlite` 。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-113">The database provider to use, typically selected by invoking a method such as `UseSqlServer` or `UseSqlite`.</span></span> <span data-ttu-id="6c6dc-114">這些擴充方法需要對應的提供者封裝，例如 `Microsoft.EntityFrameworkCore.SqlServer` 或 `Microsoft.EntityFrameworkCore.Sqlite` 。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-114">These extension methods require the corresponding provider package, such as `Microsoft.EntityFrameworkCore.SqlServer` or `Microsoft.EntityFrameworkCore.Sqlite`.</span></span> <span data-ttu-id="6c6dc-115">這些方法會在 `Microsoft.EntityFrameworkCore` 命名空間中定義。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-115">The methods are defined in the `Microsoft.EntityFrameworkCore` namespace.</span></span>
- <span data-ttu-id="6c6dc-116">資料庫實例的任何必要連接字串或識別碼，通常會做為引數傳遞至上面所述的提供者選取方法</span><span class="sxs-lookup"><span data-stu-id="6c6dc-116">Any necessary connection string or identifier of the database instance, typically passed as an argument to the provider selection method mentioned above</span></span>
- <span data-ttu-id="6c6dc-117">任何提供者層級的選用行為選取器，通常也會在對提供者選取方法的呼叫內連結</span><span class="sxs-lookup"><span data-stu-id="6c6dc-117">Any provider-level optional behavior selectors, typically also chained inside the call to the provider selection method</span></span>
- <span data-ttu-id="6c6dc-118">任何一般 EF Core 行為選取器，通常是在提供者選取器方法之後或之前連結</span><span class="sxs-lookup"><span data-stu-id="6c6dc-118">Any general EF Core behavior selectors, typically chained after or before the provider selector method</span></span>

<span data-ttu-id="6c6dc-119">下列範例會將設定 `DbContextOptions` 為使用 SQL Server 提供者、變數中包含的連接 `connectionString` 、提供者層級的命令逾時，以及讓所有查詢在 `DbContext` [no-tracking](xref:core/querying/tracking#no-tracking-queries)預設情況下執行的 EF Core 行為選取器：</span><span class="sxs-lookup"><span data-stu-id="6c6dc-119">The following example configures the `DbContextOptions` to use the SQL Server provider, a connection contained in the `connectionString` variable, a provider-level command timeout, and an EF Core behavior selector that makes all queries executed in the `DbContext` [no-tracking](xref:core/querying/tracking#no-tracking-queries) by default:</span></span>

``` csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]  
> <span data-ttu-id="6c6dc-120">上述提供者選取器方法和其他行為選取器方法，是 `DbContextOptions` 或提供者特定選項類別上的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-120">Provider selector methods and other behavior selector methods mentioned above are extension methods on `DbContextOptions` or provider-specific option classes.</span></span> <span data-ttu-id="6c6dc-121">若要能夠存取這些擴充方法，您可能需要命名空間 (通常 `Microsoft.EntityFrameworkCore`) 在範圍內，並在專案中包含額外的套件相依性。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-121">In order to have access to these extension methods you may need to have a namespace (typically `Microsoft.EntityFrameworkCore`) in scope and include additional package dependencies in the project.</span></span>

<span data-ttu-id="6c6dc-122">您 `DbContextOptions` 可以藉 `DbContext` 由覆寫 `OnConfiguring` 方法或從外部透過函式引數來提供。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-122">The `DbContextOptions` can be supplied to the `DbContext` by overriding the `OnConfiguring` method or externally via a constructor argument.</span></span>

<span data-ttu-id="6c6dc-123">如果使用這兩種 `OnConfiguring` 方法，則會最後套用，而且可以覆寫提供給函式引數的選項。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-123">If both are used, `OnConfiguring` is applied last and can overwrite options supplied to the constructor argument.</span></span>

### <a name="constructor-argument"></a><span data-ttu-id="6c6dc-124">函數引數</span><span class="sxs-lookup"><span data-stu-id="6c6dc-124">Constructor argument</span></span>

<span data-ttu-id="6c6dc-125">您的函式可以直接接受，如下 `DbContextOptions` 所示：</span><span class="sxs-lookup"><span data-stu-id="6c6dc-125">Your constructor can simply accept a `DbContextOptions` as follows:</span></span>

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
> <span data-ttu-id="6c6dc-126">DbCoNtext 的基底函式也會接受的非泛型版本 `DbContextOptions` ，但不建議針對具有多個內容類型的應用程式使用非泛型版本。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-126">The base constructor of DbContext also accepts the non-generic version of `DbContextOptions`, but using the non-generic version is not recommended for applications with multiple context types.</span></span>

<span data-ttu-id="6c6dc-127">您的應用程式現在可以在具現 `DbContextOptions` 化內容時傳遞，如下所示：</span><span class="sxs-lookup"><span data-stu-id="6c6dc-127">Your application can now pass the `DbContextOptions` when instantiating a context, as follows:</span></span>

``` csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a><span data-ttu-id="6c6dc-128">OnConfiguring</span><span class="sxs-lookup"><span data-stu-id="6c6dc-128">OnConfiguring</span></span>

<span data-ttu-id="6c6dc-129">您也可以在 `DbContextOptions` 內容本身內初始化。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-129">You can also initialize the `DbContextOptions` within the context itself.</span></span> <span data-ttu-id="6c6dc-130">雖然您可以使用這項技術進行基本設定，但通常還是需要從外部取得某些設定詳細資料，例如資料庫連接字串。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-130">While you can use this technique for basic configuration, you will typically still need to get certain configuration details from the outside, e.g. a database connection string.</span></span> <span data-ttu-id="6c6dc-131">這可以透過設定 API 或任何其他方式來完成。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-131">This can be done with a configuration API or any other means.</span></span>

<span data-ttu-id="6c6dc-132">若要在 `DbContextOptions` 內容中初始化，請覆寫 `OnConfiguring` 方法，並在提供的上呼叫方法 `DbContextOptionsBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="6c6dc-132">To initialize `DbContextOptions` within the context, override the `OnConfiguring` method and call the methods on the provided `DbContextOptionsBuilder`:</span></span>

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

<span data-ttu-id="6c6dc-133">應用程式可以直接將這類內容具現化，而不將任何內容傳遞至其函式：</span><span class="sxs-lookup"><span data-stu-id="6c6dc-133">An application can simply instantiate such a context without passing anything to its constructor:</span></span>

``` csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> <span data-ttu-id="6c6dc-134">除非測試以完整資料庫為目標，否則此方法不會自行測試。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-134">This approach does not lend itself to testing, unless the tests target the full database.</span></span>

### <a name="using-dbcontext-with-dependency-injection"></a><span data-ttu-id="6c6dc-135">使用 DbCoNtext 搭配相依性插入</span><span class="sxs-lookup"><span data-stu-id="6c6dc-135">Using DbContext with dependency injection</span></span>

<span data-ttu-id="6c6dc-136">EF Core 支援搭配相依性 `DbContext` 插入容器使用。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-136">EF Core supports using `DbContext` with a dependency injection container.</span></span> <span data-ttu-id="6c6dc-137">您可以使用方法，將您的 DbCoNtext 類型加入至服務容器 `AddDbContext<TContext>` 。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-137">Your DbContext type can be added to the service container by using the `AddDbContext<TContext>` method.</span></span>

<span data-ttu-id="6c6dc-138">`AddDbContext<TContext>` 會讓您的 DbCoNtext 類型、 `TContext` 和對應的 `DbContextOptions<TContext>` 可從服務容器中插入。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-138">`AddDbContext<TContext>` will make both your DbContext type, `TContext`, and the corresponding `DbContextOptions<TContext>` available for injection from the service container.</span></span>

<span data-ttu-id="6c6dc-139">如需有關相依性插入的詳細資訊，請參閱下面的 [詳細](#more-reading) 資訊。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-139">See [more reading](#more-reading) below for additional information on dependency injection.</span></span>

<span data-ttu-id="6c6dc-140">將加入 `DbContext` 至相依性插入：</span><span class="sxs-lookup"><span data-stu-id="6c6dc-140">Adding the `DbContext` to dependency injection:</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

<span data-ttu-id="6c6dc-141">這需要在接受的 DbCoNtext 型別中加入一個函式 [引數](#constructor-argument) `DbContextOptions<TContext>` 。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-141">This requires adding a [constructor argument](#constructor-argument) to your DbContext type that accepts `DbContextOptions<TContext>`.</span></span>

<span data-ttu-id="6c6dc-142">內容程式碼：</span><span class="sxs-lookup"><span data-stu-id="6c6dc-142">Context code:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

<span data-ttu-id="6c6dc-143">ASP.NET Core) 中的應用程式程式碼 (：</span><span class="sxs-lookup"><span data-stu-id="6c6dc-143">Application code (in ASP.NET Core):</span></span>

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

<span data-ttu-id="6c6dc-144">應用程式程式碼 (直接使用 ServiceProvider，較不常見) ：</span><span class="sxs-lookup"><span data-stu-id="6c6dc-144">Application code (using ServiceProvider directly, less common):</span></span>

``` csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="avoiding-dbcontext-threading-issues"></a><span data-ttu-id="6c6dc-145">避免 DbCoNtext 執行緒問題</span><span class="sxs-lookup"><span data-stu-id="6c6dc-145">Avoiding DbContext threading issues</span></span>

<span data-ttu-id="6c6dc-146">Entity Framework Core 不支援在相同實例上執行多個平行作業 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-146">Entity Framework Core does not support multiple parallel operations being run on the same `DbContext` instance.</span></span> <span data-ttu-id="6c6dc-147">這包括平行執行非同步查詢，以及從多個執行緒進行任何明確的並行使用。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-147">This includes both parallel execution of async queries and any explicit concurrent use from multiple threads.</span></span> <span data-ttu-id="6c6dc-148">因此，一律會 `await` 立即非同步呼叫，或 `DbContext` 針對平行執行的作業使用個別的實例。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-148">Therefore, always `await` async calls immediately, or use separate `DbContext` instances for operations that execute in parallel.</span></span>

<span data-ttu-id="6c6dc-149">當 EF Core 偵測到同時嘗試使用 `DbContext` 實例時，您會看到 `InvalidOperationException` 含有如下的訊息：</span><span class="sxs-lookup"><span data-stu-id="6c6dc-149">When EF Core detects an attempt to use a `DbContext` instance concurrently, you'll see an `InvalidOperationException` with a message like this:</span></span>

> <span data-ttu-id="6c6dc-150">在先前的作業完成之前，在此內容上啟動的第二個作業。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-150">A second operation started on this context before a previous operation completed.</span></span> <span data-ttu-id="6c6dc-151">這通常是由使用相同 DbCoNtext 實例的不同執行緒所造成，但是實例成員並不保證是安全線程。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-151">This is usually caused by different threads using the same instance of DbContext, however instance members are not guaranteed to be thread safe.</span></span>

<span data-ttu-id="6c6dc-152">未偵測到平行存取時，可能會導致未定義的行為、應用程式當機和資料損毀。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-152">When concurrent access goes undetected, it can result in undefined behavior, application crashes and data corruption.</span></span>

<span data-ttu-id="6c6dc-153">有一些常見的錯誤，可能會不慎導致相同實例上的平行存取 `DbContext` ：</span><span class="sxs-lookup"><span data-stu-id="6c6dc-153">There are common mistakes that can inadvertently cause concurrent access on the same `DbContext` instance:</span></span>

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a><span data-ttu-id="6c6dc-154">忘記等候非同步作業完成後，再于相同 DbCoNtext 上啟動任何其他作業</span><span class="sxs-lookup"><span data-stu-id="6c6dc-154">Forgetting to await the completion of an asynchronous operation before starting any other operation on the same DbContext</span></span>

<span data-ttu-id="6c6dc-155">非同步方法可讓 EF Core 起始以非封鎖方式存取資料庫的作業。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-155">Asynchronous methods enable EF Core to initiate operations that access the database in a non-blocking way.</span></span> <span data-ttu-id="6c6dc-156">但是，如果呼叫端未等候完成其中一種方法，並繼續在上執行其他作業 `DbContext` ，的狀態 `DbContext` 可能是， (且很可能會) 損毀。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-156">But if a caller does not await the completion of one of these methods, and proceeds to perform other operations on the `DbContext`, the state of the `DbContext` can be, (and very likely will be) corrupted.</span></span>

<span data-ttu-id="6c6dc-157">請一律立即等候 EF Core 非同步方法。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-157">Always await EF Core asynchronous methods immediately.</span></span>  

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a><span data-ttu-id="6c6dc-158">透過相依性插入，隱含地跨多個執行緒共用 DbCoNtext 實例</span><span class="sxs-lookup"><span data-stu-id="6c6dc-158">Implicitly sharing DbContext instances across multiple threads via dependency injection</span></span>

<span data-ttu-id="6c6dc-159">[`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)擴充方法預設會 `DbContext` 註冊具有[範圍存留期](/aspnet/core/fundamentals/dependency-injection#service-lifetimes)的類型。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-159">The [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) extension method registers `DbContext` types with a [scoped lifetime](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) by default.</span></span>

<span data-ttu-id="6c6dc-160">這可安全地避免大多數 ASP.NET Core 應用程式中的平行存取問題，因為在指定的時間內只有一個執行緒執行每個用戶端要求，而且每個要求都會取得個別的相依性插入範圍 (，因此) 個別的 `DbContext` 實例。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-160">This is safe from concurrent access issues in most ASP.NET Core applications because there is only one thread executing each client request at a given time, and because each request gets a separate dependency injection scope (and therefore a separate `DbContext` instance).</span></span> <span data-ttu-id="6c6dc-161">針對 Blazor 伺服器裝載模型，會使用一個邏輯要求來維護 Blazor 使用者電路，因此，如果使用預設的插入範圍，每個使用者電路只能使用一個範圍的 DbCoNtext 實例。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-161">For Blazor Server hosting model, one logical request is used for maintaining the Blazor user circuit, and thus only one scoped DbContext instance is available per user circuit if the default injection scope is used.</span></span>

<span data-ttu-id="6c6dc-162">任何明確地以平行方式執行多個執行緒的程式碼，都應該確保 `DbContext` 不會同時存取實例。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-162">Any code that explicitly executes multiple threads in parallel should ensure that `DbContext` instances aren't ever accessed concurrently.</span></span>

<span data-ttu-id="6c6dc-163">使用相依性插入時，您可以將內容註冊為已設定範圍，並為 `IServiceScopeFactory` 每個執行緒建立範圍)  (，或使用 `DbContext` `AddDbContext` 接受參數) 的多載來註冊作為暫時性 (來達成 `ServiceLifetime` 。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-163">Using dependency injection, this can be achieved by either registering the context as scoped, and creating scopes (using `IServiceScopeFactory`) for each thread, or by registering the `DbContext` as transient (using the overload of `AddDbContext` which takes a `ServiceLifetime` parameter).</span></span>

## <a name="more-reading"></a><span data-ttu-id="6c6dc-164">更多閱讀</span><span class="sxs-lookup"><span data-stu-id="6c6dc-164">More reading</span></span>

- <span data-ttu-id="6c6dc-165">若要深入瞭解如何使用 DI，請參閱相依性 [插入](/aspnet/core/fundamentals/dependency-injection) 。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-165">Read [Dependency Injection](/aspnet/core/fundamentals/dependency-injection) to learn more about using DI.</span></span>
- <span data-ttu-id="6c6dc-166">閱讀 [測試](xref:core/miscellaneous/testing/index) 以取得詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="6c6dc-166">Read [Testing](xref:core/miscellaneous/testing/index) for more information.</span></span>
