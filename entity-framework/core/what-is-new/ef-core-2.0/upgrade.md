---
title: 從舊版升級至 EF Core 2-EF Core
description: 升級至 Entity Framework Core 2.0 的指示和注意事項
author: ajcvickers
ms.date: 08/13/2017
uid: core/what-is-new/ef-core-2.0/upgrade
ms.openlocfilehash: c7c736629209da99f191ceb0d4000d19f40414b9
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063435"
---
# <a name="upgrading-applications-from-previous-versions-to-ef-core-20"></a>將繼承應用程式升級至 EF Core 2。0

我們已在2.0 中大幅調整現有 Api 和行為的機會。 有幾項改進可能需要修改現有的應用程式程式碼，雖然我們認為對大部分的應用程式來說，影響會很低，但在大部分情況下，只需要重新編譯和基本的引導式變更來取代淘汰的 Api。

將現有的應用程式更新為 EF Core 2.0 可能需要：

1. 將應用程式的目標 .NET 執行升級為支援 .NET Standard 2.0 的應用程式。 如需詳細資料，請參閱 [支援的 .net](xref:core/platforms/index) 執行。

2. 識別與 EF Core 2.0 相容的目標資料庫提供者。 請參閱下列 [EF Core 2.0 需要2.0 資料庫提供者](#ef-core-20-requires-a-20-database-provider) 。

3. 將 (執行時間和工具) 的所有 EF Core 套件升級為2.0。 如需詳細資訊，請參閱 [安裝 EF Core](xref:core/get-started/install/index) 。

4. 進行任何必要的程式碼變更，以補償本檔其餘部分所述的重大變更。

## <a name="aspnet-core-now-includes-ef-core"></a>ASP.NET Core 現在包含 EF Core

除了協力廠商資料庫提供者之外，將目標設為 ASP.NET Core 2.0 的應用程式還可以使用 EF Core 2.0，而且沒有其他相依性。 不過，以舊版 ASP.NET Core 為目標的應用程式需要升級至 ASP.NET Core 2.0，才能使用 EF Core 2.0。 如需將 ASP.NET Core 應用程式升級至2.0 的詳細資訊，請參閱 [主題的 ASP.NET Core 檔](/aspnet/core/migration/1x-to-2x/)。

## <a name="new-way-of-getting-application-services-in-aspnet-core"></a>在 ASP.NET Core 中取得應用程式服務的新方法

針對 ASP.NET Core 的 web 應用程式，建議的模式已針對2.0 進行更新，而該方法中斷了1.x 中使用的設計階段邏輯 EF Core。 先前在設計階段，EF Core 會嘗試直接叫用，以便 `Startup.ConfigureServices` 存取應用程式的服務提供者。 在 ASP.NET Core 2.0 中，設定會在類別之外初始化 `Startup` 。 使用 EF Core 的應用程式通常會從設定存取其連接字串，因此 `Startup` 本身已不再足夠。 如果您升級 ASP.NET Core 1.x 應用程式，使用 EF Core 工具時，可能會收到下列錯誤。

> 在 ' ApplicationCoNtext ' 上找不到無參數的函數。 請在 ' ApplicationCoNtext ' 中新增無參數的函式，或在與 &lt; &gt; ' ApplicationCoNtext ' 相同的元件中新增 ' IDesignTimeDbCoNtextFactory ApplicationCoNtext ' 的實作為

ASP.NET Core 2.0 的預設範本中已加入新的設計階段勾點。 靜態 `Program.BuildWebHost` 方法可讓 EF Core 在設計階段存取應用程式的服務提供者。 如果您要升級 ASP.NET Core 1.x 應用程式，則必須將 `Program` 類別更新為如下所示。

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;

namespace AspNetCoreDotNetCore2._0App
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
}
```

強烈建議您在將應用程式更新為2.0 時採用這個新模式，因為產品功能（例如 Entity Framework Core 的遷移）必須是必要的。 另一個常見的替代方式[是*執行 \<TContext> IDesignTimeDbCoNtextFactory*](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory)。

## <a name="idbcontextfactory-renamed"></a>IDbCoNtextFactory 已重新命名

為了支援各種不同的應用程式模式，並讓使用者能夠更充分掌控其 `DbContext` 在設計階段的使用方式，我們在過去提供了 `IDbContextFactory<TContext>` 介面。 在設計階段，EF Core 工具將會在您的專案中探索此介面的執行，並使用它來建立 `DbContext` 物件。

此介面有一個非常一般的名稱，會誤導某些使用者嘗試重複使用它來進行其他 `DbContext` 建立的案例。 當 EF Tools 接著嘗試在設計階段使用其執行，並導致命令（例如或）失敗時，就會攔截到它們 `Update-Database` `dotnet ef database update` 。

為了傳達這個介面的強大設計階段語義，我們已將它重新命名為 `IDesignTimeDbContextFactory<TContext>` 。

2.0 版本的 `IDbContextFactory<TContext>` 仍存在，但標示為已淘汰。

## <a name="dbcontextfactoryoptions-removed"></a>DbCoNtextFactoryOptions 已移除

由於上述的 ASP.NET Core 2.0 變更，我們發現 `DbContextFactoryOptions` 新介面不再需要 `IDesignTimeDbContextFactory<TContext>` 。 以下是您應該改為使用的替代方案。

| DbCoNtextFactoryOptions | 替代函式                                                  |
|:------------------------|:-------------------------------------------------------------|
| ApplicationBasePath     | AppCoNtext. BaseDirectory                                     |
| ContentRootPath         | GetCurrentDirectory ( # A1                              |
| EnvironmentName         | GetEnvironmentVariable ( "ASPNETCORE_ENVIRONMENT" )  |

## <a name="design-time-working-directory-changed"></a>設計階段工作目錄已變更

ASP.NET Core 2.0 變更也需要用 `dotnet ef` 來與執行您的應用程式時所 Visual Studio 使用的工作目錄一致的工作目錄。 其中一個可觀察的副作用是，SQLite 檔案名現在會相對於專案目錄，而不是像過去一樣的輸出目錄。

## <a name="ef-core-20-requires-a-20-database-provider"></a>EF Core 2.0 需要2.0 資料庫提供者

在 EF Core 2.0 中，我們已對資料庫提供者的運作方式進行了許多簡化和改進。 這表示 1.0. x 和 1.1. x 提供者將無法使用 EF Core 2.0。

EF team 隨附的 SQL Server 和 SQLite 提供者，2.0 版將提供2.0 版的一部分。 適用于 [SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)、 [于 postgresql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)和 [MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) 的開放原始碼協力廠商提供者正在更新2.0。 若為所有其他提供者，請洽詢提供者寫入器。

## <a name="logging-and-diagnostics-events-have-changed"></a>記錄和診斷事件已變更

注意：這些變更應該不會影響大部分的應用程式程式碼。

傳送至 [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) 之訊息的事件識別碼在2.0 中已變更。 在 EF Core 程式碼中，事件識別碼現在是唯一的。 例如，這些訊息現在也遵循 MVC 所使用結構化記錄的標準模式。

記錄器類別也已經變更。 現在已有一組類別可透過 [DbLoggerCategory](https://github.com/aspnet/EntityFrameworkCore/blob/rel/2.0.0/src/EFCore/DbLoggerCategory.cs) 進行存取。

[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) 事件現在會使用相同的事件識別碼名稱作為對應的 `ILogger` 訊息。 事件承載是衍生自 [EventData](/dotnet/api/microsoft.entityframeworkcore.diagnostics.eventdata)的所有名義類型。

事件識別碼、承載類型和類別記錄在 [CoreEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.coreeventid) 和 [RelationalEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.relationaleventid) 類別中。

識別碼也會從 Microsoft.entityframeworkcore 移至新的 Microsoft.entityframeworkcore 命名空間。

## <a name="ef-core-relational-metadata-api-changes"></a>EF Core 關聯式中繼資料 API 變更

EF Core 2.0 現在會為使用的每個不同提供者建置不同的 [IModel](/dotnet/api/microsoft.entityframeworkcore.metadata.imodel)。 應用程式通常可以看到這項作業。 這項功能簡化了較低層級中繼資料 Api 的簡化，讓 _一般關聯式中繼資料概念_ 的任何存取一律透過呼叫 `.Relational` 而非 `.SqlServer` 、等等進行 `.Sqlite` 。例如，像這樣的 1.1. x 程式碼：

```csharp
var tableName = context.Model.FindEntityType(typeof(User)).SqlServer().TableName;
```

現在應該如下撰寫：

```csharp
var tableName = context.Model.FindEntityType(typeof(User)).Relational().TableName;
```

`ForSqlServerToTable`擴充方法現在可以用來根據目前使用中的提供者來撰寫條件式程式碼，而不是使用類似的方法。 例如︰

```csharp
modelBuilder.Entity<User>().ToTable(
    Database.IsSqlServer() ? "SqlServerName" : "OtherName");
```

請注意，這項變更僅適用于針對 _所有_ 關聯式提供者定義的 api/中繼資料。 當 API 和中繼資料僅適用于單一提供者時，它會維持不變。 例如，叢集索引是 SQL server 專屬的，因此， `ForSqlServerIsClustered` 您  `.SqlServer().IsClustered()` 仍然必須使用。

## <a name="dont-take-control-of-the-ef-service-provider"></a>不要掌控 EF 服務提供者

EF Core 使用內部 (相依性 `IServiceProvider` 插入容器) 進行內部執行。 應用程式應該允許 EF Core 建立和管理此提供者，但在特殊情況下除外。 強烈考慮移除對的任何呼叫 `UseInternalServiceProvider` 。 如果應用程式需要呼叫 `UseInternalServiceProvider` ，請考慮提出 [問題](https://github.com/dotnet/efcore/Issues) ，讓我們可以調查其他處理您案例的方式。

`AddEntityFramework` `AddEntityFrameworkSqlServer` 除非 `UseInternalServiceProvider` 也呼叫，否則應用程式程式碼不需要呼叫、等等。 移除對或的任何現有呼叫 `AddEntityFramework` `AddEntityFrameworkSqlServer` 等等。 `AddDbContext` 仍應以與之前相同的方式使用。

## <a name="in-memory-databases-must-be-named"></a>記憶體內部資料庫必須命名為

全域未命名的記憶體中資料庫已移除，而不是所有記憶體中的資料庫都必須命名為。 例如︰

```csharp
optionsBuilder.UseInMemoryDatabase("MyDatabase");
```

這會建立/使用名稱為 "MyDatabase" 的資料庫。 如果 `UseInMemoryDatabase` 使用相同的名稱再次呼叫，則會使用相同的記憶體中資料庫，讓多個內容實例共用它。

## <a name="read-only-api-changes"></a>唯讀 API 變更

`IsReadOnlyBeforeSave`、 `IsReadOnlyAfterSave` 和已淘汰 `IsStoreGeneratedAlways` 並取代為 [BeforeSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.beforesavebehavior) 和 [AfterSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.aftersavebehavior)。 這些行為會套用至任何屬性 (但不只是存放區產生的屬性) ，而且會決定當插入資料庫資料列 (`BeforeSaveBehavior`) 或更新現有的資料庫資料列 () 時，應如何使用屬性的值 `AfterSaveBehavior` 。

標記為 [ValueGenerated. OnAddOrUpdate](/dotnet/api/microsoft.entityframeworkcore.metadata.valuegenerated) (例如，針對計算資料行，) 預設會忽略目前在屬性上設定的任何值。 這表示無論是否已在追蹤的實體上設定或修改任何值，一律會取得存放區產生的值。 這可以藉由設定不同的方式進行變更 `Before\AfterSaveBehavior` 。

## <a name="new-clientsetnull-delete-behavior"></a>新的 Deletebehavior.clientsetnull 刪除行為

在先前的版本中， [deletebehavior.restrict](/dotnet/api/microsoft.entityframeworkcore.deletebehavior) 會針對內容所追蹤的實體，具有更關閉的相符 `SetNull` 語義。 在 EF Core 2.0 中，引進了新 `ClientSetNull` 的行為做為選擇性關聯性的預設值。 此行為具有 `SetNull` 追蹤實體的語義，以及 `Restrict` 使用 EF Core 建立之資料庫的行為。 在我們的經驗中，這些是追蹤實體和資料庫的最預期/有用行為。 `DeleteBehavior.Restrict` 當針對選擇性關聯性設定時，現在會接受追蹤實體的。

## <a name="provider-design-time-packages-removed"></a>已移除提供者設計階段套件

已 `Microsoft.EntityFrameworkCore.Relational.Design` 移除套件。 其內容已合併到 `Microsoft.EntityFrameworkCore.Relational` 和 `Microsoft.EntityFrameworkCore.Design` 中。

這會傳播至提供者設計階段套件。 這些套件 (`Microsoft.EntityFrameworkCore.Sqlite.Design` 、 `Microsoft.EntityFrameworkCore.SqlServer.Design` 等 ) 已移除，且其內容會合並到主要提供者套件中。

若要 `Scaffold-DbContext` `dotnet ef dbcontext scaffold` 在 EF Core 2.0 中啟用或，您只需要參考單一提供者套件：

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer"
    Version="2.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools"
    Version="2.0.0"
    PrivateAssets="All" />
<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
    Version="2.0.0" />
```
