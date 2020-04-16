---
title: 非同步查詢與儲存 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d56e6f1d-4bd1-4b50-9558-9a30e04a8ec3
ms.openlocfilehash: a001df726b7cd3342fcca566c1373f452811d07d
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434236"
---
# <a name="async-query-and-save"></a>非同步查詢及儲存
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。

EF6 引入了對異步查詢的支援,並使用 .NET 4.5 中引入[的異步和等待關鍵字](https://msdn.microsoft.com/library/vstudio/hh191443.aspx)進行保存。 雖然並非所有應用程式都可能從非同步中受益,但它可用於在處理長時間運行、網路或 I/O 綁定任務時提高用戶端回應能力和伺服器可擴充性。

## <a name="when-to-really-use-async"></a>何時真正使用非同步

本演練的目的是以一種易於觀察非同步程式執行之間的區別的方式介紹異步概念。 本演練的目的不是說明非同步程式設計帶來好處的任何關鍵方案。

非同步編程主要側重於釋放當前託管線程(線程執行 .NET 代碼),以執行其他工作,同時等待不需要從託管線程進行任何計算時間的操作。 例如,當資料庫引擎正在處理查詢時,.NET代碼將執行任何操作。

在用戶端應用程式(WinForms、WPF等)中,當前線程可用於執行非同步操作時保持 UI 回應。 在伺服器應用程式(ASP.NET等)中,線程可用於處理其他傳入請求 - 這可以減少記憶體使用量和/或提高伺服器的輸送量。

在大多數使用異步的應用程式將沒有明顯的好處,甚至可能是有害的。 在提交特定方案之前,使用測試、分析和常識來測量非同步對特定方案的影響。

以下是一些更多有關非同步的資源:

-   [布蘭登·佈雷在 .NET 4.5 中的異步/等待概述](https://devblogs.microsoft.com/dotnet/async-in-4-5-worth-the-await/)
-   MSDN 函式庫中的[非同步編程](https://msdn.microsoft.com/library/hh191443.aspx)頁面
-   [如何使用 Async 建構 ASP.NET Web 應用程式](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337)(包括伺服器輸送量增加的展示)

## <a name="create-the-model"></a>建立模型

我們將使用[Code First 工作流](~/ef6/modeling/code-first/workflows/new-database.md)創建模型並生成資料庫,但是非同步功能將適用於所有 EF 模型,包括使用 EF 設計器創建的模型。

-   建立主控台應用程式並稱之為**AsyncDemo**
-   新增實體框架 NuGet 套件
    -   在解決方案資源管理員中,右鍵按**下 AsyncDemo**專案
    -   選擇 **"管理 NuGet 包..."...**
    -   在「管理 NuGet」 套件「對話框中,選擇 **」連線**「工具」選項卡並選擇 **「實體框架**」套件
    -   單擊"**安裝"**
-   新增具有以下實現**Model.cs**類別

``` csharp
    using System.Collections.Generic;
    using System.Data.Entity;

    namespace AsyncDemo
    {
        public class BloggingContext : DbContext
        {
            public DbSet<Blog> Blogs { get; set; }
            public DbSet<Post> Posts { get; set; }
        }

        public class Blog
        {
            public int BlogId { get; set; }
            public string Name { get; set; }

            public virtual List<Post> Posts { get; set; }
        }

        public class Post
        {
            public int PostId { get; set; }
            public string Title { get; set; }
            public string Content { get; set; }

            public int BlogId { get; set; }
            public virtual Blog Blog { get; set; }
        }
    }
```

 

## <a name="create-a-synchronous-program"></a>建立同步程式

現在,我們有一個 EF 模型,讓我們編寫一些代碼,用它來執行一些數據訪問。

-   將**Program.cs**的內容取代為以下代碼

``` csharp
    using System;
    using System.Linq;

    namespace AsyncDemo
    {
        class Program
        {
            static void Main(string[] args)
            {
                PerformDatabaseOperations();

                Console.WriteLine("Quote of the day");
                Console.WriteLine(" Don't worry about the world coming to an end today... ");
                Console.WriteLine(" It's already tomorrow in Australia.");

                Console.WriteLine();
                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            public static void PerformDatabaseOperations()
            {
                using (var db = new BloggingContext())
                {
                    // Create a new blog and save it
                    db.Blogs.Add(new Blog
                    {
                        Name = "Test Blog #" + (db.Blogs.Count() + 1)
                    });
                    Console.WriteLine("Calling SaveChanges.");
                    db.SaveChanges();
                    Console.WriteLine("SaveChanges completed.");

                    // Query for all blogs ordered by name
                    Console.WriteLine("Executing query.");
                    var blogs = (from b in db.Blogs
                                orderby b.Name
                                select b).ToList();

                    // Write all blogs out to Console
                    Console.WriteLine("Query completed with following results:");
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(" " + blog.Name);
                    }
                }
            }
        }
    }
```

此程式呼叫**執行資料庫管理**方法,該方法將新**部落格**儲存到資料庫中,然後從資料庫中檢索所有**部落格**並將其列印到**主控台**。 此之後,程式將當天的報價寫入**主控台**。

由於代碼是同步的,因此在運行程式時,我們可以觀察到以下執行流:

1.  **儲存變更**開始將新**部落格**推送到資料庫
2.  **儲存變更**完成
3.  查詢所有**部落格**送出到資料庫
4.  查詢傳回及結果寫入**主控台**
5.  當天的報價寫入**主控台**

![同步輸出](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a>使其非同步

現在,我們已經啟動和運行我們的程序,我們可以開始使用新的非同步和等待關鍵字。 我們對Program.cs進行了以下更改

1.  第 2 行 **:System.Data.Entity**命名空間的 using 語句允許我們存取 EF 非同步擴充方法。
2.  第 4 行:**系統.執行緒的**using 語句,任務命名空間允許我們使用**任務**類型。
3.  第 12 行 & 18:我們將捕獲為任務,用於監視**執行某些資料庫操作**(第 12 行)的進度,然後在完成程式的所有工作(第 18 行)後阻止此任務的程式執行完成。
4.  第 25 行:我們更新了**執行一些資料庫操作**,以標記為**非同步**並傳回**工作**。
5.  第 35 行:我們現在正在調用"保存更改"的 Async 版本,等待它完成。
6.  第 42 行:我們現在正在調用 ToList 的 Async 版本,等待結果。

有關 System.Data.Entity 命名空間中可用擴充方法的完整清單,請參閱可查詢擴展類。 *您還需要將「使用系統.Data.Entity」添加到您的使用語句中。*

``` csharp
    using System;
    using System.Data.Entity;
    using System.Linq;
    using System.Threading.Tasks;

    namespace AsyncDemo
    {
        class Program
        {
            static void Main(string[] args)
            {
                var task = PerformDatabaseOperations();

                Console.WriteLine("Quote of the day");
                Console.WriteLine(" Don't worry about the world coming to an end today... ");
                Console.WriteLine(" It's already tomorrow in Australia.");

                task.Wait();

                Console.WriteLine();
                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            public static async Task PerformDatabaseOperations()
            {
                using (var db = new BloggingContext())
                {
                    // Create a new blog and save it
                    db.Blogs.Add(new Blog
                    {
                        Name = "Test Blog #" + (db.Blogs.Count() + 1)
                    });
                    Console.WriteLine("Calling SaveChanges.");
                    await db.SaveChangesAsync();
                    Console.WriteLine("SaveChanges completed.");

                    // Query for all blogs ordered by name
                    Console.WriteLine("Executing query.");
                    var blogs = await (from b in db.Blogs
                                orderby b.Name
                                select b).ToListAsync();

                    // Write all blogs out to Console
                    Console.WriteLine("Query completed with following results:");
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(" - " + blog.Name);
                    }
                }
            }
        }
    }
```

現在代碼是非同步的,我們可以在執行程式時觀察到不同的執行流:

1. **儲存變更**開始將新**部落格**推送到資料庫  
    *將命令發送到資料庫後,當前託管線程不需要更多的計算時間。**執行資料庫操作**方法返回(即使它尚未完成執行),並且 Main 方法中的程式流將繼續。*
2. **當天的報價寫入主控台**  
    *由於 Main 方法中沒有更多工作要做,因此在 Wait 調用上阻止託管線程,直到資料庫操作完成。完成後,將執行**其餘執行資料庫操作**。*
3.  **儲存變更**完成  
4.  查詢所有**部落格**送出到資料庫  
    *同樣,託管線程在資料庫中處理查詢時可以自由地執行其他工作。由於所有其他執行已完成,線程將在"等待"調用時停止。*
5.  查詢傳回及結果寫入**主控台**  

![非同步輸出](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a>外賣

現在,我們看到使用 EF 的非同步方法是多麼容易。 儘管非同步的優點在簡單的控制台應用中可能並不十分明顯,但在長時間運行或網路綁定活動可能會阻塞應用程式或導致大量線程增加記憶體佔用的情況下,可以應用這些相同的策略。
