---
title: 設定 DbCoNtext-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d7a22b5a-4c5b-4e3b-9897-4d7320fcd13f
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: e04c1b65df96819f3493e0ed34ccf26609511f6a
ms.sourcegitcommit: 37d0e0fd1703467918665a64837dc54ad2ec7484
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72445918"
---
# <a name="configuring-a-dbcontext"></a>設定 DbContext

本文說明透過 `DbContextOptions` 來設定 `DbContext` 的基本模式，以使用特定的 EF Core 提供者和選擇性的行為來連接到資料庫。

## <a name="design-time-dbcontext-configuration"></a>設計階段 DbCoNtext 設定

EF Core 設計階段工具（例如[遷移](xref:core/managing-schemas/migrations/index)）必須能夠探索並建立 @no__t 1 類型的工作實例，才能收集應用程式實體類型的詳細資料，以及它們如何對應到資料庫架構。 此程式可以是自動的，只要工具可以輕鬆地建立 `DbContext`，如此一來，它就會以類似于執行時間設定的方式來設定。

雖然提供必要設定資訊給 `DbContext` 的任何模式在執行時間都可以運作，但是在設計階段需要使用 `DbContext` 的工具只能使用有限數目的模式。 在[設計階段內容建立](xref:core/miscellaneous/cli/dbcontext-creation)一節中，將會更詳細地說明這些功能。

## <a name="configuring-dbcontextoptions"></a>設定 DbCoNtextOptions

`DbContext` 必須有 `DbContextOptions` 的實例，才能執行任何工作。 @No__t-0 實例會攜帶如下的設定資訊：

- 要使用的資料庫提供者，通常是藉由叫用方法（例如 `UseSqlServer` 或 `UseSqlite`）來選取。 這些擴充方法需要對應的提供者封裝，例如 `Microsoft.EntityFrameworkCore.SqlServer` 或 `Microsoft.EntityFrameworkCore.Sqlite`。 這些方法會在 `Microsoft.EntityFrameworkCore` 命名空間中定義。
- 資料庫實例的任何必要連接字串或識別碼，通常會當做引數傳遞給上述提供者選取方法
- 任何提供者層級的選擇性行為選取器，通常也會在對提供者選取方法的呼叫內進行連結
- 任何一般 EF Core 行為選取器，通常會在提供者選取器方法之後或之前連結

下列範例會將 `DbContextOptions` 設定為使用 SQL Server 提供者、包含在 @no__t 1 變數中的連接、提供者層級的命令逾時，以及 EF Core 行為選取器，讓所有查詢都在 `DbContext` 中執行（[不追蹤）](xref:core/querying/tracking#no-tracking-queries)預設：

``` csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]  
> 上述提供者選取器方法和其他行為選取器方法是 @no__t 0 或提供者專屬選項類別上的擴充方法。 為了能夠存取這些擴充方法，您可能需要在範圍中具有命名空間（通常是 `Microsoft.EntityFrameworkCore`），並在專案中包含其他封裝相依性。

@No__t-0 可以藉由覆寫 @no__t 2 方法或外部透過函式引數來提供給 `DbContext`。

如果同時使用這兩個參數，`OnConfiguring` 會最後套用，而且可以覆寫提供給此函式引數的選項。

### <a name="constructor-argument"></a>函數引數

您的函式可以直接接受 `DbContextOptions`，如下所示：

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
> DbCoNtext 的基底函式也會接受非泛型版本的 `DbContextOptions`，但不建議針對具有多個內容類型的應用程式使用非泛型版本。

您的應用程式現在可以在具現化內容時傳遞 `DbContextOptions`，如下所示：

``` csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a>OnConfiguring

您也可以在內容本身中初始化 `DbContextOptions`。 雖然您可以使用這項技術來進行基本設定，但您通常還是需要從外部取得特定的設定詳細資料，例如資料庫連接字串。 這可以透過設定 API 或任何其他方式來完成。

若要在內容中初始化 `DbContextOptions`，請覆寫 `OnConfiguring` 方法，並在提供的 `DbContextOptionsBuilder` 上呼叫方法：

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

應用程式可以直接具現化這類內容，而不需要將任何專案傳遞至其「其」

``` csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> 除非測試是以完整資料庫為目標，否則此方法不會使其本身進行測試。

### <a name="using-dbcontext-with-dependency-injection"></a>搭配使用 DbCoNtext 與相依性插入

EF Core 支援使用 `DbContext` 搭配相依性插入容器。 您可以使用 `AddDbContext<TContext>` 方法，將您的 DbCoNtext 類型新增至服務容器。

`AddDbContext<TContext>` 會讓您的 DbCoNtext 類型（`TContext`）和對應的 `DbContextOptions<TContext>` 可從服務容器中進行插入。

如需有關相依性插入的詳細資訊，請參閱下方的進一步[閱讀](#more-reading)。

將 `DbContext` 新增至相依性插入：

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

這需要將函式[引數](#constructor-argument)加入至接受 `DbContextOptions<TContext>` 的 DbCoNtext 類型。

內容代碼：

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

應用程式代碼（在 ASP.NET Core 中）：

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

應用程式代碼（直接使用 ServiceProvider，較不常見）：

``` csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```
## <a name="avoiding-dbcontext-threading-issues"></a>避免 DbCoNtext 執行緒問題

Entity Framework Core 不支援在相同的 `DbContext` 實例上執行多個平行作業。 這包括平行執行非同步查詢，以及從多個執行緒進行任何明確的並行使用。 因此，一律會立即 `await` 非同步呼叫，或針對平行執行的作業使用個別的 @no__t 1 實例。

當 EF Core 偵測到同時嘗試使用 `DbContext` 實例時，您會看到一則訊息 `InvalidOperationException`，如下所示： 

> 在先前的作業完成之前，已在此內容上啟動第二個作業。 這通常是因為不同的執行緒使用相同的 DbCoNtext 實例而造成，但是實例成員不一定是安全線程。

未偵測到平行存取時，可能會導致未定義的行為、應用程式當機和資料損毀。

在相同的 `DbContext` 實例上，可能會不小心造成平行存取的常見錯誤：

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a>忘了等待非同步作業完成，再于相同的 DbCoNtext 上啟動任何其他作業

非同步方法可讓 EF Core 起始以非封鎖方式存取資料庫的作業。 但是，如果呼叫端不會等待其中一個方法完成，並繼續在 `DbContext` 上執行其他作業，則 `DbContext` 的狀態可以是（而且很可能會）損毀。 

一律等待立即 EF Core 非同步方法。  

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a>透過相依性插入，在多個執行緒之間隱含共用 DbCoNtext 實例

[@No__t 1](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)擴充方法預設會註冊具有[限定範圍存留期](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)的 @no__t 2 類型。 

這不是 ASP.NET Core 應用程式中的平行存取問題的安全，因為在指定的時間內只有一個執行緒執行每個用戶端要求，而且每個要求都會取得個別的相依性插入範圍（因此是個別的 `DbContext` 實例）。

不過，任何明確地以平行方式執行多個執行緒的程式碼，都應該確保不會同時存取 @no__t 0 的實例。

使用相依性插入，您可以將內容註冊為已設定範圍並為每個執行緒建立範圍（使用 `IServiceScopeFactory`），或將 `DbContext` 註冊為暫時性（使用採用 `ServiceLifetime` 參數的 `AddDbContext` 的多載），藉此達成此目的。

## <a name="more-reading"></a>閱讀更多

* 若要深入瞭解如何使用 DI，請參閱相依性[插入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)。
* 如需詳細資訊，請參閱[測試](testing/index.md)。
