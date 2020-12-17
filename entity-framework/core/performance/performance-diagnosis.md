---
title: 效能診斷-EF Core
description: 診斷 Entity Framework Core 效能並找出瓶頸
author: roji
ms.date: 12/1/2020
uid: core/performance/performance-diagnosis
ms.openlocfilehash: 9416acf3326056ef7a5d732c4bd456dac751167b
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657787"
---
# <a name="performance-diagnosis"></a>效能診斷

本節討論在 EF 應用程式中偵測效能問題的方法，以及在識別出有問題的區域之後，如何進一步分析這些問題以找出根本問題。 在跳到任何結論之前，請務必仔細診斷和調查任何問題，並避免發生問題根源的位置。

## <a name="identifying-slow-database-commands-via-logging"></a>透過記錄來識別慢速資料庫命令

在一天結束時，EF 會準備並執行要對資料庫執行的命令;使用關係資料庫時，這表示透過 ADO.NET 資料庫 API 來執行 SQL 語句。 如果某個查詢花費太多時間 (例如，因為遺漏了索引) ，檢查命令執行記錄並觀察實際花費的時間，就可以看到這種情況。

EF 可讓您輕鬆地透過 [簡單的記錄](xref:core/logging-events-diagnostics/simple-logging) 或 Microsoft 副檔名來捕捉命令執行時間 [。記錄](xref:core/logging-events-diagnostics/extensions-logging)：

### <a name="simple-logging"></a>[簡單記錄](#tab/simple-logging)

[!code-csharp[Main](../../../samples/core/Performance/BloggingContext.cs#SimpleLogging)]

### <a name="microsoftextensionslogging"></a>[Microsoft.Extensions.Logging](#tab/microsoft-extensions-logging)

[!code-csharp[Main](../../../samples/core/Performance/ExtensionsLoggingContext.cs#ExtensionsLogging)]

***

當記錄層級設定為時 `LogLevel.Information` ，EF 會發出每個命令執行的記錄檔訊息，以及花費的時間：

```log
info: 06/12/2020 09:12:36.117 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (4ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      SELECT [b].[Id], [b].[Name]
      FROM [Blogs] AS [b]
      WHERE [b].[Name] = N'foo'
```

上述命令所花的時間是4毫秒。 如果某個命令所使用的不是預期的，您發現效能問題可能有問題，而且現在可以將焦點放在其上，以瞭解其執行速度很慢的原因。 命令記錄也可以顯示正在進行非預期的資料庫往返的情況;這會顯示為只預期一個的多個命令。

> [!WARNING]
> 在生產環境中啟用命令執行記錄，通常是個不錯的主意。 記錄本身會讓您的應用程式變慢，而且可以快速建立大量記錄檔，以填滿您的伺服器磁片。 建議您只保持登入一小段時間來收集資料，並仔細監視您的應用程式，或在進入生產階段前系統上捕獲記錄資料。

## <a name="correlating-database-commands-to-linq-queries"></a>將資料庫命令與 LINQ 查詢相互關聯

命令執行記錄的其中一個問題是，有時很難相互關聯 SQL 查詢和 LINQ 查詢： EF 所執行的 SQL 命令看起來可能與產生它們的 LINQ 查詢非常不同。 為了協助解決這個問題，您可能會想要使用 EF 的 [查詢標記](xref:core/querying/tags) 功能，讓您在 SQL 查詢中插入一個小型的識別批註：

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#BasicQueryTag)]

標記會顯示在記錄中：

```sql
-- This is my spatial query!

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

通常值得以這種方式標記應用程式的主要查詢，讓命令執行記錄更容易讀取。

## <a name="other-interfaces-for-capturing-performance-data"></a>用於捕獲效能資料的其他介面

EF 的記錄功能有許多替代方法可以用來捕捉命令執行時間，這可能更強大。 資料庫通常會隨附自己的追蹤和效能分析工具，這些工具通常會提供更豐富的資料庫特定資訊，而不只是簡單的執行時間;實際的設定、功能和使用方式在不同的資料庫之間會有很大的差別。

例如， [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 是一個功能強大的用戶端，可連接到您的 SQL Server 實例，並提供重要的管理和效能資訊。 本章節涵蓋的詳細資料不在本章節的討論範圍內，但有兩項值得一提的功能是 [活動監視器](/sql/relational-databases/performance-monitor/open-activity-monitor-sql-server-management-studio)，它提供了伺服器活動的即時儀表板 (包括) 的最高成本查詢，以及 [擴充的事件 (XEvent) ](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server) 功能，可讓您定義可針對您的確切需求量身打造的任意資料捕獲會話。 [有關監視的 SQL Server 檔](/sql/relational-databases/performance/monitor-and-tune-for-performance) 會提供這些功能以及其他功能的詳細資訊。

另一種捕獲效能資料的方法是透過介面收集由 EF 或資料庫驅動程式自動發出的資訊 `DiagnosticSource` ，然後分析該資料，或將其顯示在儀表板上。 如果您使用 Azure， [Azure 應用程式 Insights](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-performance) 會提供現成的強大監視功能，並在分析您的 web 要求的速度時，整合資料庫效能和查詢執行時間。 有關此功能的詳細資訊可在 [Application Insights 效能教學](/azure/azure-monitor/learn/tutorial-performance)課程和 [Azure SQL 分析頁面](/azure/azure-monitor/insights/azure-sql)中取得。

## <a name="inspecting-query-execution-plans"></a>檢查查詢執行計畫

當您指出需要優化的有問題查詢之後，下一個步驟通常是分析查詢的 *執行計畫*。 當資料庫收到 SQL 語句時，它們通常會產生計畫的執行方式，這有時候需要根據已定義的索引、資料表中有多少資料，以及資料表中有多少資料，而這種情況下，也需要在伺服器上快取計畫本身，以獲得最佳效能) 的 (。 關係資料庫通常會提供一種方式，讓使用者查看查詢計劃，以及查詢的不同部分的計算成本。這對改善您的查詢非常有用。

若要開始 SQL Server，請參閱 [查詢執行計畫](/sql/relational-databases/performance/execution-plans)的相關檔。 一般分析工作流程是使用 [SQL Server Management Studio](/sql/relational-databases/performance/display-an-actual-execution-plan)、貼上透過上述其中一種方式識別的慢速查詢 SQL，以及 [產生圖形化執行計畫](/sql/relational-databases/performance/display-an-actual-execution-plan)：

![顯示 SQL Server 執行計畫](_static/actualexecplan.png)

雖然執行計畫在一開始可能有點複雜，但值得花一點時間熟悉它們。 特別重要的是，請注意與方案的每個節點相關聯的成本，以及識別如何在不同的節點中 () 使用索引。

雖然上述資訊是 SQL Server 特有的資訊，但其他資料庫通常會提供具有類似視覺效果的相同類型工具。

> [!IMPORTANT]
> 資料庫有時會根據資料庫中的實際資料來產生不同的查詢計劃。 例如，如果資料表只包含幾個資料列，則資料庫可能會選擇不使用該資料表上的索引，而是改為執行完整資料表掃描。 如果要分析測試資料庫上的查詢計劃，請務必確定它包含的資料與您的生產系統類似。

## <a name="event-counters"></a>事件計數器

上述各節著重于如何取得命令的相關資訊，以及如何在資料庫中執行這些命令。 除此之外，EF 也會公開一組 *事件計數器* ，以提供 EF 本身內發生之狀況的較低層級資訊，以及您的應用程式使用它的方式。 這些計數器有助於診斷特定效能問題和效能異常，例如導致重新編譯的查詢快取 [問題](xref:core/performance/advanced-performance-topics#dynamically-constructed-queries) 、尚未處置 DbCoNtext 流失，以及其他。

如需詳細資訊，請參閱 [EF 事件計數器](xref:core/logging-events-diagnostics/event-counters) 的專用頁面。

## <a name="benchmarking-with-ef-core"></a>EF Core 的基準測試

在一天結束時，您有時需要知道撰寫或執行查詢的特定方式是否比另一種方法更快。 絕對不要設想或 speculate 答案很重要，而且很容易就能將快速基準測試放在一起以取得答案。 撰寫基準測試時，強烈建議使用已知的 [BenchmarkDotNet](https://benchmarkdotnet.org/index.html) 程式庫，它會處理使用者嘗試撰寫自己的基準測試時遇到的許多陷阱：您是否已執行一些預先準備的反復專案？ 您的基準測試實際執行的反覆運算次數，以及原因為何？ 讓我們看看 EF Core 的基準測試。

> [!TIP]
> [以下提供適用](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Benchmarks/AverageBlogRanking.cs)于下列來源的完整基準測試專案。 建議您將它複製，並使用它作為您自己基準測試的範本。

作為簡單的基準測試案例，讓我們比較下列不同的方法來計算資料庫中所有 Blog 的平均排名：

* 載入所有實體、加總其個別排名，並計算平均值。
* 與上述相同，只使用非追蹤查詢。 這應該會更快，因為不會執行身分識別解析，而且實體不會基於變更追蹤的用途進行快照。
* 藉由只投射排名來避免載入整個 Blog 實體實例。 會讓我們無法傳輸其他不必要的 Blog 資料行的 Blog 實體類型。
* 藉由使其成為查詢的一部分，計算資料庫中的平均值。 這應該是最快的方式，因為所有專案都會在資料庫中計算出來，而且只會將結果傳回給用戶端。

有了 BenchmarkDotNet，您就可以撰寫程式碼以做為簡單的方法，就像單元測試一樣，BenchmarkDotNet 會自動針對足夠的反復專案數執行每個方法，以可靠的方式測量花費的時間，以及配置多少記憶體。 以下是不同的方法 ([完整的基準測試程式碼可以在這裡看到](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Benchmarks/AverageBlogRanking.cs)) ：

### <a name="load-entities"></a>[載入實體](#tab/load-entities)

[!code-csharp[Main](../../../samples/core/Benchmarks/AverageBlogRanking.cs?name=LoadEntities)]

### <a name="load-entities-no-tracking"></a>[載入實體，不追蹤](#tab/load-entities-no-tracking)

[!code-csharp[Main](../../../samples/core/Benchmarks/AverageBlogRanking.cs?name=LoadEntitiesNoTracking)]

### <a name="project-only-ranking"></a>[僅限專案排名](#tab/project-only-ranking)

[!code-csharp[Main](../../../samples/core/Benchmarks/AverageBlogRanking.cs?name=ProjectOnlyRanking)]

### <a name="calculate-in-database"></a>[在資料庫中計算](#tab/calculate-in-database)

[!code-csharp[Main](../../../samples/core/Benchmarks/AverageBlogRanking.cs?name=CalculateInDatabase)]

***

以下是 BenchmarkDotNet 所列印的結果：

|                 方法 |       平均數 |    錯誤 |   StdDev |     Median | 外觀比例 | RatioSD |    Gen 0 |   Gen 1 | Gen 2 |  已配置 |
|----------------------- |-----------:|---------:|---------:|-----------:|------:|--------:|---------:|--------:|------:|-----------:|
|           LoadEntities | 2860.4 美國 | 54.31 美國 | 93.68 美國 | 2844.5 美國 |  4.55 |    0.33 | 210.9375 | 70.3125 |     - | 1309.56 KB |
| LoadEntitiesNoTracking | 1353.0 美國 | 21.26 美國 | 18.85 美國 | 1355.6 美國 |  2.10 |    0.14 |  87.8906 |  3.9063 |     - |  540.09 KB |
|     ProjectOnlyRanking |   910.9 美國 | 20.91 美國 | 61.65 美國 |   892.9 美國 |  1.46 |    0.14 |  41.0156 |  0.9766 |     - |  252.08 KB |
|    CalculateInDatabase |   627.1 美國 | 14.58 美國 | 42.54 美國 |   626.4 美國 |  1.00 |    0.00 |   4.8828 |       - |     - |   33.27 KB |

> [!NOTE]
> 當方法在方法中具現化和處置內容時，這些作業會計算基準測試，雖然嚴格來說，它們不是查詢程式的一部分。 如果目標是要比較兩個替代專案到另一個 (，因為內容具現化和處置是相同的) ，並為整個作業提供更全面的測量，這應該不重要。

BenchmarkDotNet 的其中一項限制是它會測量您所提供之方法的簡單、單一執行緒效能，因此不適合用來測量並行案例。

> [!IMPORTANT]
> 在效能評定時，請務必確定您資料庫中的資料與生產資料類似，否則基準測試結果可能不代表生產環境中的實際效能。
