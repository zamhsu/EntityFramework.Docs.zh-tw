---
title: DbCoNtext 存留期、設定和初始化-EF Core
description: 使用或不使用相依性插入來建立和管理 DbCoNtext 實例的模式
author: ajcvickers
ms.date: 11/07/2020
uid: core/dbcontext-configuration/index
ms.openlocfilehash: f4d51e10555844b5a14000a464f86d3440d5749e
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003285"
---
# <a name="dbcontext-lifetime-configuration-and-initialization"></a><span data-ttu-id="6885d-103">DbCoNtext 存留期、設定和初始化</span><span class="sxs-lookup"><span data-stu-id="6885d-103">DbContext Lifetime, Configuration, and Initialization</span></span>

<span data-ttu-id="6885d-104">本文說明初始化和設定實例的基本模式 <xref:Microsoft.EntityFrameworkCore.DbContext> 。</span><span class="sxs-lookup"><span data-stu-id="6885d-104">This article shows basic patterns for initialization and configuration of a <xref:Microsoft.EntityFrameworkCore.DbContext> instance.</span></span>

## <a name="the-dbcontext-lifetime"></a><span data-ttu-id="6885d-105">DbCoNtext 存留期</span><span class="sxs-lookup"><span data-stu-id="6885d-105">The DbContext lifetime</span></span>

<span data-ttu-id="6885d-106">的存留期 `DbContext` 會在實例建立時開始，並在實例 [處置](/dotnet/standard/garbage-collection/unmanaged)時結束。</span><span class="sxs-lookup"><span data-stu-id="6885d-106">The lifetime of a `DbContext` begins when the instance is created and ends when the instance is [disposed](/dotnet/standard/garbage-collection/unmanaged).</span></span> <span data-ttu-id="6885d-107">`DbContext`實例的設計目的是要用於 _單一_[工作單位](https://www.martinfowler.com/eaaCatalog/unitOfWork.html)。</span><span class="sxs-lookup"><span data-stu-id="6885d-107">A `DbContext` instance is designed to be used for a _single_ [unit-of-work](https://www.martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="6885d-108">這表示實例的存留期 `DbContext` 通常很短。</span><span class="sxs-lookup"><span data-stu-id="6885d-108">This means that the lifetime of a `DbContext` instance is usually very short.</span></span>

> [!TIP]
> <span data-ttu-id="6885d-109">若要從上述連結報價 Fowler，您可以在商務交易期間追蹤可能影響資料庫的所有專案。</span><span class="sxs-lookup"><span data-stu-id="6885d-109">To quote Martin Fowler from the link above, "A Unit of Work keeps track of everything you do during a business transaction that can affect the database.</span></span> <span data-ttu-id="6885d-110">當您完成時，它會找出因您的工作而改變資料庫所需完成的所有工作。」</span><span class="sxs-lookup"><span data-stu-id="6885d-110">When you're done, it figures out everything that needs to be done to alter the database as a result of your work."</span></span>

<span data-ttu-id="6885d-111">使用 Entity Framework Core (EF Core) 的一般工作單位包括：</span><span class="sxs-lookup"><span data-stu-id="6885d-111">A typical unit-of-work when using Entity Framework Core (EF Core) involves:</span></span>

- <span data-ttu-id="6885d-112">建立 `DbContext` 實例</span><span class="sxs-lookup"><span data-stu-id="6885d-112">Creation of a `DbContext` instance</span></span>
- <span data-ttu-id="6885d-113">依內容追蹤實體實例。</span><span class="sxs-lookup"><span data-stu-id="6885d-113">Tracking of entity instances by the context.</span></span> <span data-ttu-id="6885d-114">實體會被追蹤</span><span class="sxs-lookup"><span data-stu-id="6885d-114">Entities become tracked by</span></span>
  - <span data-ttu-id="6885d-115">[從查詢傳回](xref:core/querying/tracking)</span><span class="sxs-lookup"><span data-stu-id="6885d-115">Being [returned from a query](xref:core/querying/tracking)</span></span>
  - <span data-ttu-id="6885d-116">[新增或附加至內容](xref:core/saving/disconnected-entities)</span><span class="sxs-lookup"><span data-stu-id="6885d-116">Being [added or attached to the context](xref:core/saving/disconnected-entities)</span></span>
- <span data-ttu-id="6885d-117">視需要對追蹤的實體進行變更，以執行商務規則</span><span class="sxs-lookup"><span data-stu-id="6885d-117">Changes are made to the tracked entities as needed to implement the business rule</span></span>
- <span data-ttu-id="6885d-118"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 或 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> 呼叫。</span><span class="sxs-lookup"><span data-stu-id="6885d-118"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> is called.</span></span> <span data-ttu-id="6885d-119">EF Core 會偵測所做的變更，並將它們寫入資料庫中。</span><span class="sxs-lookup"><span data-stu-id="6885d-119">EF Core detects the changes made and writes them to the database.</span></span>
- <span data-ttu-id="6885d-120">`DbContext`實例已處置</span><span class="sxs-lookup"><span data-stu-id="6885d-120">The `DbContext` instance is disposed</span></span>

> [!IMPORTANT]
>
> - <span data-ttu-id="6885d-121"><xref:Microsoft.EntityFrameworkCore.DbContext>使用之後，請務必處置。</span><span class="sxs-lookup"><span data-stu-id="6885d-121">It is very important to dispose the <xref:Microsoft.EntityFrameworkCore.DbContext> after use.</span></span> <span data-ttu-id="6885d-122">這可確保釋放任何未管理的資源，並取消註冊任何事件或其他攔截，以避免在實例維持參考時記憶體流失。</span><span class="sxs-lookup"><span data-stu-id="6885d-122">This ensures both that any unmanaged resources are freed, and that any events or other hooks are unregistered so as to prevent memory leaks in case the instance remains referenced.</span></span>
> - <span data-ttu-id="6885d-123">[DbCoNtext **不是安全線程**](#avoiding-dbcontext-threading-issues)。</span><span class="sxs-lookup"><span data-stu-id="6885d-123">[DbContext is **not thread-safe**](#avoiding-dbcontext-threading-issues).</span></span> <span data-ttu-id="6885d-124">請勿線上程之間共用內容。</span><span class="sxs-lookup"><span data-stu-id="6885d-124">Do not share contexts between threads.</span></span> <span data-ttu-id="6885d-125">在繼續使用內容實例之前，請務必 [等待](/dotnet/csharp/language-reference/operators/await) 所有非同步呼叫。</span><span class="sxs-lookup"><span data-stu-id="6885d-125">Make sure to [await](/dotnet/csharp/language-reference/operators/await) all async calls before continuing to use the context instance.</span></span>
> - <span data-ttu-id="6885d-126"><xref:System.InvalidOperationException>EF Core 程式碼擲回的會使內容進入無法復原的狀態。</span><span class="sxs-lookup"><span data-stu-id="6885d-126">An <xref:System.InvalidOperationException> thrown by EF Core code can put the context into an unrecoverable state.</span></span> <span data-ttu-id="6885d-127">這類例外狀況表示程式錯誤，不是設計來復原。</span><span class="sxs-lookup"><span data-stu-id="6885d-127">Such exceptions indicate a program error and are not designed to be recovered from.</span></span>

## <a name="dbcontext-in-dependency-injection-for-aspnet-core"></a><span data-ttu-id="6885d-128">ASP.NET Core 的相依性插入中 DbCoNtext</span><span class="sxs-lookup"><span data-stu-id="6885d-128">DbContext in dependency injection for ASP.NET Core</span></span>

<span data-ttu-id="6885d-129">在許多 web 應用程式中，每個 HTTP 要求都對應至單一工作單位。</span><span class="sxs-lookup"><span data-stu-id="6885d-129">In many web applications, each HTTP request corresponds to a single unit-of-work.</span></span> <span data-ttu-id="6885d-130">這會將內容存留期與要求的內容存留期系結至 web 應用程式的良好預設值。</span><span class="sxs-lookup"><span data-stu-id="6885d-130">This makes tying the context lifetime to that of the request a good default for web applications.</span></span>

<span data-ttu-id="6885d-131">ASP.NET Core 的應用程式會使用相依性 [插入進行設定](/aspnet/core/fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="6885d-131">ASP.NET Core applications are [configured using dependency injection](/aspnet/core/fundamentals/startup).</span></span> <span data-ttu-id="6885d-132">您可以使用的方法，將 EF Core 加入至這個設定 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> [`ConfigurureServices`](/aspnet/core/fundamentals/startup#the-configureservices-method) `Startup.cs` 。</span><span class="sxs-lookup"><span data-stu-id="6885d-132">EF Core can be added to this configuration using <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> in the [`ConfigurureServices`](/aspnet/core/fundamentals/startup#the-configureservices-method) method of `Startup.cs`.</span></span> <span data-ttu-id="6885d-133">例如：</span><span class="sxs-lookup"><span data-stu-id="6885d-133">For example:</span></span>

<!--
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddControllers();
            
            services.AddDbContext<ApplicationDbContext>(
                options => options.UseSqlServer("name=ConnectionStrings:DefaultConnection"));
        }
-->
[!code-csharp[ConfigureServices](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/Startup.cs?name=ConfigureServices)]

<span data-ttu-id="6885d-134">此範例會 `DbContext` `ApplicationDbContext` 在 ASP.NET Core 應用程式服務提供者 (中註冊一個稱為範圍服務的子類別也稱為</span><span class="sxs-lookup"><span data-stu-id="6885d-134">This example registers a `DbContext` subclass called `ApplicationDbContext` as a scoped service in the ASP.NET Core application service provider (a.k.a.</span></span> <span data-ttu-id="6885d-135">相依性插入容器) 。</span><span class="sxs-lookup"><span data-stu-id="6885d-135">the dependency injection container).</span></span> <span data-ttu-id="6885d-136">內容設定為使用 SQL Server 資料庫提供者，而且會從 ASP.NET Core 設定讀取連接字串。</span><span class="sxs-lookup"><span data-stu-id="6885d-136">The context is configured to use the SQL Server database provider and will read the connection string from ASP.NET Core configuration.</span></span> <span data-ttu-id="6885d-137">在呼叫中的 _位置_ ，通常並不重要 `ConfigureServices` `AddDbContext` 。</span><span class="sxs-lookup"><span data-stu-id="6885d-137">It typically does not matter _where_ in `ConfigureServices` the call to `AddDbContext` is made.</span></span>

<span data-ttu-id="6885d-138">`ApplicationDbContext`類別必須使用參數公開公用的函式 `DbContextOptions<ApplicationDbContext>` 。</span><span class="sxs-lookup"><span data-stu-id="6885d-138">The `ApplicationDbContext` class must expose a public constructor with a `DbContextOptions<ApplicationDbContext>` parameter.</span></span> <span data-ttu-id="6885d-139">這是將內容設定 `AddDbContext` 傳遞至的方式 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="6885d-139">This is how context configuration from `AddDbContext` is passed to the `DbContext`.</span></span> <span data-ttu-id="6885d-140">例如：</span><span class="sxs-lookup"><span data-stu-id="6885d-140">For example:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="6885d-141">`ApplicationDbContext` 然後可以透過函式插入，在 ASP.NET Core 控制器或其他服務中使用。</span><span class="sxs-lookup"><span data-stu-id="6885d-141">`ApplicationDbContext` can then be used in ASP.NET Core controllers or other services through constructor injection.</span></span> <span data-ttu-id="6885d-142">例如：</span><span class="sxs-lookup"><span data-stu-id="6885d-142">For example:</span></span>

<!--
    public class MyController
    {
        private readonly ApplicationDbContext _context;

        public MyController(ApplicationDbContext context)
        {
            _context = context;
        }
    }
-->
[!code-csharp[MyController](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/Controllers/MyController.cs?name=MyController)]

<span data-ttu-id="6885d-143">最終結果是 `ApplicationDbContext` 針對每個要求所建立的實例，並傳遞至控制器，以在要求結束時進行處置之前，先執行工作單位。</span><span class="sxs-lookup"><span data-stu-id="6885d-143">The final result is an `ApplicationDbContext` instance created for each request and passed to the controller to perform a unit-of-work before being disposed when the request ends.</span></span>

<span data-ttu-id="6885d-144">進一步閱讀本文，以深入瞭解設定選項。</span><span class="sxs-lookup"><span data-stu-id="6885d-144">Read further in this article to learn more about configuration options.</span></span> <span data-ttu-id="6885d-145">此外，如需有關 ASP.NET Core 中的設定和相依性插入的詳細資訊，請參閱 ASP.NET Core 中的 [應用程式啟動](/aspnet/core/fundamentals/startup) 以及 [ASP.NET Core 中](/aspnet/core/fundamentals/dependency-injection) 的相依性插入。</span><span class="sxs-lookup"><span data-stu-id="6885d-145">In addition, see [App startup in ASP.NET Core](/aspnet/core/fundamentals/startup) and [Dependency injection in ASP.NET Core](/aspnet/core/fundamentals/dependency-injection) for more information on configuration and dependency injection in ASP.NET Core.</span></span>

<!-- See also [Using Dependency Injection](TODO) for advanced dependency injection configuration with EF Core. -->

## <a name="simple-dbcontext-initialization-with-new"></a><span data-ttu-id="6885d-146">使用 ' new ' 的簡單 DbCoNtext 初始化</span><span class="sxs-lookup"><span data-stu-id="6885d-146">Simple DbContext initialization with 'new'</span></span>

<span data-ttu-id="6885d-147">`DbContext` 您可以使用一般的 .NET 方式來建立實例，例如 `new` 在 c # 中。</span><span class="sxs-lookup"><span data-stu-id="6885d-147">`DbContext` instances can be constructed in the normal .NET way, for example with `new` in C#.</span></span> <span data-ttu-id="6885d-148">您可以藉由覆寫 `OnConfiguring` 方法或將選項傳遞至函式，來執行設定。</span><span class="sxs-lookup"><span data-stu-id="6885d-148">Configuration can be performed by overriding the `OnConfiguring` method, or by passing options to the constructor.</span></span> <span data-ttu-id="6885d-149">例如：</span><span class="sxs-lookup"><span data-stu-id="6885d-149">For example:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test");
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithNew/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="6885d-150">此模式也可讓您輕鬆地透過函式傳遞設定，例如連接字串 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="6885d-150">This pattern also makes it easy to pass configuration like the connection string via the `DbContext` constructor.</span></span> <span data-ttu-id="6885d-151">例如：</span><span class="sxs-lookup"><span data-stu-id="6885d-151">For example:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        private readonly string _connectionString;

        public ApplicationDbContext(string connectionString)
        {
            _connectionString = connectionString;
        }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(_connectionString);
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithNewAndArgs/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="6885d-152">或者，您 `DbContextOptionsBuilder` 可以使用來建立 `DbContextOptions` 物件，然後傳遞至該函式 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="6885d-152">Alternately, `DbContextOptionsBuilder` can be used to create a `DbContextOptions` object that is then passed to the `DbContext` constructor.</span></span> <span data-ttu-id="6885d-153">這可讓 `DbContext` 設定的相依性插入也明確地建立。</span><span class="sxs-lookup"><span data-stu-id="6885d-153">This allows a `DbContext` configured for dependency injection to also be constructed explicitly.</span></span> <span data-ttu-id="6885d-154">例如，針對 `ApplicationDbContext` 上述 ASP.NET Core web 應用程式使用定義時：</span><span class="sxs-lookup"><span data-stu-id="6885d-154">For example, when using `ApplicationDbContext` defined for ASP.NET Core web apps above:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="6885d-155">`DbContextOptions`可以建立，並且可以明確呼叫函式：</span><span class="sxs-lookup"><span data-stu-id="6885d-155">The `DbContextOptions` can be created and the constructor can be called explicitly:</span></span>

<!--
            var contextOptions = new DbContextOptionsBuilder<ApplicationDbContext>()
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test")
                .Options;

            using var context = new ApplicationDbContext(contextOptions);
-->
[!code-csharp[UseNewForWebApp](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/UseNewForWebApp.cs?name=UseNewForWebApp)]

## <a name="using-a-dbcontext-factory-eg-for-blazor"></a><span data-ttu-id="6885d-156">使用 DbCoNtext factory (例如 Blazor) </span><span class="sxs-lookup"><span data-stu-id="6885d-156">Using a DbContext factory (e.g. for Blazor)</span></span>

<span data-ttu-id="6885d-157">某些應用程式類型 (例如 [ASP.NET Core Blazor](/aspnet/core/blazor/)) 使用相依性插入，但不會建立與所需存留期相符的服務範圍 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="6885d-157">Some application types (e.g. [ASP.NET Core Blazor](/aspnet/core/blazor/)) use dependency injection but do not create a service scope that aligns with the desired `DbContext` lifetime.</span></span> <span data-ttu-id="6885d-158">即使這類的對齊存在，應用程式可能需要在此範圍內執行多個工作單位。</span><span class="sxs-lookup"><span data-stu-id="6885d-158">Even where such an alignment does exist, the application may need to perform multiple units-of-work within this scope.</span></span> <span data-ttu-id="6885d-159">例如，單一 HTTP 要求內的多個工作單位。</span><span class="sxs-lookup"><span data-stu-id="6885d-159">For example, multiple units-of-work within a single HTTP request.</span></span>

<span data-ttu-id="6885d-160">在這些情況下， <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextFactory%2A> 可以用來註冊建立實例的 factory `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="6885d-160">In these cases, <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextFactory%2A> can be used to register a factory for creation of `DbContext` instances.</span></span> <span data-ttu-id="6885d-161">例如：</span><span class="sxs-lookup"><span data-stu-id="6885d-161">For example:</span></span>

<!--
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddDbContextFactory<ApplicationDbContext>(options =>
                options.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
        }
-->
[!code-csharp[ConfigureServices](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/FactoryServicesExample.cs?name=ConfigureServices)]

<span data-ttu-id="6885d-162">`ApplicationDbContext`類別必須使用參數公開公用的函式 `DbContextOptions<ApplicationDbContext>` 。</span><span class="sxs-lookup"><span data-stu-id="6885d-162">The `ApplicationDbContext` class must expose a public constructor with a `DbContextOptions<ApplicationDbContext>` parameter.</span></span> <span data-ttu-id="6885d-163">這與上述傳統 ASP.NET Core 一節中所使用的模式相同。</span><span class="sxs-lookup"><span data-stu-id="6885d-163">This is the same pattern as used in the traditional ASP.NET Core section above.</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="6885d-164">然後，您 `DbContextFactory` 可以透過使用函式插入，在其他服務中使用 factory。</span><span class="sxs-lookup"><span data-stu-id="6885d-164">The `DbContextFactory` factory can then be used in other services through constructor injection.</span></span> <span data-ttu-id="6885d-165">例如：</span><span class="sxs-lookup"><span data-stu-id="6885d-165">For example:</span></span>

<!--
        private readonly IDbContextFactory<ApplicationDbContext> _contextFactory;

        public MyController(IDbContextFactory<ApplicationDbContext> contextFactory)
        {
            _contextFactory = contextFactory;
        }
-->
[!code-csharp[Construct](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/MyController.cs?name=Construct)]

<span data-ttu-id="6885d-166">然後，您可以在服務程式代碼中使用插入的 factory 來建立 DbCoNtext 實例。</span><span class="sxs-lookup"><span data-stu-id="6885d-166">The injected factory can then be used to construct DbContext instances in the service code.</span></span> <span data-ttu-id="6885d-167">例如：</span><span class="sxs-lookup"><span data-stu-id="6885d-167">For example:</span></span>

<!--
        public void DoSomething()
        {
            using (var context = _contextFactory.CreateDbContext())
            {
                // ...
            }
        }
-->
[!code-csharp[DoSomething](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/MyController.cs?name=DoSomething)]

<span data-ttu-id="6885d-168">請注意， `DbContext` 以這種方式建立的實例 **不** 是由應用程式的服務提供者所管理，因此必須由應用程式處置。</span><span class="sxs-lookup"><span data-stu-id="6885d-168">Notice that the `DbContext` instances created in this way are **not** managed by the application's service provider and therefore must be disposed by the application.</span></span>

<span data-ttu-id="6885d-169">如需搭配 Blazor 使用 EF Core 的詳細資訊，請參閱 [ASP.NET Core Blazor Server with Entity Framework Core](/aspnet/core/blazor/blazor-server-ef-core) 。</span><span class="sxs-lookup"><span data-stu-id="6885d-169">See [ASP.NET Core Blazor Server with Entity Framework Core](/aspnet/core/blazor/blazor-server-ef-core) for more information on using EF Core with Blazor.</span></span>

## <a name="dbcontextoptions"></a><span data-ttu-id="6885d-170">DbCoNtextOptions</span><span class="sxs-lookup"><span data-stu-id="6885d-170">DbContextOptions</span></span>

<span data-ttu-id="6885d-171">所有設定的起點 `DbContext` 都是 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> 。</span><span class="sxs-lookup"><span data-stu-id="6885d-171">The starting point for all `DbContext` configuration is <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>.</span></span> <span data-ttu-id="6885d-172">有三種方式可取得此產生器：</span><span class="sxs-lookup"><span data-stu-id="6885d-172">There are three ways to get this builder:</span></span>

- <span data-ttu-id="6885d-173">`AddDbContext`和相關方法</span><span class="sxs-lookup"><span data-stu-id="6885d-173">In `AddDbContext` and related methods</span></span>
- <span data-ttu-id="6885d-174">在 `OnConfiguring` 中</span><span class="sxs-lookup"><span data-stu-id="6885d-174">In `OnConfiguring`</span></span>
- <span data-ttu-id="6885d-175">明確地使用 `new`</span><span class="sxs-lookup"><span data-stu-id="6885d-175">Constructed explicitly with `new`</span></span>

<span data-ttu-id="6885d-176">上述各節會顯示上述各項的範例。</span><span class="sxs-lookup"><span data-stu-id="6885d-176">Examples of each of these are shown in the preceding sections.</span></span> <span data-ttu-id="6885d-177">無論產生器的來源為何，都可以套用相同的設定。</span><span class="sxs-lookup"><span data-stu-id="6885d-177">The same configuration can be applied regardless of where the builder comes from.</span></span> <span data-ttu-id="6885d-178">此外， `OnConfiguring` 不論內容的建立方式為何，一律會呼叫。</span><span class="sxs-lookup"><span data-stu-id="6885d-178">In addition, `OnConfiguring` is always called regardless of how the context is constructed.</span></span> <span data-ttu-id="6885d-179">這表示 `OnConfiguring` 即使在使用中，也可以用來執行其他設定 `AddDbContext` 。</span><span class="sxs-lookup"><span data-stu-id="6885d-179">This means `OnConfiguring` can be used to perform additional configuration even when `AddDbContext` is being used.</span></span>

### <a name="configuring-the-database-provider"></a><span data-ttu-id="6885d-180">設定資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="6885d-180">Configuring the database provider</span></span>

<span data-ttu-id="6885d-181">每個 `DbContext` 實例都必須設定為只使用一個資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="6885d-181">Each `DbContext` instance must be configured to use one and only one database provider.</span></span> <span data-ttu-id="6885d-182">子類型 (不同的實例 `DbContext` 可以搭配不同的資料庫提供者使用，但單一實例只能使用一個。 ) 資料庫提供者是使用特定的「呼叫」進行設定。 `Use*`</span><span class="sxs-lookup"><span data-stu-id="6885d-182">(Different instances of a `DbContext` subtype can be used with different database providers, but a single instance must only use one.) A database provider is configured using a specific `Use*`" call.</span></span> <span data-ttu-id="6885d-183">例如，若要使用 SQL Server 資料庫提供者：</span><span class="sxs-lookup"><span data-stu-id="6885d-183">For example, to use the SQL Server database provider:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test");
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithNew/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="6885d-184">這些 `Use*` 方法是由資料庫提供者所執行的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="6885d-184">These `Use*`" methods are extension methods implemented by the database provider.</span></span> <span data-ttu-id="6885d-185">這表示必須先安裝資料庫提供者 NuGet 封裝，才能使用擴充方法。</span><span class="sxs-lookup"><span data-stu-id="6885d-185">This means that the database provider NuGet package must be installed before the extension method can be used.</span></span>

> [!TIP]
> <span data-ttu-id="6885d-186">EF Core 資料庫提供者會大量使用 [擴充方法](/dotnet/csharp/programming-guide/classes-and-structs/extension-methods)。</span><span class="sxs-lookup"><span data-stu-id="6885d-186">EF Core database providers make extensive use of [extension methods](/dotnet/csharp/programming-guide/classes-and-structs/extension-methods).</span></span> <span data-ttu-id="6885d-187">如果編譯器指出找不到方法，則請確定已安裝提供者的 NuGet 套件，且您已 `using Microsoft.EntityFrameworkCore;` 在程式碼中。</span><span class="sxs-lookup"><span data-stu-id="6885d-187">If the compiler indicates that a method cannot be found, then make sure that the provider's NuGet package is installed and that you have `using Microsoft.EntityFrameworkCore;` in your code.</span></span>

<span data-ttu-id="6885d-188">下表包含一般資料庫提供者的範例。</span><span class="sxs-lookup"><span data-stu-id="6885d-188">The following table contains examples for common database providers.</span></span>

| <span data-ttu-id="6885d-189">資料庫系統</span><span class="sxs-lookup"><span data-stu-id="6885d-189">Database system</span></span>              | <span data-ttu-id="6885d-190">範例設定</span><span class="sxs-lookup"><span data-stu-id="6885d-190">Example configuration</span></span>                         | <span data-ttu-id="6885d-191">Nuget 套件</span><span class="sxs-lookup"><span data-stu-id="6885d-191">NuGet package</span></span>
|:-----------------------------|-----------------------------------------------|--------------
| <span data-ttu-id="6885d-192">SQL Server 或 Azure SQL</span><span class="sxs-lookup"><span data-stu-id="6885d-192">SQL Server or Azure SQL</span></span>      | `.UseSqlServer(connectionString)`             | [<span data-ttu-id="6885d-193">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="6885d-193">Microsoft.EntityFrameworkCore.SqlServer</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)
| <span data-ttu-id="6885d-194">Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="6885d-194">Azure Cosmos DB</span></span>              | `.UseCosmos(connectionString, databaseName)`  | [<span data-ttu-id="6885d-195">Microsoft.EntityFrameworkCore.Cosmos</span><span class="sxs-lookup"><span data-stu-id="6885d-195">Microsoft.EntityFrameworkCore.Cosmos</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/)
| <span data-ttu-id="6885d-196">SQLite</span><span class="sxs-lookup"><span data-stu-id="6885d-196">SQLite</span></span>                       | `.UseSqlite(connectionString)`                | [<span data-ttu-id="6885d-197">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="6885d-197">Microsoft.EntityFrameworkCore.Sqlite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)
| <span data-ttu-id="6885d-198">EF Core 記憶體內部資料庫</span><span class="sxs-lookup"><span data-stu-id="6885d-198">EF Core in-memory database</span></span>   | `.UseInMemoryDatabase(databaseName)`          | [<span data-ttu-id="6885d-199">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="6885d-199">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)
| <span data-ttu-id="6885d-200">于 postgresql</span><span class="sxs-lookup"><span data-stu-id="6885d-200">PostgreSQL\*</span></span>                  | `.UseNpgsql(connectionString)`                | [<span data-ttu-id="6885d-201">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="6885d-201">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL/)
| <span data-ttu-id="6885d-202">MySQL/適用于 mariadb \*</span><span class="sxs-lookup"><span data-stu-id="6885d-202">MySQL/MariaDB\*</span></span>               | `.UseMySql((connectionString)`                | [<span data-ttu-id="6885d-203">Pomelo.EntityFrameworkCore.MySql</span><span class="sxs-lookup"><span data-stu-id="6885d-203">Pomelo.EntityFrameworkCore.MySql</span></span>](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql/)
| <span data-ttu-id="6885d-204">Oracle\*</span><span class="sxs-lookup"><span data-stu-id="6885d-204">Oracle\*</span></span>                      | `.UseOracle(connectionString)`                | [<span data-ttu-id="6885d-205">Oracle.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="6885d-205">Oracle.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Oracle.EntityFrameworkCore/)

<span data-ttu-id="6885d-206">\* 這些資料庫提供者並未隨附于 Microsoft。</span><span class="sxs-lookup"><span data-stu-id="6885d-206">\*These database providers are not shipped by Microsoft.</span></span> <span data-ttu-id="6885d-207">如需有關資料庫提供者的詳細資訊，請參閱 [資料庫提供者](xref:core/providers/index) 。</span><span class="sxs-lookup"><span data-stu-id="6885d-207">See [Database Providers](xref:core/providers/index) for more information about database providers.</span></span>

> [!WARNING]
> <span data-ttu-id="6885d-208">記憶體內部資料庫的 EF Core 並非針對生產環境使用而設計。</span><span class="sxs-lookup"><span data-stu-id="6885d-208">The EF Core in-memory database is not designed for production use.</span></span> <span data-ttu-id="6885d-209">此外，它可能不是最佳選擇，甚至是進行測試。</span><span class="sxs-lookup"><span data-stu-id="6885d-209">In addition, it may not be the best choice even for testing.</span></span> <span data-ttu-id="6885d-210">如需詳細資訊，請參閱 [使用 EF Core 的測試程式碼](xref:core/testing/index) 。</span><span class="sxs-lookup"><span data-stu-id="6885d-210">See [Testing Code That Uses EF Core](xref:core/testing/index) for more information.</span></span>

<span data-ttu-id="6885d-211">如需搭配使用連接字串與 EF Core 的詳細資訊，請參閱 [連接字串](xref:core/miscellaneous/connection-strings) 。</span><span class="sxs-lookup"><span data-stu-id="6885d-211">See [Connection Strings](xref:core/miscellaneous/connection-strings) for more information on using connection strings with EF Core.</span></span>

<span data-ttu-id="6885d-212">資料庫提供者專屬的選擇性設定是在其他提供者特定的產生器中執行。</span><span class="sxs-lookup"><span data-stu-id="6885d-212">Optional configuration specific to the database provider is performed in an additional provider-specific builder.</span></span> <span data-ttu-id="6885d-213">例如，使用 <xref:Microsoft.EntityFrameworkCore.Infrastructure.SqlServerDbContextOptionsBuilder.EnableRetryOnFailure%2A> 來設定連接到 AZURE SQL 時的連線恢復功能：</span><span class="sxs-lookup"><span data-stu-id="6885d-213">For example, using <xref:Microsoft.EntityFrameworkCore.Infrastructure.SqlServerDbContextOptionsBuilder.EnableRetryOnFailure%2A> to configure retries for connection resiliency when connecting to Azure SQL:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder
                .UseSqlServer(
                    @"Server=(localdb)\mssqllocaldb;Database=Test",
                    providerOptions =>
                        {
                            providerOptions.EnableRetryOnFailure();
                        });
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/AdditionalProviderConfiguration/ApplicationDbContext.cs?name=ApplicationDbContext)]

> [!TIP]
> <span data-ttu-id="6885d-214">SQL Server 和 Azure SQL 都使用相同的資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="6885d-214">The same database provider is used for SQL Server and Azure SQL.</span></span> <span data-ttu-id="6885d-215">不過，建議您在連接到 SQL Azure 時使用 [連接復原](xref:core/miscellaneous/connection-resiliency) 。</span><span class="sxs-lookup"><span data-stu-id="6885d-215">However, it is recommended that [connection resiliency](xref:core/miscellaneous/connection-resiliency) be used when connecting to SQL Azure.</span></span>

<span data-ttu-id="6885d-216">如需提供者特定設定的詳細資訊，請參閱 [資料庫提供者](xref:core/providers/index) 。</span><span class="sxs-lookup"><span data-stu-id="6885d-216">See [Database Providers](xref:core/providers/index) for more information on provider-specific configuration.</span></span>

### <a name="other-dbcontext-configuration"></a><span data-ttu-id="6885d-217">其他 DbCoNtext 設定</span><span class="sxs-lookup"><span data-stu-id="6885d-217">Other DbContext configuration</span></span>

<span data-ttu-id="6885d-218">其他設定 `DbContext` 可以在 (之前或之後連結，不會造成任何差異) `Use*` 呼叫。</span><span class="sxs-lookup"><span data-stu-id="6885d-218">Other `DbContext` configuration can be chained either before or after (it makes no difference which) the `Use*` call.</span></span> <span data-ttu-id="6885d-219">例如，若要開啟機密資料記錄：</span><span class="sxs-lookup"><span data-stu-id="6885d-219">For example, to turn on sensitive-data logging:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder
                .EnableSensitiveDataLogging()
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test");
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/AdditionalConfiguration/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="6885d-220">下表包含在上呼叫的一般方法的範例 `DbContextOptionsBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="6885d-220">The following table contains examples of common methods called on `DbContextOptionsBuilder`.</span></span>

| <span data-ttu-id="6885d-221">DbCoNtextOptionsBuilder 方法</span><span class="sxs-lookup"><span data-stu-id="6885d-221">DbContextOptionsBuilder method</span></span>                                                             | <span data-ttu-id="6885d-222">作用</span><span class="sxs-lookup"><span data-stu-id="6885d-222">What it does</span></span>                                                | <span data-ttu-id="6885d-223">深入了解</span><span class="sxs-lookup"><span data-stu-id="6885d-223">Learn more</span></span>
|:-------------------------------------------------------------------------------------------|-------------------------------------------------------------|--------------
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.UseQueryTrackingBehavior%2A>   | <span data-ttu-id="6885d-224">設定查詢的預設追蹤行為</span><span class="sxs-lookup"><span data-stu-id="6885d-224">Sets the default tracking behavior for queries</span></span>              | [<span data-ttu-id="6885d-225">查詢追蹤行為</span><span class="sxs-lookup"><span data-stu-id="6885d-225">Query Tracking Behavior</span></span>](xref:core/querying/tracking)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A>                      | <span data-ttu-id="6885d-226">取得 EF Core 記錄 (EF Core 5.0 和更新版本的簡單方式) </span><span class="sxs-lookup"><span data-stu-id="6885d-226">A simple way to get EF Core logs (EF Core 5.0 and later)</span></span>    | [<span data-ttu-id="6885d-227">記錄、事件和診斷</span><span class="sxs-lookup"><span data-stu-id="6885d-227">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.UseLoggerFactory%2A>           | <span data-ttu-id="6885d-228">註冊 `Micrsofot.Extensions.Logging` factory</span><span class="sxs-lookup"><span data-stu-id="6885d-228">Registers an `Micrsofot.Extensions.Logging` factory</span></span>         | [<span data-ttu-id="6885d-229">記錄、事件和診斷</span><span class="sxs-lookup"><span data-stu-id="6885d-229">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> | <span data-ttu-id="6885d-230">包含例外狀況和記錄中的應用程式資料</span><span class="sxs-lookup"><span data-stu-id="6885d-230">Includes application data in exceptions and logging</span></span>         | [<span data-ttu-id="6885d-231">記錄、事件和診斷</span><span class="sxs-lookup"><span data-stu-id="6885d-231">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A>       | <span data-ttu-id="6885d-232">更詳細的查詢錯誤 (的效能成本) </span><span class="sxs-lookup"><span data-stu-id="6885d-232">More detailed query errors (at the expense of performance)</span></span>  | [<span data-ttu-id="6885d-233">記錄、事件和診斷</span><span class="sxs-lookup"><span data-stu-id="6885d-233">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A>          | <span data-ttu-id="6885d-234">忽略或擲回警告和其他事件</span><span class="sxs-lookup"><span data-stu-id="6885d-234">Ignore or throw for warnings and other events</span></span>               | [<span data-ttu-id="6885d-235">記錄、事件和診斷</span><span class="sxs-lookup"><span data-stu-id="6885d-235">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A>            | <span data-ttu-id="6885d-236">註冊 EF Core 攔截器</span><span class="sxs-lookup"><span data-stu-id="6885d-236">Registers EF Core interceptors</span></span>                              | [<span data-ttu-id="6885d-237">記錄、事件和診斷</span><span class="sxs-lookup"><span data-stu-id="6885d-237">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A>            | <span data-ttu-id="6885d-238">使用動態 proxy 進行消極式載入</span><span class="sxs-lookup"><span data-stu-id="6885d-238">Use dynamic proxies for lazy-loading</span></span>                        | [<span data-ttu-id="6885d-239">延遲載入</span><span class="sxs-lookup"><span data-stu-id="6885d-239">Lazy Loading</span></span>](xref:core/querying/related-data/lazy)
| <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A>         | <span data-ttu-id="6885d-240">使用動態 proxy 進行變更追蹤</span><span class="sxs-lookup"><span data-stu-id="6885d-240">Use dynamic proxies for change-tracking</span></span>                     | <span data-ttu-id="6885d-241">即將推出…</span><span class="sxs-lookup"><span data-stu-id="6885d-241">Coming soon...</span></span>

> [!NOTE]
> <span data-ttu-id="6885d-242"><xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A> 和 <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> 是 [microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) NuGet 套件中的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="6885d-242"><xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A> and <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> are extension methods from the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) NuGet package.</span></span> <span data-ttu-id="6885d-243">這種類型的」。UseSomething ( # A1」呼叫是設定及/或使用其他套件中所含 EF Core 擴充功能的建議方式。</span><span class="sxs-lookup"><span data-stu-id="6885d-243">This kind of ".UseSomething()" call is the recommended way to configure and/or use EF Core extensions contained in other packages.</span></span>

### <a name="dbcontextoptions-verses-dbcontextoptionstcontext"></a><span data-ttu-id="6885d-244">`DbContextOptions` 詩句 `DbContextOptions<TContext>`</span><span class="sxs-lookup"><span data-stu-id="6885d-244">`DbContextOptions` verses `DbContextOptions<TContext>`</span></span>

<span data-ttu-id="6885d-245">大部分 `DbContext` 接受的子類別都 `DbContextOptions` 應該使用 [一般](/dotnet/csharp/programming-guide/generics/) `DbContextOptions<TContext>` 變化。</span><span class="sxs-lookup"><span data-stu-id="6885d-245">Most `DbContext` subclasses that accept a `DbContextOptions` should use the [generic](/dotnet/csharp/programming-guide/generics/) `DbContextOptions<TContext>` variation.</span></span> <span data-ttu-id="6885d-246">例如：</span><span class="sxs-lookup"><span data-stu-id="6885d-246">For example:</span></span>

<!--
    public sealed class SealedApplicationDbContext : DbContext
    {
        public SealedApplicationDbContext(DbContextOptions<SealedApplicationDbContext> contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[SealedApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=SealedApplicationDbContext)]

<span data-ttu-id="6885d-247">這樣可確保特定子類型的正確選項 `DbContext` 會從相依性插入中解析，即使在註冊多個子類型時也是如此 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="6885d-247">This ensures that the correct options for the specific `DbContext` subtype are resolved from dependency injection, even when multiple `DbContext` subtypes are registered.</span></span>

> [!TIP]
> <span data-ttu-id="6885d-248">您的 DbCoNtext 不需要密封，但針對不是設計為繼承自的類別，則必須進行密封。</span><span class="sxs-lookup"><span data-stu-id="6885d-248">Your DbContext does not need to be sealed, but sealing is best practice to do so for classes not designed to be inherited from.</span></span>

<span data-ttu-id="6885d-249">但是，如果 `DbContext` 子類型本身是要繼承自的，則它應該會公開受保護的函式，並採用非泛型 `DbContextOptions` 。</span><span class="sxs-lookup"><span data-stu-id="6885d-249">However, if the `DbContext` subtype is itself intended to be inherited from, then it should expose a protected constructor taking a non-generic `DbContextOptions`.</span></span> <span data-ttu-id="6885d-250">例如：</span><span class="sxs-lookup"><span data-stu-id="6885d-250">For example:</span></span>

<!--
    public abstract class ApplicationDbContextBase : DbContext
    {
        protected ApplicationDbContextBase(DbContextOptions contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContextBase](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=ApplicationDbContextBase)]

<span data-ttu-id="6885d-251">這可讓多個具體子類別使用其不同的泛型實例來呼叫這個基底的函式 `DbContextOptions<TContext>` 。</span><span class="sxs-lookup"><span data-stu-id="6885d-251">This allows multiple concrete subclasses to call this base constructor using their different generic `DbContextOptions<TContext>` instances.</span></span> <span data-ttu-id="6885d-252">例如：</span><span class="sxs-lookup"><span data-stu-id="6885d-252">For example:</span></span>

<!--
    public sealed class ApplicationDbContext1 : ApplicationDbContextBase
    {
        public ApplicationDbContext1(DbContextOptions<ApplicationDbContext1> contextOptions)
            : base(contextOptions)
        {
        }
    }

    public sealed class ApplicationDbContext2 : ApplicationDbContextBase
    {
        public ApplicationDbContext2(DbContextOptions<ApplicationDbContext2> contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext12](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=ApplicationDbContext12)]

<span data-ttu-id="6885d-253">請注意，這與直接繼承時的模式完全相同 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="6885d-253">Notice that this is exactly the same pattern as when inheriting from `DbContext` directly.</span></span> <span data-ttu-id="6885d-254">也就是說，基於這個原因，此函 `DbContext` 式本身會接受非泛型 `DbContextOptions` 。</span><span class="sxs-lookup"><span data-stu-id="6885d-254">That is, the `DbContext` constructor itself accepts a non-generic `DbContextOptions` for this reason.</span></span>

<span data-ttu-id="6885d-255">`DbContext`要同時具現化和繼承的子類別應同時公開兩種形式的函式。</span><span class="sxs-lookup"><span data-stu-id="6885d-255">A `DbContext` subclass intended to be both instantiated and inherited from should expose both forms of constructor.</span></span> <span data-ttu-id="6885d-256">例如：</span><span class="sxs-lookup"><span data-stu-id="6885d-256">For example:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> contextOptions)
            : base(contextOptions)
        {
        }

        protected ApplicationDbContext(DbContextOptions contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=ApplicationDbContext)]

## <a name="design-time-dbcontext-configuration"></a><span data-ttu-id="6885d-257">設計階段 DbCoNtext 設定</span><span class="sxs-lookup"><span data-stu-id="6885d-257">Design-time DbContext configuration</span></span>

<span data-ttu-id="6885d-258">EF Core 的設計階段工具（例如 [EF Core 遷移](xref:core/managing-schemas/migrations/index) 的工具）必須能夠探索和建立型別的工作實例，才能 `DbContext` 收集有關應用程式之實體類型的詳細資料，以及它們如何對應到資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="6885d-258">EF Core design-time tools such as those for [EF Core migrations](xref:core/managing-schemas/migrations/index) need to be able to discover and create a working instance of a `DbContext` type in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="6885d-259">這項程式可以是自動的，只要工具可以輕鬆地建立，就會以 `DbContext` 類似于執行時間設定的方式來設定。</span><span class="sxs-lookup"><span data-stu-id="6885d-259">This process can be automatic as long as the tool can easily create the `DbContext` in such a way that it will be configured similarly to how it would be configured at run-time.</span></span>

<span data-ttu-id="6885d-260">雖然提供必要設定資訊的任何模式 `DbContext` 都可以在執行時間運作，但是在設計階段需要使用的工具只能使用 `DbContext` 有限數目的模式。</span><span class="sxs-lookup"><span data-stu-id="6885d-260">While any pattern that provides the necessary configuration information to the `DbContext` can work at run-time, tools that require using a `DbContext` at design-time can only work with a limited number of patterns.</span></span> <span data-ttu-id="6885d-261">這些會在 [設計階段的內容建立](xref:core/cli/dbcontext-creation)中更詳細地討論。</span><span class="sxs-lookup"><span data-stu-id="6885d-261">These are covered in more detail in [Design-Time Context Creation](xref:core/cli/dbcontext-creation).</span></span>

## <a name="avoiding-dbcontext-threading-issues"></a><span data-ttu-id="6885d-262">避免 DbCoNtext 執行緒問題</span><span class="sxs-lookup"><span data-stu-id="6885d-262">Avoiding DbContext threading issues</span></span>

<span data-ttu-id="6885d-263">Entity Framework Core 不支援在相同實例上執行多個平行作業 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="6885d-263">Entity Framework Core does not support multiple parallel operations being run on the same `DbContext` instance.</span></span> <span data-ttu-id="6885d-264">這包括平行執行非同步查詢，以及從多個執行緒進行任何明確的並行使用。</span><span class="sxs-lookup"><span data-stu-id="6885d-264">This includes both parallel execution of async queries and any explicit concurrent use from multiple threads.</span></span> <span data-ttu-id="6885d-265">因此，一律會 `await` 立即非同步呼叫，或 `DbContext` 針對平行執行的作業使用個別的實例。</span><span class="sxs-lookup"><span data-stu-id="6885d-265">Therefore, always `await` async calls immediately, or use separate `DbContext` instances for operations that execute in parallel.</span></span>

<span data-ttu-id="6885d-266">當 EF Core 偵測到同時嘗試使用 `DbContext` 實例時，您會看到 `InvalidOperationException` 含有如下的訊息：</span><span class="sxs-lookup"><span data-stu-id="6885d-266">When EF Core detects an attempt to use a `DbContext` instance concurrently, you'll see an `InvalidOperationException` with a message like this:</span></span>

> <span data-ttu-id="6885d-267">在先前的作業完成之前，在此內容上啟動的第二個作業。</span><span class="sxs-lookup"><span data-stu-id="6885d-267">A second operation started on this context before a previous operation completed.</span></span> <span data-ttu-id="6885d-268">這通常是由使用相同 DbCoNtext 實例的不同執行緒所造成，但是實例成員並不保證是安全線程。</span><span class="sxs-lookup"><span data-stu-id="6885d-268">This is usually caused by different threads using the same instance of DbContext, however instance members are not guaranteed to be thread safe.</span></span>

<span data-ttu-id="6885d-269">未偵測到平行存取時，可能會導致未定義的行為、應用程式當機和資料損毀。</span><span class="sxs-lookup"><span data-stu-id="6885d-269">When concurrent access goes undetected, it can result in undefined behavior, application crashes and data corruption.</span></span>

<span data-ttu-id="6885d-270">有一些常見的錯誤，可能會不慎導致相同實例上的平行存取 `DbContext` ：</span><span class="sxs-lookup"><span data-stu-id="6885d-270">There are common mistakes that can inadvertently cause concurrent access on the same `DbContext` instance:</span></span>

### <a name="asynchronous-operation-pitfalls"></a><span data-ttu-id="6885d-271">非同步作業陷阱</span><span class="sxs-lookup"><span data-stu-id="6885d-271">Asynchronous operation pitfalls</span></span>

<span data-ttu-id="6885d-272">非同步方法可讓 EF Core 起始以非封鎖方式存取資料庫的作業。</span><span class="sxs-lookup"><span data-stu-id="6885d-272">Asynchronous methods enable EF Core to initiate operations that access the database in a non-blocking way.</span></span> <span data-ttu-id="6885d-273">但是，如果呼叫端未等候完成其中一種方法，並繼續在上執行其他作業 `DbContext` ，的狀態 `DbContext` 可能是， (且很可能會) 損毀。</span><span class="sxs-lookup"><span data-stu-id="6885d-273">But if a caller does not await the completion of one of these methods, and proceeds to perform other operations on the `DbContext`, the state of the `DbContext` can be, (and very likely will be) corrupted.</span></span>

<span data-ttu-id="6885d-274">請一律立即等候 EF Core 非同步方法。</span><span class="sxs-lookup"><span data-stu-id="6885d-274">Always await EF Core asynchronous methods immediately.</span></span>

### <a name="implicitly-sharing-dbcontext-instances-via-dependency-injection"></a><span data-ttu-id="6885d-275">透過相依性插入來隱含共用 DbCoNtext 實例</span><span class="sxs-lookup"><span data-stu-id="6885d-275">Implicitly sharing DbContext instances via dependency injection</span></span>

<span data-ttu-id="6885d-276">[`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)擴充方法預設會 `DbContext` 註冊具有[範圍存留期](/aspnet/core/fundamentals/dependency-injection#service-lifetimes)的類型。</span><span class="sxs-lookup"><span data-stu-id="6885d-276">The [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) extension method registers `DbContext` types with a [scoped lifetime](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) by default.</span></span>

<span data-ttu-id="6885d-277">這可安全地避免大多數 ASP.NET Core 應用程式中的平行存取問題，因為在指定的時間內只有一個執行緒執行每個用戶端要求，而且每個要求都會取得個別的相依性插入範圍 (，因此) 個別的 `DbContext` 實例。</span><span class="sxs-lookup"><span data-stu-id="6885d-277">This is safe from concurrent access issues in most ASP.NET Core applications because there is only one thread executing each client request at a given time, and because each request gets a separate dependency injection scope (and therefore a separate `DbContext` instance).</span></span> <span data-ttu-id="6885d-278">針對 Blazor 伺服器裝載模型，會使用一個邏輯要求來維護 Blazor 使用者電路，因此，如果使用預設的插入範圍，每個使用者電路只能使用一個範圍的 DbCoNtext 實例。</span><span class="sxs-lookup"><span data-stu-id="6885d-278">For Blazor Server hosting model, one logical request is used for maintaining the Blazor user circuit, and thus only one scoped DbContext instance is available per user circuit if the default injection scope is used.</span></span>

<span data-ttu-id="6885d-279">任何明確地以平行方式執行多個執行緒的程式碼，都應該確保 `DbContext` 不會同時存取實例。</span><span class="sxs-lookup"><span data-stu-id="6885d-279">Any code that explicitly executes multiple threads in parallel should ensure that `DbContext` instances aren't ever accessed concurrently.</span></span>

<span data-ttu-id="6885d-280">使用相依性插入時，您可以將內容註冊為已設定範圍，並為 `IServiceScopeFactory` 每個執行緒建立範圍)  (，或使用 `DbContext` `AddDbContext` 接受參數) 的多載來註冊作為暫時性 (來達成 `ServiceLifetime` 。</span><span class="sxs-lookup"><span data-stu-id="6885d-280">Using dependency injection, this can be achieved by either registering the context as scoped, and creating scopes (using `IServiceScopeFactory`) for each thread, or by registering the `DbContext` as transient (using the overload of `AddDbContext` which takes a `ServiceLifetime` parameter).</span></span>

## <a name="more-reading"></a><span data-ttu-id="6885d-281">更多閱讀</span><span class="sxs-lookup"><span data-stu-id="6885d-281">More reading</span></span>

- <span data-ttu-id="6885d-282">若要深入瞭解如何使用 DI，請參閱相依性 [插入](/aspnet/core/fundamentals/dependency-injection) 。</span><span class="sxs-lookup"><span data-stu-id="6885d-282">Read [Dependency Injection](/aspnet/core/fundamentals/dependency-injection) to learn more about using DI.</span></span>
- <span data-ttu-id="6885d-283">閱讀 [測試](xref:core/testing/index) 以取得詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="6885d-283">Read [Testing](xref:core/testing/index) for more information.</span></span>
