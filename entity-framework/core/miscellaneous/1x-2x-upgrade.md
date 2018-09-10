---
title: 從舊版升級至 EF Core 2 EF Core
author: divega
ms.date: 8/13/2017
ms.assetid: 8BD43C8C-63D9-4F3A-B954-7BC518A1B7DB
uid: core/miscellaneous/1x-2x-upgrade
ms.openlocfilehash: 18c7fc841affc2776d054e447aa231a5f4bcd585
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2018
ms.locfileid: "44251176"
---
# <a name="upgrading-applications-from-previous-versions-to-ef-core-20"></a>應用程式從舊版升級 EF Core 2.0

我們已大幅改善我們的現有 Api 和 2.0 的行為的機會。 有幾個可能需要修改現有的應用程式程式碼的增強功能，雖然我們相信大部分的應用程式的影響會很低，在大部分情況下，需要只重新編譯和極少引導式的變更，來取代過時的 Api。

## <a name="procedures-common-to-all-applications"></a>程序通用的所有應用程式

可能需要更新現有的應用程式為 EF Core 2.0:

1. 升級為支援.NET Standard 2.0 的應用程式的目標.NET 平台。 請參閱[支援的平台](../platforms/index.md)如需詳細資訊。

2. 識別目標資料庫的 EF Core 2.0 相容的提供者。 請參閱[EF Core 2.0 需要 2.0 資料庫提供者](#ef-core-20-requires-a-20-database-provider)下方。

3. 將所有 EF Core 封裝 （執行階段和工具） 都升級為 2.0。 請參閱[安裝 EF Core](../get-started/install/index.md)如需詳細資訊。

4. 進行任何必要的程式碼變更，以補償的重大變更。 請參閱[的重大變更](#breaking-changes)節以取得詳細資料。

## <a name="aspnet-core-applications"></a>ASP.NET Core 應用程式

1. 請參閱特別[新的模式，來初始化應用程式的服務提供者](#new-way-of-getting-application-services)如下所述。

> [!TIP]  
> 這個新模式更新為 2.0 的應用程式會強烈建議您，而且在 Entity Framework Core 移轉之類的產品功能才能運作必要的採用。 其他常見的替代做法是[實作*IDesignTimeDbContextFactory\<TContext >*](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory)。

2. 除了協力廠商資料庫提供者之外，將目標設為 ASP.NET Core 2.0 的應用程式還可以使用 EF Core 2.0，而且沒有其他相依性。 不過，目標為舊版的 ASP.NET Core 應用程式必須升級至 ASP.NET Core 2.0，才能使用 EF Core 2.0。 如需升級為 2.0 的 ASP.NET Core 應用程式的詳細資訊，請參閱[ASP.NET Core 上的文件主體](https://docs.microsoft.com/aspnet/core/migration/1x-to-2x/)。

## <a name="new-way-of-getting-application-services"></a>取得應用程式服務的新方式

已更新 ASP.NET Core web 應用程式的建議的模式 2.0 中斷 1.x 中使用的 EF Core 的設計階段邏輯的方式。 先前在設計階段 EF Core 會嘗試叫用`Startup.ConfigureServices`直接才能存取應用程式的服務提供者。 在 ASP.NET Core 2.0 中，設定初始化之外`Startup`類別。 通常使用 EF Core 應用程式存取其連接字串從組態中，因此`Startup`本身已足夠。 如果您升級 ASP.NET Core 1.x 應用程式，您可能會收到下列錯誤時使用的 EF Core 工具。

> 在 'ApplicationContext' 上找不到沒有無參數建構函式。 加入 'ApplicationContext' 中的無參數建構函式，或新增的 'IDesignTimeDbContextFactory&lt;ApplicationContext&gt;' 與 'ApplicationContext' 相同的組件中

新的設計階段攔截已加入 ASP.NET Core 2.0 的預設範本中。 靜態`Program.BuildWebHost`方法可讓在設計階段存取應用程式的服務提供者的 EF Core。 如果您要升級的 ASP.NET Core 1.x 應用程式，您必須更新您`Program`類別，如下所示。

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

## <a name="idbcontextfactory-renamed"></a>重新命名的 IDbContextFactory

為了支援各種不同的應用程式模式，並讓使用者更充分掌控如何他們`DbContext`用在設計階段，我們，在過去，提供了`IDbContextFactory<TContext>`介面。 EF Core 工具將在設計階段探索實作這個介面，在您的專案，並使用它來建立`DbContext`物件。

此介面有誤導嘗試重新將它用於其他某些使用者的一般名稱`DbContext`-建立案例。 當 EF 工具然後嘗試在設計階段中使用它們的實作而遇到導致類似的命令`Update-Database`或`dotnet ef database update`失敗。

為了進行通訊的這個介面的強式設計階段語意，我們有它重新命名為`IDesignTimeDbContextFactory<TContext>`。

2.0 版本`IDbContextFactory<TContext>`仍然存在，但已標記為過時。

## <a name="dbcontextfactoryoptions-removed"></a>DbContextFactoryOptions 移除

由於 ASP.NET Core 2.0 變更上面所述的情況下，我們發現`DbContextFactoryOptions`已不再需要針對新`IDesignTimeDbContextFactory<TContext>`介面。 以下是您應該改為使用替代項目。

| DbContextFactoryOptions | 替代函式                                                  |
|:------------------------|:-------------------------------------------------------------|
| ApplicationBasePath     | AppContext.BaseDirectory                                     |
| ContentRootPath         | Directory.GetCurrentDirectory()                              |
| EnvironmentName         | Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") |

## <a name="design-time-working-directory-changed"></a>設計階段工作目錄變更

ASP.NET Core 2.0 變更也會需要所使用的工作目錄`dotnet ef`能配合執行您的應用程式時，Visual Studio 所用的工作目錄。 這一個可預見的副作用是現在，檔名相對於專案目錄，而非輸出目錄像以往的 SQLite。

## <a name="ef-core-20-requires-a-20-database-provider"></a>EF Core 2.0 需要 2.0 資料庫提供者

EF Core 2.0 我們進行了許多簡單化和增強功能的方式資料庫提供者中運作。 這表示 EF Core 2.0 1.0.x 和 1.1.x 提供者將無法運作。

在 SQL Server 和 SQLite 提供者隨附的 EF 小組和 2.0 版則會做為一部分的 2.0 版。 開放原始碼協力廠商提供者[SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)， [PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)，並[MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)正在更新為 2.0。 對於所有其他的提供者，請連絡提供者撰寫人員。

## <a name="logging-and-diagnostics-events-have-changed"></a>記錄和診斷事件已變更

注意： 這些變更應該不會影響大部分的應用程式程式碼。

若要傳送訊息的事件識別碼[ILogger](https://github.com/aspnet/Logging/blob/dev/src/Microsoft.Extensions.Logging.Abstractions/ILogger.cs) 2.0 中已經變更。 在 EF Core 程式碼中，事件識別碼現在是唯一的。 例如，這些訊息現在也遵循 MVC 所使用結構化記錄的標準模式。

記錄器類別也已經變更。 現在已有一組類別可透過 [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/DbLoggerCategory.cs) 進行存取。

[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)事件現在會使用相同的事件識別碼名稱作為對應`ILogger`訊息。 事件裝載是所有的名義型別類型衍生自[EventData](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/EventData.cs)。

事件識別碼、 內容類型和類別目錄會記載於[CoreEventId](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/CoreEventId.cs)並[RelationalEventId](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore.Relational/Diagnostics/RelationalEventId.cs)類別。

識別碼也已從 Microsoft.EntityFrameworkCore.Infraestructure 新 Microsoft.EntityFrameworkCore.Diagnostics 命名空間。

## <a name="ef-core-relational-metadata-api-changes"></a>EF Core 關聯式中繼資料 API 變更

EF Core 2.0 現在會為使用的每個不同提供者建置不同的 [IModel](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IModel.cs)。 應用程式通常可以看到這項作業。 這已加速簡化較低階中繼資料 API；因此，任何對_一般關聯式中繼資料概念_的存取一律是透過 `.Relational` 呼叫來進行，而非 `.SqlServer`、`.Sqlite` 等等。比方說，1.1.x 如下的程式碼：

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).SqlServer().TableName;
```

現在應該撰寫像這樣：

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).Relational().TableName;
```

而不是使用類似的方法`ForSqlServerToTable`，擴充方法現在都可寫入目前使用中的提供者為基礎的條件式程式碼。 例如: 

```C#
modelBuilder.Entity<User>().ToTable(
    Database.IsSqlServer() ? "SqlServerName" : "OtherName");
```

請注意，這項變更僅適用於已定義的 Api/metadata_所有_關聯式的提供者。 API 和中繼資料會保持不變時它是單一提供者所特有。 例如，叢集的索引是特定 SQL Server，因此`ForSqlServerIsClustered`和`.SqlServer().IsClustered()`仍可使用。

## <a name="dont-take-control-of-the-ef-service-provider"></a>不需要 EF 服務提供者的控制項

EF Core 會使用內部`IServiceProvider`（相依性插入容器） 內部實作。 應用程式應該允許建立和管理在特殊情況下除外此提供者的 EF Core。 強烈建議您移除的任何呼叫`UseInternalServiceProvider`。 如果應用程式需要呼叫`UseInternalServiceProvider`，則請考慮[提出問題](https://github.com/aspnet/EntityFramework/Issues)以便我們可以調查其他方式來處理您的案例。

呼叫`AddEntityFramework`， `AddEntityFrameworkSqlServer`，除非，應用程式程式碼不需要等`UseInternalServiceProvider`也稱為。 移除任何現有的呼叫`AddEntityFramework`或是`AddEntityFrameworkSqlServer`等`AddDbContext`應該仍可用於相同的方式和以前一樣。

## <a name="in-memory-databases-must-be-named"></a>記憶體中資料庫必須命名為

已移除未命名的全域記憶體中資料庫，而是必須命名為記憶體中的所有資料庫。 例如: 

``` csharp
optionsBuilder.UseInMemoryDatabase("MyDatabase");
```

這會建立/使用名稱為"MyDatabase"的資料庫。 如果`UseInMemoryDatabase`會再次呼叫相同的名稱，則相同的記憶體中資料庫將會使用，讓它可以由多個內容執行個體共用。

## <a name="read-only-api-changes"></a>唯讀 API 變更

`IsReadOnlyBeforeSave``IsReadOnlyAfterSave`，並`IsStoreGeneratedAlways`而言已經過時並取代為[BeforeSaveBehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L39)並[AfterSaveBehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L55)。 這些行為套用至任何屬性 （不只存放區所產生的屬性），並決定將資料庫資料列插入時如何使用屬性的值 (`BeforeSaveBehavior`) 或更新現有資料庫的資料列時 (`AfterSaveBehavior`)。

屬性標示[ValueGenerated.OnAddOrUpdate](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/ValueGenerated.cs) （例如，適用於計算的資料行） 會根據預設忽略目前的屬性上設定任何值。 這表示存放區所產生的值一律取得不論是否已設定或修改的追蹤實體上的任何值。 可以變更此設定不同`Before\AfterSaveBehavior`。

## <a name="new-clientsetnull-delete-behavior"></a>新的 ClientSetNull 刪除行為

在舊版中， [DeleteBehavior.Restrict](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/DeleteBehavior.cs)有實體的行為內容追蹤多個已關閉相符`SetNull`語意。 在 EF Core 2.0 中，新`ClientSetNull`為選擇性的關聯性的預設值已經導入行為。 這種行為有`SetNull`追蹤實體的語意和`Restrict`建立使用 EF Core 資料庫的行為。 在我們的經驗，這些是追蹤的實體和資料庫必須是/實用的行為。 `DeleteBehavior.Restrict` 現在適用於追蹤的實體時為選擇性的關聯性。

## <a name="provider-design-time-packages-removed"></a>移除的提供者設計階段套件

`Microsoft.EntityFrameworkCore.Relational.Design`已移除套件。 它的內容合併至`Microsoft.EntityFrameworkCore.Relational`和`Microsoft.EntityFrameworkCore.Design`。

這會傳播到提供者設計階段套件。 這些套件 (`Microsoft.EntityFrameworkCore.Sqlite.Design`，`Microsoft.EntityFrameworkCore.SqlServer.Design`等等) 已移除和其內容合併到主要提供者套件。

若要啟用`Scaffold-DbContext`或`dotnet ef dbcontext scaffold`在 EF Core 2.0 中，您只需要參考單一提供者封裝：

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer"
    Version="2.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools"
    Version="2.0.0"
    PrivateAssets="All" />
<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
    Version="2.0.0" />
```
