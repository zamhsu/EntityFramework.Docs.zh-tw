---
title: 非同步查詢並儲存-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: d56e6f1d-4bd1-4b50-9558-9a30e04a8ec3
caps.latest.revision: 3
ms.openlocfilehash: 655676029b3a4e42bbe257ff6091179930b1814e
ms.sourcegitcommit: 390f3a37bc55105ed7cc5b0e0925b7f9c9e80ba6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2018
ms.locfileid: "39120458"
---
# <a name="async-query-and-save"></a>非同步查詢和儲存
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。

EF6 導入了非同步查詢並儲存使用的支援[async 和 await 關鍵字](https://msdn.microsoft.com/library/vstudio/hh191443.aspx).NET 4.5 中所導入。 雖然並非所有應用程式可能會受益於非同步功能，它可用來處理長時間執行、 網路或 I/O 繫結工作時，改善用戶端的回應性和伺服器的延展性。

## <a name="when-to-really-use-async"></a>真正使用非同步的時機

本逐步解說的目的是介紹非同步概念，可讓您更容易觀察非同步和同步程式執行之間的差異的方式。 本逐步解說不是為了說明任何索引鍵的情況下，非同步程式設計的優點。

非同步程式設計主要著重於釋出其等候的作業，不需要任何計算時間時執行其他工作的目前 managed 執行緒 （執行緒執行.NET 程式碼） 從受管理的執行緒。 比方說，儘管資料庫引擎在處理查詢沒有要進行.NET 程式碼。

用戶端應用程式 （WinForms、 WPF 等等） 目前的執行緒可用來執行非同步作業時保留 UI 回應性。 在 伺服器應用程式 （ASP.NET 等） 的執行緒可用來處理其他連入要求數-這可以減少記憶體使用量和/或增加輸送量的伺服器。

在大部分的應用程式使用 async 會有任何明顯的好處，甚至可能會危害。 使用測試、 分析以及相關常識，來測量特定實例中的非同步處理影響之前認可。

以下是一些更多的資源，了解非同步：

-   [Async/await.NET 4.5 中的 Brandon bray 所發表的概觀](http://blogs.msdn.com/b/dotnet/archive/2012/04/03/async-in-4-5-worth-the-await.aspx)
-   [非同步程式設計](https://msdn.microsoft.com/library/hh191443.aspx)MSDN Library 中的頁面
-   [如何建置 ASP.NET Web 應用程式使用 Async](http://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) （包括提高的伺服器輸送量的示範）

## <a name="create-the-model"></a>建立模型

我們將使用[Code First 的工作流程](~/ef6/modeling/code-first/workflows/new-database.md)建立我們的模型，並產生資料庫，但非同步功能將會搭配所有的 EF 模型，包括使用 EF 設計工具建立。

-   建立主控台應用程式並為它**AsyncDemo**
-   新增 EntityFramework NuGet 套件
    -   在 [方案總管] 中，以滑鼠右鍵按一下**AsyncDemo**專案
    -   選取**管理 NuGet 封裝...**
    -   在 [管理 NuGet 套件] 對話方塊中，選取**線上**索引標籤，然後選擇**EntityFramework**封裝
    -   按一下 **安裝**
-   新增**Model.cs**替換為下列實作的類別

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

 

## <a name="create-a-synchronous-program"></a>建立同步的程式

既然我們已經 EF 模型，讓我們撰寫一些程式碼，使用它來執行某些資料存取。

-   內容取代**Program.cs**為下列程式碼

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

                Console.WriteLine();
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
                    db.SaveChanges();

                    // Query for all blogs ordered by name
                    var blogs = (from b in db.Blogs
                                orderby b.Name
                                select b).ToList();

                    // Write all blogs out to Console
                    Console.WriteLine();
                    Console.WriteLine("All blogs:");
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(" " + blog.Name);
                    }
                }
            }
        }
    }
```

此程式碼會呼叫**PerformDatabaseOperations**方法，進而省下新**部落格**到資料庫，然後再擷取所有**部落格**從資料庫並列印至**主控台**。 在此之後，程式會寫入到一天中的報價**主控台**。

程式碼是同步，因為我們可以發現下列的執行流程，當我們執行程式：

1.  **SaveChanges**開始新的推播**部落格**資料庫
2.  **SaveChanges**完成
3.  所有的查詢**部落格**傳送至資料庫
4.  查詢會傳回，而且結果會寫入**主控台**
5.  每日會寫入至**主控台**

![SyncOutput](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a>讓非同步

既然我們已啟動並執行程式，我們可以開始進行使用新的 async 和 await 關鍵字。 我們已對 Program.cs 中的下列變更

1.  第 2 行： Using 陳述式**System.Data.Entity**命名空間可讓我們存取 EF 的非同步擴充方法。
2.  第 4 行： Using 陳述式**System.Threading.Tasks**命名空間可讓我們使用**工作**型別。
3.  行 12 & 18： 我們會擷取以監視進度的工作**PerformSomeDatabaseOperations** （第 12 行），然後封鎖程式執行此工作完成一次的所有工作的程式完成 （第 18 行）。
4.  第 25 行： 我們已更新**PerformSomeDatabaseOperations**標示為**非同步**，並傳回**工作**。
5.  第 35 行： 正在現在呼叫 SaveChanges 的非同步版本，並等候其完成。
6.  列 42： 正在現在呼叫 ToList 的非同步版本，並等候結果。

如 System.Data.Entity 命名空間中可用的擴充方法的完整清單，請參閱 QueryableExtensions 類別。 *您也需要將 「 使用 System.Data.Entity 」 使用陳述式。*

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

現在，程式碼是 asyncronous，我們可以發現不同的執行流程，當我們執行程式：

1.  **SaveChanges**開始新的推播**部落格**資料庫*之後傳送命令到資料庫不再計算目前 managed 執行緒上所需時間。**PerformDatabaseOperations**方法會傳回 （即使它尚未完成執行時），在 Main 方法中的程式流程會繼續。
2.  **每日寫入至主控台**
    因為沒有其他工作的 Main 方法中執行時，managed 的執行緒會封鎖在等候資料庫作業完成之前呼叫。完成後的其餘部分我們**PerformDatabaseOperations** *就會執行。
3.  **SaveChanges**完成
4.  所有的查詢**部落格**傳送至資料庫*同樣地，managed 的執行緒沒有執行其他工作，而查詢則會處理在資料庫中。因為所有其他執行完成之後，執行緒將會就中止等候呼叫不過。*
5.  查詢會傳回，而且結果會寫入**主控台**

![AsyncOutput](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a>因此請了解

我們現在看到多麼輕鬆容易讓使用 EF 的非同步方法。 雖然非同步的優點，可能不會使用簡單的主控台應用程式很明顯，可以在其中長時間執行或網路繫結的活動可能會否則封鎖應用程式，或是造成大量執行緒的情況下套用這些相同的策略增加記憶體使用量。
