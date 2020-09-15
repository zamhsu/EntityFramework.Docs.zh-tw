---
title: 非同步查詢和儲存-EF6
description: Entity Framework 6 中的非同步查詢和儲存
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/async
ms.openlocfilehash: 576da2d2eaa292e46a8d0a913851ba93e6f72587
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070805"
---
# <a name="async-query-and-save"></a>非同步查詢並儲存
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。

EF6 引進了非同步查詢的支援，並使用 .NET 4.5 中引進的 [async 和 await 關鍵字](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) 來儲存。 雖然並非所有應用程式都能從非同步中獲益，但是在處理長時間執行、網路或 i/o 系結的工作時，也可以使用它來改善用戶端回應能力和伺服器的擴充性。

## <a name="when-to-really-use-async"></a>真正使用 async 的時機

本逐步解說的目的是要以可讓您輕鬆觀察非同步和同步程式執行之間差異的方式，來引入非同步概念。 本逐步解說的目的不是要說明非同步程式設計提供優點的任何重要案例。

非同步程式設計主要是用來釋出目前的 managed 執行緒 (執行 .NET 程式碼) 的執行緒，以執行其他工作，同時等待不需要來自 managed 執行緒的計算時間。 例如，在資料庫引擎處理查詢時，.NET 程式碼並不會執行任何動作。

在用戶端應用程式中 (WinForms、WPF 等等 ) 在執行非同步作業時，可使用目前的執行緒讓 UI 保持回應。 在 [伺服器應用程式] (ASP.NET 等 ) 執行緒可以用來處理其他傳入的要求，這可能會減少記憶體使用量及/或增加伺服器的輸送量。

在大部分使用 async 的應用程式中，不會有明顯的好處，甚至可能會造成損害。 您可以使用測試、分析和一般意義來測量非同步在您特定案例中的影響，然後再對其進行認可。

以下是一些可深入瞭解 async 的資源：

-   [Brandon Bray 在 .NET 4.5 中的 async/await 總覽](https://devblogs.microsoft.com/dotnet/async-in-4-5-worth-the-await/)
-   MSDN Library 中的[非同步程式設計](https://msdn.microsoft.com/library/hh191443.aspx)頁面
-   [如何使用 Async (建立 ASP.NET Web 應用程式](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) ，包括增加伺服器輸送量的示範) 

## <a name="create-the-model"></a>建立模型

我們將使用 [Code First 工作流程](xref:ef6/modeling/code-first/workflows/new-database) 來建立我們的模型並產生資料庫，但非同步功能可用於所有 ef 模型，包括使用 ef 設計工具建立的模型。

-   建立主控台應用程式並呼叫它 **AsyncDemo**
-   新增 EntityFramework NuGet 套件
    -   在方案總管中，以滑鼠右鍵按一下 **AsyncDemo** 專案
    -   選取 [**管理 NuGet 套件 ...** ]
    -   在 [管理 NuGet 封裝] 對話方塊中，選取 [ **線上** ] 索引標籤，然後選擇 **EntityFramework** 套件
    -   按一下 [安裝]
-   新增具有下列執行的 **Model.cs** 類別

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

現在有了 EF 模型，讓我們撰寫一些程式碼來使用它來執行一些資料存取。

-   以下列程式碼取代 **Program.cs** 的內容

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

此程式碼會呼叫 **PerformDatabaseOperations** 方法，該方法會將新的 **blog** 儲存至資料庫，然後從資料庫中取出所有的 **blog** ，然後將其列印至 **主控台**。 之後，程式會將一天的報價寫入 **主控台**。

由於程式碼是同步的，我們可以在執行程式時觀察到下列執行流程：

1.  **SaveChanges** 開始將新的 **Blog** 推送至資料庫
2.  **SaveChanges** 完成
3.  所有 **blog** 的查詢都會傳送至資料庫
4.  查詢傳回和結果會寫入 **主控台**
5.  日期的報價會寫入 **主控台**

![同步輸出](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a>使其變成非同步

既然我們已啟動並執行程式，就可以開始使用新的 async 和 await 關鍵字。 我們已對 Program.cs 進行下列變更

1.  第2行： **system.object** 命名空間的 using 語句可讓我們存取 EF async 擴充方法。
2.  第4行： [ **system.object** ] 命名空間的 using 語句可讓我們使用**工作類型。**
3.  第12行 & 18：我們正在作為工作來監視 **PerformSomeDatabaseOperations** (第) 12 行的進度，然後在程式的所有工作完成後，封鎖此工作的程式執行 (第18行) 。
4.  第25行：我們已將**PerformSomeDatabaseOperations**更新為以**非同步**方式標記，並**傳回工作。**
5.  第35行：我們現在呼叫了 SaveChanges 的非同步版本，並等候它完成。
6.  行42：我們目前正在呼叫 ToList 的非同步版本，並等候結果。

如需 system.string 命名空間中可用擴充方法的完整清單，請參閱 QueryableExtensions 類別。 *您也需要將「使用 system.string」新增至您的 using 語句。*

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

現在程式碼是非同步，我們可以在執行程式時觀察到不同的執行流程：

1. **SaveChanges** 開始將新的 **Blog** 推送至資料庫  
    *將命令傳送至資料庫之後，目前的 managed 執行緒上不需要有更多的計算時間。 **PerformDatabaseOperations** 方法會傳回 (，即使它尚未完成執行) 和 Main 方法中的程式流程仍繼續。*
2. **日期的報價會寫入主控台**  
    *由於 Main 方法中沒有其他要執行的工作，因此在資料庫作業完成之前，會封鎖等候呼叫上的 managed 執行緒。完成之後，我們將會執行 **PerformDatabaseOperations** 的其餘部分。*
3.  **SaveChanges** 完成  
4.  所有 **blog** 的查詢都會傳送至資料庫  
    *同樣地，在資料庫中處理查詢時，managed 執行緒可以自由地執行其他工作。因為所有其他執行都已完成，所以執行緒只會在等候呼叫上停止。*
5.  查詢傳回和結果會寫入 **主控台**  

![非同步輸出](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a>重點

我們現在看到使用 EF 的非同步方法有多麼容易。 雖然 async 的優點可能不會與簡單的主控台應用程式很明顯，但是這些相同的策略也適用于長時間執行或網路系結活動可能會封鎖應用程式的情況，或導致大量的執行緒提高記憶體使用量。
