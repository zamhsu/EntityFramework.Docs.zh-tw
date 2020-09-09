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
# <a name="logging"></a>記錄

> [!TIP]  
> 您可以在 GitHub 上查看這篇文章的 [範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) 。

## <a name="aspnet-core-applications"></a>ASP.NET Core 應用程式

EF Core 在 `AddDbContext` 使用或時，會自動與 ASP.NET Core 的記錄機制整合 `AddDbContextPool` 。 因此，當您使用 ASP.NET Core 時，應該依照 [ASP.NET Core 檔](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)中所述的方式設定記錄。

## <a name="other-applications"></a>其他應用程式

EF Core 記錄需要 ILoggerFactory，它本身是以一或多個記錄提供者進行設定。 一般提供者隨附于下列套件：

* [登入][主控台](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)：簡單的主控台記錄器。
* [Microsoft.extensions.logging.azureappservices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/)：支援 Azure App 服務的「診斷記錄」和「記錄資料流程」功能。
* 在 [[記錄](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/)檔] 中，使用 [ (] 將記錄到偵錯工具監視器。
* 。 [EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/)：記錄至 Windows 事件記錄檔。
* EventListener [：支援 eventsource/](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/)。
* [TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/)：使用記錄至追蹤接聽程式 `System.Diagnostics.TraceSource.TraceEvent()` 。

 () 安裝適當的封裝之後，應用程式應建立 Server.loggerfactory 的 singleton/global 實例。 例如，使用主控台記錄器：

### <a name="version-3x"></a>[3.x 版](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[2.x 版](#tab/v2)

> [!NOTE]
> 下列程式碼範例會使用 `ConsoleLoggerProvider` 在2.2 版中已過時的函式，並在3.0 中取代。 使用2.2 時，忽略和隱藏警告是安全的。

``` csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

然後，這個 singleton/global 實例應該會向 EF Core 註冊 `DbContextOptionsBuilder` 。 例如：

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> 應用程式不會為每個內容實例建立新的 ILoggerFactory 實例，這是很重要的。 這樣做會導致記憶體流失和效能不佳。

## <a name="filtering-what-is-logged"></a>篩選記錄的內容

應用程式可以藉由設定 ILoggerProvider 上的篩選器來控制記錄的內容。 例如：

### <a name="version-3x"></a>[3.x 版](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[2.x 版](#tab/v2)

> [!NOTE]
> 下列程式碼範例會使用 `ConsoleLoggerProvider` 在2.2 版中已過時的函式，並在3.0 中取代。 使用2.2 時，忽略和隱藏警告是安全的。

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

在此範例中，會將記錄篩選成隻傳回訊息：

* 在 ' Microsoft.entityframeworkcore ' 類別中
* 在「資訊」層級

針對 EF Core，系統會在類別中定義記錄器分類 `DbLoggerCategory` ，以便輕鬆地尋找類別目錄，但這些分類會解析成簡單字串。

您可以在 [ASP.NET Core 記錄檔](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)中找到基礎記錄基礎結構的詳細資料。
