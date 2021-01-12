---
title: 使用診斷接聽程式-EF Core
description: 使用 DiagnosticListener 進行 EF Core 診斷的全球使用量
author: ajcvickers
ms.date: 10/16/2020
uid: core/logging-events-diagnostics/diagnostic-listeners
ms.openlocfilehash: afb80aa8f05f70761e423f58653f681938079858
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129261"
---
# <a name="using-diagnostic-listeners-in-ef-core"></a><span data-ttu-id="1ad7a-103">使用 EF Core 中的診斷接聽程式</span><span class="sxs-lookup"><span data-stu-id="1ad7a-103">Using Diagnostic Listeners in EF Core</span></span>

> [!TIP]
> <span data-ttu-id="1ad7a-104">您可以從 GitHub [下載本文的範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/DiagnosticListeners) 。</span><span class="sxs-lookup"><span data-stu-id="1ad7a-104">You can [download this article's sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/DiagnosticListeners) from GitHub.</span></span>

<span data-ttu-id="1ad7a-105">診斷接聽項允許接聽目前 .NET 進程中發生的任何 EF Core 事件。</span><span class="sxs-lookup"><span data-stu-id="1ad7a-105">Diagnostic listeners allow listening for any EF Core event that occurs in the current .NET process.</span></span> <span data-ttu-id="1ad7a-106"><xref:System.Diagnostics.DiagnosticListener>類別是[跨 .net 的通用機制](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)的一部分，可從執行中的應用程式取得診斷資訊。</span><span class="sxs-lookup"><span data-stu-id="1ad7a-106">The <xref:System.Diagnostics.DiagnosticListener> class is a part of a [common mechanism across .NET](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) for obtaining diagnostic information from running applications.</span></span>

<span data-ttu-id="1ad7a-107">診斷接聽程式不適合從單一 DbCoNtext 實例取得事件。</span><span class="sxs-lookup"><span data-stu-id="1ad7a-107">Diagnostic listeners are not suitable for getting events from a single DbContext instance.</span></span> <span data-ttu-id="1ad7a-108">EF Core [攔截](xref:core/logging-events-diagnostics/interceptors) 器提供對每個內容註冊之相同事件的存取權。</span><span class="sxs-lookup"><span data-stu-id="1ad7a-108">EF Core [interceptors](xref:core/logging-events-diagnostics/interceptors) provide access to the same events with per-context registration.</span></span>

<span data-ttu-id="1ad7a-109">診斷接聽程式並非設計來進行記錄。</span><span class="sxs-lookup"><span data-stu-id="1ad7a-109">Diagnostic listeners are not designed for logging.</span></span> <span data-ttu-id="1ad7a-110">請考慮使用 [簡單的記錄](xref:core/logging-events-diagnostics/simple-logging) 或 [Microsoft 副檔名](xref:core/logging-events-diagnostics/extensions-logging) 記錄。</span><span class="sxs-lookup"><span data-stu-id="1ad7a-110">Consider using [simple logging](xref:core/logging-events-diagnostics/simple-logging) or [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) for logging.</span></span>

## <a name="example-observing-diagnostic-events"></a><span data-ttu-id="1ad7a-111">範例：觀察診斷事件</span><span class="sxs-lookup"><span data-stu-id="1ad7a-111">Example: Observing diagnostic events</span></span>

<span data-ttu-id="1ad7a-112">解決 EF Core 事件是兩個步驟的程式。</span><span class="sxs-lookup"><span data-stu-id="1ad7a-112">Resolving EF Core events is a two-step process.</span></span> <span data-ttu-id="1ad7a-113">首先，必須建立本身的 [觀察](/dotnet/standard/events/observer-design-pattern) 者 `DiagnosticListener` ：</span><span class="sxs-lookup"><span data-stu-id="1ad7a-113">First, an [observer](/dotnet/standard/events/observer-design-pattern) for `DiagnosticListener` itself must be created:</span></span>

<!--
public class DiagnosticObserver : IObserver<DiagnosticListener>
{
    public void OnCompleted()
        => throw new NotImplementedException();

    public void OnError(Exception error)
        => throw new NotImplementedException();

    public void OnNext(DiagnosticListener value)
    {
        if (value.Name == DbLoggerCategory.Name) // "Microsoft.EntityFrameworkCore"
        {
            value.Subscribe(new KeyValueObserver());
        }
    }
}
-->
[!code-csharp[DiagnosticObserver](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=DiagnosticObserver)]

<span data-ttu-id="1ad7a-114">`OnNext`方法會尋找來自 EF Core 的 DiagnosticListener。</span><span class="sxs-lookup"><span data-stu-id="1ad7a-114">The `OnNext` method looks for the DiagnosticListener that comes from EF Core.</span></span> <span data-ttu-id="1ad7a-115">此接聽程式的名稱為 "Microsoft.entityframeworkcore"，您可以 <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> 如下所示從類別取得此名稱。</span><span class="sxs-lookup"><span data-stu-id="1ad7a-115">This listener has the name "Microsoft.EntityFrameworkCore", which can be obtained from the <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> class as shown.</span></span>

<span data-ttu-id="1ad7a-116">然後，此觀察者必須在全域註冊，例如在應用程式的 `Main` 方法中：</span><span class="sxs-lookup"><span data-stu-id="1ad7a-116">This observer must then be registered globally, for example in the application's `Main` method:</span></span>

<!--
        DiagnosticListener.AllListeners.Subscribe(new DiagnosticObserver());
-->
[!code-csharp[RegisterDiagnosticListener](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=RegisterDiagnosticListener)]

<span data-ttu-id="1ad7a-117">其次，一旦找到 EF Core DiagnosticListener，就會建立新的索引鍵-值觀察者來訂閱實際的 EF Core 事件。</span><span class="sxs-lookup"><span data-stu-id="1ad7a-117">Second, once the EF Core DiagnosticListener is found, a new key-value observer is created to subscribe to the actual EF Core events.</span></span> <span data-ttu-id="1ad7a-118">例如：</span><span class="sxs-lookup"><span data-stu-id="1ad7a-118">For example:</span></span>

<!--
public class KeyValueObserver : IObserver<KeyValuePair<string, object>>
{
    public void OnCompleted()
        => throw new NotImplementedException();

    public void OnError(Exception error)
        => throw new NotImplementedException();

    public void OnNext(KeyValuePair<string, object> value)
    {
        if (value.Key == CoreEventId.ContextInitialized.Name)
        {
            var payload = (ContextInitializedEventData)value.Value;
            Console.WriteLine($"EF is initializing {payload.Context.GetType().Name} ");
        }

        if (value.Key == RelationalEventId.ConnectionOpening.Name)
        {
            var payload = (ConnectionEventData)value.Value;
            Console.WriteLine($"EF is opening a connection to {payload.Connection.ConnectionString} ");
        }
    }
}
-->
[!code-csharp[KeyValueObserver](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=KeyValueObserver)]

<span data-ttu-id="1ad7a-119">`OnNext`方法會使用每個 EF Core 事件的索引鍵/值組來呼叫。</span><span class="sxs-lookup"><span data-stu-id="1ad7a-119">The `OnNext` method is this time called with a key/value pair for each EF Core event.</span></span> <span data-ttu-id="1ad7a-120">金鑰是事件的名稱，可從下列其中一個取得：</span><span class="sxs-lookup"><span data-stu-id="1ad7a-120">The key is the name of the event, which can be obtained from one of:</span></span>

* <span data-ttu-id="1ad7a-121"><xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> 適用于所有 EF Core 資料庫提供者的常見事件</span><span class="sxs-lookup"><span data-stu-id="1ad7a-121"><xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> for events common to all EF Core database providers</span></span>
* <span data-ttu-id="1ad7a-122"><xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> 適用于所有關系資料庫提供者的常見事件</span><span class="sxs-lookup"><span data-stu-id="1ad7a-122"><xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> for events common to all relational database providers</span></span>
* <span data-ttu-id="1ad7a-123">與目前資料庫提供者相關之事件的類似類別。</span><span class="sxs-lookup"><span data-stu-id="1ad7a-123">A similar class for events specific to the current database provider.</span></span> <span data-ttu-id="1ad7a-124">例如， <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> SQL Server 提供者。</span><span class="sxs-lookup"><span data-stu-id="1ad7a-124">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> for the SQL Server provider.</span></span>

<span data-ttu-id="1ad7a-125">索引鍵/值組的值是事件特定的裝載類型。</span><span class="sxs-lookup"><span data-stu-id="1ad7a-125">The value of the key/value pair is a payload type specific to the event.</span></span> <span data-ttu-id="1ad7a-126">預期的承載類型會記載于這些事件類別中定義的每個事件。</span><span class="sxs-lookup"><span data-stu-id="1ad7a-126">The type of payload to expect is documented on each event defined in these event classes.</span></span>

<span data-ttu-id="1ad7a-127">例如，上述程式碼會處理 <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ContextInitialized> 和 <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId.ConnectionOpening> 事件。</span><span class="sxs-lookup"><span data-stu-id="1ad7a-127">For example, the code above handles the <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ContextInitialized> and the <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId.ConnectionOpening> events.</span></span> <span data-ttu-id="1ad7a-128">針對其中的第一個，裝載是 <xref:Microsoft.EntityFrameworkCore.Diagnostics.ContextInitializedEventData> 。</span><span class="sxs-lookup"><span data-stu-id="1ad7a-128">For the first of these, the payload is <xref:Microsoft.EntityFrameworkCore.Diagnostics.ContextInitializedEventData>.</span></span> <span data-ttu-id="1ad7a-129">第二種是 <xref:Microsoft.EntityFrameworkCore.Diagnostics.ConnectionEventData> 。</span><span class="sxs-lookup"><span data-stu-id="1ad7a-129">For the second, it is <xref:Microsoft.EntityFrameworkCore.Diagnostics.ConnectionEventData>.</span></span>

> [!TIP]
> <span data-ttu-id="1ad7a-130">ToString 會在每個 EF Core 事件資料類別中覆寫，以產生事件的對等記錄訊息。</span><span class="sxs-lookup"><span data-stu-id="1ad7a-130">ToString is overridden in every EF Core event data class to generate the equivalent log message for the event.</span></span> <span data-ttu-id="1ad7a-131">例如，呼叫會 `ContextInitializedEventData.ToString` 使用提供者 ' microsoft.entityframeworkcore. Sqlite ' （具有選項： None）來產生 "Entity Framework Core 5.0.0 初始化 ' BlogsCoNtext '。</span><span class="sxs-lookup"><span data-stu-id="1ad7a-131">For example, calling `ContextInitializedEventData.ToString` generates "Entity Framework Core 5.0.0 initialized 'BlogsContext' using provider 'Microsoft.EntityFrameworkCore.Sqlite' with options: None".</span></span>

<span data-ttu-id="1ad7a-132">此 [範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/DiagnosticListeners) 包含一個簡單的主控台應用程式，此應用程式會變更「日誌」資料庫，並印出所遇到的診斷事件。</span><span class="sxs-lookup"><span data-stu-id="1ad7a-132">The [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/DiagnosticListeners) contains a simple console application that makes changes to the blogging database and prints out the diagnostic events encountered.</span></span>

<!--
    public static void Main()
    {
        #region RegisterDiagnosticListener
        DiagnosticListener.AllListeners.Subscribe(new DiagnosticObserver());
        #endregion

        using (var context = new BlogsContext())
        {
            context.Database.EnsureDeleted();
            context.Database.EnsureCreated();

            context.Add(
                new Blog
                {
                    Name = "EF Blog",
                    Posts =
                    {
                        new Post { Title = "EF Core 3.1!" },
                        new Post { Title = "EF Core 5.0!" }
                    }
                });

            context.SaveChanges();
        }

        using (var context = new BlogsContext())
        {
            var blog = context.Blogs.Include(e => e.Posts).Single();

            blog.Name = "EF Core Blog";
            context.Remove(blog.Posts.First());
            blog.Posts.Add(new Post { Title = "EF Core 6.0!" });

            context.SaveChanges();
        }
        #endregion
    }
-->
[!code-csharp[Program](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=Program)]

<span data-ttu-id="1ad7a-133">這段程式碼的輸出會顯示偵測到的事件：</span><span class="sxs-lookup"><span data-stu-id="1ad7a-133">The output from this code shows the events detected:</span></span>

```output
EF is initializing BlogsContext
EF is opening a connection to Data Source=blogs.db;Mode=ReadOnly
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to Data Source=blogs.db;Mode=ReadOnly
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to DataSource=blogs.db
EF is initializing BlogsContext
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to DataSource=blogs.db
```
