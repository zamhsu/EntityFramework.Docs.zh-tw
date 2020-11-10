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
# <a name="using-microsoftextensionslogging-in-ef-core"></a><span data-ttu-id="c3e85-103">使用 EF Core 中的登入</span><span class="sxs-lookup"><span data-stu-id="c3e85-103">Using Microsoft.Extensions.Logging in EF Core</span></span>

<span data-ttu-id="c3e85-104">針對許多常見的記錄系統，記錄是具有外掛程式提供者的可[延伸](/dotnet/core/extensions/logging)記錄機制。</span><span class="sxs-lookup"><span data-stu-id="c3e85-104">[Microsoft.Extensions.Logging](/dotnet/core/extensions/logging) is an extensible logging mechanism with plug-in providers for many common logging systems.</span></span> <span data-ttu-id="c3e85-105">Microsoft 提供的外掛程式 (例如 [，) 和](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) 協力廠商外掛程式 (例如 [Serilog。記錄](https://www.nuget.org/packages/Serilog.Extensions.Logging/)) 是以 NuGet 套件的形式來提供。</span><span class="sxs-lookup"><span data-stu-id="c3e85-105">Both Microsoft-supplied plug-ins (e.g [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)) and third-party plug-ins (e.g. [Serilog.Extensions.Logging](https://www.nuget.org/packages/Serilog.Extensions.Logging/)) are available as NuGet packages.</span></span>

<span data-ttu-id="c3e85-106">Entity Framework Core (EF Core) 完全與整合 `Microsoft.Extensions.Logging` 。</span><span class="sxs-lookup"><span data-stu-id="c3e85-106">Entity Framework Core (EF Core) fully integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="c3e85-107">不過，請考慮使用 [簡單的記錄](xref:core/logging-events-diagnostics/simple-logging) 來進行記錄，特別是針對未使用相依性插入的應用程式。</span><span class="sxs-lookup"><span data-stu-id="c3e85-107">However, consider using [simple logging](xref:core/logging-events-diagnostics/simple-logging) for a simpler way to log, especially for applications that don't use dependency injection.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="c3e85-108">ASP.NET Core 應用程式</span><span class="sxs-lookup"><span data-stu-id="c3e85-108">ASP.NET Core applications</span></span>

<span data-ttu-id="c3e85-109">`Microsoft.Extensions.Logging`[預設會在 ASP.NET Core 應用程式中使用](/aspnet/core/fundamentals/logging)。</span><span class="sxs-lookup"><span data-stu-id="c3e85-109">`Microsoft.Extensions.Logging` is [used by default in ASP.NET Core applications](/aspnet/core/fundamentals/logging).</span></span> <span data-ttu-id="c3e85-110">呼叫 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 或 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> 。</span><span class="sxs-lookup"><span data-stu-id="c3e85-110">Calling <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> or <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A>.</span></span>

## <a name="other-application-types"></a><span data-ttu-id="c3e85-111">其他應用程式類型</span><span class="sxs-lookup"><span data-stu-id="c3e85-111">Other application types</span></span>

<span data-ttu-id="c3e85-112">其他應用程式類型可以使用 [GenericHost](/dotnet/core/extensions/generic-host) 取得與 ASP.NET Core 中使用的相同相依性插入模式。</span><span class="sxs-lookup"><span data-stu-id="c3e85-112">Other application types can use the [GenericHost](/dotnet/core/extensions/generic-host) to get the same dependency injection patterns as are used in ASP.NET Core.</span></span> <span data-ttu-id="c3e85-113">然後可以使用 AddDbCoNtext 或 AdddbcoNtextpool 再也，就像在 ASP.NET Core 應用程式中一樣。</span><span class="sxs-lookup"><span data-stu-id="c3e85-113">AddDbContext or AddDbContextPool can then be used just like in ASP.NET Core applications.</span></span>

<span data-ttu-id="c3e85-114">`Microsoft.Extensions.Logging` 也可以用於不使用相依性插入的應用程式，雖然 [簡單的記錄](xref:core/logging-events-diagnostics/simple-logging) 可能更容易設定。</span><span class="sxs-lookup"><span data-stu-id="c3e85-114">`Microsoft.Extensions.Logging` can also be used for applications that don't use dependency injection, although [simple logging](xref:core/logging-events-diagnostics/simple-logging) can be easier to set up.</span></span>

<span data-ttu-id="c3e85-115">`Microsoft.Extensions.Logging` 需要建立 <xref:Microsoft.Extensions.Logging.LoggerFactory> 。</span><span class="sxs-lookup"><span data-stu-id="c3e85-115">`Microsoft.Extensions.Logging` requires creation of a <xref:Microsoft.Extensions.Logging.LoggerFactory>.</span></span> <span data-ttu-id="c3e85-116">此 factory 應儲存為靜態/全域實例，並在每次建立 DbCoNtext 時使用。</span><span class="sxs-lookup"><span data-stu-id="c3e85-116">This factory should be stored as a static/global instance somewhere and used each time a DbContext is created.</span></span> <span data-ttu-id="c3e85-117">例如，通常會將記錄器 factory 儲存為 DbCoNtext 上的靜態屬性。</span><span class="sxs-lookup"><span data-stu-id="c3e85-117">For example, it is common to store the logger factory as a static property on the DbContext.</span></span>

### <a name="ef-core-30-and-above"></a>[<span data-ttu-id="c3e85-118">EF Core 3.0 和更新版本</span><span class="sxs-lookup"><span data-stu-id="c3e85-118">EF Core 3.0 and above</span></span>](#tab/v3)

<!--
        public static readonly ILoggerFactory MyLoggerFactory
            = LoggerFactory.Create(builder => { builder.AddConsole(); });
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="ef-core-21"></a>[<span data-ttu-id="c3e85-119">EF Core 2.1</span><span class="sxs-lookup"><span data-stu-id="c3e85-119">EF Core 2.1</span></span>](#tab/v2)

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

> [!WARNING]
> <span data-ttu-id="c3e85-120">在 EF Core 2.1 中，應用程式不會為每個 DbCoNtext 實例建立新的 Server.loggerfactory 實例。</span><span class="sxs-lookup"><span data-stu-id="c3e85-120">In EF Core 2.1, It is very important that applications do not create a new LoggerFactory instance for each DbContext instance.</span></span> <span data-ttu-id="c3e85-121">這樣做會導致記憶體流失和效能不佳。</span><span class="sxs-lookup"><span data-stu-id="c3e85-121">Doing so will result in a memory leak and poor performance.</span></span> <span data-ttu-id="c3e85-122">這已在 EF Core 3.0 和更新版本中修正。</span><span class="sxs-lookup"><span data-stu-id="c3e85-122">This has been fixed in EF Core 3.0 and above.</span></span>

***

<span data-ttu-id="c3e85-123">然後，這個 singleton/global 實例應該會向 EF Core 註冊 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> 。</span><span class="sxs-lookup"><span data-stu-id="c3e85-123">This singleton/global instance should then be registered with EF Core on the <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>.</span></span> <span data-ttu-id="c3e85-124">例如：</span><span class="sxs-lookup"><span data-stu-id="c3e85-124">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .UseLoggerFactory(MyLoggerFactory)
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFLogging;ConnectRetryCount=0");
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

## <a name="getting-detailed-messages"></a><span data-ttu-id="c3e85-125">取得詳細訊息</span><span class="sxs-lookup"><span data-stu-id="c3e85-125">Getting detailed messages</span></span>

> [!TIP]
> <span data-ttu-id="c3e85-126">當使用 AddDbCoNtext 或將 DbCoNtextOptions 實例傳遞至 DbCoNtext 的函式時，仍會呼叫 OnConfiguring。</span><span class="sxs-lookup"><span data-stu-id="c3e85-126">OnConfiguring is still called when AddDbContext is used or a DbContextOptions instance is passed to the DbContext constructor.</span></span> <span data-ttu-id="c3e85-127">這讓它成為套用內容設定的理想位置，不論 DbCoNtext 的結構為何。</span><span class="sxs-lookup"><span data-stu-id="c3e85-127">This makes it the ideal place to apply context configuration regardless of how the DbContext is constructed.</span></span>

### <a name="sensitive-data"></a><span data-ttu-id="c3e85-128">敏感性資料</span><span class="sxs-lookup"><span data-stu-id="c3e85-128">Sensitive data</span></span>

<span data-ttu-id="c3e85-129">根據預設，EF Core 不會在例外狀況訊息中包含任何資料的值。</span><span class="sxs-lookup"><span data-stu-id="c3e85-129">By default, EF Core will not include the values of any data in exception messages.</span></span> <span data-ttu-id="c3e85-130">這是因為這類資料可能是機密資料，如果未處理例外狀況，可能會顯示在生產環境中。</span><span class="sxs-lookup"><span data-stu-id="c3e85-130">This is because such data may be confidential, and could be revealed in production use if an exception is not handled.</span></span>

<span data-ttu-id="c3e85-131">不過，在進行偵錯工具時，瞭解資料值（特別是索引鍵）可能會很有説明。</span><span class="sxs-lookup"><span data-stu-id="c3e85-131">However, knowing data values, especially for keys, can be very helpful when debugging.</span></span> <span data-ttu-id="c3e85-132">您可以藉由呼叫，在 EF Core 中啟用此功能 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> 。</span><span class="sxs-lookup"><span data-stu-id="c3e85-132">This can be enabled in EF Core by calling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging>.</span></span> <span data-ttu-id="c3e85-133">例如：</span><span class="sxs-lookup"><span data-stu-id="c3e85-133">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableSensitiveDataLogging();
-->
[!code-csharp[EnableSensitiveDataLogging](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableSensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a><span data-ttu-id="c3e85-134">詳細的查詢例外狀況</span><span class="sxs-lookup"><span data-stu-id="c3e85-134">Detailed query exceptions</span></span>

<span data-ttu-id="c3e85-135">基於效能的考慮，EF Core 不會將每次呼叫都包裝在 try-catch 區塊中讀取資料庫提供者的值。</span><span class="sxs-lookup"><span data-stu-id="c3e85-135">For performance reasons, EF Core does not wrap each call to read a value from the database provider in a try-catch block.</span></span> <span data-ttu-id="c3e85-136">不過，這有時候會導致難以診斷的例外狀況，尤其是當資料庫在模型不允許時傳回 Null 時。</span><span class="sxs-lookup"><span data-stu-id="c3e85-136">However, this sometimes results in exceptions that are hard to diagnose, especially when the database returns a NULL when not allowed by the model.</span></span>

<span data-ttu-id="c3e85-137">開啟 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> 會導致 EF 引入這些 try-catch 區塊，進而提供更詳細的錯誤。</span><span class="sxs-lookup"><span data-stu-id="c3e85-137">Turning on <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> will cause EF to introduce these try-catch blocks and thereby provide more detailed errors.</span></span> <span data-ttu-id="c3e85-138">例如：</span><span class="sxs-lookup"><span data-stu-id="c3e85-138">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableDetailedErrors)]

## <a name="configuration-for-specific-messages"></a><span data-ttu-id="c3e85-139">特定訊息的設定</span><span class="sxs-lookup"><span data-stu-id="c3e85-139">Configuration for specific messages</span></span>

<span data-ttu-id="c3e85-140">EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API 可讓應用程式變更遇到特定事件時所發生的情況。</span><span class="sxs-lookup"><span data-stu-id="c3e85-140">The EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API allows applications to change what happens when a specific event is encountered.</span></span> <span data-ttu-id="c3e85-141">這可以用來：</span><span class="sxs-lookup"><span data-stu-id="c3e85-141">This can be used to:</span></span>

* <span data-ttu-id="c3e85-142">變更記錄事件的記錄層級</span><span class="sxs-lookup"><span data-stu-id="c3e85-142">Change the log level at which the event is logged</span></span>
* <span data-ttu-id="c3e85-143">略過全部的事件記錄</span><span class="sxs-lookup"><span data-stu-id="c3e85-143">Skip logging the event altogether</span></span>
* <span data-ttu-id="c3e85-144">發生事件時擲回例外狀況</span><span class="sxs-lookup"><span data-stu-id="c3e85-144">Throw an exception when the event occurs</span></span>

### <a name="changing-the-log-level-for-an-event"></a><span data-ttu-id="c3e85-145">變更事件的記錄層級</span><span class="sxs-lookup"><span data-stu-id="c3e85-145">Changing the log level for an event</span></span>

<span data-ttu-id="c3e85-146">有時候，變更事件的預先定義記錄層級可能會很有用。</span><span class="sxs-lookup"><span data-stu-id="c3e85-146">Sometimes it can be useful to change the pre-defined log level for an event.</span></span> <span data-ttu-id="c3e85-147">例如，這可以用來將兩個額外的事件升階 `LogLevel.Debug` 至 `LogLevel.Information` ：</span><span class="sxs-lookup"><span data-stu-id="c3e85-147">For example, this can be used to promote two additional events from `LogLevel.Debug` to `LogLevel.Information`:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Log(
                    (RelationalEventId.ConnectionOpened, LogLevel.Information),
                    (RelationalEventId.ConnectionClosed, LogLevel.Information)));
-->
[!code-csharp[ChangeLogLevel](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ChangeLogLevel)]

### <a name="suppress-logging-an-event"></a><span data-ttu-id="c3e85-148">隱藏事件的記錄</span><span class="sxs-lookup"><span data-stu-id="c3e85-148">Suppress logging an event</span></span>

<span data-ttu-id="c3e85-149">您可以使用類似的方式，從記錄中隱藏個別事件。</span><span class="sxs-lookup"><span data-stu-id="c3e85-149">In a similar way, an individual event can be suppressed from logging.</span></span> <span data-ttu-id="c3e85-150">這特別適用于略過已審核並瞭解的警告。</span><span class="sxs-lookup"><span data-stu-id="c3e85-150">This is particularly useful for ignoring a warning that has been reviewed and understood.</span></span> <span data-ttu-id="c3e85-151">例如：</span><span class="sxs-lookup"><span data-stu-id="c3e85-151">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Ignore(CoreEventId.DetachedLazyLoadingWarning));
-->
[!code-csharp[SuppressMessage](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=SuppressMessage)]

### <a name="throw-for-an-event"></a><span data-ttu-id="c3e85-152">擲回事件</span><span class="sxs-lookup"><span data-stu-id="c3e85-152">Throw for an event</span></span>

<span data-ttu-id="c3e85-153">最後，EF Core 可以設定為針對指定的事件擲回。</span><span class="sxs-lookup"><span data-stu-id="c3e85-153">Finally, EF Core can be configured to throw for a given event.</span></span> <span data-ttu-id="c3e85-154">這特別適用于將警告變更為錯誤。</span><span class="sxs-lookup"><span data-stu-id="c3e85-154">This is particularly useful for changing a warning into an error.</span></span> <span data-ttu-id="c3e85-155"> (真的，這是方法的原始用途 `ConfigureWarnings` ，因此是名稱 ) 例如：</span><span class="sxs-lookup"><span data-stu-id="c3e85-155">(Indeed, this was the original purpose of `ConfigureWarnings` method, hence the name.) For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Throw(RelationalEventId.QueryPossibleUnintendedUseOfEqualsWarning));
-->
[!code-csharp[ThrowForEvent](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ThrowForEvent)]

## <a name="filtering-and-other-configuration"></a><span data-ttu-id="c3e85-156">篩選和其他設定</span><span class="sxs-lookup"><span data-stu-id="c3e85-156">Filtering and other configuration</span></span>

<span data-ttu-id="c3e85-157">如需記錄檔篩選和其他設定的指引，請參閱 [.net 中的記錄](/dotnet/core/extensions/logging) 。</span><span class="sxs-lookup"><span data-stu-id="c3e85-157">See [Logging in .NET](/dotnet/core/extensions/logging) for guidance on log filtering and other configuration.</span></span>

<span data-ttu-id="c3e85-158">EF Core 記錄事件定義于下列其中一個：</span><span class="sxs-lookup"><span data-stu-id="c3e85-158">EF Core logging events are defined in one of:</span></span>

* <span data-ttu-id="c3e85-159"><xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> 適用于所有 EF Core 資料庫提供者的常見事件</span><span class="sxs-lookup"><span data-stu-id="c3e85-159"><xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> for events common to all EF Core database providers</span></span>
* <span data-ttu-id="c3e85-160"><xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> 適用于所有關系資料庫提供者的常見事件</span><span class="sxs-lookup"><span data-stu-id="c3e85-160"><xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> for events common to all relational database providers</span></span>
* <span data-ttu-id="c3e85-161">與目前資料庫提供者相關之事件的類似類別。</span><span class="sxs-lookup"><span data-stu-id="c3e85-161">A similar class for events specific to the current database provider.</span></span> <span data-ttu-id="c3e85-162">例如， <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> SQL Server 提供者。</span><span class="sxs-lookup"><span data-stu-id="c3e85-162">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> for the SQL Server provider.</span></span>

<span data-ttu-id="c3e85-163">這些定義包含每個事件的事件識別碼、記錄層級和類別，如所使用 `Microsoft.Extensions.Logging` 。</span><span class="sxs-lookup"><span data-stu-id="c3e85-163">These definitions contain the event IDs, log level, and category for each event, as used by `Microsoft.Extensions.Logging`.</span></span>
