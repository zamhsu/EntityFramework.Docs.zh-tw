---
title: 記錄-EF Core
description: 使用 Entity Framework Core 設定記錄
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f6e35c6d-45b7-4258-be1d-87c1bb67438d
uid: core/miscellaneous/logging
ms.openlocfilehash: 56d0e3f8c5ecf56ee6bd6ddf0c9668bb8e3ac12f
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617741"
---
# <a name="logging"></a><span data-ttu-id="abf5e-103">記錄</span><span class="sxs-lookup"><span data-stu-id="abf5e-103">Logging</span></span>

> [!TIP]  
> <span data-ttu-id="abf5e-104">您可以在 GitHub 上查看這篇文章的 [範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) 。</span><span class="sxs-lookup"><span data-stu-id="abf5e-104">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) on GitHub.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="abf5e-105">ASP.NET Core 應用程式</span><span class="sxs-lookup"><span data-stu-id="abf5e-105">ASP.NET Core applications</span></span>

<span data-ttu-id="abf5e-106">EF Core 在 `AddDbContext` 使用或時，會自動與 ASP.NET Core 的記錄機制整合 `AddDbContextPool` 。</span><span class="sxs-lookup"><span data-stu-id="abf5e-106">EF Core integrates automatically with the logging mechanisms of ASP.NET Core whenever `AddDbContext` or `AddDbContextPool` is used.</span></span> <span data-ttu-id="abf5e-107">因此，當您使用 ASP.NET Core 時，應該依照 [ASP.NET Core 檔](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)中所述的方式設定記錄。</span><span class="sxs-lookup"><span data-stu-id="abf5e-107">Therefore, when using ASP.NET Core, logging should be configured as described in the [ASP.NET Core documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>

## <a name="other-applications"></a><span data-ttu-id="abf5e-108">其他應用程式</span><span class="sxs-lookup"><span data-stu-id="abf5e-108">Other applications</span></span>

<span data-ttu-id="abf5e-109">EF Core 記錄需要 ILoggerFactory，它本身是以一或多個記錄提供者進行設定。</span><span class="sxs-lookup"><span data-stu-id="abf5e-109">EF Core logging requires an ILoggerFactory which is itself configured with one or more logging providers.</span></span> <span data-ttu-id="abf5e-110">一般提供者隨附于下列套件：</span><span class="sxs-lookup"><span data-stu-id="abf5e-110">Common providers are shipped in the following packages:</span></span>

* <span data-ttu-id="abf5e-111">[登入][主控台](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)：簡單的主控台記錄器。</span><span class="sxs-lookup"><span data-stu-id="abf5e-111">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): A simple console logger.</span></span>
* <span data-ttu-id="abf5e-112">[Microsoft.extensions.logging.azureappservices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/)：支援 Azure App 服務的「診斷記錄」和「記錄資料流程」功能。</span><span class="sxs-lookup"><span data-stu-id="abf5e-112">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): Supports Azure App Services 'Diagnostics logs' and 'Log stream' features.</span></span>
* <span data-ttu-id="abf5e-113">在 [[記錄](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/)檔] 中，使用 [ (] 將記錄到偵錯工具監視器。</span><span class="sxs-lookup"><span data-stu-id="abf5e-113">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): Logs to a debugger monitor using System.Diagnostics.Debug.WriteLine().</span></span>
* <span data-ttu-id="abf5e-114">。 [EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/)：記錄至 Windows 事件記錄檔。</span><span class="sxs-lookup"><span data-stu-id="abf5e-114">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): Logs to Windows Event Log.</span></span>
* <span data-ttu-id="abf5e-115">EventListener [：支援 eventsource/](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/)。</span><span class="sxs-lookup"><span data-stu-id="abf5e-115">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): Supports EventSource/EventListener.</span></span>
* <span data-ttu-id="abf5e-116">[TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/)：使用記錄至追蹤接聽程式 `System.Diagnostics.TraceSource.TraceEvent()` 。</span><span class="sxs-lookup"><span data-stu-id="abf5e-116">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): Logs to a trace listener using `System.Diagnostics.TraceSource.TraceEvent()`.</span></span>

<span data-ttu-id="abf5e-117"> () 安裝適當的封裝之後，應用程式應建立 Server.loggerfactory 的 singleton/global 實例。</span><span class="sxs-lookup"><span data-stu-id="abf5e-117">After installing the appropriate package(s), the application should create a singleton/global instance of a LoggerFactory.</span></span> <span data-ttu-id="abf5e-118">例如，使用主控台記錄器：</span><span class="sxs-lookup"><span data-stu-id="abf5e-118">For example, using the console logger:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="abf5e-119">3.x 版</span><span class="sxs-lookup"><span data-stu-id="abf5e-119">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="abf5e-120">2.x 版</span><span class="sxs-lookup"><span data-stu-id="abf5e-120">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="abf5e-121">下列程式碼範例會使用 `ConsoleLoggerProvider` 在2.2 版中已過時的函式，並在3.0 中取代。</span><span class="sxs-lookup"><span data-stu-id="abf5e-121">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="abf5e-122">使用2.2 時，忽略和隱藏警告是安全的。</span><span class="sxs-lookup"><span data-stu-id="abf5e-122">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

``` csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

<span data-ttu-id="abf5e-123">然後，這個 singleton/global 實例應該會向 EF Core 註冊 `DbContextOptionsBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="abf5e-123">This singleton/global instance should then be registered with EF Core on the `DbContextOptionsBuilder`.</span></span> <span data-ttu-id="abf5e-124">例如：</span><span class="sxs-lookup"><span data-stu-id="abf5e-124">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> <span data-ttu-id="abf5e-125">應用程式不會為每個內容實例建立新的 ILoggerFactory 實例，這是很重要的。</span><span class="sxs-lookup"><span data-stu-id="abf5e-125">It is very important that applications do not create a new ILoggerFactory instance for each context instance.</span></span> <span data-ttu-id="abf5e-126">這樣做會導致記憶體流失和效能不佳。</span><span class="sxs-lookup"><span data-stu-id="abf5e-126">Doing so will result in a memory leak and poor performance.</span></span>

## <a name="filtering-what-is-logged"></a><span data-ttu-id="abf5e-127">篩選記錄的內容</span><span class="sxs-lookup"><span data-stu-id="abf5e-127">Filtering what is logged</span></span>

<span data-ttu-id="abf5e-128">應用程式可以藉由設定 ILoggerProvider 上的篩選器來控制記錄的內容。</span><span class="sxs-lookup"><span data-stu-id="abf5e-128">The application can control what is logged by configuring a filter on the ILoggerProvider.</span></span> <span data-ttu-id="abf5e-129">例如：</span><span class="sxs-lookup"><span data-stu-id="abf5e-129">For example:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="abf5e-130">3.x 版</span><span class="sxs-lookup"><span data-stu-id="abf5e-130">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="abf5e-131">2.x 版</span><span class="sxs-lookup"><span data-stu-id="abf5e-131">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="abf5e-132">下列程式碼範例會使用 `ConsoleLoggerProvider` 在2.2 版中已過時的函式，並在3.0 中取代。</span><span class="sxs-lookup"><span data-stu-id="abf5e-132">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="abf5e-133">使用2.2 時，忽略和隱藏警告是安全的。</span><span class="sxs-lookup"><span data-stu-id="abf5e-133">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

``` csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[]
    {
        new ConsoleLoggerProvider((category, level)
            => category == DbLoggerCategory.Database.Command.Name
               && level == LogLevel.Information, true)
    });
```

***

<span data-ttu-id="abf5e-134">在此範例中，會將記錄篩選成隻傳回訊息：</span><span class="sxs-lookup"><span data-stu-id="abf5e-134">In this example, the log is filtered to only return messages:</span></span>

* <span data-ttu-id="abf5e-135">在 ' Microsoft.entityframeworkcore ' 類別中</span><span class="sxs-lookup"><span data-stu-id="abf5e-135">in the 'Microsoft.EntityFrameworkCore.Database.Command' category</span></span>
* <span data-ttu-id="abf5e-136">在「資訊」層級</span><span class="sxs-lookup"><span data-stu-id="abf5e-136">at the 'Information' level</span></span>

<span data-ttu-id="abf5e-137">針對 EF Core，系統會在類別中定義記錄器分類 `DbLoggerCategory` ，以便輕鬆地尋找類別目錄，但這些分類會解析成簡單字串。</span><span class="sxs-lookup"><span data-stu-id="abf5e-137">For EF Core, logger categories are defined in the `DbLoggerCategory` class to make it easy to find categories, but these resolve to simple strings.</span></span>

<span data-ttu-id="abf5e-138">您可以在 [ASP.NET Core 記錄檔](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)中找到基礎記錄基礎結構的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="abf5e-138">More details on the underlying logging infrastructure can be found in the [ASP.NET Core logging documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>
