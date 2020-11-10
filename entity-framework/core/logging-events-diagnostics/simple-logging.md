---
title: 簡單記錄-EF Core
description: 使用 LogTo 從 EF Core DbCoNtext 記錄
author: ajcvickers
ms.date: 10/03/2020
uid: core/logging-events-diagnostics/simple-logging
ms.openlocfilehash: 49619cc10ea098e39e71dde347e00bbc3c39b13a
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431414"
---
# <a name="simple-logging"></a>簡單記錄

> [!NOTE]
> 這項功能已在 EF Core 5.0 中新增。

> [!TIP]  
> 您可以從 GitHub [下載本文的範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SimpleLogging) 。

Entity Framework Core (EF Core) 簡單記錄可以用來在開發和偵測應用程式時輕鬆取得記錄。 這種形式的記錄需要最基本的設定，而且沒有其他 NuGet 套件。

> [!TIP]
> EF Core 也會與需要更多設定的 [Microsoft 擴充功能](xref:core/logging-events-diagnostics/extensions-logging)整合，但通常更適合用來記錄生產應用程式。

## <a name="configuration"></a>設定

您可以使用[LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)從任何類型的應用程式存取 EF Core 記錄 <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> 設定 [DbCoNtext 實例](xref:core/dbcontext-configuration/index)時。 這項設定通常是在的覆寫中完成 <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> 。 例如：

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

或者，您也 `LogTo` 可以在 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 建立 <xref:Microsoft.EntityFrameworkCore.DbContextOptions> 實例以傳遞至該函式時，將其當作的一部分來呼叫 `DbContext` 。

> [!TIP]
> 當使用 AddDbCoNtext 或將 DbCoNtextOptions 實例傳遞至 DbCoNtext 的函式時，仍會呼叫 OnConfiguring。 這讓它成為套用內容設定的理想位置，不論 DbCoNtext 的結構為何。

## <a name="directing-the-logs"></a>引導記錄

### <a name="logging-to-the-console"></a>記錄到主控台

`LogTo` 需要可 <xref:System.Action%601> 接受字串的委派。 EF Core 會使用每個產生的記錄訊息的字串來呼叫這個委派。 然後由委派來對指定的訊息進行某個動作。

<xref:System.Console.WriteLine%2A?displayProperty=nameWithType>方法通常用於此委派，如上所示。 這會導致每個記錄訊息都寫入主控台。

### <a name="logging-to-the-debug-window"></a>記錄到 [debug] 視窗

<xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> 可以用來將輸出傳送至 Visual Studio 或其他 Ide 中的 [調試時間] 視窗。 在此情況下，必須使用[Lambda 語法](/dotnet/csharp/language-reference/operators/lambda-expressions)，因為 `Debug` 類別會編譯出發行組建。 例如：

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(message => Debug.WriteLine(message));
-->
[!code-csharp[LogToDebug](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToDebug)]

### <a name="logging-to-a-file"></a>記錄至檔案

寫入檔案時，需要建立 <xref:System.IO.StreamWriter> 或類似檔案的。 然後，您 <xref:System.IO.StreamWriter.WriteLine%2A> 可以使用方法，如同上述的其他範例。 請記得在處置內容時，藉由處置寫入器來確定檔案已完全關閉。 例如：

<!--
    private readonly StreamWriter _logStream = new StreamWriter("mylog.txt", append: true); 

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(_logStream.WriteLine);

    public override void Dispose()
    {
        base.Dispose();
        _logStream.Dispose();
    }
    
    public override async ValueTask DisposeAsync()
    {
        await base.DisposeAsync();
        await _logStream.DisposeAsync();
    }
-->
[!code-csharp[LogToFile](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToFile)]

> [!TIP]
> 請考慮使用 [ [記錄](/aspnet/core/fundamentals/logging) 檔]，記錄至實際執行應用程式中的檔案。

## <a name="getting-detailed-messages"></a>取得詳細訊息

### <a name="sensitive-data"></a>敏感性資料

根據預設，EF Core 不會在例外狀況訊息中包含任何資料的值。 這是因為這類資料可能是機密資料，如果未處理例外狀況，可能會顯示在生產環境中。

不過，在進行偵錯工具時，瞭解資料值（特別是索引鍵）可能會很有説明。 您可以藉由呼叫，在 EF Core 中啟用此功能 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> 。 例如：

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableSensitiveDataLogging();
-->
[!code-csharp[SensitiveDataLogging](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a>詳細的查詢例外狀況

基於效能的考慮，EF Core 不會將每次呼叫都包裝在 try-catch 區塊中讀取資料庫提供者的值。 不過，這有時候會導致難以診斷的例外狀況，尤其是當資料庫在模型不允許時傳回 Null 時。

開啟 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> 會導致 EF 引入這些 try-catch 區塊，進而提供更詳細的錯誤。 例如：

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EnableDetailedErrors)]

## <a name="filtering"></a>篩選

### <a name="log-levels"></a>記錄層級

每個 EF Core 記錄檔訊息都會指派給列舉所定義的層級 <xref:Microsoft.Extensions.Logging.LogLevel> 。 依預設，EF Core 簡單記錄包含每個 `Debug` 層級或以上的訊息。 `LogTo` 可以傳遞較高的最小層級，以篩選出某些訊息。 例如， `Information` 以最少的一組記錄來傳遞結果，限制為資料庫存取和一些內務處理訊息。

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[InfoOnly](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=InfoOnly)]

### <a name="specific-messages"></a>特定訊息

系統會為每個記錄檔訊息指派 <xref:Microsoft.Extensions.Logging.EventId> 。 您可以從 <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> 類別或類別存取這些識別碼， <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> 以取得關聯式特定的訊息。 資料庫提供者也可能在類似的類別中有提供者特定的識別碼。 例如， <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> SQL Server 提供者。

`LogTo` 可以設定為只記錄與一或多個事件識別碼相關聯的訊息。 例如，若要只針對正在初始化或處置的內容記錄訊息：  

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { CoreEventId.ContextDisposed, CoreEventId.ContextInitialized });
-->
[!code-csharp[EventIds](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EventIds)]

### <a name="message-categories"></a>訊息類別

每個記錄訊息都會指派給一個命名的階層式記錄器類別。 這些類別包括：

| 類別                                             | 訊息
|:-----------------------------------------------------|-------------------------------------------------
| Microsoft.EntityFrameworkCore                        | 所有 EF Core 訊息
| Microsoft.entityframeworkcore 資料庫               | 所有資料庫互動
| Microsoft.entityframeworkcore 連接    | 資料庫連接的使用
| Microsoft.entityframeworkcore。       | 使用資料庫命令
| Microsoft.entityframeworkcore。   | 資料庫交易的使用
| Microsoft.entityframeworkcore. 更新                 | 儲存實體，不包括資料庫互動
| Microsoft.entityframeworkcore。模型                  | 所有模型和中繼資料互動
| Microsoft.entityframeworkcore 驗證       | 模型驗證
| Microsoft.entityframeworkcore。查詢                  | 查詢，不包括資料庫互動
| Microsoft.entityframeworkcore 基礎結構         | 一般事件，例如內容建立
| Microsoft.entityframeworkcore            | 資料庫反向工程
| Microsoft.entityframeworkcore。             | 移轉
| Microsoft.entityframeworkcore. 變更追蹤         | 變更追蹤互動

`LogTo` 可以設定為只記錄一或多個類別目錄中的訊息。 例如，只記錄資料庫互動：

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { DbLoggerCategory.Database.Name });
-->
[!code-csharp[DatabaseCategory](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseCategory)]

請注意， <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> 類別會提供用來尋找類別的階層式 API，並避免需要硬式編碼字串。

由於分類是階層式的，因此使用此類別的範例 `Database` 將包含子類別 `Database.Connection` 、和的所有訊息 `Database.Command` `Database.Transaction` 。

### <a name="custom-filters"></a>自訂篩選器

`LogTo` 允許自訂篩選準則用於上述所有篩選選項都不足的情況。 例如，若要記錄層級或以上的任何訊息 `Information` ，以及開啟和關閉連接的訊息：

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(
                Console.WriteLine,
                (eventId, logLevel) => logLevel >= LogLevel.Information
                                       || eventId == RelationalEventId.ConnectionOpened
                                       || eventId == RelationalEventId.ConnectionClosed);
-->
[!code-csharp[CustomFilter](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=CustomFilter)]

> [!TIP]
> 使用自訂篩選或使用此處所示的任何其他選項進行篩選，比在 LogTo 委派中進行篩選更有效率。 這是因為如果篩選器決定不記錄訊息，則甚至不會建立記錄訊息。

## <a name="configuration-for-specific-messages"></a>特定訊息的設定

EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API 可讓應用程式變更遇到特定事件時所發生的情況。 這可以用來：

* 變更記錄事件的記錄層級
* 略過全部的事件記錄
* 發生事件時擲回例外狀況

### <a name="changing-the-log-level-for-an-event"></a>變更事件的記錄層級

先前的範例使用自訂篩選來記錄每個訊息，以及 `LogLevel.Information` 所定義的兩個事件 `LogLevel.Debug` 。 將兩個事件的記錄層級變更為，即可達成相同目的 `Debug` `Information` ：

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Log(
                (RelationalEventId.ConnectionOpened, LogLevel.Information),
                (RelationalEventId.ConnectionClosed, LogLevel.Information)))
            .LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[ChangeLogLevel](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=ChangeLogLevel)]

### <a name="suppress-logging-an-event"></a>隱藏事件的記錄

您可以使用類似的方式，從記錄中隱藏個別事件。 這特別適用于略過已審核並瞭解的警告。 例如：

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Ignore(CoreEventId.DetachedLazyLoadingWarning))
            .LogTo(Console.WriteLine);
-->
[!code-csharp[SuppressMessage](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SuppressMessage)]

### <a name="throw-for-an-event"></a>擲回事件

最後，EF Core 可以設定為針對指定的事件擲回。 這特別適用于將警告變更為錯誤。  (真的，這是方法的原始用途 `ConfigureWarnings` ，因此是名稱 ) 例如：

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Throw(RelationalEventId.MultipleCollectionIncludeWarning))
            .LogTo(Console.WriteLine);
-->
[!code-csharp[ThrowForEvent](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=ThrowForEvent)]

## <a name="message-contents-and-formatting"></a>訊息內容和格式

的預設內容 `LogTo` 會跨多行格式化。 第一行包含訊息中繼資料：

* <xref:Microsoft.Extensions.Logging.LogLevel>作為四個字元的前置詞
* 針對目前文化特性格式化的本機時間戳
* <xref:Microsoft.Extensions.Logging.EventId>表單中的，可以複製/貼上以從中取得成員 <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> 或其中一個其他 `EventId` 類別，加上原始識別碼值
* 事件類別目錄，如上所述。

例如：

```output
info: 10/6/2020 10:52:45.581 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE "Blogs" (
          "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,
          "Name" INTEGER NOT NULL
      );
dbug: 10/6/2020 10:52:45.582 RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committing transaction.
dbug: 10/6/2020 10:52:45.585 RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committed transaction.
```

您可以從[DbCoNtextLoggerOptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)傳遞值以自訂此內容 <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> -->，如下列各節所示。

> [!TIP]
> 請考慮使用 [Microsoft 擴充功能](/aspnet/core/fundamentals/logging) ，以進一步控制記錄格式。

### <a name="using-utc-time"></a>使用 UTC 時間

根據預設，timestamnps 是針對在進行偵錯工具時的本機耗用量所設計。 `DbContextLoggerOptions.DefaultWithUtcTime`請改用與文化特性無關的 UTC 時間戳記，但保留其他所有專案。 例如：

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithUtcTime);
-->
[!code-csharp[Utc](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=Utc)]

此範例會產生下列記錄格式：

```output
info: 2020-10-06T17:55:39.0333701Z RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE "Blogs" (
          "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,
          "Name" INTEGER NOT NULL
      );
dbug: 2020-10-06T17:55:39.0333892Z RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committing transaction.
dbug: 2020-10-06T17:55:39.0351684Z RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committed transaction.
```

### <a name="single-line-logging"></a>單行記錄

有時候，每個記錄訊息只會取得一行。 這可以由啟用 `DbContextLoggerOptions.SingleLine` 。 例如：

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithLocalTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[SingleLine](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SingleLine)]

此範例會產生下列記錄格式：

```output
info: 10/6/2020 10:52:45.723 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command) -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
dbug: 10/6/2020 10:52:45.723 RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committing transaction.
dbug: 10/6/2020 10:52:45.725 RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committed transaction.
```

### <a name="other-content-options"></a>其他內容選項

[DbCoNtextLoggerOptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)中的其他旗標 <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> --> 可以用來修剪記錄檔中包含的中繼資料量。 這有助於搭配單行記錄使用。 例如：

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.UtcTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[TerseLogs](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=TerseLogs)]

此範例會產生下列記錄格式：

```output
2020-10-06T17:52:45.7320362Z -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
2020-10-06T17:52:45.7320531Z -> Committing transaction.
2020-10-06T17:52:45.7339441Z -> Committed transaction.
```

## <a name="moving-from-ef6"></a>從 EF6 移動

EF Core 簡單的記錄與 <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> EF6 中的兩個重要方式不同：

* 記錄訊息不限於只有資料庫互動
* 必須在內容初始化時間設定記錄

針對第一個差異，上述的篩選可以用來限制要記錄的訊息。

第二個差異是刻意變更，可在不需要記錄訊息時，藉由不產生記錄檔訊息來改善效能。 不過，您仍然可以 `Log` 在上建立屬性，然後僅在已設定的情況下使用它，來取得 EF6 的類似行為 `DbContext` 。 例如：

<!--
    public Action<string> Log { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(s => Log?.Invoke(s));
-->
[!code-csharp[DatabaseLog](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseLog)]
