---
title: 從 EF Core 1.0 RC1 升級至 RC2-EF 核心
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 6d75b229-cc79-4d08-88cd-3a1c1b24d88f
ms.technology: entity-framework-core
uid: core/miscellaneous/rc1-rc2-upgrade
ms.openlocfilehash: e76886729248101ccac024568cf9abcd945fca33
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
ms.locfileid: "29678623"
---
# <a name="upgrading-from-ef-core-10-rc1-to-10-rc2"></a>從 EF Core 1.0 RC1 升級至 1.0 RC2

本文章提供移動至 RC2 RC1 封裝建置的應用程式的指引。

## <a name="package-names-and-versions"></a>封裝名稱和版本

之間 RC1 和 RC2，我們變更為 「 Entity Framework Core 」 從 「 Entity Framework 7 」。 閱讀更多有關的變更原因[由 Scott Hanselman 這篇文章](http://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx)。 這項變更，因為我們套件名稱會變更從`EntityFramework.*`至`Microsoft.EntityFrameworkCore.*`和我們版本`7.0.0-rc1-final`至`1.0.0-rc2-final`(或`1.0.0-preview1-final`工具)。

**您要完全移除 RC1 封裝，然後安裝 RC2 的。** 以下是一些常見的封裝的對應。

| RC1 封裝                                               | RC2 的對等項目                                                       |
|:----------------------------------------------------------|:---------------------------------------------------------------------|
| EntityFramework.MicrosoftSqlServer        7.0.0-rc1-final | Microsoft.EntityFrameworkCore.SqlServer         1.0.0-rc2-final      |
| EntityFramework.SQLite                    7.0.0-rc1-final | Microsoft.EntityFrameworkCore.Sqlite            1.0.0-rc2-final      |
| EntityFramework7.Npgsql                   3.1.0-rc1-3     | NpgSql.EntityFrameworkCore.Postgres             <to be advised>      |
| EntityFramework.SqlServerCompact35        7.0.0-rc1-final | EntityFrameworkCore.SqlServerCompact35          1.0.0-rc2-final      |
| EntityFramework.SqlServerCompact40        7.0.0-rc1-final | EntityFrameworkCore.SqlServerCompact40          1.0.0-rc2-final      |
| EntityFramework.InMemory                  7.0.0-rc1-final | Microsoft.EntityFrameworkCore.InMemory          1.0.0-rc2-final      |
| EntityFramework.IBMDataServer             7.0.0-beta1     | 尚無法使用 rc2                                            |
| EntityFramework.Commands                  7.0.0-rc1-final | Microsoft.EntityFrameworkCore.Tools             1.0.0-preview1-final |
| EntityFramework.MicrosoftSqlServer.Design 7.0.0-rc1-final | Microsoft.EntityFrameworkCore.SqlServer.Design  1.0.0-rc2-final      |

## <a name="namespaces"></a>命名空間

套件名稱以及命名空間已從`Microsoft.Data.Entity.*`至`Microsoft.EntityFrameworkCore.*`。 您可以處理這項變更，以尋找/取代的`using Microsoft.Data.Entity`與`using Microsoft.EntityFrameworkCore`。

## <a name="table-naming-convention-changes"></a>命名慣例變更的資料表

若要使用的名稱是重大的功能性變更，我們採用 rc2`DbSet<TEntity>`對指定的實體做為資料表名稱的屬性則會對應至，而不是類別名稱。 閱讀更多有關這項變更中[相關的公告問題](https://github.com/aspnet/Announcements/issues/167)。

對於現有 RC1 應用程式，我們建議將下列程式碼加入至開頭您`OnModelCreating`方法，以保留 RC1 命名策略：

``` csharp
foreach (var entity in modelBuilder.Model.GetEntityTypes())
{
    entity.Relational().TableName = entity.DisplayName();
}
```

如果您想要採用新的命名策略，我們會建議順利完成其餘的升級步驟以及移除的程式碼和移轉至套用資料表重新命名。

## <a name="adddbcontext--startupcs-changes-aspnet-core-projects-only"></a>AddDbContext / Startup.cs 變更 （ASP.NET Core 專案）

在 RC1，您必須在加入應用程式服務提供者的 Entity Framework 服務`Startup.ConfigureServices(...)`:

``` csharp
services.AddEntityFramework()
  .AddSqlServer()
  .AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

在 RC2 中，您可以移除呼叫`AddEntityFramework()`，`AddSqlServer()`等等。:

``` csharp
services.AddDbContext<ApplicationDbContext>(options =>
  options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

您也需要加入建構函式，為您衍生內容，取得內容的選項，並將其傳遞給基底建構函式。 這被必要由於我們移除了偷偷在幕後嚇人最特別的部分：

``` csharp
public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
    : base(options)
{
}
```

## <a name="passing-in-an-iserviceprovider"></a>IServiceProvider 中傳遞

如果您有 RC1 通過認證的程式碼`IServiceProvider`至內容，這現在已移到`DbContextOptions`，而不會出現不同的建構函式參數。 使用`DbContextOptionsBuilder.UseInternalServiceProvider(...)`設定服務提供者。

### <a name="testing"></a>測試

執行此作業最常見的案例是測試時，控制 InMemory 資料庫的範圍。 請參閱更新[測試](testing/index.md)rc2 這麼做的範例為發行項。

### <a name="resolving-internal-services-from-application-service-provider-aspnet-core-projects-only"></a>解析內部的服務，從應用程式服務提供者 （ASP.NET Core 專案）

如果您有 ASP.NET Core 應用程式，您想要用來解析內部服務應用程式服務提供者的 EF 沒有多載`AddDbContext`，可讓您進行此設定：

``` csharp
services.AddEntityFrameworkSqlServer()
  .AddDbContext<ApplicationDbContext>((serviceProvider, options) =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"])
           .UseInternalServiceProvider(serviceProvider)); );
```

> [!WARNING]  
> 我們建議您允許 EF，在內部管理自己的服務，除非您有理由要將內部的 EF 服務結合成您的應用程式服務提供者。 您可能想要執行這項操作的主要原因是要用於您的應用程式服務提供者取代 EF 會在內部使用的服務

## <a name="dnx-commands--net-cli-aspnet-core-projects-only"></a>DNX 命令 = >.NET CLI （ASP.NET Core 專案）

如果您先前使用`dnx ef`命令 ASP.NET 5 專案，這些現在已轉換成`dotnet ef`命令。 仍適用於相同的命令語法。 您可以使用`dotnet ef --help`如語法的詳細資訊。

註冊命令的方式已變更 rc2，因為.NET CLI 要取代的 DNX。 現在在註冊命令`tools`一節中`project.json`:

``` json
"tools": {
  "Microsoft.EntityFrameworkCore.Tools": {
    "version": "1.0.0-preview1-final",
    "imports": [
      "portable-net45+win8+dnxcore50",
      "portable-net45+win8"
    ]
  }
}
```

> [!TIP]  
> 如果您使用 Visual Studio，您現在可以使用 Package Manager Console 來執行 ASP.NET Core 專案 （這不支援在 RC1 中） 的 EF 命令。 您仍然需要登錄中的命令`tools`區段`project.json`若要這樣做。

## <a name="package-manager-commands-require-powershell-5"></a>封裝管理員命令需要 PowerShell 5

如果您在 Visual Studio 中的封裝管理員主控台中使用 Entity Framework 命令，則您必須確定您已安裝的 PowerShell 5。 這是中的下一個版本將移除的暫時需求 (請參閱[發出 #5327](https://github.com/aspnet/EntityFramework/issues/5327)如需詳細資訊)。

## <a name="using-imports-in-projectjson"></a>在 project.json 中使用 「 匯入 」

其中一些 EF 核心相依性不支援.NET 標準尚未。 EF 核心標準.NET 和.NET Core 專案中的，您可能需要加入 「 匯入 「 加入 project.json 暫時的解決方法。

加入時 EF，NuGet 還原 」 會顯示這則錯誤訊息：

``` Console
Package Ix-Async 1.2.5 is not compatible with netcoreapp1.0 (.NETCoreApp,Version=v1.0). Package Ix-Async 1.2.5 supports:
  - net40 (.NETFramework,Version=v4.0)
  - net45 (.NETFramework,Version=v4.5)
  - portable-net45+win8+wp8 (.NETPortable,Version=v0.0,Profile=Profile78)
Package Remotion.Linq 2.0.2 is not compatible with netcoreapp1.0 (.NETCoreApp,Version=v1.0). Package Remotion.Linq 2.0.2 supports:
  - net35 (.NETFramework,Version=v3.5)
  - net40 (.NETFramework,Version=v4.0)
  - net45 (.NETFramework,Version=v4.5)
  - portable-net45+win8+wp8+wpa81 (.NETPortable,Version=v0.0,Profile=Profile259)
```

因應措施是手動匯入 「 可攜式 net451 + win8"的可攜式設定檔。 此強制將與此二進位檔的 NuGet 提供為以.NET 標準相容的 framework，即使它們不是。 雖然"可攜式 net451 + win8"不是與標準.NET 相容的 100%，它是相容的 PCL 從轉換為.NET 標準。 當以.NET 標準最後升級 EF 的相依性，則可以移除匯入。

在陣列語法時，可以加入多個架構 」 匯入 」。 其他匯入可能需要額外的程式庫加入您的專案。

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

請參閱[發出 #5176](https://github.com/aspnet/EntityFramework/issues/5176)。
