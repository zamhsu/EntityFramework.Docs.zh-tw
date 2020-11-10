---
title: 使用 EF Core 記錄-
description: 使用 Microsoft 的 EF Core 記錄 ASP.NET Core 和其他應用程式類型中的記錄
author: ajcvickers
ms.date: 10/15/2020
uid: core/logging-events-diagnostics/extensions-logging
ms.openlocfilehash: 3732bda0ef29b05672a2dfb83405da802f899191
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431418"
---
# <a name="using-microsoftextensionslogging-in-ef-core"></a>使用 EF Core 中的登入

針對許多常見的記錄系統，記錄是具有外掛程式提供者的可[延伸](/dotnet/core/extensions/logging)記錄機制。 Microsoft 提供的外掛程式 (例如 [，) 和](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) 協力廠商外掛程式 (例如 [Serilog。記錄](https://www.nuget.org/packages/Serilog.Extensions.Logging/)) 是以 NuGet 套件的形式來提供。

Entity Framework Core (EF Core) 完全與整合 `Microsoft.Extensions.Logging` 。 不過，請考慮使用 [簡單的記錄](xref:core/logging-events-diagnostics/simple-logging) 來進行記錄，特別是針對未使用相依性插入的應用程式。

## <a name="aspnet-core-applications"></a>ASP.NET Core 應用程式

`Microsoft.Extensions.Logging`[預設會在 ASP.NET Core 應用程式中使用](/aspnet/core/fundamentals/logging)。 呼叫 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 或 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> 。

## <a name="other-application-types"></a>其他應用程式類型

其他應用程式類型可以使用 [GenericHost](/dotnet/core/extensions/generic-host) 取得與 ASP.NET Core 中使用的相同相依性插入模式。 然後可以使用 AddDbCoNtext 或 AdddbcoNtextpool 再也，就像在 ASP.NET Core 應用程式中一樣。

`Microsoft.Extensions.Logging` 也可以用於不使用相依性插入的應用程式，雖然 [簡單的記錄](xref:core/logging-events-diagnostics/simple-logging) 可能更容易設定。

`Microsoft.Extensions.Logging` 需要建立 <xref:Microsoft.Extensions.Logging.LoggerFactory> 。 此 factory 應儲存為靜態/全域實例，並在每次建立 DbCoNtext 時使用。 例如，通常會將記錄器 factory 儲存為 DbCoNtext 上的靜態屬性。

### <a name="ef-core-30-and-above"></a>[EF Core 3.0 和更新版本](#tab/v3)

<!--
        public static readonly ILoggerFactory MyLoggerFactory
            = LoggerFactory.Create(builder => { builder.AddConsole(); });
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="ef-core-21"></a>[EF Core 2.1](#tab/v2)

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

> [!WARNING]
> 在 EF Core 2.1 中，應用程式不會為每個 DbCoNtext 實例建立新的 Server.loggerfactory 實例。 這樣做會導致記憶體流失和效能不佳。 這已在 EF Core 3.0 和更新版本中修正。

***

然後，這個 singleton/global 實例應該會向 EF Core 註冊 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> 。 例如：

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .UseLoggerFactory(MyLoggerFactory)
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFLogging;ConnectRetryCount=0");
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

## <a name="getting-detailed-messages"></a>取得詳細訊息

> [!TIP]
> 當使用 AddDbCoNtext 或將 DbCoNtextOptions 實例傳遞至 DbCoNtext 的函式時，仍會呼叫 OnConfiguring。 這讓它成為套用內容設定的理想位置，不論 DbCoNtext 的結構為何。

### <a name="sensitive-data"></a>敏感性資料

根據預設，EF Core 不會在例外狀況訊息中包含任何資料的值。 這是因為這類資料可能是機密資料，如果未處理例外狀況，可能會顯示在生產環境中。

不過，在進行偵錯工具時，瞭解資料值（特別是索引鍵）可能會很有説明。 您可以藉由呼叫，在 EF Core 中啟用此功能 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> 。 例如：

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableSensitiveDataLogging();
-->
[!code-csharp[EnableSensitiveDataLogging](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableSensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a>詳細的查詢例外狀況

基於效能的考慮，EF Core 不會將每次呼叫都包裝在 try-catch 區塊中讀取資料庫提供者的值。 不過，這有時候會導致難以診斷的例外狀況，尤其是當資料庫在模型不允許時傳回 Null 時。

開啟 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> 會導致 EF 引入這些 try-catch 區塊，進而提供更詳細的錯誤。 例如：

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableDetailedErrors)]

## <a name="configuration-for-specific-messages"></a>特定訊息的設定

EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API 可讓應用程式變更遇到特定事件時所發生的情況。 這可以用來：

* 變更記錄事件的記錄層級
* 略過全部的事件記錄
* 發生事件時擲回例外狀況

### <a name="changing-the-log-level-for-an-event"></a>變更事件的記錄層級

有時候，變更事件的預先定義記錄層級可能會很有用。 例如，這可以用來將兩個額外的事件升階 `LogLevel.Debug` 至 `LogLevel.Information` ：

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Log(
                    (RelationalEventId.ConnectionOpened, LogLevel.Information),
                    (RelationalEventId.ConnectionClosed, LogLevel.Information)));
-->
[!code-csharp[ChangeLogLevel](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ChangeLogLevel)]

### <a name="suppress-logging-an-event"></a>隱藏事件的記錄

您可以使用類似的方式，從記錄中隱藏個別事件。 這特別適用于略過已審核並瞭解的警告。 例如：

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Ignore(CoreEventId.DetachedLazyLoadingWarning));
-->
[!code-csharp[SuppressMessage](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=SuppressMessage)]

### <a name="throw-for-an-event"></a>擲回事件

最後，EF Core 可以設定為針對指定的事件擲回。 這特別適用于將警告變更為錯誤。  (真的，這是方法的原始用途 `ConfigureWarnings` ，因此是名稱 ) 例如：

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Throw(RelationalEventId.QueryPossibleUnintendedUseOfEqualsWarning));
-->
[!code-csharp[ThrowForEvent](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ThrowForEvent)]

## <a name="filtering-and-other-configuration"></a>篩選和其他設定

如需記錄檔篩選和其他設定的指引，請參閱 [.net 中的記錄](/dotnet/core/extensions/logging) 。

EF Core 記錄事件定義于下列其中一個：

* <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> 適用于所有 EF Core 資料庫提供者的常見事件
* <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> 適用于所有關系資料庫提供者的常見事件
* 與目前資料庫提供者相關之事件的類似類別。 例如， <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> SQL Server 提供者。

這些定義包含每個事件的事件識別碼、記錄層級和類別，如所使用 `Microsoft.Extensions.Logging` 。
