---
title: 記錄-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f6e35c6d-45b7-4258-be1d-87c1bb67438d
uid: core/miscellaneous/logging
ms.openlocfilehash: 0a996403afdbe076b1690c98eeb305b40c4d1f4a
ms.sourcegitcommit: 109a16478de498b65717a6e09be243647e217fb3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/10/2019
ms.locfileid: "55985570"
---
# <a name="logging"></a><span data-ttu-id="84190-102">記錄</span><span class="sxs-lookup"><span data-stu-id="84190-102">Logging</span></span>

> [!TIP]  
> <span data-ttu-id="84190-103">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="84190-103">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) on GitHub.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="84190-104">ASP.NET Core 應用程式</span><span class="sxs-lookup"><span data-stu-id="84190-104">ASP.NET Core applications</span></span>

<span data-ttu-id="84190-105">EF Core 自動記錄與機制整合的 ASP.NET Core 每當`AddDbContext`或`AddDbContextPool`用。</span><span class="sxs-lookup"><span data-stu-id="84190-105">EF Core integrates automatically with the logging mechanisms of ASP.NET Core whenever `AddDbContext` or `AddDbContextPool` is used.</span></span> <span data-ttu-id="84190-106">因此，當使用 ASP.NET Core 時，應該設定記錄中所述[ASP.NET Core 文件](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)。</span><span class="sxs-lookup"><span data-stu-id="84190-106">Therefore, when using ASP.NET Core, logging should be configured as described in the [ASP.NET Core documentation](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>

## <a name="other-applications"></a><span data-ttu-id="84190-107">其他應用程式</span><span class="sxs-lookup"><span data-stu-id="84190-107">Other applications</span></span>

<span data-ttu-id="84190-108">記錄目前的 EF Core 需要其本身設定一或多個 ILoggerProvider ILoggerFactory。</span><span class="sxs-lookup"><span data-stu-id="84190-108">EF Core logging currently requires an ILoggerFactory which is itself configured with one or more ILoggerProvider.</span></span> <span data-ttu-id="84190-109">常見的提供者都隨附在下列套件：</span><span class="sxs-lookup"><span data-stu-id="84190-109">Common providers are shipped in the following packages:</span></span>

* <span data-ttu-id="84190-110">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/):簡單的主控台記錄器。</span><span class="sxs-lookup"><span data-stu-id="84190-110">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): A simple console logger.</span></span>
* <span data-ttu-id="84190-111">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/):支援 Azure App Service '診斷 logs' 和 '記錄資料流' 功能。</span><span class="sxs-lookup"><span data-stu-id="84190-111">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): Supports Azure App Services 'Diagnostics logs' and 'Log stream' features.</span></span>
* <span data-ttu-id="84190-112">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/):若要使用 System.Diagnostics.Debug.WriteLine() 偵錯工具監視的記錄檔。</span><span class="sxs-lookup"><span data-stu-id="84190-112">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): Logs to a debugger monitor using System.Diagnostics.Debug.WriteLine().</span></span>
* <span data-ttu-id="84190-113">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/):Windows 事件記錄檔的記錄檔。</span><span class="sxs-lookup"><span data-stu-id="84190-113">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): Logs to Windows Event Log.</span></span>
* <span data-ttu-id="84190-114">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/):支援 EventSource/EventListener。</span><span class="sxs-lookup"><span data-stu-id="84190-114">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): Supports EventSource/EventListener.</span></span>
* <span data-ttu-id="84190-115">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/):若要使用 System.Diagnostics.TraceSource.TraceEvent() 的追蹤接聽程式的記錄檔。</span><span class="sxs-lookup"><span data-stu-id="84190-115">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): Logs to a trace listener using System.Diagnostics.TraceSource.TraceEvent().</span></span>

> [!NOTE]
> <span data-ttu-id="84190-116">下列程式碼範例會使用`ConsoleLoggerProvider`有 2.2 版已而言已經過時的建構函式。</span><span class="sxs-lookup"><span data-stu-id="84190-116">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2.</span></span> <span data-ttu-id="84190-117">適當取代過時的記錄 Api 可在 3.0 版。</span><span class="sxs-lookup"><span data-stu-id="84190-117">Proper replacements for obsolete logging APIs will be available in version 3.0.</span></span> <span data-ttu-id="84190-118">在此同時，它是放心忽略，並隱藏警告。</span><span class="sxs-lookup"><span data-stu-id="84190-118">In the meantime, it is safe to ignore and suppress the warnings.</span></span>

<span data-ttu-id="84190-119">安裝適當的套件之後, 應用程式應該建立 LoggerFactory / 單一全域執行個體。</span><span class="sxs-lookup"><span data-stu-id="84190-119">After installing the appropriate package(s), the application should create a singleton/global instance of a LoggerFactory.</span></span> <span data-ttu-id="84190-120">例如，使用的主控台記錄器：</span><span class="sxs-lookup"><span data-stu-id="84190-120">For example, using the console logger:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

<span data-ttu-id="84190-121">這個單一/全域執行個體應該然後再註冊 EF Core 上`DbContextOptionsBuilder`。</span><span class="sxs-lookup"><span data-stu-id="84190-121">This singleton/global instance should then be registered with EF Core on the `DbContextOptionsBuilder`.</span></span> <span data-ttu-id="84190-122">例如: </span><span class="sxs-lookup"><span data-stu-id="84190-122">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> <span data-ttu-id="84190-123">它是非常重要的是，應用程式不會建立新的 ILoggerFactory 執行個體，每個內容執行個體。</span><span class="sxs-lookup"><span data-stu-id="84190-123">It is very important that applications do not create a new ILoggerFactory instance for each context instance.</span></span> <span data-ttu-id="84190-124">這樣會導致記憶體流失和效能不佳。</span><span class="sxs-lookup"><span data-stu-id="84190-124">Doing so will result in a memory leak and poor performance.</span></span>

## <a name="filtering-what-is-logged"></a><span data-ttu-id="84190-125">篩選記錄的內容</span><span class="sxs-lookup"><span data-stu-id="84190-125">Filtering what is logged</span></span>

> [!NOTE]
> <span data-ttu-id="84190-126">下列程式碼範例會使用`ConsoleLoggerProvider`有 2.2 版已而言已經過時的建構函式。</span><span class="sxs-lookup"><span data-stu-id="84190-126">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2.</span></span> <span data-ttu-id="84190-127">適當取代過時的記錄 Api 可在 3.0 版。</span><span class="sxs-lookup"><span data-stu-id="84190-127">Proper replacements for obsolete logging APIs will be available in version 3.0.</span></span> <span data-ttu-id="84190-128">在此同時，它是放心忽略，並隱藏警告。</span><span class="sxs-lookup"><span data-stu-id="84190-128">In the meantime, it is safe to ignore and suppress the warnings.</span></span>

<span data-ttu-id="84190-129">註冊 ILoggerProvider 時將它設定為最簡單的方式來篩選記錄的內容。</span><span class="sxs-lookup"><span data-stu-id="84190-129">The easiest way to filter what is logged is to configure it when registering the ILoggerProvider.</span></span> <span data-ttu-id="84190-130">例如: </span><span class="sxs-lookup"><span data-stu-id="84190-130">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

<span data-ttu-id="84190-131">在此範例中，記錄檔會篩選傳回只有訊息：</span><span class="sxs-lookup"><span data-stu-id="84190-131">In this example, the log is filtered to return only messages:</span></span>
 * <span data-ttu-id="84190-132">在 'Microsoft.EntityFrameworkCore.Database.Command' 類別</span><span class="sxs-lookup"><span data-stu-id="84190-132">in the 'Microsoft.EntityFrameworkCore.Database.Command' category</span></span>
 * <span data-ttu-id="84190-133">在 「 資訊 」 層級</span><span class="sxs-lookup"><span data-stu-id="84190-133">at the 'Information' level</span></span>

<span data-ttu-id="84190-134">記錄器類別目錄中的定義 EF Core`DbLoggerCategory`類別，以讓您輕鬆尋找類別目錄，但這些解析成簡單字串。</span><span class="sxs-lookup"><span data-stu-id="84190-134">For EF Core, logger categories are defined in the `DbLoggerCategory` class to make it easy to find categories, but these resolve to simple strings.</span></span>

<span data-ttu-id="84190-135">位於基礎的記錄基礎結構的詳細[ASP.NET Core 記錄文件](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)。</span><span class="sxs-lookup"><span data-stu-id="84190-135">More details on the underlying logging infrastructure can be found in the [ASP.NET Core logging documentation](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>
