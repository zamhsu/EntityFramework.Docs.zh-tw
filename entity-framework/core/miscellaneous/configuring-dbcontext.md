---
title: 設定-建立的 DbContext EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d7a22b5a-4c5b-4e3b-9897-4d7320fcd13f
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: f5a9ae17471391442170d8c40264e4db6922cb08
ms.sourcegitcommit: 39080d38e1adea90db741257e60dc0e7ed08aa82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979998"
---
# <a name="configuring-a-dbcontext"></a><span data-ttu-id="44560-102">設定 DbContext</span><span class="sxs-lookup"><span data-stu-id="44560-102">Configuring a DbContext</span></span>

<span data-ttu-id="44560-103">本文示範基本模式設定`DbContext`透過`DbContextOptions`連接到資料庫，使用特定的 EF Core 提供者和選擇性的行為。</span><span class="sxs-lookup"><span data-stu-id="44560-103">This article shows basic patterns for configuring a `DbContext` via a `DbContextOptions` to connect to a database using a specific EF Core provider and optional behaviors.</span></span>

## <a name="design-time-dbcontext-configuration"></a><span data-ttu-id="44560-104">設計階段 DbContext 組態</span><span class="sxs-lookup"><span data-stu-id="44560-104">Design-time DbContext configuration</span></span>

<span data-ttu-id="44560-105">EF Core 設計階段工具，例如[移轉](xref:core/managing-schemas/migrations/index)必須能夠探索和建立的工作執行個體`DbContext`從中收集有關應用程式的實體類型和它們如何對應到資料庫結構描述的詳細資料的類型。</span><span class="sxs-lookup"><span data-stu-id="44560-105">EF Core design-time tools such as [migrations](xref:core/managing-schemas/migrations/index) need to be able to discover and create a working instance of a `DbContext` type in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="44560-106">此程序可以是自動，只要此工具可以輕鬆地建立`DbContext`的方式，它會設定同樣的方式設定它在執行階段。</span><span class="sxs-lookup"><span data-stu-id="44560-106">This process can be automatic as long as the tool can easily create the `DbContext` in such a way that it will be configured similarly to how it would be configured at run-time.</span></span>

<span data-ttu-id="44560-107">同時提供必要的組態資訊的任何模式`DbContext`能夠在執行階段，需要使用的工具`DbContext`在設計階段只能使用有限數目的模式。</span><span class="sxs-lookup"><span data-stu-id="44560-107">While any pattern that provides the necessary configuration information to the `DbContext` can work at run-time, tools that require using a `DbContext` at design-time can only work with a limited number of patterns.</span></span> <span data-ttu-id="44560-108">這些先決條件詳細[設計階段內容建立](xref:core/miscellaneous/cli/dbcontext-creation)一節。</span><span class="sxs-lookup"><span data-stu-id="44560-108">These are covered in more detail in the [Design-Time Context Creation](xref:core/miscellaneous/cli/dbcontext-creation) section.</span></span>

## <a name="configuring-dbcontextoptions"></a><span data-ttu-id="44560-109">設定 DbContextOptions</span><span class="sxs-lookup"><span data-stu-id="44560-109">Configuring DbContextOptions</span></span>

<span data-ttu-id="44560-110">`DbContext` 必須具有執行個體的`DbContextOptions`才能執行任何工作。</span><span class="sxs-lookup"><span data-stu-id="44560-110">`DbContext` must have an instance of `DbContextOptions` in order to perform any work.</span></span> <span data-ttu-id="44560-111">`DbContextOptions`這類執行個體帶有的組態資訊：</span><span class="sxs-lookup"><span data-stu-id="44560-111">The `DbContextOptions` instance carries configuration information such as:</span></span>

- <span data-ttu-id="44560-112">若要使用，資料庫提供者通常會選取叫用方法時，這類`UseSqlServer`或`UseSqlite`。</span><span class="sxs-lookup"><span data-stu-id="44560-112">The database provider to use, typically selected by invoking a method such as `UseSqlServer` or `UseSqlite`.</span></span> <span data-ttu-id="44560-113">這些擴充方法需要對應的提供者套件，例如`Microsoft.EntityFrameworkCore.SqlServer`或`Microsoft.EntityFrameworkCore.Sqlite`。</span><span class="sxs-lookup"><span data-stu-id="44560-113">These extension methods require the corresponding provider package, such as `Microsoft.EntityFrameworkCore.SqlServer` or `Microsoft.EntityFrameworkCore.Sqlite`.</span></span> <span data-ttu-id="44560-114">方法的定義位於`Microsoft.EntityFrameworkCore`命名空間。</span><span class="sxs-lookup"><span data-stu-id="44560-114">The methods are defined in the `Microsoft.EntityFrameworkCore` namespace.</span></span>
- <span data-ttu-id="44560-115">任何必要的連接字串或識別項的資料庫執行個體，通常會傳遞做為引數至上述提供者選取方法</span><span class="sxs-lookup"><span data-stu-id="44560-115">Any necessary connection string or identifier of the database instance, typically passed as an argument to the provider selection method mentioned above</span></span>
- <span data-ttu-id="44560-116">所有提供者層級選擇性行為的選取器，通常也會在提供者選取方法的呼叫鏈結</span><span class="sxs-lookup"><span data-stu-id="44560-116">Any provider-level optional behavior selectors, typically also chained inside the call to the provider selection method</span></span>
- <span data-ttu-id="44560-117">所有一般 EF Core 行為的選取器，通常鏈結提供者選取器方法之前或之後</span><span class="sxs-lookup"><span data-stu-id="44560-117">Any general EF Core behavior selectors, typically chained after or before the provider selector method</span></span>

<span data-ttu-id="44560-118">下列範例會設定`DbContextOptions`若要使用 SQL Server 提供者，連接中所包含`connectionString`變數、 提供者層級命令逾時和 EF Core 行為選取器，讓執行中的所有查詢`DbContext`[否追蹤](xref:core/querying/tracking#no-tracking-queries)預設：</span><span class="sxs-lookup"><span data-stu-id="44560-118">The following example configures the `DbContextOptions` to use the SQL Server provider, a connection contained in the `connectionString` variable, a provider-level command timeout, and an EF Core behavior selector that makes all queries executed in the `DbContext` [no-tracking](xref:core/querying/tracking#no-tracking-queries) by default:</span></span>

``` csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]  
> <span data-ttu-id="44560-119">提供者選取器方法和其他行為選取器方法先前所述的擴充方法上`DbContextOptions`或提供者特有的選項類別。</span><span class="sxs-lookup"><span data-stu-id="44560-119">Provider selector methods and other behavior selector methods mentioned above are extension methods on `DbContextOptions` or provider-specific option classes.</span></span> <span data-ttu-id="44560-120">若要存取這些擴充方法，您可能需要有命名空間 (通常`Microsoft.EntityFrameworkCore`) 中的範圍，並在專案中包含其他套件相依性。</span><span class="sxs-lookup"><span data-stu-id="44560-120">In order to have access to these extension methods you may need to have a namespace (typically `Microsoft.EntityFrameworkCore`) in scope and include additional package dependencies in the project.</span></span>

<span data-ttu-id="44560-121">`DbContextOptions`可以提供給`DbContext`藉由覆寫`OnConfiguring`方法或從外部透過建構函式引數。</span><span class="sxs-lookup"><span data-stu-id="44560-121">The `DbContextOptions` can be supplied to the `DbContext` by overriding the `OnConfiguring` method or externally via a constructor argument.</span></span>

<span data-ttu-id="44560-122">如果使用，`OnConfiguring`最後套用的而且可以覆寫建構函式的引數所提供的選項。</span><span class="sxs-lookup"><span data-stu-id="44560-122">If both are used, `OnConfiguring` is applied last and can overwrite options supplied to the constructor argument.</span></span>

### <a name="constructor-argument"></a><span data-ttu-id="44560-123">建構函式引數</span><span class="sxs-lookup"><span data-stu-id="44560-123">Constructor argument</span></span>

<span data-ttu-id="44560-124">使用建構函式的內容程式碼：</span><span class="sxs-lookup"><span data-stu-id="44560-124">Context code with constructor:</span></span>

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
> <span data-ttu-id="44560-125">DbContext 的基底建構函式也可接受的非泛型版本`DbContextOptions`，但有多個內容類型的應用程式不建議使用非泛型版本。</span><span class="sxs-lookup"><span data-stu-id="44560-125">The base constructor of DbContext also accepts the non-generic version of `DbContextOptions`, but using the non-generic version is not recommended for applications with multiple context types.</span></span>

<span data-ttu-id="44560-126">從建構函式引數初始化應用程式程式碼：</span><span class="sxs-lookup"><span data-stu-id="44560-126">Application code to initialize from constructor argument:</span></span>

``` csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a><span data-ttu-id="44560-127">OnConfiguring</span><span class="sxs-lookup"><span data-stu-id="44560-127">OnConfiguring</span></span>

<span data-ttu-id="44560-128">內容的程式碼`OnConfiguring`:</span><span class="sxs-lookup"><span data-stu-id="44560-128">Context code with `OnConfiguring`:</span></span>

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

<span data-ttu-id="44560-129">應用程式程式碼，來初始化`DbContext`使用`OnConfiguring`:</span><span class="sxs-lookup"><span data-stu-id="44560-129">Application code to initialize a `DbContext` that uses `OnConfiguring`:</span></span>

``` csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> <span data-ttu-id="44560-130">這種方法本身就無法進行測試，除非測試目標完整的資料庫。</span><span class="sxs-lookup"><span data-stu-id="44560-130">This approach does not lend itself to testing, unless the tests target the full database.</span></span>

### <a name="using-dbcontext-with-dependency-injection"></a><span data-ttu-id="44560-131">使用相依性插入使用 DbContext</span><span class="sxs-lookup"><span data-stu-id="44560-131">Using DbContext with dependency injection</span></span>

<span data-ttu-id="44560-132">EF Core 支援使用`DbContext`與相依性插入容器。</span><span class="sxs-lookup"><span data-stu-id="44560-132">EF Core supports using `DbContext` with a dependency injection container.</span></span> <span data-ttu-id="44560-133">您的 DbContext 類型可以利用加入至服務容器`AddDbContext<TContext>`方法。</span><span class="sxs-lookup"><span data-stu-id="44560-133">Your DbContext type can be added to the service container by using the `AddDbContext<TContext>` method.</span></span>

<span data-ttu-id="44560-134">`AddDbContext<TContext>` 將這兩個至 DbContext 類型`TContext`，和對應`DbContextOptions<TContext>`適用於資料隱碼攻擊，從服務容器。</span><span class="sxs-lookup"><span data-stu-id="44560-134">`AddDbContext<TContext>` will make both your DbContext type, `TContext`, and the corresponding `DbContextOptions<TContext>` available for injection from the service container.</span></span>

<span data-ttu-id="44560-135">請參閱[詳細閱讀](#more-reading)如下如需相依性插入所需的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="44560-135">See [more reading](#more-reading) below for additional information on dependency injection.</span></span>

<span data-ttu-id="44560-136">新增`Dbcontext`相依性插入式攻擊：</span><span class="sxs-lookup"><span data-stu-id="44560-136">Adding the `Dbcontext` to dependency injection:</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

<span data-ttu-id="44560-137">這需要新增[建構函式引數](#constructor-argument)為您的 DbContext 類型可接受`DbContextOptions<TContext>`。</span><span class="sxs-lookup"><span data-stu-id="44560-137">This requires adding a [constructor argument](#constructor-argument) to your DbContext type that accepts `DbContextOptions<TContext>`.</span></span>

<span data-ttu-id="44560-138">內容的程式碼：</span><span class="sxs-lookup"><span data-stu-id="44560-138">Context code:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

<span data-ttu-id="44560-139">（在 ASP.NET Core) 的應用程式程式碼：</span><span class="sxs-lookup"><span data-stu-id="44560-139">Application code (in ASP.NET Core):</span></span>

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

<span data-ttu-id="44560-140">（ServiceProvider 直接使用，較不常見） 的應用程式程式碼：</span><span class="sxs-lookup"><span data-stu-id="44560-140">Application code (using ServiceProvider directly, less common):</span></span>

``` csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="more-reading"></a><span data-ttu-id="44560-141">詳細閱讀</span><span class="sxs-lookup"><span data-stu-id="44560-141">More reading</span></span>

* <span data-ttu-id="44560-142">讀取[開始使用 ASP.NET Core](../get-started/aspnetcore/index.md) 如需有關使用 EF 與 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="44560-142">Read [Getting Started on ASP.NET Core](../get-started/aspnetcore/index.md) for more information on using EF with ASP.NET Core.</span></span>
* <span data-ttu-id="44560-143">讀取[相依性插入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)若要深入了解使用 DI。</span><span class="sxs-lookup"><span data-stu-id="44560-143">Read [Dependency Injection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) to learn more about using DI.</span></span>
* <span data-ttu-id="44560-144">讀取[測試](testing/index.md)如需詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="44560-144">Read [Testing](testing/index.md) for more information.</span></span>
