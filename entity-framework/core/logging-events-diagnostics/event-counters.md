---
title: 事件計數器-EF Core
description: 使用 .NET 事件計數器追蹤 EF Core 效能和診斷異常
author: roji
ms.date: 11/17/2020
uid: core/logging-events-diagnostics/event-counters
ms.openlocfilehash: 46acfe82d8aeb7d16146bae0cc2cd4ff733e2831
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003700"
---
# <a name="event-counters"></a>事件計數器

> [!NOTE]
> 這項功能已在 EF Core 5.0 中新增。

Entity Framework Core (EF Core) 會公開可提供程式健康情況良好指示的連續數值度量。 這些計量可用於下列用途：

* 當應用程式正在執行時，即時追蹤一般資料庫載入
* 公開可能導致效能降低的有問題的編碼作法
* 追蹤並隔離異常程式列為

EF Core 透過標準 .NET 事件計數器功能報告計量;建議您閱讀 [這篇 blog 文章](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) ，以快速瞭解計數器的運作方式。

## <a name="attach-to-a-process-using-dotnet-counters"></a>使用 dotnet 附加至進程

[Dotnet 計數器工具](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters)可用來附加到執行中的進程，並定期報告 EF Core 的事件計數器;在程式中不需要進行任何特殊動作，即可使用這些計數器。

首先，請安裝此 `dotnet-counters` 工具： `dotnet tool install --global dotnet-counters` 。

接下來，找出執行您 EF Core 應用程式之 .NET 進程的處理序識別碼 (PID) ：

### <a name="windows"></a>[Windows](#tab/windows)

1. 開啟 Windows 工作管理員，方法是以滑鼠右鍵按一下工作列，然後選取 [工作管理員]。
2. 請確定已在視窗底部選取 [更多詳細資料] 選項。
3. 在 [處理常式] 索引標籤中，以滑鼠右鍵按一下資料行，並確定已啟用 [PID] 資料行。
4. 在 [進程] 清單中找出您的應用程式，並從 [PID] 資料行取得其處理序識別碼。

### <a name="linux-or-macos"></a>[Linux 或 macOS](#tab/fluent-api)

1. 使用 `ps` 命令可列出針對您的使用者執行的所有處理常式。
2. 在 [進程] 清單中找出您的應用程式，並從 [PID] 資料行取得其處理序識別碼。

***

在您的 .NET 應用程式中，程式識別碼可供使用， `Process.GetCurrentProcess().Id` 這有助於在啟動時列印 PID。

最後，啟動 `dotnet-counters` 方式如下：

```console
dotnet counters monitor Microsoft.EntityFrameworkCore -p <PID>
```

`dotnet-counters` 現在會附加到執行中的進程，並開始報告連續的計數器資料：

```console
Press p to pause, r to resume, q to quit.
 Status: Running

[Microsoft.EntityFrameworkCore]
    Active DbContexts                                               1
    Execution Strategy Operation Failures (Count / 1 sec)           0
    Execution Strategy Operation Failures (Total)                   0
    Optimistic Concurrency Failures (Count / 1 sec)                 0
    Optimistic Concurrency Failures (Total)                         0
    Queries (Count / 1 sec)                                         1
    Queries (Total)                                               189
    Query Cache Hit Rate (%)                                      100
    SaveChanges (Count / 1 sec)                                     0
    SaveChanges (Total)                                             0
```

## <a name="counters-and-their-meaning"></a>計數器及其意義

計數器名稱                          | 描述
------------------------------------- | ----
現用 DbcoNtext                     | 目前在您應用程式中的作用中尚未處置 DbCoNtext 實例數目。 如果此數位持續成長，您可能會因為未正確處置 DbCoNtext 實例而發生流失問題。 請注意，如果已啟用 [內容](xref:core/miscellaneous/context-pooling) 共用，此數目包括目前未使用的共用 DbCoNtext 實例。
執行策略作業失敗 | 資料庫作業無法執行的次數。 如果已啟用重試執行策略，這會在相同作業的多次嘗試中包含每個個別的失敗。 這可以用來偵測基礎結構的暫時性問題。
開放式平行存取失敗       | `SaveChanges`因為開放式平行存取錯誤而失敗的次數，因為資料存放區中的資料在您的程式碼載入之後已經變更。 這對應于擲回的 <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException> 。
查詢                               | 執行的查詢數目。
查詢快取命中率 (% )               | 查詢快取點擊的比率。 當指定的 LINQ 查詢第一次執行時，EF Core () 中排除參數，則必須在相對較繁重的進程中進行編譯。 在一般應用程式中，所有查詢都會重複使用，而且查詢快取點擊率在初始預熱期間之後應穩定為100%。 如果這個數位在一段時間內小於100%，您可能會因為重複編譯而遭遇效能降低的情況，這可能是因為產生了較佳的動態查詢。
SaveChanges                           | 已呼叫的次數 `SaveChanges` 。 請注意， `SaveChanges` 在單一批次中儲存多項變更，因此這不一定代表在單一實體上進行的每個個別更新。

## <a name="additional-resources"></a>其他資源

* [有關事件計數器的 .NET 檔](https://docs.microsoft.com/dotnet/core/diagnostics/event-counters)
