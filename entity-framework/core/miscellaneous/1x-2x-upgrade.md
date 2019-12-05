---
title: 從舊版升級至 EF Core 2 EF Core
author: divega
ms.date: 08/13/2017
ms.assetid: 8BD43C8C-63D9-4F3A-B954-7BC518A1B7DB
uid: core/miscellaneous/1x-2x-upgrade
ms.openlocfilehash: b27c09fdb6210dd7c6aa0c8bc912a8bd183c16b9
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824424"
---
# <a name="upgrading-applications-from-previous-versions-to-ef-core-20"></a>將繼承應用程式升級至 EF Core 2。0

我們已在2.0 中大幅縮小現有 Api 和行為的機會。 有一些改良功能可能需要修改現有的應用程式程式碼，但我們相信大部分的應用程式都會造成影響，而在大部分情況下，只需要重新編譯和最小的引導式變更來取代已淘汰的 Api。

將現有的應用程式更新為 EF Core 2.0 可能需要：

1. 將應用程式的目標 .NET 執行升級為支援 .NET Standard 2.0 的其中一個。 如需詳細資訊，請參閱[支援的 .Net 部署](xref:core/platforms/index)。

2. 識別與 EF Core 2.0 相容的目標資料庫提供者。 請參閱下面[的 EF Core 2.0 需要2.0 資料庫提供者](#ef-core-20-requires-a-20-database-provider)。

3. 將所有 EF Core 封裝（執行時間和工具）升級至2.0。 如需詳細資訊，請參閱[安裝 EF Core](xref:core/get-started/install/index) 。

4. 進行任何必要的程式碼變更，以補償本檔其餘部分所述的重大變更。

## <a name="aspnet-core-now-includes-ef-core"></a>ASP.NET Core 現在包含 EF Core

除了協力廠商資料庫提供者之外，將目標設為 ASP.NET Core 2.0 的應用程式還可以使用 EF Core 2.0，而且沒有其他相依性。 不過，以舊版 ASP.NET Core 為目標的應用程式需要升級至 ASP.NET Core 2.0，才能使用 EF Core 2.0。 如需將 ASP.NET Core 應用程式升級為2.0 的詳細資訊，請參閱[主題的 ASP.NET Core 檔](/aspnet/core/migration/1x-to-2x/)。

## <a name="new-way-of-getting-application-services-in-aspnet-core"></a>在 ASP.NET Core 中取得應用程式服務的新方式

ASP.NET Core web 應用程式的建議模式已針對2.0 進行更新，這種方式會中斷在1.x 中使用的設計階段邏輯 EF Core。 在設計階段，EF Core 會嘗試直接叫用 `Startup.ConfigureServices`，才能存取應用程式的服務提供者。 在 ASP.NET Core 2.0 中，設定會在 `Startup` 類別外初始化。 使用 EF Core 的應用程式通常會從設定存取其連接字串，因此 `Startup` 本身已不再足夠。 如果您升級 ASP.NET Core 1.x 應用程式，當您使用 EF Core 工具時，可能會收到下列錯誤。

> ' ApplicationCoNtext ' 上找不到無參數的函數。 請將無參數的函式新增至 ' ApplicationCoNtext '，或在與 ' ApplicationCoNtext ' 相同的元件中加入 ' IDesignTimeDbCoNtextFactory&lt;ApplicationCoNtext&gt;' 的執行

ASP.NET Core 2.0 的預設範本中已加入新的設計階段勾點。 靜態 `Program.BuildWebHost` 方法可讓 EF Core 在設計階段存取應用程式的服務提供者。 如果您要升級 ASP.NET Core 1.x 應用程式，您將需要更新 `Program` 類別，如下所示。

``` csharp
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

強烈建議您在將應用程式更新為2.0 時採用這個新模式，這是為了讓 Entity Framework Core 遷移的產品功能正常執行所需。 另一個常見的替代方法是[執行*IDesignTimeDbCoNtextFactory\<TCoNtext >* ](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory)。

## <a name="idbcontextfactory-renamed"></a>IDbCoNtextFactory 已重新命名

為了支援不同的應用程式模式，並讓使用者更能控制其在設計階段的 `DbContext` 使用方式，我們在過去已提供 `IDbContextFactory<TContext>` 介面。 在設計階段，EF Core 工具會在您的專案中探索此介面的執行，並使用它來建立 `DbContext` 物件。

此介面具有非常一般的名稱，會誤導某些使用者嘗試重新使用它來進行其他 `DbContext`建立案例。 當 EF 工具嘗試在設計階段使用其實作為，並導致 `Update-Database` 或 `dotnet ef database update` 之類的命令失敗時，就會攔截到防護。

為了傳達此介面的強式設計階段語義，我們已將它重新命名為 `IDesignTimeDbContextFactory<TContext>`。

若為2.0 版本，`IDbContextFactory<TContext>` 仍然存在，但標示為過時。

## <a name="dbcontextfactoryoptions-removed"></a>DbCoNtextFactoryOptions 已移除

基於上述 ASP.NET Core 2.0 變更，我們發現新的 `IDesignTimeDbContextFactory<TContext>` 介面上不再需要 `DbContextFactoryOptions`。 以下是您應該改為使用的替代方案。

| DbCoNtextFactoryOptions | 選項                                                  |
|:------------------------|:-------------------------------------------------------------|
| ApplicationBasePath     | AppCoNtext. BaseDirectory                                     |
| ContentRootPath         | 目錄. GetCurrentDirectory （）                              |
| EnvironmentName         | GetEnvironmentVariable （"ASPNETCORE_ENVIRONMENT"） |

## <a name="design-time-working-directory-changed"></a>設計階段工作目錄已變更

ASP.NET Core 2.0 變更也需要 `dotnet ef` 所使用的工作目錄，以配合 Visual Studio 執行應用程式時所使用的工作目錄。 其中一個可觀察的副作用是，SQLite 檔案名現在是相對於專案目錄，而不是輸出目錄，如同它們的作用。

## <a name="ef-core-20-requires-a-20-database-provider"></a>EF Core 2.0 需要2.0 資料庫提供者

針對 EF Core 2.0，我們在資料庫提供者的使用方式方面做了許多簡化和改進。 這表示 1.0. x 和 1.1. x 提供者不會與 EF Core 2.0 搭配使用。

SQL Server 和 SQLite 提供者都是由 EF 小組隨附，而2.0 版本則會在2.0 版本中提供。 適用于[SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)、[于 postgresql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)和[MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)的開放原始碼協力廠商提供者正在更新2.0。 對於所有其他提供者，請洽詢提供者寫入器。

## <a name="logging-and-diagnostics-events-have-changed"></a>記錄和診斷事件已變更

注意：這些變更應該不會影響大部分的應用程式程式碼。

傳送至[ILogger](/dotnet/api/microsoft.extensions.logging.ilogger)的訊息事件識別碼在2.0 中已變更。 在 EF Core 程式碼中，事件識別碼現在是唯一的。 例如，這些訊息現在也遵循 MVC 所使用結構化記錄的標準模式。

記錄器類別也已經變更。 現在已有一組類別可透過 [DbLoggerCategory](https://github.com/aspnet/EntityFrameworkCore/blob/rel/2.0.0/src/EFCore/DbLoggerCategory.cs) 進行存取。

[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)事件現在會使用與對應 `ILogger` 訊息相同的事件識別碼名稱。 事件裝載是衍生自[EventData](/dotnet/api/microsoft.entityframeworkcore.diagnostics.eventdata)的所有名義類型。

事件識別碼、裝載類型和類別記載于[CoreEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.coreeventid)和[RelationalEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.relationaleventid)類別中。

識別碼也已從 Microsoft.entityframeworkcore 移至新的 Microsoft.entityframeworkcore. Diagnostics 命名空間。

## <a name="ef-core-relational-metadata-api-changes"></a>EF Core 關聯式中繼資料 API 變更

EF Core 2.0 現在會為使用的每個不同提供者建置不同的 [IModel](/dotnet/api/microsoft.entityframeworkcore.metadata.imodel)。 應用程式通常可以看到這項作業。 這簡化了較低層級的中繼資料 Api，讓_一般關聯式中繼資料概念_的任何存取一律透過呼叫 `.Relational`，而不是 `.SqlServer`、`.Sqlite`等等。例如，1.1. x 程式碼，如下所示：

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).SqlServer().TableName;
```

現在應如下所示撰寫：

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).Relational().TableName;
```

擴充方法現在可以根據目前使用中的提供者來撰寫條件式程式碼，而不是使用 `ForSqlServerToTable`之類的方法。 例如：

```csharp
modelBuilder.Entity<User>().ToTable(
    Database.IsSqlServer() ? "SqlServerName" : "OtherName");
```

請注意，這種變更僅適用于針對_所有_關聯式提供者所定義的 api/中繼資料。 當 API 和中繼資料僅適用于單一提供者時，它會維持不變。 例如，叢集索引是 SQL server 特有的，因此 `ForSqlServerIsClustered` 和 `.SqlServer().IsClustered()` 仍然必須使用。

## <a name="dont-take-control-of-the-ef-service-provider"></a>不要接管 EF 服務提供者的控制權

EF Core 使用內部的 `IServiceProvider` （相依性插入容器）來進行內部執行。 除非在特殊情況下，否則應用程式應該允許 EF Core 建立和管理此提供者。 請考慮移除對 `UseInternalServiceProvider`的任何呼叫。 如果應用程式需要呼叫 `UseInternalServiceProvider`，請考慮提出[問題](https://github.com/aspnet/EntityFramework/Issues)，讓我們可以調查其他處理您案例的方式。

除非同時呼叫 `UseInternalServiceProvider`，否則應用程式代碼不需要呼叫 `AddEntityFramework`、`AddEntityFrameworkSqlServer`等等。 移除 `AddEntityFramework` 或 `AddEntityFrameworkSqlServer`的任何現有呼叫等等。 `AddDbContext` 仍應使用與之前相同的方式。

## <a name="in-memory-databases-must-be-named"></a>記憶體內部資料庫必須命名為

已移除全域未命名的記憶體內部資料庫，而是必須將所有記憶體中的資料庫命名為。 例如：

``` csharp
optionsBuilder.UseInMemoryDatabase("MyDatabase");
```

這會建立/使用名為 "MyDatabase" 的資料庫。 如果使用相同的名稱再次呼叫 `UseInMemoryDatabase`，則會使用相同的記憶體內部資料庫，讓多個內容實例能夠共用它。

## <a name="read-only-api-changes"></a>唯讀 API 變更

`IsReadOnlyBeforeSave`、`IsReadOnlyAfterSave`和 `IsStoreGeneratedAlways` 已過時，並已取代為[BeforeSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.beforesavebehavior)和[AfterSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.aftersavebehavior)。 這些行為適用于任何屬性（而不只是存放區產生的屬性），並決定在插入至資料庫資料列（`BeforeSaveBehavior`）或更新現有的資料庫資料列（`AfterSaveBehavior`）時，應該如何使用屬性的值。

標記為[ValueGenerated. OnAddOrUpdate](/dotnet/api/microsoft.entityframeworkcore.metadata.valuegenerated) （例如，針對計算資料行）的屬性預設會忽略屬性上目前設定的任何值。 這表示不論是否已在追蹤的實體上設定或修改任何值，一律會取得存放區產生的值。 這可以藉由設定不同的 `Before\AfterSaveBehavior`來變更。

## <a name="new-clientsetnull-delete-behavior"></a>新的 Deletebehavior.clientsetnull 刪除行為

在舊版中， [deletebehavior.setnull](/dotnet/api/microsoft.entityframeworkcore.deletebehavior)會對內容所追蹤的實體行為而言，這些實體會有更多的相符 `SetNull` 語義。 在 EF Core 2.0 中，已將新的 `ClientSetNull` 行為引進為選擇性關聯性的預設值。 此行為具有追蹤實體的 `SetNull` 語義，以及使用 EF Core 建立之資料庫的 `Restrict` 行為。 在我們的經驗中，這些是追蹤實體和資料庫最預期/實用的行為。 針對選擇性關聯性設定時，已追蹤的實體現在會接受 `DeleteBehavior.Restrict`。

## <a name="provider-design-time-packages-removed"></a>已移除提供者設計階段套件

已移除 `Microsoft.EntityFrameworkCore.Relational.Design` 套件。 其內容已合併到 `Microsoft.EntityFrameworkCore.Relational` 和 `Microsoft.EntityFrameworkCore.Design`。

這會傳播到提供者設計階段套件。 這些套件（`Microsoft.EntityFrameworkCore.Sqlite.Design`、`Microsoft.EntityFrameworkCore.SqlServer.Design`等）已移除，且其內容會合並到主要提供者套件中。

若要在 EF Core 2.0 中啟用 `Scaffold-DbContext` 或 `dotnet ef dbcontext scaffold`，您只需要參考單一提供者套件：

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer"
    Version="2.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools"
    Version="2.0.0"
    PrivateAssets="All" />
<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
    Version="2.0.0" />
```
