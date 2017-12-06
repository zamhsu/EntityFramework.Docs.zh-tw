---
title: "設定-建立的 DbContext EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: d7a22b5a-4c5b-4e3b-9897-4d7320fcd13f
ms.technology: entity-framework-core
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: 96abf3b94be3e1d19f833644f1c2f6f13fe0e730
ms.sourcegitcommit: 860ec5d047342fbc4063a0de881c9861cc1f8813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2017
---
# <a name="configuring-a-dbcontext"></a><span data-ttu-id="d0560-102">設定建立的 DbContext</span><span class="sxs-lookup"><span data-stu-id="d0560-102">Configuring a DbContext</span></span>

<span data-ttu-id="d0560-103">本文將說明的設定模式`DbContext`與`DbContextOptions`。</span><span class="sxs-lookup"><span data-stu-id="d0560-103">This article shows patterns for configuring a `DbContext` with `DbContextOptions`.</span></span> <span data-ttu-id="d0560-104">選項主要用來選取並設定資料存放區。</span><span class="sxs-lookup"><span data-stu-id="d0560-104">Options are primarily used to select and configure the data store.</span></span>

## <a name="configuring-dbcontextoptions"></a><span data-ttu-id="d0560-105">設定 DbContextOptions</span><span class="sxs-lookup"><span data-stu-id="d0560-105">Configuring DbContextOptions</span></span>

<span data-ttu-id="d0560-106">`DbContext`必須具有執行個體的`DbContextOptions`才能執行。</span><span class="sxs-lookup"><span data-stu-id="d0560-106">`DbContext` must have an instance of `DbContextOptions` in order to execute.</span></span> <span data-ttu-id="d0560-107">這可以透過覆寫設定`OnConfiguring`，或從外部提供透過建構函式引數。</span><span class="sxs-lookup"><span data-stu-id="d0560-107">This can be configured by overriding `OnConfiguring`, or supplied externally via a constructor argument.</span></span>

<span data-ttu-id="d0560-108">如果同時使用`OnConfiguring`上提供選項，也就是說，它加總，而且可以執行覆寫建構函式的引數所提供的選項。</span><span class="sxs-lookup"><span data-stu-id="d0560-108">If both are used, `OnConfiguring` is executed on the supplied options, meaning it is additive and can overwrite  options supplied to the constructor argument.</span></span>

### <a name="constructor-argument"></a><span data-ttu-id="d0560-109">建構函式引數</span><span class="sxs-lookup"><span data-stu-id="d0560-109">Constructor argument</span></span>

<span data-ttu-id="d0560-110">使用建構函式的內容程式碼</span><span class="sxs-lookup"><span data-stu-id="d0560-110">Context code with constructor</span></span>

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
> <span data-ttu-id="d0560-111">基底的 DbContext 建構函式也可接受的非泛型版本`DbContextOptions`。</span><span class="sxs-lookup"><span data-stu-id="d0560-111">The base constructor of DbContext also accepts the non-generic version of `DbContextOptions`.</span></span> <span data-ttu-id="d0560-112">有多個內容類型的應用程式，不建議使用非泛型版本。</span><span class="sxs-lookup"><span data-stu-id="d0560-112">Using the non-generic version is not recommended for applications with multiple context types.</span></span>

<span data-ttu-id="d0560-113">應用程式程式碼，以從建構函式引數初始化</span><span class="sxs-lookup"><span data-stu-id="d0560-113">Application code to initialize from constructor argument</span></span>

``` csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a><span data-ttu-id="d0560-114">OnConfiguring</span><span class="sxs-lookup"><span data-stu-id="d0560-114">OnConfiguring</span></span>

> [!WARNING]  
> <span data-ttu-id="d0560-115">`OnConfiguring`上次發生，而且可以覆寫從 DI 或建構函式取得的選項。</span><span class="sxs-lookup"><span data-stu-id="d0560-115">`OnConfiguring` occurs last and can overwrite options obtained from DI or the constructor.</span></span> <span data-ttu-id="d0560-116">這種方法本身就無法測試 （除非您的目標完整的資料庫）。</span><span class="sxs-lookup"><span data-stu-id="d0560-116">This approach does not lend itself to testing (unless you target the full database).</span></span>

<span data-ttu-id="d0560-117">內容的程式碼與`OnConfiguring`:</span><span class="sxs-lookup"><span data-stu-id="d0560-117">Context code with `OnConfiguring`:</span></span>

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

<span data-ttu-id="d0560-118">應用程式程式碼以初始化`OnConfiguring`:</span><span class="sxs-lookup"><span data-stu-id="d0560-118">Application code to initialize with `OnConfiguring`:</span></span>

``` csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

## <a name="using-dbcontext-with-dependency-injection"></a><span data-ttu-id="d0560-119">使用相依性插入的 DbContext</span><span class="sxs-lookup"><span data-stu-id="d0560-119">Using DbContext with dependency injection</span></span>

<span data-ttu-id="d0560-120">EF 支援使用`DbContext`與相依性插入容器。</span><span class="sxs-lookup"><span data-stu-id="d0560-120">EF supports using `DbContext` with a dependency injection container.</span></span> <span data-ttu-id="d0560-121">DbContext 類型可以使用新增至服務容器`AddDbContext<TContext>`。</span><span class="sxs-lookup"><span data-stu-id="d0560-121">Your DbContext type can be added to the service container by using `AddDbContext<TContext>`.</span></span>

<span data-ttu-id="d0560-122">`AddDbContext`將這兩個您 DbContext 類型`TContext`，和`DbContextOptions<TContext>`適用於資料隱碼，從服務容器。</span><span class="sxs-lookup"><span data-stu-id="d0560-122">`AddDbContext` will make both your DbContext type, `TContext`, and `DbContextOptions<TContext>` available for injection from the service container.</span></span>

<span data-ttu-id="d0560-123">請參閱[詳細閱讀](#more-reading)下方的相依性插入的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="d0560-123">See [more reading](#more-reading) below for information on dependency injection.</span></span>

<span data-ttu-id="d0560-124">加入相依性插入的 dbcontext</span><span class="sxs-lookup"><span data-stu-id="d0560-124">Adding dbcontext to dependency injection</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

<span data-ttu-id="d0560-125">這需要將[建構函式引數](#constructor-argument)給 DbContext 類型可接受`DbContextOptions`。</span><span class="sxs-lookup"><span data-stu-id="d0560-125">This requires adding a [constructor argument](#constructor-argument) to your DbContext type that accepts `DbContextOptions`.</span></span>

<span data-ttu-id="d0560-126">內容的程式碼：</span><span class="sxs-lookup"><span data-stu-id="d0560-126">Context code:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

<span data-ttu-id="d0560-127">（在 ASP.NET Core) 的應用程式程式碼：</span><span class="sxs-lookup"><span data-stu-id="d0560-127">Application code (in ASP.NET Core):</span></span>

``` csharp
public MyController(BloggingContext context)
```

<span data-ttu-id="d0560-128">（ServiceProvider 直接使用，較不常見） 的應用程式程式碼：</span><span class="sxs-lookup"><span data-stu-id="d0560-128">Application code (using ServiceProvider directly, less common):</span></span>

``` csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="using-idesigntimedbcontextfactorytcontext"></a><span data-ttu-id="d0560-129">使用`IDesignTimeDbContextFactory<TContext>`</span><span class="sxs-lookup"><span data-stu-id="d0560-129">Using `IDesignTimeDbContextFactory<TContext>`</span></span>

<span data-ttu-id="d0560-130">上述選項的替代方案，您可能也提供的實作`IDesignTimeDbContextFactory<TContext>`。</span><span class="sxs-lookup"><span data-stu-id="d0560-130">As an alternative to the options above, you may also provide an implementation of `IDesignTimeDbContextFactory<TContext>`.</span></span> <span data-ttu-id="d0560-131">EF 工具可以使用這個處理站，以建立您的 DbContext 執行個體。</span><span class="sxs-lookup"><span data-stu-id="d0560-131">EF tools can use this factory to create an instance of your DbContext.</span></span> <span data-ttu-id="d0560-132">這可能需要以啟用特定的設計階段體驗，例如移轉。</span><span class="sxs-lookup"><span data-stu-id="d0560-132">This may be required in order to enable specific design-time experiences such as migrations.</span></span>

<span data-ttu-id="d0560-133">實作這個介面來啟用並沒有公用預設建構函式的內容類型的設計階段服務。</span><span class="sxs-lookup"><span data-stu-id="d0560-133">Implement this interface to enable design-time services for context types that do not have a public default constructor.</span></span> <span data-ttu-id="d0560-134">設計階段服務會自動探索此介面衍生的內容相同的組件中的實作。</span><span class="sxs-lookup"><span data-stu-id="d0560-134">Design-time services will automatically discover implementations of this interface that are in the same assembly as the derived context.</span></span>

<span data-ttu-id="d0560-135">範例：</span><span class="sxs-lookup"><span data-stu-id="d0560-135">Example:</span></span>

``` csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Infrastructure;

namespace MyProject
{
    public class BloggingContextFactory : IDesignTimeDbContextFactory<BloggingContext>
    {
        public BloggingContext CreateDbContext(string[] args)
        {
            var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
            optionsBuilder.UseSqlite("Data Source=blog.db");

            return new BloggingContext(optionsBuilder.Options);
        }
    }
}
```

## <a name="more-reading"></a><span data-ttu-id="d0560-136">詳細閱讀</span><span class="sxs-lookup"><span data-stu-id="d0560-136">More reading</span></span>

* <span data-ttu-id="d0560-137">讀取[開始使用 ASP.NET Core](../get-started/aspnetcore/index.md)如需有關使用 EF 與 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="d0560-137">Read [Getting Started on ASP.NET Core](../get-started/aspnetcore/index.md) for more information on using EF with ASP.NET Core.</span></span>
* <span data-ttu-id="d0560-138">讀取[相依性插入](https://docs.asp.net/en/latest/fundamentals/dependency-injection.html)若要深入了解使用 DI。</span><span class="sxs-lookup"><span data-stu-id="d0560-138">Read [Dependency Injection](https://docs.asp.net/en/latest/fundamentals/dependency-injection.html) to learn more about using DI.</span></span>
* <span data-ttu-id="d0560-139">讀取[測試](testing/index.md)如需詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="d0560-139">Read [Testing](testing/index.md) for more information.</span></span>
