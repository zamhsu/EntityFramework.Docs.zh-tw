---
title: 設計階段 DbContext 建立-EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/16/2019
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: f83d4b16227d114a1cac1514667484a908fea4ac
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197570"
---
<a name="design-time-dbcontext-creation"></a>設計階段 DbContext 建立
==============================
某些 EF Core 工具命令（例如，[遷移][1]命令）需要在設計階段建立衍生`DbContext`實例，才能收集應用程式實體類型的詳細資料，以及它們如何對應到資料庫架構。 在大部分的情況下，最好是以`DbContext`類似于[在執行時間設定][2]它的方式來設定建立的。

工具有各種方式可嘗試建立`DbContext`：

<a name="from-application-services"></a>從應用程式服務
-------------------------
如果您的啟始專案使用[ASP.NET Core Web 主機][3]或[.Net Core 泛型主機][4]，則工具會嘗試從應用程式的服務提供者取得 DbCoNtext 物件。

這些工具會先藉由叫用`Program.CreateHostBuilder()`、呼叫`Build()`，然後存取`Services`屬性，來嘗試取得服務提供者。

``` csharp
public class Program
{
    public static void Main(string[] args)
        => CreateHostBuilder(args).Build().Run();

    // EF Core uses this method at design time to access the DbContext
    public static IHostBuilder CreateHostBuilder(string[] args)
        => Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(
                webBuilder => webBuilder.UseStartup<Startup>());
}

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
        => services.AddDbContext<ApplicationDbContext>();
}

public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }
}
```

> [!NOTE]
> 當您建立新的 ASP.NET Core 應用程式時，預設會包含此攔截。

`DbContext`本身和其任何函式中的任何相依性都必須在應用程式的服務提供者中註冊為服務。 [在接受實例`DbContext` `DbContextOptions<TContext>` ][5] [做為引數並使用方法的上，就可以輕鬆達成此目的。`AddDbContext<TContext>` ][6]

<a name="using-a-constructor-with-no-parameters"></a>使用不含參數的函式
--------------------------------------
如果無法從應用程式服務提供者取得 DbCoNtext，工具會在專案內尋找衍生`DbContext`的型別。 然後，他們會嘗試使用不含參數的函式來建立實例。 如果`DbContext` [是`OnConfiguring`][7]使用方法設定，這可以是預設的函式。

<a name="from-a-design-time-factory"></a>從設計階段 factory
--------------------------
您也可以藉由執行`IDesignTimeDbContextFactory<TContext>`介面，告訴工具如何建立您的 DbCoNtext：如果在與衍生`DbContext`的或應用程式啟始專案相同的專案中找到實此介面的類別，則工具會略過其他建立 DbCoNtext 的方式，並改用設計階段 factory。

``` csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Design;
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

> [!NOTE]
> `args`參數目前未使用。 追蹤從工具指定設計階段引數的能力時發生[問題][8]。

如果您需要在設計階段中以不同的方式設定 DbCoNtext，而不是在執行時間時， `DbContext`如果您不是在 di 中註冊其他參數，則設計階段 factory 會特別有用，如果您不是使用 di，或針對某些您偏好不要在 ASP.NET Core `BuildWebHost` `Main`應用程式類別中使用方法的原因。

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [6]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [7]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [8]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
