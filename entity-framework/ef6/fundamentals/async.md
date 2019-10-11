---
title: 非同步查詢和儲存-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d56e6f1d-4bd1-4b50-9558-9a30e04a8ec3
ms.openlocfilehash: 0642dc13e7aa3906fa1495031c62701fc16f0192
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181838"
---
# <a name="async-query-and-save"></a>非同步查詢和儲存
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。

EF6 引進了非同步查詢的支援，並使用 .NET 4.5 中引進的[async 和 await 關鍵字](https://msdn.microsoft.com/library/vstudio/hh191443.aspx)來儲存。 雖然並非所有應用程式都能受益于非同步，但它可以在處理長時間執行、網路或 i/o 系結的工作時，用來改善用戶端回應能力和伺服器的擴充性。

## <a name="when-to-really-use-async"></a>實際使用 async 的時機

本逐步解說的目的是要以一種方式引進非同步概念，讓您可以輕鬆觀察非同步和同步程式執行之間的差異。 本逐步解說的目的不是為了說明非同步程式設計提供優點的任何主要案例。

非同步程式設計主要著重于釋放目前的 managed 執行緒（執行 .NET 程式碼的執行緒），以在等候不需要來自受控執行緒之任何計算時間的作業時，進行其他工作。 例如，當資料庫引擎正在處理查詢時，.NET 程式碼不會執行任何動作。

在用戶端應用程式（WinForms、WPF 等）中，您可以使用目前的執行緒，在執行非同步作業時，讓 UI 保持回應狀態。 在 伺服器應用程式 （ASP.NET 等） 的執行緒可用來處理其他連入要求數-這可以減少記憶體使用量和/或增加輸送量的伺服器。

在大部分使用 async 的應用程式中，都不會有明顯的好處，甚至可能會造成損害。 使用測試、程式碼剖析和一般意義，在認可之前，測量非同步在特定案例中的影響。

以下是一些可深入瞭解非同步資源：

-   [Brandon Bray 在 .NET 4.5 中的 async/await 總覽](https://blogs.msdn.com/b/dotnet/archive/2012/04/03/async-in-4-5-worth-the-await.aspx)
-   MSDN Library 中的[非同步程式設計](https://msdn.microsoft.com/library/hh191443.aspx)頁面
-   [如何使用 Async 建立 ASP.NET Web 應用程式](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337)（包括增加伺服器輸送量的示範）

## <a name="create-the-model"></a>建立模型

我們將使用[Code First 工作流程](~/ef6/modeling/code-first/workflows/new-database.md)來建立模型並產生資料庫，不過，非同步功能會使用所有 ef 模型，包括使用 ef 設計工具所建立的模型。

-   建立主控台應用程式並呼叫它**AsyncDemo**
-   新增 EntityFramework NuGet 套件
    -   在方案總管中，以滑鼠右鍵按一下**AsyncDemo**專案
    -   選取 [**管理 NuGet 套件 ...** ]
    -   在 [管理 NuGet 套件] 對話方塊中，選取 [**線上**] 索引標籤，然後選擇 [ **EntityFramework** ] 套件
    -   按一下 [安裝]。
-   使用下列執行方式新增**Model.cs**類別

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

既然我們已經有 EF 模型，讓我們撰寫一些程式碼來使用它來執行一些資料存取。

-   使用下列程式碼取代**Program.cs**的內容

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

此程式碼會呼叫**PerformDatabaseOperations**方法，以將新的**blog**儲存至資料庫，然後從資料庫中抓取所有的**blog** ，並將其列印至**主控台**。 在此之後，程式會將日期的報價寫入**主控台**。

由於程式碼是同步的，因此我們可以在執行程式時觀察下列執行流程：

1.  **SaveChanges**會開始將新的**Blog**推送至資料庫
2.  **SaveChanges**完成
3.  所有**blog**的查詢都會傳送到資料庫
4.  查詢傳回並將結果寫入**主控台**
5.  日期的引號會寫入**主控台**

![同步輸出](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a>使其成為非同步

既然我們已啟動並執行程式，我們就可以開始使用新的 async 和 await 關鍵字。 我們已對 Program.cs 進行下列變更

1.  第2行：**Entity**命名空間的 using 語句可讓我們存取 EF async 擴充方法。
2.  第4行：[ **System.object** ] 命名空間的 using 語句可讓我們使用**工作類型。**
3.  第12行 & 18：我們正在捕捉的工作是監視**PerformSomeDatabaseOperations** （第12行）的進度，然後封鎖程式執行，讓此工作完成程式的所有工作（第18行）。
4.  第25行：我們已將**PerformSomeDatabaseOperations**更新為標記為**非同步**，並傳回**工作。**
5.  行35：我們現在正在呼叫 SaveChanges 的非同步版本，並等候它完成。
6.  行42：我們現在正在呼叫非同步版本的 ToList，並等候結果。

如需 System.web 命名空間中可用擴充方法的完整清單，請參閱 QueryableExtensions 類別。 *您也必須將「使用 system.string」新增至您的 using 語句。*

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

既然程式碼是非同步，我們就可以在執行程式時觀察到不同的執行流程：

1. **SaveChanges**會開始將新的**Blog**推送至資料庫  
    @no__t 0Once 命令會傳送至資料庫，目前的 managed 執行緒上不再需要計算時間。**PerformDatabaseOperations**方法會傳回 （即使它尚未完成執行時），在 Main 方法中的程式流程會繼續。*
2. **日期的引號會寫入主控台**  
    @no__t 0Since 在 Main 方法中沒有其他要執行的工作，在資料庫作業完成之前，會封鎖等候呼叫的 managed 執行緒。完成後, 將會執行 **PerformDatabaseOperations** 的其餘部分*。
3.  **SaveChanges**完成  
4.  所有**blog**的查詢都會傳送到資料庫  
    @no__t 0Again，managed 執行緒可以在資料庫中處理查詢時，自由執行其他工作。由於所有其他的執行都已完成，因此執行緒只會在等候呼叫時中斷。 *
5.  查詢傳回並將結果寫入**主控台**  

![非同步輸出](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a>重點

我們現在看到使用 EF 的非同步方法有多麼容易。 雖然透過簡單的主控台應用程式，非同步優點可能不會很明顯，但這些相同的策略也適用于長時間執行或網路系結活動可能會封鎖應用程式的情況，或造成大量的執行緒增加記憶體使用量。
