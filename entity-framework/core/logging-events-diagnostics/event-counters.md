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
# <a name="event-counters"></a><span data-ttu-id="e2041-103">事件計數器</span><span class="sxs-lookup"><span data-stu-id="e2041-103">Event Counters</span></span>

> [!NOTE]
> <span data-ttu-id="e2041-104">這項功能已在 EF Core 5.0 中新增。</span><span class="sxs-lookup"><span data-stu-id="e2041-104">This feature was added in EF Core 5.0.</span></span>

<span data-ttu-id="e2041-105">Entity Framework Core (EF Core) 會公開可提供程式健康情況良好指示的連續數值度量。</span><span class="sxs-lookup"><span data-stu-id="e2041-105">Entity Framework Core (EF Core) exposes continuous numeric metrics which can provide a good indication of your program's health.</span></span> <span data-ttu-id="e2041-106">這些計量可用於下列用途：</span><span class="sxs-lookup"><span data-stu-id="e2041-106">These metrics can be used for the following purposes:</span></span>

* <span data-ttu-id="e2041-107">當應用程式正在執行時，即時追蹤一般資料庫載入</span><span class="sxs-lookup"><span data-stu-id="e2041-107">Track general database load in realtime as the application is running</span></span>
* <span data-ttu-id="e2041-108">公開可能導致效能降低的有問題的編碼作法</span><span class="sxs-lookup"><span data-stu-id="e2041-108">Expose problematic coding practices which can lead to degraded performance</span></span>
* <span data-ttu-id="e2041-109">追蹤並隔離異常程式列為</span><span class="sxs-lookup"><span data-stu-id="e2041-109">Track down and isolate anomalous program behavior</span></span>

<span data-ttu-id="e2041-110">EF Core 透過標準 .NET 事件計數器功能報告計量;建議您閱讀 [這篇 blog 文章](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) ，以快速瞭解計數器的運作方式。</span><span class="sxs-lookup"><span data-stu-id="e2041-110">EF Core reports metrics via the standard .NET event counters feature; it's recommended to read [this blog post](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) for a quick overview of how counters work.</span></span>

## <a name="attach-to-a-process-using-dotnet-counters"></a><span data-ttu-id="e2041-111">使用 dotnet 附加至進程</span><span class="sxs-lookup"><span data-stu-id="e2041-111">Attach to a process using dotnet-counters</span></span>

<span data-ttu-id="e2041-112">[Dotnet 計數器工具](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters)可用來附加到執行中的進程，並定期報告 EF Core 的事件計數器;在程式中不需要進行任何特殊動作，即可使用這些計數器。</span><span class="sxs-lookup"><span data-stu-id="e2041-112">The [dotnet-counters tool](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) can be used to attach to a running process and report EF Core event counters regularly; nothing special needs to be done in the program for these counters to be available.</span></span>

<span data-ttu-id="e2041-113">首先，請安裝此 `dotnet-counters` 工具： `dotnet tool install --global dotnet-counters` 。</span><span class="sxs-lookup"><span data-stu-id="e2041-113">First, install the `dotnet-counters` tool: `dotnet tool install --global dotnet-counters`.</span></span>

<span data-ttu-id="e2041-114">接下來，找出執行您 EF Core 應用程式之 .NET 進程的處理序識別碼 (PID) ：</span><span class="sxs-lookup"><span data-stu-id="e2041-114">Next, find the process ID (PID) of the .NET process running your EF Core application:</span></span>

### <a name="windows"></a>[<span data-ttu-id="e2041-115">Windows</span><span class="sxs-lookup"><span data-stu-id="e2041-115">Windows</span></span>](#tab/windows)

1. <span data-ttu-id="e2041-116">開啟 Windows 工作管理員，方法是以滑鼠右鍵按一下工作列，然後選取 [工作管理員]。</span><span class="sxs-lookup"><span data-stu-id="e2041-116">Open the Windows Task Manager by right-clicking on the task bar and selecting "Task Manager".</span></span>
2. <span data-ttu-id="e2041-117">請確定已在視窗底部選取 [更多詳細資料] 選項。</span><span class="sxs-lookup"><span data-stu-id="e2041-117">Make sure that the "More details" option is selected at the bottom of the window.</span></span>
3. <span data-ttu-id="e2041-118">在 [處理常式] 索引標籤中，以滑鼠右鍵按一下資料行，並確定已啟用 [PID] 資料行。</span><span class="sxs-lookup"><span data-stu-id="e2041-118">In the Processes tab, right-click a column and make sure that the PID column is enabled.</span></span>
4. <span data-ttu-id="e2041-119">在 [進程] 清單中找出您的應用程式，並從 [PID] 資料行取得其處理序識別碼。</span><span class="sxs-lookup"><span data-stu-id="e2041-119">Locate your application in the process list, and get its process ID from the PID column.</span></span>

### <a name="linux-or-macos"></a>[<span data-ttu-id="e2041-120">Linux 或 macOS</span><span class="sxs-lookup"><span data-stu-id="e2041-120">Linux or macOS</span></span>](#tab/fluent-api)

1. <span data-ttu-id="e2041-121">使用 `ps` 命令可列出針對您的使用者執行的所有處理常式。</span><span class="sxs-lookup"><span data-stu-id="e2041-121">Use the `ps` command to list all processes running for your user.</span></span>
2. <span data-ttu-id="e2041-122">在 [進程] 清單中找出您的應用程式，並從 [PID] 資料行取得其處理序識別碼。</span><span class="sxs-lookup"><span data-stu-id="e2041-122">Locate your application in the process list, and get its process ID from the PID column.</span></span>

***

<span data-ttu-id="e2041-123">在您的 .NET 應用程式中，程式識別碼可供使用， `Process.GetCurrentProcess().Id` 這有助於在啟動時列印 PID。</span><span class="sxs-lookup"><span data-stu-id="e2041-123">Inside your .NET application, the process ID is available as `Process.GetCurrentProcess().Id`; this can be useful for printing the PID upon startup.</span></span>

<span data-ttu-id="e2041-124">最後，啟動 `dotnet-counters` 方式如下：</span><span class="sxs-lookup"><span data-stu-id="e2041-124">Finally, launch `dotnet-counters` as follows:</span></span>

```console
dotnet counters monitor Microsoft.EntityFrameworkCore -p <PID>
```

<span data-ttu-id="e2041-125">`dotnet-counters` 現在會附加到執行中的進程，並開始報告連續的計數器資料：</span><span class="sxs-lookup"><span data-stu-id="e2041-125">`dotnet-counters` will now attach to your running process and start reporting continuous counter data:</span></span>

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

## <a name="counters-and-their-meaning"></a><span data-ttu-id="e2041-126">計數器及其意義</span><span class="sxs-lookup"><span data-stu-id="e2041-126">Counters and their meaning</span></span>

<span data-ttu-id="e2041-127">計數器名稱</span><span class="sxs-lookup"><span data-stu-id="e2041-127">Counter name</span></span>                          | <span data-ttu-id="e2041-128">描述</span><span class="sxs-lookup"><span data-stu-id="e2041-128">Description</span></span>
------------------------------------- | ----
<span data-ttu-id="e2041-129">現用 DbcoNtext</span><span class="sxs-lookup"><span data-stu-id="e2041-129">Active DbContexts</span></span>                     | <span data-ttu-id="e2041-130">目前在您應用程式中的作用中尚未處置 DbCoNtext 實例數目。</span><span class="sxs-lookup"><span data-stu-id="e2041-130">The number of active, undisposed DbContext instances currently in your application.</span></span> <span data-ttu-id="e2041-131">如果此數位持續成長，您可能會因為未正確處置 DbCoNtext 實例而發生流失問題。</span><span class="sxs-lookup"><span data-stu-id="e2041-131">If this number grows continuously, you may have a leak because DbContext instances aren't being properly disposed.</span></span> <span data-ttu-id="e2041-132">請注意，如果已啟用 [內容](xref:core/miscellaneous/context-pooling) 共用，此數目包括目前未使用的共用 DbCoNtext 實例。</span><span class="sxs-lookup"><span data-stu-id="e2041-132">Note that if [context pooling](xref:core/miscellaneous/context-pooling) is enabled, this number includes pooled DbContext instances not currently in use.</span></span>
<span data-ttu-id="e2041-133">執行策略作業失敗</span><span class="sxs-lookup"><span data-stu-id="e2041-133">Execution Strategy Operation Failures</span></span> | <span data-ttu-id="e2041-134">資料庫作業無法執行的次數。</span><span class="sxs-lookup"><span data-stu-id="e2041-134">The number of times a database operation failed to execute.</span></span> <span data-ttu-id="e2041-135">如果已啟用重試執行策略，這會在相同作業的多次嘗試中包含每個個別的失敗。</span><span class="sxs-lookup"><span data-stu-id="e2041-135">If a retrying execution strategy is enabled, this includes each individual failure within multiple attempts on the same operation.</span></span> <span data-ttu-id="e2041-136">這可以用來偵測基礎結構的暫時性問題。</span><span class="sxs-lookup"><span data-stu-id="e2041-136">This can be used to detect transient issues with your infrastructure.</span></span>
<span data-ttu-id="e2041-137">開放式平行存取失敗</span><span class="sxs-lookup"><span data-stu-id="e2041-137">Optimistic Concurrency Failures</span></span>       | <span data-ttu-id="e2041-138">`SaveChanges`因為開放式平行存取錯誤而失敗的次數，因為資料存放區中的資料在您的程式碼載入之後已經變更。</span><span class="sxs-lookup"><span data-stu-id="e2041-138">The number of times `SaveChanges` failed because of an optimistic concurrency error, because data in the data store was changed since your code loaded it.</span></span> <span data-ttu-id="e2041-139">這對應于擲回的 <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException> 。</span><span class="sxs-lookup"><span data-stu-id="e2041-139">This corresponds to a <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException> being thrown.</span></span>
<span data-ttu-id="e2041-140">查詢</span><span class="sxs-lookup"><span data-stu-id="e2041-140">Queries</span></span>                               | <span data-ttu-id="e2041-141">執行的查詢數目。</span><span class="sxs-lookup"><span data-stu-id="e2041-141">The number of queries executed.</span></span>
<span data-ttu-id="e2041-142">查詢快取命中率 (% ) </span><span class="sxs-lookup"><span data-stu-id="e2041-142">Query Cache Hit Rate (%)</span></span>              | <span data-ttu-id="e2041-143">查詢快取點擊的比率。</span><span class="sxs-lookup"><span data-stu-id="e2041-143">The ratio of query cache hits to misses.</span></span> <span data-ttu-id="e2041-144">當指定的 LINQ 查詢第一次執行時，EF Core () 中排除參數，則必須在相對較繁重的進程中進行編譯。</span><span class="sxs-lookup"><span data-stu-id="e2041-144">The first time a given LINQ query is executed by EF Core (excluding parameters), it must be compiled in what is a relatively heavy process.</span></span> <span data-ttu-id="e2041-145">在一般應用程式中，所有查詢都會重複使用，而且查詢快取點擊率在初始預熱期間之後應穩定為100%。</span><span class="sxs-lookup"><span data-stu-id="e2041-145">In a normal application, all queries are reused, and the query cache hit rate should be stable at 100% after an initial warmup period.</span></span> <span data-ttu-id="e2041-146">如果這個數位在一段時間內小於100%，您可能會因為重複編譯而遭遇效能降低的情況，這可能是因為產生了較佳的動態查詢。</span><span class="sxs-lookup"><span data-stu-id="e2041-146">If this number is less than 100% over time, you may experience degraded perf due to repeated compilations, which could be a result of suboptimal dynamic query generation.</span></span>
<span data-ttu-id="e2041-147">SaveChanges</span><span class="sxs-lookup"><span data-stu-id="e2041-147">SaveChanges</span></span>                           | <span data-ttu-id="e2041-148">已呼叫的次數 `SaveChanges` 。</span><span class="sxs-lookup"><span data-stu-id="e2041-148">The number of times `SaveChanges` has been called.</span></span> <span data-ttu-id="e2041-149">請注意， `SaveChanges` 在單一批次中儲存多項變更，因此這不一定代表在單一實體上進行的每個個別更新。</span><span class="sxs-lookup"><span data-stu-id="e2041-149">Note that `SaveChanges` saves multiple changes in a single batch, so this doesn't necessarily represent each individual update done on a single entity.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e2041-150">其他資源</span><span class="sxs-lookup"><span data-stu-id="e2041-150">Additional resources</span></span>

* [<span data-ttu-id="e2041-151">有關事件計數器的 .NET 檔</span><span class="sxs-lookup"><span data-stu-id="e2041-151">.NET documentation on event counters</span></span>](https://docs.microsoft.com/dotnet/core/diagnostics/event-counters)
