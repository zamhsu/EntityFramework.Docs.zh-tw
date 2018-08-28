---
title: 設定-建立的 DbContext EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d7a22b5a-4c5b-4e3b-9897-4d7320fcd13f
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: 393349c05ffaf42c6d2520e73abce23def6becc0
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995934"
---
# <a name="configuring-a-dbcontext"></a>設定 DbContext

本文示範基本模式設定`DbContext`透過`DbContextOptions`連接到資料庫，使用特定的 EF Core 提供者和選擇性的行為。

## <a name="design-time-dbcontext-configuration"></a>設計階段 DbContext 組態

EF Core 設計階段工具，例如[移轉](xref:core/managing-schemas/migrations/index)必須能夠探索和建立的工作執行個體`DbContext`從中收集有關應用程式的實體類型和它們如何對應到資料庫結構描述的詳細資料的類型。 此程序可以是自動，只要此工具可以輕鬆地建立`DbContext`的方式，它會設定同樣的方式設定它在執行階段。

同時提供必要的組態資訊的任何模式`DbContext`能夠在執行階段，需要使用的工具`DbContext`在設計階段只能使用有限數目的模式。 這些先決條件詳細[設計階段內容建立](xref:core/miscellaneous/cli/dbcontext-creation)一節。

## <a name="configuring-dbcontextoptions"></a>設定 DbContextOptions

`DbContext` 必須具有執行個體的`DbContextOptions`才能執行任何工作。 `DbContextOptions`這類執行個體帶有的組態資訊：

- 若要使用，資料庫提供者通常會選取叫用方法時，例如`UseSqlServer`或 `UseSqlite`
- 任何必要的連接字串或識別項的資料庫執行個體，通常會傳遞做為引數至上述提供者選取方法
- 所有提供者層級選擇性行為的選取器，通常也會在提供者選取方法的呼叫鏈結
- 所有一般 EF Core 行為的選取器，通常鏈結提供者選取器方法之前或之後

下列範例會設定`DbContextOptions`若要使用 SQL Server 提供者，連接中所包含`connectionString`變數、 提供者層級命令逾時和 EF Core 行為選取器，讓執行中的所有查詢`DbContext`[否追蹤](xref:core/querying/tracking#no-tracking-queries)預設：

``` csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]  
> 提供者選取器方法和其他行為選取器方法先前所述的擴充方法上`DbContextOptions`或提供者特有的選項類別。 若要存取這些擴充方法，您可能需要有命名空間 (通常`Microsoft.EntityFrameworkCore`) 中的範圍，並在專案中包含其他套件相依性。

`DbContextOptions`可以提供給`DbContext`藉由覆寫`OnConfiguring`方法或從外部透過建構函式引數。

如果使用，`OnConfiguring`最後套用的而且可以覆寫建構函式的引數所提供的選項。

### <a name="constructor-argument"></a>建構函式引數

使用建構函式的內容程式碼：

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
> DbContext 的基底建構函式也可接受的非泛型版本`DbContextOptions`，但有多個內容類型的應用程式不建議使用非泛型版本。

從建構函式引數初始化應用程式程式碼：

``` csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a>OnConfiguring

內容的程式碼`OnConfiguring`:

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

應用程式程式碼，來初始化`DbContext`使用`OnConfiguring`:

``` csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> 這種方法本身就無法進行測試，除非測試目標完整的資料庫。

### <a name="using-dbcontext-with-dependency-injection"></a>使用相依性插入使用 DbContext

EF Core 支援使用`DbContext`與相依性插入容器。 您的 DbContext 類型可以利用加入至服務容器`AddDbContext<TContext>`方法。

`AddDbContext<TContext>` 將這兩個至 DbContext 類型`TContext`，和對應`DbContextOptions<TContext>`適用於資料隱碼攻擊，從服務容器。

請參閱[詳細閱讀](#more-reading)如下如需相依性插入所需的詳細資訊。

新增`Dbcontext`相依性插入式攻擊：

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

這需要新增[建構函式引數](#constructor-argument)為您的 DbContext 類型可接受`DbContextOptions<TContext>`。

內容的程式碼：

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

（在 ASP.NET Core) 的應用程式程式碼：

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

（ServiceProvider 直接使用，較不常見） 的應用程式程式碼：

``` csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="more-reading"></a>詳細閱讀

* 讀取[開始使用 ASP.NET Core](../get-started/aspnetcore/index.md) 如需有關使用 EF 與 ASP.NET Core。
* 讀取[相依性插入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)若要深入了解使用 DI。
* 讀取[測試](testing/index.md)如需詳細資訊。
