---
title: 設定 DbCoNtext-EF Core
description: 使用 Entity Framework Core 設定 DbcoNtext 的策略
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d7a22b5a-4c5b-4e3b-9897-4d7320fcd13f
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: 3e45199e6fc0c8c105ccb0bb03175b4b08716b3f
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617791"
---
# <a name="configuring-a-dbcontext"></a>設定 DbContext

本文說明 `DbContext` `DbContextOptions` 使用特定 EF Core 提供者和選擇性行為來設定 via 的基本模式，以連接到資料庫。

## <a name="design-time-dbcontext-configuration"></a>設計階段 DbCoNtext 設定

EF Core 的設計階段工具（例如 [遷移](xref:core/managing-schemas/migrations/index) ）必須能夠探索和建立型別的工作實例，才能 `DbContext` 收集有關應用程式實體類型的詳細資料，以及它們如何對應到資料庫架構。 這項程式可以是自動的，只要工具可以輕鬆地建立，就會以 `DbContext` 類似于執行時間設定的方式來設定。

雖然提供必要設定資訊的任何模式 `DbContext` 都可以在執行時間運作，但是在設計階段需要使用的工具只能使用 `DbContext` 有限數目的模式。 這些在 [設計階段內容建立](xref:core/miscellaneous/cli/dbcontext-creation) 一節中會更詳細地討論。

## <a name="configuring-dbcontextoptions"></a>設定 DbCoNtextOptions

`DbContext` 必須具有的實例 `DbContextOptions` ，才能執行任何工作。 此 `DbContextOptions` 實例會攜帶設定資訊，例如：

- 要使用的資料庫提供者，通常是透過叫用方法（例如或）來選取 `UseSqlServer` `UseSqlite` 。 這些擴充方法需要對應的提供者封裝，例如 `Microsoft.EntityFrameworkCore.SqlServer` 或 `Microsoft.EntityFrameworkCore.Sqlite` 。 這些方法會在 `Microsoft.EntityFrameworkCore` 命名空間中定義。
- 資料庫實例的任何必要連接字串或識別碼，通常會做為引數傳遞至上面所述的提供者選取方法
- 任何提供者層級的選用行為選取器，通常也會在對提供者選取方法的呼叫內連結
- 任何一般 EF Core 行為選取器，通常是在提供者選取器方法之後或之前連結

下列範例會將設定 `DbContextOptions` 為使用 SQL Server 提供者、變數中包含的連接 `connectionString` 、提供者層級的命令逾時，以及讓所有查詢在 `DbContext` [no-tracking](xref:core/querying/tracking#no-tracking-queries)預設情況下執行的 EF Core 行為選取器：

``` csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]  
> 上述提供者選取器方法和其他行為選取器方法，是 `DbContextOptions` 或提供者特定選項類別上的擴充方法。 若要能夠存取這些擴充方法，您可能需要命名空間 (通常 `Microsoft.EntityFrameworkCore`) 在範圍內，並在專案中包含額外的套件相依性。

您 `DbContextOptions` 可以藉 `DbContext` 由覆寫 `OnConfiguring` 方法或從外部透過函式引數來提供。

如果使用這兩種 `OnConfiguring` 方法，則會最後套用，而且可以覆寫提供給函式引數的選項。

### <a name="constructor-argument"></a>函數引數

您的函式可以直接接受，如下 `DbContextOptions` 所示：

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
> DbCoNtext 的基底函式也會接受的非泛型版本 `DbContextOptions` ，但不建議針對具有多個內容類型的應用程式使用非泛型版本。

您的應用程式現在可以在具現 `DbContextOptions` 化內容時傳遞，如下所示：

``` csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a>OnConfiguring

您也可以在 `DbContextOptions` 內容本身內初始化。 雖然您可以使用這項技術進行基本設定，但通常還是需要從外部取得某些設定詳細資料，例如資料庫連接字串。 這可以透過設定 API 或任何其他方式來完成。

若要在 `DbContextOptions` 內容中初始化，請覆寫 `OnConfiguring` 方法，並在提供的上呼叫方法 `DbContextOptionsBuilder` ：

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

應用程式可以直接將這類內容具現化，而不將任何內容傳遞至其函式：

``` csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> 除非測試以完整資料庫為目標，否則此方法不會自行測試。

### <a name="using-dbcontext-with-dependency-injection"></a>使用 DbCoNtext 搭配相依性插入

EF Core 支援搭配相依性 `DbContext` 插入容器使用。 您可以使用方法，將您的 DbCoNtext 類型加入至服務容器 `AddDbContext<TContext>` 。

`AddDbContext<TContext>` 會讓您的 DbCoNtext 類型、 `TContext` 和對應的 `DbContextOptions<TContext>` 可從服務容器中插入。

如需有關相依性插入的詳細資訊，請參閱下面的 [詳細](#more-reading) 資訊。

將加入 `DbContext` 至相依性插入：

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

這需要在接受的 DbCoNtext 型別中加入一個函式 [引數](#constructor-argument) `DbContextOptions<TContext>` 。

內容程式碼：

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

ASP.NET Core) 中的應用程式程式碼 (：

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

應用程式程式碼 (直接使用 ServiceProvider，較不常見) ：

``` csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="avoiding-dbcontext-threading-issues"></a>避免 DbCoNtext 執行緒問題

Entity Framework Core 不支援在相同實例上執行多個平行作業 `DbContext` 。 這包括平行執行非同步查詢，以及從多個執行緒進行任何明確的並行使用。 因此，一律會 `await` 立即非同步呼叫，或 `DbContext` 針對平行執行的作業使用個別的實例。

當 EF Core 偵測到同時嘗試使用 `DbContext` 實例時，您會看到 `InvalidOperationException` 含有如下的訊息：

> 在先前的作業完成之前，在此內容上啟動的第二個作業。 這通常是由使用相同 DbCoNtext 實例的不同執行緒所造成，但是實例成員並不保證是安全線程。

未偵測到平行存取時，可能會導致未定義的行為、應用程式當機和資料損毀。

有一些常見的錯誤，可能會不慎導致相同實例上的平行存取 `DbContext` ：

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a>忘記等候非同步作業完成後，再于相同 DbCoNtext 上啟動任何其他作業

非同步方法可讓 EF Core 起始以非封鎖方式存取資料庫的作業。 但是，如果呼叫端未等候完成其中一種方法，並繼續在上執行其他作業 `DbContext` ，的狀態 `DbContext` 可能是， (且很可能會) 損毀。

請一律立即等候 EF Core 非同步方法。  

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a>透過相依性插入，隱含地跨多個執行緒共用 DbCoNtext 實例

[`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)擴充方法預設會 `DbContext` 註冊具有[範圍存留期](/aspnet/core/fundamentals/dependency-injection#service-lifetimes)的類型。

這可安全地避免大多數 ASP.NET Core 應用程式中的平行存取問題，因為在指定的時間內只有一個執行緒執行每個用戶端要求，而且每個要求都會取得個別的相依性插入範圍 (，因此) 個別的 `DbContext` 實例。 針對 Blazor 伺服器裝載模型，會使用一個邏輯要求來維護 Blazor 使用者電路，因此，如果使用預設的插入範圍，每個使用者電路只能使用一個範圍的 DbCoNtext 實例。

任何明確地以平行方式執行多個執行緒的程式碼，都應該確保 `DbContext` 不會同時存取實例。

使用相依性插入時，您可以將內容註冊為已設定範圍，並為 `IServiceScopeFactory` 每個執行緒建立範圍)  (，或使用 `DbContext` `AddDbContext` 接受參數) 的多載來註冊作為暫時性 (來達成 `ServiceLifetime` 。

## <a name="more-reading"></a>更多閱讀

- 若要深入瞭解如何使用 DI，請參閱相依性 [插入](/aspnet/core/fundamentals/dependency-injection) 。
- 閱讀 [測試](xref:core/miscellaneous/testing/index) 以取得詳細資訊。
