---
title: 從 EF Core 1.0 RC1 升級至 RC2-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 6d75b229-cc79-4d08-88cd-3a1c1b24d88f
uid: core/miscellaneous/rc1-rc2-upgrade
ms.openlocfilehash: 83b98fda5ac9491994b5b3fb333c9951ec01188a
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996893"
---
# <a name="upgrading-from-ef-core-10-rc1-to-10-rc2"></a>從 EF Core 1.0 RC1 升級至 1.0 RC2

本文章提供移動至 RC2 RC1 封裝建置的應用程式的指引。

## <a name="package-names-and-versions"></a>封裝名稱和版本

之間 RC1 和 RC2，我們變更為 「 Entity Framework Core 」 從 「 Entity Framework 7 」。 您可以深入了解之變更的原因[由 Scott hanselman 主講的這篇文章](http://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx)。 這項變更，因為我們的套件名稱則是從變更`EntityFramework.*`要`Microsoft.EntityFrameworkCore.*`和我們的版本，從`7.0.0-rc1-final`要`1.0.0-rc2-final`(或`1.0.0-preview1-final`工具)。

**您必須完全移除 RC1 封裝，然後再安裝 RC2 的。** 以下是一些常見的套件的對應。

| RC1 封裝                                               | RC2 的對等項目                                                       |
|:----------------------------------------------------------|:---------------------------------------------------------------------|
| EntityFramework.MicrosoftSqlServer        7.0.0-rc1-final | Microsoft.EntityFrameworkCore.SqlServer         1.0.0-rc2-final      |
| EntityFramework.SQLite                    7.0.0-rc1-final | Microsoft.EntityFrameworkCore.Sqlite            1.0.0-rc2-final      |
| EntityFramework7.Npgsql                   3.1.0-rc1-3     | NpgSql.EntityFrameworkCore.Postgres             <to be advised>      |
| EntityFramework.SqlServerCompact35        7.0.0-rc1-final | EntityFrameworkCore.SqlServerCompact35          1.0.0-rc2-final      |
| EntityFramework.SqlServerCompact40        7.0.0-rc1-final | EntityFrameworkCore.SqlServerCompact40          1.0.0-rc2-final      |
| EntityFramework.InMemory 7.0.0-rc1-final | Microsoft.EntityFrameworkCore.InMemory          1.0.0-rc2-final      |
| EntityFramework.IBMDataServer             7.0.0-beta1     | 尚無法使用 rc2                                            |
| EntityFramework.Commands                  7.0.0-rc1-final | Microsoft.EntityFrameworkCore.Tools             1.0.0-preview1-final |
| EntityFramework.MicrosoftSqlServer.Design 7.0.0-rc1-final | Microsoft.EntityFrameworkCore.SqlServer.Design  1.0.0-rc2-final      |

## <a name="namespaces"></a>命名空間

從變更封裝名稱以及命名空間`Microsoft.Data.Entity.*`至`Microsoft.EntityFrameworkCore.*`。 您可以處理這項變更，使用 尋找/取代的`using Microsoft.Data.Entity`與`using Microsoft.EntityFrameworkCore`。

## <a name="table-naming-convention-changes"></a>命名慣例變更的資料表

我們在 RC2 中的重大功能變更，是使用名稱`DbSet<TEntity>`作為資料表名稱指定之實體的屬性對應，而非只是類別名稱。 您可以深入了解這項變更[相關的公告問題](https://github.com/aspnet/Announcements/issues/167)。

對於現有的 RC1 應用程式，我們建議將下列程式碼新增至開始您`OnModelCreating`方法，以保留 RC1 命名策略：

``` csharp
foreach (var entity in modelBuilder.Model.GetEntityTypes())
{
    entity.Relational().TableName = entity.DisplayName();
}
```

如果您想要採用新的命名策略，我們建議已順利完成的升級步驟，然後移除的程式碼和建立移轉至適用於資料表的其餘部分會重新命名。

## <a name="adddbcontext--startupcs-changes-aspnet-core-projects-only"></a>AddDbContext / Startup.cs 變更 （ASP.NET Core 專案）

在 RC1，您必須將 Entity Framework 的服務加入應用程式服務提供者- `Startup.ConfigureServices(...)`:

``` csharp
services.AddEntityFramework()
  .AddSqlServer()
  .AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

Rc2，您可以移除對`AddEntityFramework()`，`AddSqlServer()`等等。:

``` csharp
services.AddDbContext<ApplicationDbContext>(options =>
  options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

您也需要將建構函式，加入至您的衍生內容，取得內容的選項，並將它們傳遞給基底建構函式。 由於我們移除了一些 scary 偷偷在幕後的魔力，會需要此項目：

``` csharp
public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
    : base(options)
{
}
```

## <a name="passing-in-an-iserviceprovider"></a>由傳入 IServiceProvider

如果您有將傳遞的 RC1 代碼`IServiceProvider`內容，這現已移至`DbContextOptions`，而不會出現不同的建構函式參數。 使用`DbContextOptionsBuilder.UseInternalServiceProvider(...)`設定服務提供者。

### <a name="testing"></a>測試

執行此動作最常見的案例是測試時控制 InMemory 資料庫的範圍。 請參閱更新[測試](testing/index.md)rc2 中執行此範例的文章。

### <a name="resolving-internal-services-from-application-service-provider-aspnet-core-projects-only"></a>解析內部的服務，從應用程式服務提供者 （ASP.NET Core 專案）

如果您有 ASP.NET Core 應用程式，您想要用來解析內部服務應用程式服務提供者的 EF 沒有多載`AddDbContext`，可讓您進行此設定：

``` csharp
services.AddEntityFrameworkSqlServer()
  .AddDbContext<ApplicationDbContext>((serviceProvider, options) =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"])
           .UseInternalServiceProvider(serviceProvider)); );
```

> [!WARNING]  
> 我們建議您讓 EF，在內部管理自己的服務，除非您有理由，將內部的 EF 服務結合成您的應用程式服務提供者。 您可能想要執行這項操作的主要原因是要使用您的應用程式服務提供者來取代 EF 會在內部使用的服務

## <a name="dnx-commands--net-cli-aspnet-core-projects-only"></a>DNX 命令 = >.NET CLI （ASP.NET Core 專案）

如果您先前使用`dnx ef`ASP.NET 5 專案的命令，這些現在都已移至`dotnet ef`命令。 相同的命令語法仍然適用。 您可以使用`dotnet ef --help`如語法的詳細資訊。

註冊命令的方式已變更 rc2，由於 DNX.NET CLI 所取代。 命令現在會在中註冊`tools`一節中`project.json`:

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
> 如果您使用 Visual Studio，您現在可以使用 Package Manager Console 來執行 ASP.NET Core 專案 （這不支援在 RC1 中） 的 EF 命令。 您仍然需要註冊中的命令`tools`一節`project.json`若要這樣做。

## <a name="package-manager-commands-require-powershell-5"></a>封裝管理員命令都需要 PowerShell 5

如果您使用 Entity Framework 中的命令在 Visual Studio 中的套件管理員主控台，則您必須確定您已安裝的 PowerShell 5。 這是將在下一步 的版本中移除的暫時需求 (請參閱[發出 #5327](https://github.com/aspnet/EntityFramework/issues/5327)如需詳細資訊)。

## <a name="using-imports-in-projectjson"></a>使用 project.json 中的"imports"

其中一些 EF Core相依性不支援.NET 標準尚未。 EF Core 標準.NET 和.NET Core 專案中的，您可能需要加入 「 匯入 「 加入 project.json 暫時的解決方法。

新增 EF，當 NuGet 還原將會顯示這則錯誤訊息：

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

因應措施是手動匯入 「 可攜式 net451 + win8"的可攜式設定檔。 因而不得不將符合此二進位檔的 NuGet 提供做為相容的架構與.NET Standard，即使它們不是。 雖然不是與.NET Standard 相容的 100%的 「 可攜式 net451 + win8"，它是相容的 PCL 從轉換到.NET Standard。 當 EF 的相依性最終升級至.NET Standard 時，就可以移除匯入。

多個架構可以新增至 「 imports 」，在陣列語法。 其他匯入可能需要額外的程式庫加入您的專案。

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
