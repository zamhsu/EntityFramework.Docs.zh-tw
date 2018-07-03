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
# <a name="logging"></a>記錄

> [!TIP]  
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) \(英文\)。

## <a name="aspnet-core-applications"></a>ASP.NET Core 應用程式

EF Core 自動記錄與機制整合的 ASP.NET Core 每當`AddDbContext`或`AddDbContextPool`用。 因此，當使用 ASP.NET Core 時，應該設定記錄中所述[ASP.NET Core 文件](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)。

## <a name="other-applications"></a>其他應用程式

記錄目前的 EF Core 需要其本身設定一或多個 ILoggerProvider ILoggerFactory。 常見的提供者都隨附在下列封裝：

* [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)： 簡單的主控台記錄器。
* [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/)： 支援 Azure 應用程式服務 '診斷記錄檔' 和 '記錄資料流' 功能。
* [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/)： 記錄檔，以使用 System.Diagnostics.Debug.WriteLine() 偵錯工具監視。
* [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/)： 記錄至 Windows 事件記錄檔。
* [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/)： 支援 EventSource/EventListener。
* [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/)： 記錄檔，以使用 System.Diagnostics.TraceSource.TraceEvent() 追蹤接聽程式。

安裝之後適當的封裝，應用程式應該建立 LoggerFactory 單一/全域執行個體。 例如，使用主控台記錄器：

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

這個單一/全域執行個體應該然後再註冊 EF Core 上`DbContextOptionsBuilder`。 例如: 

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> 它是非常重要的是，應用程式不會建立新的 ILoggerFactory 執行個體，每個內容執行個體。 這樣會導致記憶體流失和效能不佳。

## <a name="filtering-what-is-logged"></a>篩選記錄的內容

註冊 ILoggerProvider 時將它設定為最簡單的方式來篩選記錄的內容。 例如: 

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

在此範例中，記錄檔時篩選為傳回只有訊息：
 * 在 'Microsoft.EntityFrameworkCore.Database.Command' 類別
 * 在 「 資訊 」 層級

記錄器類別目錄中的定義 EF Core`DbLoggerCategory`類別，以讓您輕鬆尋找類別目錄，但這些解析成簡單字串。

位於基礎的記錄基礎結構的詳細[ASP.NET Core 記錄文件](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)。
