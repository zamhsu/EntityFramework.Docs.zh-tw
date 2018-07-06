---
title: 記錄-EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: f6e35c6d-45b7-4258-be1d-87c1bb67438d
ms.technology: entity-framework-core
uid: core/miscellaneous/logging
ms.openlocfilehash: 60d76bf3360eb47cdd9836494c1f135d1005a215
ms.sourcegitcommit: 3adf1267be92effc3c9daa893906a7f36834204f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35232132"
---
# <a name="logging"></a><span data-ttu-id="cff27-102">記錄</span><span class="sxs-lookup"><span data-stu-id="cff27-102">Logging</span></span>

> [!TIP]  
> <span data-ttu-id="cff27-103">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="cff27-103">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) on GitHub.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="cff27-104">ASP.NET Core 應用程式</span><span class="sxs-lookup"><span data-stu-id="cff27-104">ASP.NET Core applications</span></span>

<span data-ttu-id="cff27-105">EF Core 自動記錄與機制整合的 ASP.NET Core 每當`AddDbContext`或`AddDbContextPool`用。</span><span class="sxs-lookup"><span data-stu-id="cff27-105">EF Core integrates automatically with the logging mechanisms of ASP.NET Core whenever `AddDbContext` or `AddDbContextPool` is used.</span></span> <span data-ttu-id="cff27-106">因此，當使用 ASP.NET Core 時，應該設定記錄中所述[ASP.NET Core 文件](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)。</span><span class="sxs-lookup"><span data-stu-id="cff27-106">Therefore, when using ASP.NET Core, logging should be configured as described in the [ASP.NET Core documentation](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>

## <a name="other-applications"></a><span data-ttu-id="cff27-107">其他應用程式</span><span class="sxs-lookup"><span data-stu-id="cff27-107">Other applications</span></span>

<span data-ttu-id="cff27-108">記錄目前的 EF Core 需要其本身設定一或多個 ILoggerProvider ILoggerFactory。</span><span class="sxs-lookup"><span data-stu-id="cff27-108">EF Core logging currently requires an ILoggerFactory which is itself configured with one or more ILoggerProvider.</span></span> <span data-ttu-id="cff27-109">常見的提供者都隨附在下列封裝：</span><span class="sxs-lookup"><span data-stu-id="cff27-109">Common providers are shipped in the following packages:</span></span>

* <span data-ttu-id="cff27-110">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)： 簡單的主控台記錄器。</span><span class="sxs-lookup"><span data-stu-id="cff27-110">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): A simple console logger.</span></span>
* <span data-ttu-id="cff27-111">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/)： 支援 Azure 應用程式服務 '診斷記錄檔' 和 '記錄資料流' 功能。</span><span class="sxs-lookup"><span data-stu-id="cff27-111">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): Supports Azure App Services 'Diagnostics logs' and 'Log stream' features.</span></span>
* <span data-ttu-id="cff27-112">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/)： 記錄檔，以使用 System.Diagnostics.Debug.WriteLine() 偵錯工具監視。</span><span class="sxs-lookup"><span data-stu-id="cff27-112">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): Logs to a debugger monitor using System.Diagnostics.Debug.WriteLine().</span></span>
* <span data-ttu-id="cff27-113">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/)： 記錄至 Windows 事件記錄檔。</span><span class="sxs-lookup"><span data-stu-id="cff27-113">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): Logs to Windows Event Log.</span></span>
* <span data-ttu-id="cff27-114">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/)： 支援 EventSource/EventListener。</span><span class="sxs-lookup"><span data-stu-id="cff27-114">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): Supports EventSource/EventListener.</span></span>
* <span data-ttu-id="cff27-115">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/)： 記錄檔，以使用 System.Diagnostics.TraceSource.TraceEvent() 追蹤接聽程式。</span><span class="sxs-lookup"><span data-stu-id="cff27-115">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): Logs to a trace listener using System.Diagnostics.TraceSource.TraceEvent().</span></span>

<span data-ttu-id="cff27-116">安裝之後適當的封裝，應用程式應該建立 LoggerFactory 單一/全域執行個體。</span><span class="sxs-lookup"><span data-stu-id="cff27-116">After installing the appropriate package(s), the application should create a singleton/global instance of a LoggerFactory.</span></span> <span data-ttu-id="cff27-117">例如，使用主控台記錄器：</span><span class="sxs-lookup"><span data-stu-id="cff27-117">For example, using the console logger:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

<span data-ttu-id="cff27-118">這個單一/全域執行個體應該然後再註冊 EF Core 上`DbContextOptionsBuilder`。</span><span class="sxs-lookup"><span data-stu-id="cff27-118">This singleton/global instance should then be registered with EF Core on the `DbContextOptionsBuilder`.</span></span> <span data-ttu-id="cff27-119">例如: </span><span class="sxs-lookup"><span data-stu-id="cff27-119">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> <span data-ttu-id="cff27-120">它是非常重要的是，應用程式不會建立新的 ILoggerFactory 執行個體，每個內容執行個體。</span><span class="sxs-lookup"><span data-stu-id="cff27-120">It is very important that applications do not create a new ILoggerFactory instance for each context instance.</span></span> <span data-ttu-id="cff27-121">這樣會導致記憶體流失和效能不佳。</span><span class="sxs-lookup"><span data-stu-id="cff27-121">Doing so will result in a memory leak and poor performance.</span></span>

## <a name="filtering-what-is-logged"></a><span data-ttu-id="cff27-122">篩選記錄的內容</span><span class="sxs-lookup"><span data-stu-id="cff27-122">Filtering what is logged</span></span>

<span data-ttu-id="cff27-123">註冊 ILoggerProvider 時將它設定為最簡單的方式來篩選記錄的內容。</span><span class="sxs-lookup"><span data-stu-id="cff27-123">The easiest way to filter what is logged is to configure it when registering the ILoggerProvider.</span></span> <span data-ttu-id="cff27-124">例如: </span><span class="sxs-lookup"><span data-stu-id="cff27-124">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

<span data-ttu-id="cff27-125">在此範例中，記錄檔時篩選為傳回只有訊息：</span><span class="sxs-lookup"><span data-stu-id="cff27-125">In this example, the log is filtered to return only messages:</span></span>
 * <span data-ttu-id="cff27-126">在 'Microsoft.EntityFrameworkCore.Database.Command' 類別</span><span class="sxs-lookup"><span data-stu-id="cff27-126">in the 'Microsoft.EntityFrameworkCore.Database.Command' category</span></span>
 * <span data-ttu-id="cff27-127">在 「 資訊 」 層級</span><span class="sxs-lookup"><span data-stu-id="cff27-127">at the 'Information' level</span></span>

<span data-ttu-id="cff27-128">記錄器類別目錄中的定義 EF Core`DbLoggerCategory`類別，以讓您輕鬆尋找類別目錄，但這些解析成簡單字串。</span><span class="sxs-lookup"><span data-stu-id="cff27-128">For EF Core, logger categories are defined in the `DbLoggerCategory` class to make it easy to find categories, but these resolve to simple strings.</span></span>

<span data-ttu-id="cff27-129">位於基礎的記錄基礎結構的詳細[ASP.NET Core 記錄文件](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)。</span><span class="sxs-lookup"><span data-stu-id="cff27-129">More details on the underlying logging infrastructure can be found in the [ASP.NET Core logging documentation](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>
