---
title: 記錄-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f6e35c6d-45b7-4258-be1d-87c1bb67438d
uid: core/miscellaneous/logging
ms.openlocfilehash: e8adc39ec01ff75112b03446a488df6199cc7041
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416579"
---
# <a name="logging"></a>記錄

> [!TIP]  
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) \(英文\)。

## <a name="aspnet-core-applications"></a>ASP.NET Core 應用程式

EF Core 在使用 `AddDbContext` 或 `AddDbContextPool` 時，會自動與 ASP.NET Core 的記錄機制整合。 因此，使用 ASP.NET Core 時，應該依照[ASP.NET Core 檔](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)中所述設定記錄。

## <a name="other-applications"></a>其他應用程式

EF Core 記錄需要 ILoggerFactory，其本身是以一或多個記錄提供者來設定。 一般提供者隨附于下列套件：

* [ [Microsoft Extensions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)]：簡單的主控台記錄器。
* [Microsoft.extensions.logging.azureappservices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/)：支援 Azure App 服務的「診斷記錄」和「記錄資料流程」功能。
* [[記錄](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/)檔]：使用 system.servicemodel （）記錄到偵錯工具監視器。
* [記錄檔]。 [EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/)：記錄到 Windows 事件記錄檔。
* 。 [Eventsource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/)：支援 Eventsource/EventListener。
* [TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/)：使用 `System.Diagnostics.TraceSource.TraceEvent()`記錄至追蹤接聽程式。

安裝適當的封裝之後，應用程式應該建立 Server.loggerfactory 的單一或全域實例。 例如，使用主控台記錄器：

### <a name="version-3x"></a>[3.x 版](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[2.x 版](#tab/v2)

> [!NOTE]
> 下列程式碼範例會使用在2.2 版中已過時且已在3.0 中取代的 `ConsoleLoggerProvider` 函式。 使用2.2 時，忽略和隱藏警告是安全的。

``` csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

接著，這個 singleton/global 實例就應該向 `DbContextOptionsBuilder`上的 EF Core 註冊。 例如：

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> 應用程式不會為每個內容實例建立新的 ILoggerFactory 實例，這點非常重要。 這麼做會導致記憶體流失和效能不佳。

## <a name="filtering-what-is-logged"></a>篩選記錄的內容

應用程式可以藉由在 ILoggerProvider 上設定篩選準則，來控制所記錄的內容。 例如：

### <a name="version-3x"></a>[3.x 版](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[2.x 版](#tab/v2)

> [!NOTE]
> 下列程式碼範例會使用在2.2 版中已過時且已在3.0 中取代的 `ConsoleLoggerProvider` 函式。 使用2.2 時，忽略和隱藏警告是安全的。

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

在此範例中，記錄檔已篩選為只傳回訊息：

* 在 [Microsoft.entityframeworkcore] 類別中
* 在「資訊」層級

針對 EF Core，記錄器分類會在 `DbLoggerCategory` 類別中定義，讓您輕鬆地尋找類別目錄，但這些會解析成簡單的字串。

如需基礎記錄基礎結構的詳細資訊，請參閱[ASP.NET Core 記錄檔](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)。
