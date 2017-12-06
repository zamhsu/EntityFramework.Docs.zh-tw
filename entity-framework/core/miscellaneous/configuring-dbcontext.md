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
# <a name="configuring-a-dbcontext"></a>設定建立的 DbContext

本文將說明的設定模式`DbContext`與`DbContextOptions`。 選項主要用來選取並設定資料存放區。

## <a name="configuring-dbcontextoptions"></a>設定 DbContextOptions

`DbContext`必須具有執行個體的`DbContextOptions`才能執行。 這可以透過覆寫設定`OnConfiguring`，或從外部提供透過建構函式引數。

如果同時使用`OnConfiguring`上提供選項，也就是說，它加總，而且可以執行覆寫建構函式的引數所提供的選項。

### <a name="constructor-argument"></a>建構函式引數

使用建構函式的內容程式碼

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
> 基底的 DbContext 建構函式也可接受的非泛型版本`DbContextOptions`。 有多個內容類型的應用程式，不建議使用非泛型版本。

應用程式程式碼，以從建構函式引數初始化

``` csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a>OnConfiguring

> [!WARNING]  
> `OnConfiguring`上次發生，而且可以覆寫從 DI 或建構函式取得的選項。 這種方法本身就無法測試 （除非您的目標完整的資料庫）。

內容的程式碼與`OnConfiguring`:

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

應用程式程式碼以初始化`OnConfiguring`:

``` csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

## <a name="using-dbcontext-with-dependency-injection"></a>使用相依性插入的 DbContext

EF 支援使用`DbContext`與相依性插入容器。 DbContext 類型可以使用新增至服務容器`AddDbContext<TContext>`。

`AddDbContext`將這兩個您 DbContext 類型`TContext`，和`DbContextOptions<TContext>`適用於資料隱碼，從服務容器。

請參閱[詳細閱讀](#more-reading)下方的相依性插入的詳細資訊。

加入相依性插入的 dbcontext

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

這需要將[建構函式引數](#constructor-argument)給 DbContext 類型可接受`DbContextOptions`。

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
public MyController(BloggingContext context)
```

（ServiceProvider 直接使用，較不常見） 的應用程式程式碼：

``` csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="using-idesigntimedbcontextfactorytcontext"></a>使用`IDesignTimeDbContextFactory<TContext>`

上述選項的替代方案，您可能也提供的實作`IDesignTimeDbContextFactory<TContext>`。 EF 工具可以使用這個處理站，以建立您的 DbContext 執行個體。 這可能需要以啟用特定的設計階段體驗，例如移轉。

實作這個介面來啟用並沒有公用預設建構函式的內容類型的設計階段服務。 設計階段服務會自動探索此介面衍生的內容相同的組件中的實作。

範例：

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

## <a name="more-reading"></a>詳細閱讀

* 讀取[開始使用 ASP.NET Core](../get-started/aspnetcore/index.md)如需有關使用 EF 與 ASP.NET Core。
* 讀取[相依性插入](https://docs.asp.net/en/latest/fundamentals/dependency-injection.html)若要深入了解使用 DI。
* 讀取[測試](testing/index.md)如需詳細資訊。
