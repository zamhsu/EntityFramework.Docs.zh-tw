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
# <a name="dbcontext-lifetime-configuration-and-initialization"></a>DbCoNtext 存留期、設定和初始化

本文說明初始化和設定實例的基本模式 <xref:Microsoft.EntityFrameworkCore.DbContext> 。

## <a name="the-dbcontext-lifetime"></a>DbCoNtext 存留期

的存留期 `DbContext` 會在實例建立時開始，並在實例 [處置](/dotnet/standard/garbage-collection/unmanaged)時結束。 `DbContext`實例的設計目的是要用於 _單一_[工作單位](https://www.martinfowler.com/eaaCatalog/unitOfWork.html)。 這表示實例的存留期 `DbContext` 通常很短。

> [!TIP]
> 若要從上述連結報價 Fowler，您可以在商務交易期間追蹤可能影響資料庫的所有專案。 當您完成時，它會找出因您的工作而改變資料庫所需完成的所有工作。」

使用 Entity Framework Core (EF Core) 的一般工作單位包括：

- 建立 `DbContext` 實例
- 依內容追蹤實體實例。 實體會被追蹤
  - [從查詢傳回](xref:core/querying/tracking)
  - [新增或附加至內容](xref:core/saving/disconnected-entities)
- 視需要對追蹤的實體進行變更，以執行商務規則
- <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 或 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> 呼叫。 EF Core 會偵測所做的變更，並將它們寫入資料庫中。
- `DbContext`實例已處置

> [!IMPORTANT]
>
> - <xref:Microsoft.EntityFrameworkCore.DbContext>使用之後，請務必處置。 這可確保釋放任何未管理的資源，並取消註冊任何事件或其他攔截，以避免在實例維持參考時記憶體流失。
> - [DbCoNtext **不是安全線程**](#avoiding-dbcontext-threading-issues)。 請勿線上程之間共用內容。 在繼續使用內容實例之前，請務必 [等待](/dotnet/csharp/language-reference/operators/await) 所有非同步呼叫。
> - <xref:System.InvalidOperationException>EF Core 程式碼擲回的會使內容進入無法復原的狀態。 這類例外狀況表示程式錯誤，不是設計來復原。

## <a name="dbcontext-in-dependency-injection-for-aspnet-core"></a>ASP.NET Core 的相依性插入中 DbCoNtext

在許多 web 應用程式中，每個 HTTP 要求都對應至單一工作單位。 這會將內容存留期與要求的內容存留期系結至 web 應用程式的良好預設值。

ASP.NET Core 的應用程式會使用相依性 [插入進行設定](/aspnet/core/fundamentals/startup)。 您可以使用的方法，將 EF Core 加入至這個設定 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> [`ConfigurureServices`](/aspnet/core/fundamentals/startup#the-configureservices-method) `Startup.cs` 。 例如：

<!--
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddControllers();
            
            services.AddDbContext<ApplicationDbContext>(
                options => options.UseSqlServer("name=ConnectionStrings:DefaultConnection"));
        }
-->
[!code-csharp[ConfigureServices](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/Startup.cs?name=ConfigureServices)]

此範例會 `DbContext` `ApplicationDbContext` 在 ASP.NET Core 應用程式服務提供者 (中註冊一個稱為範圍服務的子類別也稱為 相依性插入容器) 。 內容設定為使用 SQL Server 資料庫提供者，而且會從 ASP.NET Core 設定讀取連接字串。 在呼叫中的 _位置_ ，通常並不重要 `ConfigureServices` `AddDbContext` 。

`ApplicationDbContext`類別必須使用參數公開公用的函式 `DbContextOptions<ApplicationDbContext>` 。 這是將內容設定 `AddDbContext` 傳遞至的方式 `DbContext` 。 例如：

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

`ApplicationDbContext` 然後可以透過函式插入，在 ASP.NET Core 控制器或其他服務中使用。 例如：

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

最終結果是 `ApplicationDbContext` 針對每個要求所建立的實例，並傳遞至控制器，以在要求結束時進行處置之前，先執行工作單位。

進一步閱讀本文，以深入瞭解設定選項。 此外，如需有關 ASP.NET Core 中的設定和相依性插入的詳細資訊，請參閱 ASP.NET Core 中的 [應用程式啟動](/aspnet/core/fundamentals/startup) 以及 [ASP.NET Core 中](/aspnet/core/fundamentals/dependency-injection) 的相依性插入。

<!-- See also [Using Dependency Injection](TODO) for advanced dependency injection configuration with EF Core. -->

## <a name="simple-dbcontext-initialization-with-new"></a>使用 ' new ' 的簡單 DbCoNtext 初始化

`DbContext` 您可以使用一般的 .NET 方式來建立實例，例如 `new` 在 c # 中。 您可以藉由覆寫 `OnConfiguring` 方法或將選項傳遞至函式，來執行設定。 例如：

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

此模式也可讓您輕鬆地透過函式傳遞設定，例如連接字串 `DbContext` 。 例如：

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

或者，您 `DbContextOptionsBuilder` 可以使用來建立 `DbContextOptions` 物件，然後傳遞至該函式 `DbContext` 。 這可讓 `DbContext` 設定的相依性插入也明確地建立。 例如，針對 `ApplicationDbContext` 上述 ASP.NET Core web 應用程式使用定義時：

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

`DbContextOptions`可以建立，並且可以明確呼叫函式：

<!--
            var contextOptions = new DbContextOptionsBuilder<ApplicationDbContext>()
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test")
                .Options;

            using var context = new ApplicationDbContext(contextOptions);
-->
[!code-csharp[UseNewForWebApp](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/UseNewForWebApp.cs?name=UseNewForWebApp)]

## <a name="using-a-dbcontext-factory-eg-for-blazor"></a>使用 DbCoNtext factory (例如 Blazor) 

某些應用程式類型 (例如 [ASP.NET Core Blazor](/aspnet/core/blazor/)) 使用相依性插入，但不會建立與所需存留期相符的服務範圍 `DbContext` 。 即使這類的對齊存在，應用程式可能需要在此範圍內執行多個工作單位。 例如，單一 HTTP 要求內的多個工作單位。

在這些情況下， <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextFactory%2A> 可以用來註冊建立實例的 factory `DbContext` 。 例如：

<!--
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddDbContextFactory<ApplicationDbContext>(options =>
                options.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
        }
-->
[!code-csharp[ConfigureServices](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/FactoryServicesExample.cs?name=ConfigureServices)]

`ApplicationDbContext`類別必須使用參數公開公用的函式 `DbContextOptions<ApplicationDbContext>` 。 這與上述傳統 ASP.NET Core 一節中所使用的模式相同。

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

然後，您 `DbContextFactory` 可以透過使用函式插入，在其他服務中使用 factory。 例如：

<!--
        private readonly IDbContextFactory<ApplicationDbContext> _contextFactory;

        public MyController(IDbContextFactory<ApplicationDbContext> contextFactory)
        {
            _contextFactory = contextFactory;
        }
-->
[!code-csharp[Construct](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/MyController.cs?name=Construct)]

然後，您可以在服務程式代碼中使用插入的 factory 來建立 DbCoNtext 實例。 例如：

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

請注意， `DbContext` 以這種方式建立的實例 **不** 是由應用程式的服務提供者所管理，因此必須由應用程式處置。

如需搭配 Blazor 使用 EF Core 的詳細資訊，請參閱 [ASP.NET Core Blazor Server with Entity Framework Core](/aspnet/core/blazor/blazor-server-ef-core) 。

## <a name="dbcontextoptions"></a>DbCoNtextOptions

所有設定的起點 `DbContext` 都是 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> 。 有三種方式可取得此產生器：

- `AddDbContext`和相關方法
- 在 `OnConfiguring` 中
- 明確地使用 `new`

上述各節會顯示上述各項的範例。 無論產生器的來源為何，都可以套用相同的設定。 此外， `OnConfiguring` 不論內容的建立方式為何，一律會呼叫。 這表示 `OnConfiguring` 即使在使用中，也可以用來執行其他設定 `AddDbContext` 。

### <a name="configuring-the-database-provider"></a>設定資料庫提供者

每個 `DbContext` 實例都必須設定為只使用一個資料庫提供者。 子類型 (不同的實例 `DbContext` 可以搭配不同的資料庫提供者使用，但單一實例只能使用一個。 ) 資料庫提供者是使用特定的「呼叫」進行設定。 `Use*` 例如，若要使用 SQL Server 資料庫提供者：

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

這些 `Use*` 方法是由資料庫提供者所執行的擴充方法。 這表示必須先安裝資料庫提供者 NuGet 封裝，才能使用擴充方法。

> [!TIP]
> EF Core 資料庫提供者會大量使用 [擴充方法](/dotnet/csharp/programming-guide/classes-and-structs/extension-methods)。 如果編譯器指出找不到方法，則請確定已安裝提供者的 NuGet 套件，且您已 `using Microsoft.EntityFrameworkCore;` 在程式碼中。

下表包含一般資料庫提供者的範例。

| 資料庫系統              | 範例設定                         | Nuget 套件
|:-----------------------------|-----------------------------------------------|--------------
| SQL Server 或 Azure SQL      | `.UseSqlServer(connectionString)`             | [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)
| Azure Cosmos DB              | `.UseCosmos(connectionString, databaseName)`  | [Microsoft.EntityFrameworkCore.Cosmos](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/)
| SQLite                       | `.UseSqlite(connectionString)`                | [Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)
| EF Core 記憶體內部資料庫   | `.UseInMemoryDatabase(databaseName)`          | [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)
| 于 postgresql                  | `.UseNpgsql(connectionString)`                | [Npgsql.EntityFrameworkCore.PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL/)
| MySQL/適用于 mariadb *               | `.UseMySql((connectionString)`                | [Pomelo.EntityFrameworkCore.MySql](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql/)
| Oracle*                      | `.UseOracle(connectionString)`                | [Oracle.EntityFrameworkCore](https://www.nuget.org/packages/Oracle.EntityFrameworkCore/)

* 這些資料庫提供者並未隨附于 Microsoft。 如需有關資料庫提供者的詳細資訊，請參閱 [資料庫提供者](xref:core/providers/index) 。

> [!WARNING]
> 記憶體內部資料庫的 EF Core 並非針對生產環境使用而設計。 此外，它可能不是最佳選擇，甚至是進行測試。 如需詳細資訊，請參閱 [使用 EF Core 的測試程式碼](xref:core/testing/index) 。

如需搭配使用連接字串與 EF Core 的詳細資訊，請參閱 [連接字串](xref:core/miscellaneous/connection-strings) 。

資料庫提供者專屬的選擇性設定是在其他提供者特定的產生器中執行。 例如，使用 <xref:Microsoft.EntityFrameworkCore.Infrastructure.SqlServerDbContextOptionsBuilder.EnableRetryOnFailure%2A> 來設定連接到 AZURE SQL 時的連線恢復功能：

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
> SQL Server 和 Azure SQL 都使用相同的資料庫提供者。 不過，建議您在連接到 SQL Azure 時使用 [連接復原](xref:core/miscellaneous/connection-resiliency) 。

如需提供者特定設定的詳細資訊，請參閱 [資料庫提供者](xref:core/providers/index) 。

### <a name="other-dbcontext-configuration"></a>其他 DbCoNtext 設定

其他設定 `DbContext` 可以在 (之前或之後連結，不會造成任何差異) `Use*` 呼叫。 例如，若要開啟機密資料記錄：

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

下表包含在上呼叫的一般方法的範例 `DbContextOptionsBuilder` 。

| DbCoNtextOptionsBuilder 方法                                                             | 作用                                                | 深入了解
|:-------------------------------------------------------------------------------------------|-------------------------------------------------------------|--------------
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.UseQueryTrackingBehavior%2A>   | 設定查詢的預設追蹤行為              | [查詢追蹤行為](xref:core/querying/tracking)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A>                      | 取得 EF Core 記錄 (EF Core 5.0 和更新版本的簡單方式)     | [記錄、事件和診斷](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.UseLoggerFactory%2A>           | 註冊 `Micrsofot.Extensions.Logging` factory         | [記錄、事件和診斷](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> | 包含例外狀況和記錄中的應用程式資料         | [記錄、事件和診斷](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A>       | 更詳細的查詢錯誤 (的效能成本)   | [記錄、事件和診斷](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A>          | 忽略或擲回警告和其他事件               | [記錄、事件和診斷](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A>            | 註冊 EF Core 攔截器                              | [記錄、事件和診斷](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A>            | 使用動態 proxy 進行消極式載入                        | [延遲載入](xref:core/querying/related-data/lazy)
| <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A>         | 使用動態 proxy 進行變更追蹤                     | 即將推出…

> [!NOTE]
> <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A> 和 <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> 是 [microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) NuGet 套件中的擴充方法。 這種類型的」。UseSomething ( # A1」呼叫是設定及/或使用其他套件中所含 EF Core 擴充功能的建議方式。

### <a name="dbcontextoptions-verses-dbcontextoptionstcontext"></a>`DbContextOptions` 詩句 `DbContextOptions<TContext>`

大部分 `DbContext` 接受的子類別都 `DbContextOptions` 應該使用 [一般](/dotnet/csharp/programming-guide/generics/) `DbContextOptions<TContext>` 變化。 例如：

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

這樣可確保特定子類型的正確選項 `DbContext` 會從相依性插入中解析，即使在註冊多個子類型時也是如此 `DbContext` 。

> [!TIP]
> 您的 DbCoNtext 不需要密封，但針對不是設計為繼承自的類別，則必須進行密封。

但是，如果 `DbContext` 子類型本身是要繼承自的，則它應該會公開受保護的函式，並採用非泛型 `DbContextOptions` 。 例如：

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

這可讓多個具體子類別使用其不同的泛型實例來呼叫這個基底的函式 `DbContextOptions<TContext>` 。 例如：

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

請注意，這與直接繼承時的模式完全相同 `DbContext` 。 也就是說，基於這個原因，此函 `DbContext` 式本身會接受非泛型 `DbContextOptions` 。

`DbContext`要同時具現化和繼承的子類別應同時公開兩種形式的函式。 例如：

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

## <a name="design-time-dbcontext-configuration"></a>設計階段 DbCoNtext 設定

EF Core 的設計階段工具（例如 [EF Core 遷移](xref:core/managing-schemas/migrations/index) 的工具）必須能夠探索和建立型別的工作實例，才能 `DbContext` 收集有關應用程式之實體類型的詳細資料，以及它們如何對應到資料庫架構。 這項程式可以是自動的，只要工具可以輕鬆地建立，就會以 `DbContext` 類似于執行時間設定的方式來設定。

雖然提供必要設定資訊的任何模式 `DbContext` 都可以在執行時間運作，但是在設計階段需要使用的工具只能使用 `DbContext` 有限數目的模式。 這些會在 [設計階段的內容建立](xref:core/cli/dbcontext-creation)中更詳細地討論。

## <a name="avoiding-dbcontext-threading-issues"></a>避免 DbCoNtext 執行緒問題

Entity Framework Core 不支援在相同實例上執行多個平行作業 `DbContext` 。 這包括平行執行非同步查詢，以及從多個執行緒進行任何明確的並行使用。 因此，一律會 `await` 立即非同步呼叫，或 `DbContext` 針對平行執行的作業使用個別的實例。

當 EF Core 偵測到同時嘗試使用 `DbContext` 實例時，您會看到 `InvalidOperationException` 含有如下的訊息：

> 在先前的作業完成之前，在此內容上啟動的第二個作業。 這通常是由使用相同 DbCoNtext 實例的不同執行緒所造成，但是實例成員並不保證是安全線程。

未偵測到平行存取時，可能會導致未定義的行為、應用程式當機和資料損毀。

有一些常見的錯誤，可能會不慎導致相同實例上的平行存取 `DbContext` ：

### <a name="asynchronous-operation-pitfalls"></a>非同步作業陷阱

非同步方法可讓 EF Core 起始以非封鎖方式存取資料庫的作業。 但是，如果呼叫端未等候完成其中一種方法，並繼續在上執行其他作業 `DbContext` ，的狀態 `DbContext` 可能是， (且很可能會) 損毀。

請一律立即等候 EF Core 非同步方法。

### <a name="implicitly-sharing-dbcontext-instances-via-dependency-injection"></a>透過相依性插入來隱含共用 DbCoNtext 實例

[`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)擴充方法預設會 `DbContext` 註冊具有[範圍存留期](/aspnet/core/fundamentals/dependency-injection#service-lifetimes)的類型。

這可安全地避免大多數 ASP.NET Core 應用程式中的平行存取問題，因為在指定的時間內只有一個執行緒執行每個用戶端要求，而且每個要求都會取得個別的相依性插入範圍 (，因此) 個別的 `DbContext` 實例。 針對 Blazor 伺服器裝載模型，會使用一個邏輯要求來維護 Blazor 使用者電路，因此，如果使用預設的插入範圍，每個使用者電路只能使用一個範圍的 DbCoNtext 實例。

任何明確地以平行方式執行多個執行緒的程式碼，都應該確保 `DbContext` 不會同時存取實例。

使用相依性插入時，您可以將內容註冊為已設定範圍，並為 `IServiceScopeFactory` 每個執行緒建立範圍)  (，或使用 `DbContext` `AddDbContext` 接受參數) 的多載來註冊作為暫時性 (來達成 `ServiceLifetime` 。

## <a name="more-reading"></a>更多閱讀

- 若要深入瞭解如何使用 DI，請參閱相依性 [插入](/aspnet/core/fundamentals/dependency-injection) 。
- 閱讀 [測試](xref:core/testing/index) 以取得詳細資訊。
