---
title: 使用診斷接聽程式-EF Core
description: 使用 DiagnosticListener 進行 EF Core 診斷的全球耗用量
author: ajcvickers
ms.date: 10/16/2020
uid: core/logging-events-diagnostics/diagnostic-listeners
ms.openlocfilehash: 7035acaa6d306e73a0a2c071532ece0d8e9a0a1d
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024247"
---
# <a name="using-diagnostic-listeners-in-ef-core"></a>使用 EF Core 中的診斷接聽程式

> [!TIP]
> 您可以從 GitHub [下載本文的範例](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/DiagnosticListeners) 。

診斷接聽項允許接聽目前 .NET 進程中發生的任何 EF Core 事件。 <xref:System.Diagnostics.DiagnosticListener>類別是[跨 .net 的通用機制](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)的一部分，可從執行中的應用程式取得診斷資訊。

診斷接聽程式不適合從單一 DbCoNtext 實例取得事件。 EF Core [攔截](xref:core/logging-events-diagnostics/interceptors) 器提供對每個內容註冊之相同事件的存取權。

診斷接聽程式並非設計來進行記錄。 請考慮使用 [簡單的記錄](xref:core/logging-events-diagnostics/simple-logging) 或 [Microsoft 副檔名](xref:core/logging-events-diagnostics/extensions-logging) 記錄。

## <a name="example-observing-diagnostic-events"></a>範例：觀察診斷事件

解決 EF Core 事件是兩個步驟的程式。 首先，必須建立本身的 [觀察](/dotnet/standard/events/observer-design-pattern) 者 `DiagnosticListener` ：

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

`OnNext`方法會尋找來自 EF Core 的 DiagnosticListener。 此接聽程式的名稱為 "Microsoft.entityframeworkcore"，您可以 <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> 如下所示從類別取得此名稱。

然後，此觀察者必須在全域註冊，例如在應用程式的 `Main` 方法中：

<!--
        DiagnosticListener.AllListeners.Subscribe(new DiagnosticObserver());
-->
[!code-csharp[RegisterDiagnosticListener](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=RegisterDiagnosticListener)]

其次，一旦找到 EF Core DiagnosticListener 之後，就會建立新的索引鍵-值觀察者來訂閱實際的 EF Core 事件。 例如：

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

`OnNext`此時間會使用每個 EF Core 事件的索引鍵/值組來呼叫。 金鑰是事件的名稱，可從下列其中一個取得：

* <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> 適用于所有 EF Core 資料庫提供者的常見事件
* <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> 適用于所有關系資料庫提供者的常見事件
* 與目前資料庫提供者相關之事件的類似類別。 例如， <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> SQL Server 提供者。

索引鍵/值組的值是事件特定的裝載類型。 預期的承載類型會記載于這些事件類別中定義的每個事件。

例如，上述程式碼會處理 <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ContextInitialized> 和 <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId.ConnectionOpening> 事件。 針對其中的第一個，裝載是 <xref:Microsoft.EntityFrameworkCore.Diagnostics.ContextInitializedEventData> 。 第二種是 <xref:Microsoft.EntityFrameworkCore.Diagnostics.ConnectionEventData> 。

> [!TIP]
> ToString 會在每個 EF Core 事件資料類別中覆寫，以產生事件的對等記錄訊息。 例如，呼叫會 `ContextInitializedEventData.ToString` 使用提供者 ' microsoft.entityframeworkcore. Sqlite ' （具有選項： None）來產生「Entity Framework Core 5.0.0 初始化的 ' BlogsCoNtext '」。

此 [範例](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/DiagnosticListeners) 包含一個簡單的主控台應用程式，此應用程式會變更「日誌」資料庫，並印出所遇到的診斷事件。

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

這段程式碼的輸出會顯示偵測到的事件：

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
