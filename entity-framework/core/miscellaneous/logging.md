---
title: 記錄-EF Core
description: 使用 Entity Framework Core 設定記錄
author: ajcvickers
ms.date: 10/06/2020
uid: core/miscellaneous/logging
ms.openlocfilehash: 389834b3822aeeaefb8c085538bc6359ccfa7094
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063006"
---
# <a name="logging"></a><span data-ttu-id="3c940-103">記錄</span><span class="sxs-lookup"><span data-stu-id="3c940-103">Logging</span></span>

> [!TIP]
> <span data-ttu-id="3c940-104">您可以在 GitHub 上檢視此文章的[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="3c940-104">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) on GitHub.</span></span>

## <a name="simple-logging"></a><span data-ttu-id="3c940-105">簡單記錄</span><span class="sxs-lookup"><span data-stu-id="3c940-105">Simple logging</span></span>

> [!NOTE]
> <span data-ttu-id="3c940-106">這項功能已在 EF Core 5.0 中新增。</span><span class="sxs-lookup"><span data-stu-id="3c940-106">This feature was added in EF Core 5.0.</span></span>

<span data-ttu-id="3c940-107">Entity Framework Core (EF Core) 會產生作業的記錄訊息，例如執行查詢或儲存資料庫的變更。</span><span class="sxs-lookup"><span data-stu-id="3c940-107">Entity Framework Core (EF Core) generates log messages for operations such as executing a query or saving changes to the database.</span></span> <span data-ttu-id="3c940-108">您可以使用[LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)從任何類型的應用程式存取這些應用程式</span><span class="sxs-lookup"><span data-stu-id="3c940-108">These can be accessed from any type of application through use of [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> <span data-ttu-id="3c940-109">設定 [DbCoNtext 實例](xref:core/miscellaneous/configuring-dbcontext)時。</span><span class="sxs-lookup"><span data-stu-id="3c940-109">when [configuring a DbContext instance](xref:core/miscellaneous/configuring-dbcontext).</span></span> <span data-ttu-id="3c940-110">這項設定通常是在的覆寫中完成 <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="3c940-110">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="3c940-111">例如︰</span><span class="sxs-lookup"><span data-stu-id="3c940-111">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="3c940-112">此概念類似于 <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> EF6。</span><span class="sxs-lookup"><span data-stu-id="3c940-112">This concept is similar to <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6.</span></span>

<span data-ttu-id="3c940-113">如需詳細資訊，請參閱 [簡單記錄](xref:core/miscellaneous/events/simple-logging) 。</span><span class="sxs-lookup"><span data-stu-id="3c940-113">See [Simple Logging](xref:core/miscellaneous/events/simple-logging) for more information.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="3c940-114">ASP.NET Core 應用程式</span><span class="sxs-lookup"><span data-stu-id="3c940-114">ASP.NET Core applications</span></span>

<span data-ttu-id="3c940-115">EF Core 在 `AddDbContext` 使用或時，會自動與 ASP.NET Core 的記錄機制整合 `AddDbContextPool` 。</span><span class="sxs-lookup"><span data-stu-id="3c940-115">EF Core integrates automatically with the logging mechanisms of ASP.NET Core whenever `AddDbContext` or `AddDbContextPool` is used.</span></span> <span data-ttu-id="3c940-116">因此，當您使用 ASP.NET Core 時，應該依照 [ASP.NET Core 檔](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)中所述的方式設定記錄。</span><span class="sxs-lookup"><span data-stu-id="3c940-116">Therefore, when using ASP.NET Core, logging should be configured as described in the [ASP.NET Core documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>

## <a name="other-applications"></a><span data-ttu-id="3c940-117">其他應用程式</span><span class="sxs-lookup"><span data-stu-id="3c940-117">Other applications</span></span>

<span data-ttu-id="3c940-118">EF Core 記錄需要 ILoggerFactory，它本身是以一或多個記錄提供者進行設定。</span><span class="sxs-lookup"><span data-stu-id="3c940-118">EF Core logging requires an ILoggerFactory which is itself configured with one or more logging providers.</span></span> <span data-ttu-id="3c940-119">一般提供者隨附于下列套件：</span><span class="sxs-lookup"><span data-stu-id="3c940-119">Common providers are shipped in the following packages:</span></span>

* <span data-ttu-id="3c940-120">[登入][主控台](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)：簡單的主控台記錄器。</span><span class="sxs-lookup"><span data-stu-id="3c940-120">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): A simple console logger.</span></span>
* <span data-ttu-id="3c940-121">[Microsoft.extensions.logging.azureappservices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/)：支援 Azure App 服務的「診斷記錄」和「記錄資料流程」功能。</span><span class="sxs-lookup"><span data-stu-id="3c940-121">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): Supports Azure App Services 'Diagnostics logs' and 'Log stream' features.</span></span>
* <span data-ttu-id="3c940-122">在 [[記錄](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/)檔] 中，使用 [ (] 將記錄到偵錯工具監視器。</span><span class="sxs-lookup"><span data-stu-id="3c940-122">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): Logs to a debugger monitor using System.Diagnostics.Debug.WriteLine().</span></span>
* <span data-ttu-id="3c940-123">。 [EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/)：記錄至 Windows 事件記錄檔。</span><span class="sxs-lookup"><span data-stu-id="3c940-123">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): Logs to Windows Event Log.</span></span>
* <span data-ttu-id="3c940-124">EventListener [：支援 eventsource/](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/)。</span><span class="sxs-lookup"><span data-stu-id="3c940-124">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): Supports EventSource/EventListener.</span></span>
* <span data-ttu-id="3c940-125">[TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/)：使用記錄至追蹤接聽程式 `System.Diagnostics.TraceSource.TraceEvent()` 。</span><span class="sxs-lookup"><span data-stu-id="3c940-125">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): Logs to a trace listener using `System.Diagnostics.TraceSource.TraceEvent()`.</span></span>

<span data-ttu-id="3c940-126"> () 安裝適當的封裝之後，應用程式應建立 Server.loggerfactory 的 singleton/global 實例。</span><span class="sxs-lookup"><span data-stu-id="3c940-126">After installing the appropriate package(s), the application should create a singleton/global instance of a LoggerFactory.</span></span> <span data-ttu-id="3c940-127">例如，使用主控台記錄器：</span><span class="sxs-lookup"><span data-stu-id="3c940-127">For example, using the console logger:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="3c940-128">3.x 版</span><span class="sxs-lookup"><span data-stu-id="3c940-128">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="3c940-129">2.x 版</span><span class="sxs-lookup"><span data-stu-id="3c940-129">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="3c940-130">下列程式碼範例會使用 `ConsoleLoggerProvider` 在2.2 版中已過時的函式，並在3.0 中取代。</span><span class="sxs-lookup"><span data-stu-id="3c940-130">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="3c940-131">使用2.2 時，忽略和隱藏警告是安全的。</span><span class="sxs-lookup"><span data-stu-id="3c940-131">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

<span data-ttu-id="3c940-132">然後，這個 singleton/global 實例應該會向 EF Core 註冊 `DbContextOptionsBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="3c940-132">This singleton/global instance should then be registered with EF Core on the `DbContextOptionsBuilder`.</span></span> <span data-ttu-id="3c940-133">例如︰</span><span class="sxs-lookup"><span data-stu-id="3c940-133">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> <span data-ttu-id="3c940-134">應用程式不會為每個內容實例建立新的 ILoggerFactory 實例，這是很重要的。</span><span class="sxs-lookup"><span data-stu-id="3c940-134">It is very important that applications do not create a new ILoggerFactory instance for each context instance.</span></span> <span data-ttu-id="3c940-135">這樣做會導致記憶體流失和效能不佳。</span><span class="sxs-lookup"><span data-stu-id="3c940-135">Doing so will result in a memory leak and poor performance.</span></span>

## <a name="filtering-what-is-logged"></a><span data-ttu-id="3c940-136">篩選記錄的內容</span><span class="sxs-lookup"><span data-stu-id="3c940-136">Filtering what is logged</span></span>

<span data-ttu-id="3c940-137">應用程式可以藉由設定 ILoggerProvider 上的篩選器來控制記錄的內容。</span><span class="sxs-lookup"><span data-stu-id="3c940-137">The application can control what is logged by configuring a filter on the ILoggerProvider.</span></span> <span data-ttu-id="3c940-138">例如︰</span><span class="sxs-lookup"><span data-stu-id="3c940-138">For example:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="3c940-139">3.x 版</span><span class="sxs-lookup"><span data-stu-id="3c940-139">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="3c940-140">2.x 版</span><span class="sxs-lookup"><span data-stu-id="3c940-140">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="3c940-141">下列程式碼範例會使用 `ConsoleLoggerProvider` 在2.2 版中已過時的函式，並在3.0 中取代。</span><span class="sxs-lookup"><span data-stu-id="3c940-141">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="3c940-142">使用2.2 時，忽略和隱藏警告是安全的。</span><span class="sxs-lookup"><span data-stu-id="3c940-142">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[]
    {
        new ConsoleLoggerProvider((category, level)
            => category == DbLoggerCategory.Database.Command.Name
               && level == LogLevel.Information, true)
    });
```

***

<span data-ttu-id="3c940-143">在此範例中，會將記錄篩選成隻傳回訊息：</span><span class="sxs-lookup"><span data-stu-id="3c940-143">In this example, the log is filtered to only return messages:</span></span>

* <span data-ttu-id="3c940-144">在 ' Microsoft.entityframeworkcore ' 類別中</span><span class="sxs-lookup"><span data-stu-id="3c940-144">in the 'Microsoft.EntityFrameworkCore.Database.Command' category</span></span>
* <span data-ttu-id="3c940-145">在「資訊」層級</span><span class="sxs-lookup"><span data-stu-id="3c940-145">at the 'Information' level</span></span>

<span data-ttu-id="3c940-146">針對 EF Core，系統會在類別中定義記錄器分類 `DbLoggerCategory` ，以便輕鬆地尋找類別目錄，但這些分類會解析成簡單字串。</span><span class="sxs-lookup"><span data-stu-id="3c940-146">For EF Core, logger categories are defined in the `DbLoggerCategory` class to make it easy to find categories, but these resolve to simple strings.</span></span>

<span data-ttu-id="3c940-147">您可以在 [ASP.NET Core 記錄檔](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)中找到基礎記錄基礎結構的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="3c940-147">More details on the underlying logging infrastructure can be found in the [ASP.NET Core logging documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>
