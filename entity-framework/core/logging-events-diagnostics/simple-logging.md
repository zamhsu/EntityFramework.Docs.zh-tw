---
title: 簡單記錄-EF Core
description: 使用 LogTo 從 EF Core DbCoNtext 記錄
author: ajcvickers
ms.date: 10/03/2020
uid: core/logging-events-diagnostics/simple-logging
ms.openlocfilehash: 5c2dc41122dfa3919d1e6a26b0760883d77ee1a0
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129209"
---
# <a name="simple-logging"></a><span data-ttu-id="7f100-103">簡單記錄</span><span class="sxs-lookup"><span data-stu-id="7f100-103">Simple Logging</span></span>

> [!NOTE]
> <span data-ttu-id="7f100-104">這項功能是在 EF Core 5.0 中引進。</span><span class="sxs-lookup"><span data-stu-id="7f100-104">This feature was introduced in EF Core 5.0.</span></span>

> [!TIP]
> <span data-ttu-id="7f100-105">您可以從 GitHub [下載本文的範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging/SimpleLogging) 。</span><span class="sxs-lookup"><span data-stu-id="7f100-105">You can [download this article's sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging/SimpleLogging) from GitHub.</span></span>

<span data-ttu-id="7f100-106">Entity Framework Core (EF Core) 簡單記錄可以用來在開發和偵測應用程式時輕鬆取得記錄。</span><span class="sxs-lookup"><span data-stu-id="7f100-106">Entity Framework Core (EF Core) simple logging can be used to easily obtain logs while developing and debugging applications.</span></span> <span data-ttu-id="7f100-107">這種形式的記錄需要最基本的設定，而且沒有其他 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="7f100-107">This form of logging requires minimal configuration and no additional NuGet packages.</span></span>

> [!TIP]
> <span data-ttu-id="7f100-108">EF Core 也會與需要更多設定的 [Microsoft 擴充功能](xref:core/logging-events-diagnostics/extensions-logging)整合，但通常更適合用來記錄生產應用程式。</span><span class="sxs-lookup"><span data-stu-id="7f100-108">EF Core also integrates with [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging), which requires more configuration, but is often more suitable for logging in production applications.</span></span>

## <a name="configuration"></a><span data-ttu-id="7f100-109">設定</span><span class="sxs-lookup"><span data-stu-id="7f100-109">Configuration</span></span>

<span data-ttu-id="7f100-110">在設定 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> [DbCoNtext 實例](xref:core/dbcontext-configuration/index)時，可以使用任何類型的應用程式存取 EF Core 記錄。</span><span class="sxs-lookup"><span data-stu-id="7f100-110">EF Core logs can be accessed from any type of application through the use of <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> when [configuring a DbContext instance](xref:core/dbcontext-configuration/index).</span></span> <span data-ttu-id="7f100-111">這項設定通常是在的覆寫中完成 <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="7f100-111">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="7f100-112">例如：</span><span class="sxs-lookup"><span data-stu-id="7f100-112">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="7f100-113">或者，您也 `LogTo` 可以在 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 建立 <xref:Microsoft.EntityFrameworkCore.DbContextOptions> 實例以傳遞至該函式時，將其當作的一部分來呼叫 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="7f100-113">Alternately, `LogTo` can be called as part of <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> or when creating a <xref:Microsoft.EntityFrameworkCore.DbContextOptions> instance to pass to the `DbContext` constructor.</span></span>

> [!TIP]
> <span data-ttu-id="7f100-114">當使用 AddDbCoNtext 或將 DbCoNtextOptions 實例傳遞至 DbCoNtext 的函式時，仍會呼叫 OnConfiguring。</span><span class="sxs-lookup"><span data-stu-id="7f100-114">OnConfiguring is still called when AddDbContext is used or a DbContextOptions instance is passed to the DbContext constructor.</span></span> <span data-ttu-id="7f100-115">這讓它成為套用內容設定的理想位置，不論 DbCoNtext 的結構為何。</span><span class="sxs-lookup"><span data-stu-id="7f100-115">This makes it the ideal place to apply context configuration regardless of how the DbContext is constructed.</span></span>

## <a name="directing-the-logs"></a><span data-ttu-id="7f100-116">引導記錄</span><span class="sxs-lookup"><span data-stu-id="7f100-116">Directing the logs</span></span>

### <a name="logging-to-the-console"></a><span data-ttu-id="7f100-117">記錄到主控台</span><span class="sxs-lookup"><span data-stu-id="7f100-117">Logging to the console</span></span>

<span data-ttu-id="7f100-118">`LogTo` 需要可 <xref:System.Action%601> 接受字串的委派。</span><span class="sxs-lookup"><span data-stu-id="7f100-118">`LogTo` requires an <xref:System.Action%601> delegate that accepts a string.</span></span> <span data-ttu-id="7f100-119">EF Core 會使用每個產生的記錄訊息的字串來呼叫這個委派。</span><span class="sxs-lookup"><span data-stu-id="7f100-119">EF Core will call this delegate with a string for each log message generated.</span></span> <span data-ttu-id="7f100-120">然後由委派來對指定的訊息進行某個動作。</span><span class="sxs-lookup"><span data-stu-id="7f100-120">It is then up to the delegate to do something with the given message.</span></span>

<span data-ttu-id="7f100-121"><xref:System.Console.WriteLine%2A?displayProperty=nameWithType>方法通常用於此委派，如上所示。</span><span class="sxs-lookup"><span data-stu-id="7f100-121">The <xref:System.Console.WriteLine%2A?displayProperty=nameWithType> method is often used for this delegate, as shown above.</span></span> <span data-ttu-id="7f100-122">這會導致每個記錄訊息都寫入主控台。</span><span class="sxs-lookup"><span data-stu-id="7f100-122">This results in each log message being written to the console.</span></span>

### <a name="logging-to-the-debug-window"></a><span data-ttu-id="7f100-123">記錄到 [debug] 視窗</span><span class="sxs-lookup"><span data-stu-id="7f100-123">Logging to the debug window</span></span>

<span data-ttu-id="7f100-124"><xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> 可以用來將輸出傳送至 Visual Studio 或其他 Ide 中的 [調試時間] 視窗。</span><span class="sxs-lookup"><span data-stu-id="7f100-124"><xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> can be used to send output to the Debug window in Visual Studio or other IDEs.</span></span> <span data-ttu-id="7f100-125">在此情況下，必須使用[Lambda 語法](/dotnet/csharp/language-reference/operators/lambda-expressions)，因為 `Debug` 類別會編譯出發行組建。</span><span class="sxs-lookup"><span data-stu-id="7f100-125">[Lambda syntax](/dotnet/csharp/language-reference/operators/lambda-expressions) must be used in this case because the `Debug` class is compiled out of release builds.</span></span> <span data-ttu-id="7f100-126">例如：</span><span class="sxs-lookup"><span data-stu-id="7f100-126">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(message => Debug.WriteLine(message));
-->
[!code-csharp[LogToDebug](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToDebug)]

### <a name="logging-to-a-file"></a><span data-ttu-id="7f100-127">記錄至檔案</span><span class="sxs-lookup"><span data-stu-id="7f100-127">Logging to a file</span></span>

<span data-ttu-id="7f100-128">寫入檔案時，需要建立 <xref:System.IO.StreamWriter> 或類似檔案的。</span><span class="sxs-lookup"><span data-stu-id="7f100-128">Writing to a file requires creating a <xref:System.IO.StreamWriter> or similar for the file.</span></span> <span data-ttu-id="7f100-129">然後，您 <xref:System.IO.StreamWriter.WriteLine%2A> 可以使用方法，如同上述的其他範例。</span><span class="sxs-lookup"><span data-stu-id="7f100-129">The <xref:System.IO.StreamWriter.WriteLine%2A> method can then be used as in the other examples above.</span></span> <span data-ttu-id="7f100-130">請記得在處置內容時，藉由處置寫入器來確定檔案已完全關閉。</span><span class="sxs-lookup"><span data-stu-id="7f100-130">Remember to ensure the file is closed cleanly by disposing the writer when the context is disposed.</span></span> <span data-ttu-id="7f100-131">例如：</span><span class="sxs-lookup"><span data-stu-id="7f100-131">For example:</span></span>

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
> <span data-ttu-id="7f100-132">請考慮使用 [ [記錄](/aspnet/core/fundamentals/logging) 檔]，記錄至實際執行應用程式中的檔案。</span><span class="sxs-lookup"><span data-stu-id="7f100-132">Consider using [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging) for logging to files in production applications.</span></span>

## <a name="getting-detailed-messages"></a><span data-ttu-id="7f100-133">取得詳細訊息</span><span class="sxs-lookup"><span data-stu-id="7f100-133">Getting detailed messages</span></span>

### <a name="sensitive-data"></a><span data-ttu-id="7f100-134">敏感性資料</span><span class="sxs-lookup"><span data-stu-id="7f100-134">Sensitive data</span></span>

<span data-ttu-id="7f100-135">根據預設，EF Core 不會在例外狀況訊息中包含任何資料的值。</span><span class="sxs-lookup"><span data-stu-id="7f100-135">By default, EF Core will not include the values of any data in exception messages.</span></span> <span data-ttu-id="7f100-136">這是因為這類資料可能是機密資料，如果未處理例外狀況，可能會顯示在生產環境中。</span><span class="sxs-lookup"><span data-stu-id="7f100-136">This is because such data may be confidential, and could be revealed in production use if an exception is not handled.</span></span>

<span data-ttu-id="7f100-137">不過，在進行偵錯工具時，瞭解資料值（特別是索引鍵）可能會很有説明。</span><span class="sxs-lookup"><span data-stu-id="7f100-137">However, knowing data values, especially for keys, can be very helpful when debugging.</span></span> <span data-ttu-id="7f100-138">您可以藉由呼叫，在 EF Core 中啟用此功能 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> 。</span><span class="sxs-lookup"><span data-stu-id="7f100-138">This can be enabled in EF Core by calling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging>.</span></span> <span data-ttu-id="7f100-139">例如：</span><span class="sxs-lookup"><span data-stu-id="7f100-139">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableSensitiveDataLogging();
-->
[!code-csharp[SensitiveDataLogging](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a><span data-ttu-id="7f100-140">詳細的查詢例外狀況</span><span class="sxs-lookup"><span data-stu-id="7f100-140">Detailed query exceptions</span></span>

<span data-ttu-id="7f100-141">基於效能的考慮，EF Core 不會將每次呼叫都包裝在 try-catch 區塊中讀取資料庫提供者的值。</span><span class="sxs-lookup"><span data-stu-id="7f100-141">For performance reasons, EF Core does not wrap each call to read a value from the database provider in a try-catch block.</span></span> <span data-ttu-id="7f100-142">不過，這有時候會導致難以診斷的例外狀況，尤其是當資料庫在模型不允許時傳回 Null 時。</span><span class="sxs-lookup"><span data-stu-id="7f100-142">However, this sometimes results in exceptions that are hard to diagnose, especially when the database returns a NULL when not allowed by the model.</span></span>

<span data-ttu-id="7f100-143">開啟 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> 會導致 EF 引入這些 try-catch 區塊，進而提供更詳細的錯誤。</span><span class="sxs-lookup"><span data-stu-id="7f100-143">Turning on <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> will cause EF to introduce these try-catch blocks and thereby provide more detailed errors.</span></span> <span data-ttu-id="7f100-144">例如：</span><span class="sxs-lookup"><span data-stu-id="7f100-144">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EnableDetailedErrors)]

## <a name="filtering"></a><span data-ttu-id="7f100-145">篩選</span><span class="sxs-lookup"><span data-stu-id="7f100-145">Filtering</span></span>

### <a name="log-levels"></a><span data-ttu-id="7f100-146">記錄層級</span><span class="sxs-lookup"><span data-stu-id="7f100-146">Log levels</span></span>

<span data-ttu-id="7f100-147">每個 EF Core 記錄檔訊息都會指派給列舉所定義的層級 <xref:Microsoft.Extensions.Logging.LogLevel> 。</span><span class="sxs-lookup"><span data-stu-id="7f100-147">Every EF Core log message is assigned to a level defined by the <xref:Microsoft.Extensions.Logging.LogLevel> enum.</span></span> <span data-ttu-id="7f100-148">依預設，EF Core 簡單記錄包含每個 `Debug` 層級或以上的訊息。</span><span class="sxs-lookup"><span data-stu-id="7f100-148">By default, EF Core simple logging includes every message at `Debug` level or above.</span></span> <span data-ttu-id="7f100-149">`LogTo` 可以傳遞較高的最小層級，以篩選出某些訊息。</span><span class="sxs-lookup"><span data-stu-id="7f100-149">`LogTo` can be passed a higher minimum level to filter out some messages.</span></span> <span data-ttu-id="7f100-150">例如， `Information` 以最少的一組記錄來傳遞結果，限制為資料庫存取和一些內務處理訊息。</span><span class="sxs-lookup"><span data-stu-id="7f100-150">For example, passing `Information` results in a minimal set of logs limited to database access and some housekeeping messages.</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[InfoOnly](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=InfoOnly)]

### <a name="specific-messages"></a><span data-ttu-id="7f100-151">特定訊息</span><span class="sxs-lookup"><span data-stu-id="7f100-151">Specific messages</span></span>

<span data-ttu-id="7f100-152">系統會為每個記錄檔訊息指派 <xref:Microsoft.Extensions.Logging.EventId> 。</span><span class="sxs-lookup"><span data-stu-id="7f100-152">Every log message is assigned an <xref:Microsoft.Extensions.Logging.EventId>.</span></span> <span data-ttu-id="7f100-153">您可以從 <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> 類別或類別存取這些識別碼， <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> 以取得關聯式特定的訊息。</span><span class="sxs-lookup"><span data-stu-id="7f100-153">These IDs can be accessed from the <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> class or the <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> class for relational-specific messages.</span></span> <span data-ttu-id="7f100-154">資料庫提供者也可能在類似的類別中有提供者特定的識別碼。</span><span class="sxs-lookup"><span data-stu-id="7f100-154">A database provider may also have provider-specific IDs in a similar class.</span></span> <span data-ttu-id="7f100-155">例如， <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> SQL Server 提供者。</span><span class="sxs-lookup"><span data-stu-id="7f100-155">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> for the SQL Server provider.</span></span>

<span data-ttu-id="7f100-156">`LogTo` 可以設定為只記錄與一或多個事件識別碼相關聯的訊息。</span><span class="sxs-lookup"><span data-stu-id="7f100-156">`LogTo` can be configured to only log the messages associated with one or more event IDs.</span></span> <span data-ttu-id="7f100-157">例如，若要只針對正在初始化或處置的內容記錄訊息：</span><span class="sxs-lookup"><span data-stu-id="7f100-157">For example, to log only messages for the context being initialized or disposed:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { CoreEventId.ContextDisposed, CoreEventId.ContextInitialized });
-->
[!code-csharp[EventIds](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EventIds)]

### <a name="message-categories"></a><span data-ttu-id="7f100-158">訊息類別</span><span class="sxs-lookup"><span data-stu-id="7f100-158">Message categories</span></span>

<span data-ttu-id="7f100-159">每個記錄訊息都會指派給一個命名的階層式記錄器類別。</span><span class="sxs-lookup"><span data-stu-id="7f100-159">Every log message is assigned to a named hierarchical logger category.</span></span> <span data-ttu-id="7f100-160">這些類別包括：</span><span class="sxs-lookup"><span data-stu-id="7f100-160">The categories are:</span></span>

| <span data-ttu-id="7f100-161">類別</span><span class="sxs-lookup"><span data-stu-id="7f100-161">Category</span></span>                                             | <span data-ttu-id="7f100-162">訊息</span><span class="sxs-lookup"><span data-stu-id="7f100-162">Messages</span></span>
|:-----------------------------------------------------|-------------------------------------------------
| <span data-ttu-id="7f100-163">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="7f100-163">Microsoft.EntityFrameworkCore</span></span>                        | <span data-ttu-id="7f100-164">所有 EF Core 訊息</span><span class="sxs-lookup"><span data-stu-id="7f100-164">All EF Core messages</span></span>
| <span data-ttu-id="7f100-165">Microsoft.entityframeworkcore 資料庫</span><span class="sxs-lookup"><span data-stu-id="7f100-165">Microsoft.EntityFrameworkCore.Database</span></span>               | <span data-ttu-id="7f100-166">所有資料庫互動</span><span class="sxs-lookup"><span data-stu-id="7f100-166">All database interactions</span></span>
| <span data-ttu-id="7f100-167">Microsoft.entityframeworkcore 連接</span><span class="sxs-lookup"><span data-stu-id="7f100-167">Microsoft.EntityFrameworkCore.Database.Connection</span></span>    | <span data-ttu-id="7f100-168">資料庫連接的使用</span><span class="sxs-lookup"><span data-stu-id="7f100-168">Uses of a database connection</span></span>
| <span data-ttu-id="7f100-169">Microsoft.entityframeworkcore。</span><span class="sxs-lookup"><span data-stu-id="7f100-169">Microsoft.EntityFrameworkCore.Database.Command</span></span>       | <span data-ttu-id="7f100-170">使用資料庫命令</span><span class="sxs-lookup"><span data-stu-id="7f100-170">Uses of a database command</span></span>
| <span data-ttu-id="7f100-171">Microsoft.entityframeworkcore。</span><span class="sxs-lookup"><span data-stu-id="7f100-171">Microsoft.EntityFrameworkCore.Database.Transaction</span></span>   | <span data-ttu-id="7f100-172">資料庫交易的使用</span><span class="sxs-lookup"><span data-stu-id="7f100-172">Uses of a database transaction</span></span>
| <span data-ttu-id="7f100-173">Microsoft.entityframeworkcore. 更新</span><span class="sxs-lookup"><span data-stu-id="7f100-173">Microsoft.EntityFrameworkCore.Update</span></span>                 | <span data-ttu-id="7f100-174">儲存實體，不包括資料庫互動</span><span class="sxs-lookup"><span data-stu-id="7f100-174">Saving entities, excluding database interactions</span></span>
| <span data-ttu-id="7f100-175">Microsoft.entityframeworkcore。模型</span><span class="sxs-lookup"><span data-stu-id="7f100-175">Microsoft.EntityFrameworkCore.Model</span></span>                  | <span data-ttu-id="7f100-176">所有模型和中繼資料互動</span><span class="sxs-lookup"><span data-stu-id="7f100-176">All model and metadata interactions</span></span>
| <span data-ttu-id="7f100-177">Microsoft.entityframeworkcore 驗證</span><span class="sxs-lookup"><span data-stu-id="7f100-177">Microsoft.EntityFrameworkCore.Model.Validation</span></span>       | <span data-ttu-id="7f100-178">模型驗證</span><span class="sxs-lookup"><span data-stu-id="7f100-178">Model validation</span></span>
| <span data-ttu-id="7f100-179">Microsoft.entityframeworkcore。查詢</span><span class="sxs-lookup"><span data-stu-id="7f100-179">Microsoft.EntityFrameworkCore.Query</span></span>                  | <span data-ttu-id="7f100-180">查詢，不包括資料庫互動</span><span class="sxs-lookup"><span data-stu-id="7f100-180">Queries, excluding database interactions</span></span>
| <span data-ttu-id="7f100-181">Microsoft.entityframeworkcore 基礎結構</span><span class="sxs-lookup"><span data-stu-id="7f100-181">Microsoft.EntityFrameworkCore.Infrastructure</span></span>         | <span data-ttu-id="7f100-182">一般事件，例如內容建立</span><span class="sxs-lookup"><span data-stu-id="7f100-182">General events, such as context creation</span></span>
| <span data-ttu-id="7f100-183">Microsoft.entityframeworkcore</span><span class="sxs-lookup"><span data-stu-id="7f100-183">Microsoft.EntityFrameworkCore.Scaffolding</span></span>            | <span data-ttu-id="7f100-184">資料庫反向工程</span><span class="sxs-lookup"><span data-stu-id="7f100-184">Database reverse engineering</span></span>
| <span data-ttu-id="7f100-185">Microsoft.entityframeworkcore。</span><span class="sxs-lookup"><span data-stu-id="7f100-185">Microsoft.EntityFrameworkCore.Migrations</span></span>             | <span data-ttu-id="7f100-186">移轉</span><span class="sxs-lookup"><span data-stu-id="7f100-186">Migrations</span></span>
| <span data-ttu-id="7f100-187">Microsoft.entityframeworkcore. 變更追蹤</span><span class="sxs-lookup"><span data-stu-id="7f100-187">Microsoft.EntityFrameworkCore.ChangeTracking</span></span>         | <span data-ttu-id="7f100-188">變更追蹤互動</span><span class="sxs-lookup"><span data-stu-id="7f100-188">Change tracking interactions</span></span>

<span data-ttu-id="7f100-189">`LogTo` 可以設定為只記錄一或多個類別目錄中的訊息。</span><span class="sxs-lookup"><span data-stu-id="7f100-189">`LogTo` can be configured to only log the messages from one or more categories.</span></span> <span data-ttu-id="7f100-190">例如，只記錄資料庫互動：</span><span class="sxs-lookup"><span data-stu-id="7f100-190">For example, to log only database interactions:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { DbLoggerCategory.Database.Name });
-->
[!code-csharp[DatabaseCategory](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseCategory)]

<span data-ttu-id="7f100-191">請注意， <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> 類別會提供用來尋找類別的階層式 API，並避免需要硬式編碼字串。</span><span class="sxs-lookup"><span data-stu-id="7f100-191">Notice that the <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> class provides a hierarchical API for finding a category and avoids the need to hard-code strings.</span></span>

<span data-ttu-id="7f100-192">由於分類是階層式的，因此使用此類別的範例 `Database` 將包含子類別 `Database.Connection` 、和的所有訊息 `Database.Command` `Database.Transaction` 。</span><span class="sxs-lookup"><span data-stu-id="7f100-192">Since categories are hierarchical, this example using the `Database` category will include all messages for the subcategories `Database.Connection`, `Database.Command`, and `Database.Transaction`.</span></span>

### <a name="custom-filters"></a><span data-ttu-id="7f100-193">自訂篩選器</span><span class="sxs-lookup"><span data-stu-id="7f100-193">Custom filters</span></span>

<span data-ttu-id="7f100-194">`LogTo` 允許自訂篩選準則用於上述所有篩選選項都不足的情況。</span><span class="sxs-lookup"><span data-stu-id="7f100-194">`LogTo` allows a custom filter to be used for cases where none of the filtering options above are sufficient.</span></span> <span data-ttu-id="7f100-195">例如，若要記錄層級或以上的任何訊息 `Information` ，以及開啟和關閉連接的訊息：</span><span class="sxs-lookup"><span data-stu-id="7f100-195">For example, to log any message at level `Information` or above, as well as messages for opening and closing a connection:</span></span>

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
> <span data-ttu-id="7f100-196">使用自訂篩選或使用此處所示的任何其他選項進行篩選，比在委派中進行篩選更有效率 `LogTo` 。</span><span class="sxs-lookup"><span data-stu-id="7f100-196">Filtering using custom filters or using any of the other options shown here is more efficient than filtering in the `LogTo` delegate.</span></span> <span data-ttu-id="7f100-197">這是因為如果篩選器決定不記錄訊息，則甚至不會建立記錄訊息。</span><span class="sxs-lookup"><span data-stu-id="7f100-197">This is because if the filter determines the message should not be logged, then the log message is not even created.</span></span>

## <a name="configuration-for-specific-messages"></a><span data-ttu-id="7f100-198">特定訊息的設定</span><span class="sxs-lookup"><span data-stu-id="7f100-198">Configuration for specific messages</span></span>

<span data-ttu-id="7f100-199">EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API 可讓應用程式變更遇到特定事件時所發生的情況。</span><span class="sxs-lookup"><span data-stu-id="7f100-199">The EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API allows applications to change what happens when a specific event is encountered.</span></span> <span data-ttu-id="7f100-200">這可以用來：</span><span class="sxs-lookup"><span data-stu-id="7f100-200">This can be used to:</span></span>

* <span data-ttu-id="7f100-201">變更記錄事件的記錄層級</span><span class="sxs-lookup"><span data-stu-id="7f100-201">Change the log level at which the event is logged</span></span>
* <span data-ttu-id="7f100-202">略過全部的事件記錄</span><span class="sxs-lookup"><span data-stu-id="7f100-202">Skip logging the event altogether</span></span>
* <span data-ttu-id="7f100-203">發生事件時擲回例外狀況</span><span class="sxs-lookup"><span data-stu-id="7f100-203">Throw an exception when the event occurs</span></span>

### <a name="changing-the-log-level-for-an-event"></a><span data-ttu-id="7f100-204">變更事件的記錄層級</span><span class="sxs-lookup"><span data-stu-id="7f100-204">Changing the log level for an event</span></span>

<span data-ttu-id="7f100-205">先前的範例使用自訂篩選來記錄每個訊息，以及 `LogLevel.Information` 所定義的兩個事件 `LogLevel.Debug` 。</span><span class="sxs-lookup"><span data-stu-id="7f100-205">The previous example used a custom filter to log every message at `LogLevel.Information` as well as two events defined for `LogLevel.Debug`.</span></span> <span data-ttu-id="7f100-206">將兩個事件的記錄層級變更為，即可達成相同目的 `Debug` `Information` ：</span><span class="sxs-lookup"><span data-stu-id="7f100-206">The same can be achieved by changing the log level of the two `Debug` events to `Information`:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Log(
                (RelationalEventId.ConnectionOpened, LogLevel.Information),
                (RelationalEventId.ConnectionClosed, LogLevel.Information)))
            .LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[ChangeLogLevel](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=ChangeLogLevel)]

### <a name="suppress-logging-an-event"></a><span data-ttu-id="7f100-207">隱藏事件的記錄</span><span class="sxs-lookup"><span data-stu-id="7f100-207">Suppress logging an event</span></span>

<span data-ttu-id="7f100-208">您可以使用類似的方式，從記錄中隱藏個別事件。</span><span class="sxs-lookup"><span data-stu-id="7f100-208">In a similar way, an individual event can be suppressed from logging.</span></span> <span data-ttu-id="7f100-209">這特別適用于略過已審核並瞭解的警告。</span><span class="sxs-lookup"><span data-stu-id="7f100-209">This is particularly useful for ignoring a warning that has been reviewed and understood.</span></span> <span data-ttu-id="7f100-210">例如：</span><span class="sxs-lookup"><span data-stu-id="7f100-210">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Ignore(CoreEventId.DetachedLazyLoadingWarning))
            .LogTo(Console.WriteLine);
-->
[!code-csharp[SuppressMessage](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SuppressMessage)]

### <a name="throw-for-an-event"></a><span data-ttu-id="7f100-211">擲回事件</span><span class="sxs-lookup"><span data-stu-id="7f100-211">Throw for an event</span></span>

<span data-ttu-id="7f100-212">最後，EF Core 可以設定為針對指定的事件擲回。</span><span class="sxs-lookup"><span data-stu-id="7f100-212">Finally, EF Core can be configured to throw for a given event.</span></span> <span data-ttu-id="7f100-213">這特別適用于將警告變更為錯誤。</span><span class="sxs-lookup"><span data-stu-id="7f100-213">This is particularly useful for changing a warning into an error.</span></span> <span data-ttu-id="7f100-214"> (真的，這是方法的原始用途 `ConfigureWarnings` ，因此是名稱 ) 例如：</span><span class="sxs-lookup"><span data-stu-id="7f100-214">(Indeed, this was the original purpose of `ConfigureWarnings` method, hence the name.) For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Throw(RelationalEventId.MultipleCollectionIncludeWarning))
            .LogTo(Console.WriteLine);
-->
[!code-csharp[ThrowForEvent](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=ThrowForEvent)]

## <a name="message-contents-and-formatting"></a><span data-ttu-id="7f100-215">訊息內容和格式</span><span class="sxs-lookup"><span data-stu-id="7f100-215">Message contents and formatting</span></span>

<span data-ttu-id="7f100-216">的預設內容 `LogTo` 會跨多行格式化。</span><span class="sxs-lookup"><span data-stu-id="7f100-216">The default content from `LogTo` is formatted across multiple lines.</span></span> <span data-ttu-id="7f100-217">第一行包含訊息中繼資料：</span><span class="sxs-lookup"><span data-stu-id="7f100-217">The first line contains message metadata:</span></span>

* <span data-ttu-id="7f100-218"><xref:Microsoft.Extensions.Logging.LogLevel>作為四個字元的前置詞</span><span class="sxs-lookup"><span data-stu-id="7f100-218">The <xref:Microsoft.Extensions.Logging.LogLevel> as a four-character prefix</span></span>
* <span data-ttu-id="7f100-219">針對目前文化特性格式化的本機時間戳</span><span class="sxs-lookup"><span data-stu-id="7f100-219">A local timestamp, formatted for the current culture</span></span>
* <span data-ttu-id="7f100-220"><xref:Microsoft.Extensions.Logging.EventId>表單中的，可以複製/貼上以從中取得成員 <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> 或其中一個其他 `EventId` 類別，加上原始識別碼值</span><span class="sxs-lookup"><span data-stu-id="7f100-220">The <xref:Microsoft.Extensions.Logging.EventId> in the form that can be copy/pasted to get the member from <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> or one of the other `EventId` classes, plus the raw ID value</span></span>
* <span data-ttu-id="7f100-221">事件類別目錄，如上所述。</span><span class="sxs-lookup"><span data-stu-id="7f100-221">The event category, as described above.</span></span>

<span data-ttu-id="7f100-222">例如：</span><span class="sxs-lookup"><span data-stu-id="7f100-222">For example:</span></span>

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

<span data-ttu-id="7f100-223">您可以從傳遞值以自訂此內容 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> ，如下列各節所示。</span><span class="sxs-lookup"><span data-stu-id="7f100-223">This content can be customized by passing values from <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions>, as shown in the following sections.</span></span>

> [!TIP]
> <span data-ttu-id="7f100-224">請考慮使用 [Microsoft 擴充功能](/aspnet/core/fundamentals/logging) ，以進一步控制記錄格式。</span><span class="sxs-lookup"><span data-stu-id="7f100-224">Consider using [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging) for more control over log formatting.</span></span>

### <a name="using-utc-time"></a><span data-ttu-id="7f100-225">使用 UTC 時間</span><span class="sxs-lookup"><span data-stu-id="7f100-225">Using UTC time</span></span>

<span data-ttu-id="7f100-226">根據預設，時間戳記是針對在進行偵錯工具時的本機耗用量所設計。</span><span class="sxs-lookup"><span data-stu-id="7f100-226">By default, timestamps are designed for local consumption while debugging.</span></span> <span data-ttu-id="7f100-227"><xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions.DefaultWithUtcTime?displayProperty=nameWithType>請改用與文化特性無關的 UTC 時間戳記，但保留其他所有專案。</span><span class="sxs-lookup"><span data-stu-id="7f100-227">Use <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions.DefaultWithUtcTime?displayProperty=nameWithType> to use culture-agnostic UTC timestamps instead, but keep everything else the same.</span></span> <span data-ttu-id="7f100-228">例如：</span><span class="sxs-lookup"><span data-stu-id="7f100-228">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithUtcTime);
-->
[!code-csharp[Utc](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=Utc)]

<span data-ttu-id="7f100-229">此範例會產生下列記錄格式：</span><span class="sxs-lookup"><span data-stu-id="7f100-229">This example results in the following log formatting:</span></span>

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

### <a name="single-line-logging"></a><span data-ttu-id="7f100-230">單行記錄</span><span class="sxs-lookup"><span data-stu-id="7f100-230">Single line logging</span></span>

<span data-ttu-id="7f100-231">有時候，每個記錄訊息只會取得一行。</span><span class="sxs-lookup"><span data-stu-id="7f100-231">Sometimes it is useful to get exactly one line per log message.</span></span> <span data-ttu-id="7f100-232">這可以由啟用 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions.SingleLine?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="7f100-232">This can be enabled by <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions.SingleLine?displayProperty=nameWithType>.</span></span> <span data-ttu-id="7f100-233">例如：</span><span class="sxs-lookup"><span data-stu-id="7f100-233">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithLocalTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[SingleLine](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SingleLine)]

<span data-ttu-id="7f100-234">此範例會產生下列記錄格式：</span><span class="sxs-lookup"><span data-stu-id="7f100-234">This example results in the following log formatting:</span></span>

```output
info: 10/6/2020 10:52:45.723 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command) -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
dbug: 10/6/2020 10:52:45.723 RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committing transaction.
dbug: 10/6/2020 10:52:45.725 RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committed transaction.
```

### <a name="other-content-options"></a><span data-ttu-id="7f100-235">其他內容選項</span><span class="sxs-lookup"><span data-stu-id="7f100-235">Other content options</span></span>

<span data-ttu-id="7f100-236">中的其他旗標 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> 可以用來修剪記錄檔中包含的中繼資料量。</span><span class="sxs-lookup"><span data-stu-id="7f100-236">Other flags in <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> can be used to trim down the amount of metadata included in the log.</span></span> <span data-ttu-id="7f100-237">這有助於搭配單行記錄使用。</span><span class="sxs-lookup"><span data-stu-id="7f100-237">This is can be useful in conjunction with single-line logging.</span></span> <span data-ttu-id="7f100-238">例如：</span><span class="sxs-lookup"><span data-stu-id="7f100-238">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.UtcTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[TerseLogs](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=TerseLogs)]

<span data-ttu-id="7f100-239">此範例會產生下列記錄格式：</span><span class="sxs-lookup"><span data-stu-id="7f100-239">This example results in the following log formatting:</span></span>

```output
2020-10-06T17:52:45.7320362Z -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
2020-10-06T17:52:45.7320531Z -> Committing transaction.
2020-10-06T17:52:45.7339441Z -> Committed transaction.
```

## <a name="moving-from-ef6"></a><span data-ttu-id="7f100-240">從 EF6 移動</span><span class="sxs-lookup"><span data-stu-id="7f100-240">Moving from EF6</span></span>

<span data-ttu-id="7f100-241">EF Core 簡單的記錄與 <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> EF6 中的兩個重要方式不同：</span><span class="sxs-lookup"><span data-stu-id="7f100-241">EF Core simple logging differs from <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6 in two important ways:</span></span>

* <span data-ttu-id="7f100-242">記錄訊息不限於只有資料庫互動</span><span class="sxs-lookup"><span data-stu-id="7f100-242">Log messages are not limited to only database interactions</span></span>
* <span data-ttu-id="7f100-243">必須在內容初始化時間設定記錄</span><span class="sxs-lookup"><span data-stu-id="7f100-243">The logging must be configured at context initialization time</span></span>

<span data-ttu-id="7f100-244">針對第一個差異，上述的篩選可以用來限制要記錄的訊息。</span><span class="sxs-lookup"><span data-stu-id="7f100-244">For the first difference, the filtering described above can be used to limit which messages are logged.</span></span>

<span data-ttu-id="7f100-245">第二個差異是刻意變更，可在不需要記錄訊息時，藉由不產生記錄檔訊息來改善效能。</span><span class="sxs-lookup"><span data-stu-id="7f100-245">The second difference is an intentional change to improve performance by not generating log messages when they are not needed.</span></span> <span data-ttu-id="7f100-246">不過，您仍然可以 `Log` 在上建立屬性，然後僅在已設定的情況下使用它，來取得 EF6 的類似行為 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="7f100-246">However, it is still possible to get a similar behavior to EF6 by creating a `Log` property on your `DbContext` and then using it only when it has been set.</span></span> <span data-ttu-id="7f100-247">例如：</span><span class="sxs-lookup"><span data-stu-id="7f100-247">For example:</span></span>

<!--
    public Action<string> Log { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(s => Log?.Invoke(s));
-->
[!code-csharp[DatabaseLog](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseLog)]
