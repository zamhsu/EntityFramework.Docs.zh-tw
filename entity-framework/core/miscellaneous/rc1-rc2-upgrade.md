---
title: 從 EF Core 1.0 RC1 升級至 RC2-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 6d75b229-cc79-4d08-88cd-3a1c1b24d88f
uid: core/miscellaneous/rc1-rc2-upgrade
ms.openlocfilehash: 887b7cd539b9c0f5a680398f5039757420228710
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181280"
---
# <a name="upgrading-from-ef-core-10-rc1-to-10-rc2"></a>從 EF Core 1.0 RC1 升級至 1.0 RC2

本文提供將 RC1 套件建立的應用程式移至 RC2 的指引。

## <a name="package-names-and-versions"></a>套件名稱和版本

在 RC1 與 RC2 之間，我們已將 "Entity Framework 7" 變更為 "Entity Framework Core"。 您可以透過 Scott Hanselman，深入瞭解[這篇文章](https://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx)中變更的原因。 由於這項變更，我們的套件名稱從 `EntityFramework.*` 變更為 `Microsoft.EntityFrameworkCore.*`，而我們的版本從 `7.0.0-rc1-final` 改為 `1.0.0-rc2-final` （或適用于工具的 `1.0.0-preview1-final`）。

**您必須完全移除 RC1 的套件，然後安裝 RC2。** 以下是一些一般封裝的對應。

| RC1 套件                                               | RC2 對等                                                       |
|:----------------------------------------------------------|:---------------------------------------------------------------------|
| EntityFramework.MicrosoftSqlServer        7.0.0-rc1-final | Microsoft.EntityFrameworkCore.SqlServer         1.0.0-rc2-final      |
| EntityFramework.SQLite                    7.0.0-rc1-final | Microsoft.EntityFrameworkCore.Sqlite            1.0.0-rc2-final      |
| EntityFramework7.Npgsql                   3.1.0-rc1-3     | NpgSql.EntityFrameworkCore.Postgres             <to be advised>      |
| EntityFramework.SqlServerCompact35        7.0.0-rc1-final | EntityFrameworkCore.SqlServerCompact35          1.0.0-rc2-final      |
| EntityFramework.SqlServerCompact40        7.0.0-rc1-final | EntityFrameworkCore.SqlServerCompact40          1.0.0-rc2-final      |
| EntityFramework. InMemory 7.0.0-rc1-最終 | Microsoft.EntityFrameworkCore.InMemory          1.0.0-rc2-final      |
| EntityFramework.IBMDataServer             7.0.0-beta1     | 尚未提供 RC2                                            |
| EntityFramework.Commands                  7.0.0-rc1-final | Microsoft.EntityFrameworkCore.Tools             1.0.0-preview1-final |
| EntityFramework.MicrosoftSqlServer.Design 7.0.0-rc1-final | Microsoft.EntityFrameworkCore.SqlServer.Design  1.0.0-rc2-final      |

## <a name="namespaces"></a>命名空間

除了封裝名稱之外，命名空間也會從 `Microsoft.Data.Entity.*` 變更為 `Microsoft.EntityFrameworkCore.*`。 您可以使用 `using Microsoft.EntityFrameworkCore` 的 `using Microsoft.Data.Entity` 的 [尋找/取代] 來處理這項變更。

## <a name="table-naming-convention-changes"></a>資料表命名慣例變更

我們在 RC2 中所做的重大功能變更，就是將指定實體的 `DbSet<TEntity>` 屬性名稱當做其對應的資料表名稱，而不只是類別名稱。 如需這項變更的詳細資訊，請參閱[相關公告問題](https://github.com/aspnet/Announcements/issues/167)。

針對現有的 RC1 應用程式，我們建議您將下列程式碼新增至 `OnModelCreating` 方法的開頭，以保留 RC1 命名策略：

``` csharp
foreach (var entity in modelBuilder.Model.GetEntityTypes())
{
    entity.Relational().TableName = entity.DisplayName();
}
```

如果您想要採用新的命名策略，建議您成功完成其餘的升級步驟，然後移除程式碼並建立遷移來套用資料表重新命名。

## <a name="adddbcontext--startupcs-changes-aspnet-core-projects-only"></a>AddDbContext / Startup.cs 變更 （ASP.NET Core 專案）

在 RC1 中，您必須將 Entity Framework 服務新增至應用程式服務提供者，`Startup.ConfigureServices(...)`：

``` csharp
services.AddEntityFramework()
  .AddSqlServer()
  .AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

在 RC2 中，您可以移除對 `AddEntityFramework()`、`AddSqlServer()` 等的呼叫：

``` csharp
services.AddDbContext<ApplicationDbContext>(options =>
  options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

您也需要在衍生內容中加入一個可接受內容選項並將它們傳遞至基底函式的函式。 這是必要的，因為我們移除了在幕後 snuck 它們的一些可怕神奇之處：

``` csharp
public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
    : base(options)
{
}
```

## <a name="passing-in-an-iserviceprovider"></a>傳入 IServiceProvider

如果您的 RC1 程式碼會將 `IServiceProvider` 傳遞至內容，這現在已移至 `DbContextOptions`，而不是個別的函式參數。 使用 `DbContextOptionsBuilder.UseInternalServiceProvider(...)` 來設定服務提供者。

### <a name="testing"></a>測試

執行此作業最常見的案例是在測試時控制 InMemory 資料庫的範圍。 如需以 RC2 執行此作業的範例，請參閱更新的[測試](testing/index.md)文章。

### <a name="resolving-internal-services-from-application-service-provider-aspnet-core-projects-only"></a>解析內部的服務，從應用程式服務提供者 （ASP.NET Core 專案）

如果您有 ASP.NET Core 應用程式，您想要用來解析內部服務應用程式服務提供者的 EF 沒有多載`AddDbContext`，可讓您進行此設定：

``` csharp
services.AddEntityFrameworkSqlServer()
  .AddDbContext<ApplicationDbContext>((serviceProvider, options) =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"])
           .UseInternalServiceProvider(serviceProvider));
```

> [!WARNING]  
> 除非您有理由將內部 EF 服務結合到您的應用程式服務提供者，否則建議允許 EF 在內部管理自己的服務。 您可能想要這樣做的主要原因是使用您的應用程式服務提供者來取代 EF 在內部使用的服務

## <a name="dnx-commands--net-cli-aspnet-core-projects-only"></a>DNX 命令 = >.NET CLI （ASP.NET Core 專案）

如果您先前已使用 ASP.NET 5 專案的 `dnx ef` 命令，這些命令現在已移至 `dotnet ef` 個命令。 相同的命令語法仍然適用。 您可以使用 `dotnet ef --help` 來取得語法資訊。

由於 DNX 由 .NET CLI 取代，因此在 RC2 中註冊命令的方式已變更。 命令現在已在 `project.json` 的 `tools` 區段中註冊：

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
> 如果您使用 Visual Studio，您現在可以使用 Package Manager Console 來執行 ASP.NET Core 專案 （這不支援在 RC1 中） 的 EF 命令。 您仍然需要在 `project.json` 的 `tools` 區段中註冊命令來執行這項操作。

## <a name="package-manager-commands-require-powershell-5"></a>套件管理員命令需要 PowerShell 5

如果您在 Visual Studio 中使用 [套件管理員主控台] 中的 [Entity Framework] 命令，則必須確定已安裝 PowerShell 5。 這是在下一版中將會移除的暫時需求（如需詳細資訊，請參閱[問題 #5327](https://github.com/aspnet/EntityFramework/issues/5327) ）。

## <a name="using-imports-in-projectjson"></a>在專案 json 中使用 "imports"

其中一些 EF Core相依性不支援.NET 標準尚未。 EF Core 標準.NET 和.NET Core 專案中的，您可能需要加入 「 匯入 「 加入 project.json 暫時的解決方法。

新增 EF 時，NuGet 還原將會顯示此錯誤訊息：

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

因應措施是手動匯入可移植的設定檔 "net451 + win8"。 這會強制 NuGet 將符合此提供的二進位檔視為與 .NET Standard 相容的架構，即使不是如此。 雖然 "net451 + win8" 並不是 100% 與 .NET Standard 相容，但它與 PCL 之間的轉換十分相容，可 .NET Standard。 當 EF 的相依性最終升級至 .NET Standard 時，可以移除匯入。

在陣列語法中，可以將多個架構新增至「匯入」。 如果您將其他程式庫新增至專案，則可能需要其他匯入。

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

請參閱[問題 #5176](https://github.com/aspnet/EntityFramework/issues/5176)。
