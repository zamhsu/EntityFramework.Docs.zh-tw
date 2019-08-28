---
title: 非同步查詢和儲存-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d56e6f1d-4bd1-4b50-9558-9a30e04a8ec3
ms.openlocfilehash: bf2039110962e8dd114242dcd0b9454963750774
ms.sourcegitcommit: c9c3e00c2d445b784423469838adc071a946e7c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68306588"
---
# <a name="async-query-and-save"></a><span data-ttu-id="99592-102">非同步查詢和儲存</span><span class="sxs-lookup"><span data-stu-id="99592-102">Async query and save</span></span>
> [!NOTE]
> <span data-ttu-id="99592-103">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="99592-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="99592-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="99592-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="99592-105">EF6 引進了非同步查詢的支援, 並使用 .NET 4.5 中引進的[async 和 await 關鍵字](https://msdn.microsoft.com/library/vstudio/hh191443.aspx)來儲存。</span><span class="sxs-lookup"><span data-stu-id="99592-105">EF6 introduced support for asynchronous query and save using the [async and await keywords](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) that were introduced in .NET 4.5.</span></span> <span data-ttu-id="99592-106">雖然並非所有應用程式都能受益于非同步, 但它可以在處理長時間執行、網路或 i/o 系結的工作時, 用來改善用戶端回應能力和伺服器的擴充性。</span><span class="sxs-lookup"><span data-stu-id="99592-106">While not all applications may benefit from asynchrony, it can be used to improve client responsiveness and server scalability when handling long-running, network or I/O-bound tasks.</span></span>

## <a name="when-to-really-use-async"></a><span data-ttu-id="99592-107">實際使用 async 的時機</span><span class="sxs-lookup"><span data-stu-id="99592-107">When to really use async</span></span>

<span data-ttu-id="99592-108">本逐步解說的目的是要以一種方式引進非同步概念, 讓您可以輕鬆觀察非同步和同步程式執行之間的差異。</span><span class="sxs-lookup"><span data-stu-id="99592-108">The purpose of this walkthrough is to introduce the async concepts in a way that makes it easy to observe the difference between asynchronous and synchronous program execution.</span></span> <span data-ttu-id="99592-109">本逐步解說的目的不是為了說明非同步程式設計提供優點的任何主要案例。</span><span class="sxs-lookup"><span data-stu-id="99592-109">This walkthrough is not intended to illustrate any of the key scenarios where async programming provides benefits.</span></span>

<span data-ttu-id="99592-110">非同步程式設計主要著重于釋放目前的 managed 執行緒 (執行 .NET 程式碼的執行緒), 以在等候不需要來自受控執行緒之任何計算時間的作業時, 進行其他工作。</span><span class="sxs-lookup"><span data-stu-id="99592-110">Async programming is primarily focused on freeing up the current managed thread (thread running .NET code) to do other work while it waits for an operation that does not require any compute time from a managed thread.</span></span> <span data-ttu-id="99592-111">例如, 當資料庫引擎正在處理查詢時, .NET 程式碼不會執行任何動作。</span><span class="sxs-lookup"><span data-stu-id="99592-111">For example, whilst the database engine is processing a query there is nothing to be done by .NET code.</span></span>

<span data-ttu-id="99592-112">在用戶端應用程式 (WinForms、WPF 等) 中, 您可以使用目前的執行緒, 在執行非同步作業時, 讓 UI 保持回應狀態。</span><span class="sxs-lookup"><span data-stu-id="99592-112">In client applications (WinForms, WPF, etc.) the current thread can be used to keep the UI responsive while the async operation is performed.</span></span> <span data-ttu-id="99592-113">在 伺服器應用程式 （ASP.NET 等） 的執行緒可用來處理其他連入要求數-這可以減少記憶體使用量和/或增加輸送量的伺服器。</span><span class="sxs-lookup"><span data-stu-id="99592-113">In server applications (ASP.NET etc.) the thread can be used to process other incoming requests - this can reduce memory usage and/or increase throughput of the server.</span></span>

<span data-ttu-id="99592-114">在大部分使用 async 的應用程式中, 都不會有明顯的好處, 甚至可能會造成損害。</span><span class="sxs-lookup"><span data-stu-id="99592-114">In most applications using async will have no noticeable benefits and even could be detrimental.</span></span> <span data-ttu-id="99592-115">使用測試、程式碼剖析和一般意義, 在認可之前, 測量非同步在特定案例中的影響。</span><span class="sxs-lookup"><span data-stu-id="99592-115">Use tests, profiling and common sense to measure the impact of async in your particular scenario before committing to it.</span></span>

<span data-ttu-id="99592-116">以下是一些可深入瞭解非同步資源:</span><span class="sxs-lookup"><span data-stu-id="99592-116">Here are some more resources to learn about async:</span></span>

-   [<span data-ttu-id="99592-117">Brandon Bray 在 .NET 4.5 中的 async/await 總覽</span><span class="sxs-lookup"><span data-stu-id="99592-117">Brandon Bray’s overview of async/await in .NET 4.5</span></span>](https://blogs.msdn.com/b/dotnet/archive/2012/04/03/async-in-4-5-worth-the-await.aspx)
-   <span data-ttu-id="99592-118">MSDN Library 中的[非同步程式設計](https://msdn.microsoft.com/library/hh191443.aspx)頁面</span><span class="sxs-lookup"><span data-stu-id="99592-118">[Asynchronous Programming](https://msdn.microsoft.com/library/hh191443.aspx) pages in the MSDN Library</span></span>
-   <span data-ttu-id="99592-119">[如何使用 Async 建立 ASP.NET Web 應用程式](http://channel9.msdn.com/events/teched/northamerica/2013/dev-b337)(包含增加伺服器輸送量的示範)</span><span class="sxs-lookup"><span data-stu-id="99592-119">[How to Build ASP.NET Web Applications Using Async](http://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (includes a demo of increased server throughput)</span></span>

## <a name="create-the-model"></a><span data-ttu-id="99592-120">建立模型</span><span class="sxs-lookup"><span data-stu-id="99592-120">Create the model</span></span>

<span data-ttu-id="99592-121">我們將使用[Code First 工作流程](~/ef6/modeling/code-first/workflows/new-database.md)來建立模型並產生資料庫, 不過, 非同步功能會使用所有 ef 模型, 包括使用 ef 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="99592-121">We’ll be using the [Code First workflow](~/ef6/modeling/code-first/workflows/new-database.md) to create our model and generate the database, however the asynchronous functionality will work with all EF models including those created with the EF Designer.</span></span>

-   <span data-ttu-id="99592-122">建立主控台應用程式並呼叫它**AsyncDemo**</span><span class="sxs-lookup"><span data-stu-id="99592-122">Create a Console Application and call it **AsyncDemo**</span></span>
-   <span data-ttu-id="99592-123">新增 EntityFramework NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="99592-123">Add the EntityFramework NuGet package</span></span>
    -   <span data-ttu-id="99592-124">在方案總管中, 以滑鼠右鍵按一下**AsyncDemo**專案</span><span class="sxs-lookup"><span data-stu-id="99592-124">In Solution Explorer, right-click on the **AsyncDemo** project</span></span>
    -   <span data-ttu-id="99592-125">選取 [**管理 NuGet 套件 ...** ]</span><span class="sxs-lookup"><span data-stu-id="99592-125">Select **Manage NuGet Packages…**</span></span>
    -   <span data-ttu-id="99592-126">在 [管理 NuGet 套件] 對話方塊中, 選取 [**線上**] 索引標籤, 然後選擇 [ **EntityFramework** ] 套件</span><span class="sxs-lookup"><span data-stu-id="99592-126">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
    -   <span data-ttu-id="99592-127">按一下 [安裝]。</span><span class="sxs-lookup"><span data-stu-id="99592-127">Click **Install**</span></span>
-   <span data-ttu-id="99592-128">使用下列執行方式新增**Model.cs**類別</span><span class="sxs-lookup"><span data-stu-id="99592-128">Add a **Model.cs** class with the following implementation</span></span>

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

 

## <a name="create-a-synchronous-program"></a><span data-ttu-id="99592-129">建立同步程式</span><span class="sxs-lookup"><span data-stu-id="99592-129">Create a synchronous program</span></span>

<span data-ttu-id="99592-130">既然我們已經有 EF 模型, 讓我們撰寫一些程式碼來使用它來執行一些資料存取。</span><span class="sxs-lookup"><span data-stu-id="99592-130">Now that we have an EF model, let's write some code that uses it to perform some data access.</span></span>

-   <span data-ttu-id="99592-131">使用下列程式碼取代**Program.cs**的內容</span><span class="sxs-lookup"><span data-stu-id="99592-131">Replace the contents of **Program.cs** with the following code</span></span>

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

<span data-ttu-id="99592-132">此程式碼會呼叫**PerformDatabaseOperations**方法, 以將新的**blog**儲存至資料庫, 然後從資料庫中抓取所有的**blog** , 並將其列印至**主控台**。</span><span class="sxs-lookup"><span data-stu-id="99592-132">This code calls the **PerformDatabaseOperations** method which saves a new **Blog** to the database and then retrieves all **Blogs** from the database and prints them to the **Console**.</span></span> <span data-ttu-id="99592-133">在此之後, 程式會將日期的報價寫入**主控台**。</span><span class="sxs-lookup"><span data-stu-id="99592-133">After this, the program writes a quote of the day to the **Console**.</span></span>

<span data-ttu-id="99592-134">由於程式碼是同步的, 因此我們可以在執行程式時觀察下列執行流程:</span><span class="sxs-lookup"><span data-stu-id="99592-134">Since the code is synchronous, we can observe the following execution flow when we run the program:</span></span>

1.  <span data-ttu-id="99592-135">**SaveChanges**會開始將新的**Blog**推送至資料庫</span><span class="sxs-lookup"><span data-stu-id="99592-135">**SaveChanges** begins to push the new **Blog** to the database</span></span>
2.  <span data-ttu-id="99592-136">**SaveChanges**完成</span><span class="sxs-lookup"><span data-stu-id="99592-136">**SaveChanges** completes</span></span>
3.  <span data-ttu-id="99592-137">所有**blog**的查詢都會傳送到資料庫</span><span class="sxs-lookup"><span data-stu-id="99592-137">Query for all **Blogs** is sent to the database</span></span>
4.  <span data-ttu-id="99592-138">查詢傳回並將結果寫入**主控台**</span><span class="sxs-lookup"><span data-stu-id="99592-138">Query returns and results are written to **Console**</span></span>
5.  <span data-ttu-id="99592-139">日期的引號會寫入**主控台**</span><span class="sxs-lookup"><span data-stu-id="99592-139">Quote of the day is written to **Console**</span></span>

![同步輸出](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a><span data-ttu-id="99592-141">使其成為非同步</span><span class="sxs-lookup"><span data-stu-id="99592-141">Making it asynchronous</span></span>

<span data-ttu-id="99592-142">既然我們已啟動並執行程式, 我們就可以開始使用新的 async 和 await 關鍵字。</span><span class="sxs-lookup"><span data-stu-id="99592-142">Now that we have our program up and running, we can begin making use of the new async and await keywords.</span></span> <span data-ttu-id="99592-143">我們已對 Program.cs 進行下列變更</span><span class="sxs-lookup"><span data-stu-id="99592-143">We've made the following changes to Program.cs</span></span>

1.  <span data-ttu-id="99592-144">第2行:**Entity**命名空間的 using 語句可讓我們存取 EF async 擴充方法。</span><span class="sxs-lookup"><span data-stu-id="99592-144">Line 2: The using statement for the **System.Data.Entity** namespace gives us access to the EF async extension methods.</span></span>
2.  <span data-ttu-id="99592-145">第4行:[ **System.object** ] 命名空間的 using 語句可讓我們使用工作類型。</span><span class="sxs-lookup"><span data-stu-id="99592-145">Line 4: The using statement for the **System.Threading.Tasks** namespace allows us to use the **Task** type.</span></span>
3.  <span data-ttu-id="99592-146">第12行 & 18:我們正在捕捉的工作是監視**PerformSomeDatabaseOperations** (第12行) 的進度, 然後封鎖程式執行, 讓此工作完成程式的所有工作 (第18行)。</span><span class="sxs-lookup"><span data-stu-id="99592-146">Line 12 & 18: We are capturing as task that monitors the progress of **PerformSomeDatabaseOperations** (line 12) and then blocking program execution for this task to complete once all the work for the program is done (line 18).</span></span>
4.  <span data-ttu-id="99592-147">第25行:我們已將**PerformSomeDatabaseOperations**更新為標記為**非同步**, 並傳回工作。</span><span class="sxs-lookup"><span data-stu-id="99592-147">Line 25: We've update **PerformSomeDatabaseOperations** to be marked as **async** and return a **Task**.</span></span>
5.  <span data-ttu-id="99592-148">行 35:我們現在正在呼叫 SaveChanges 的非同步版本, 並等候它完成。</span><span class="sxs-lookup"><span data-stu-id="99592-148">Line 35: We're now calling the Async version of SaveChanges and awaiting it's completion.</span></span>
6.  <span data-ttu-id="99592-149">行 42:我們現在正在呼叫非同步版本的 ToList, 並等候結果。</span><span class="sxs-lookup"><span data-stu-id="99592-149">Line 42: We're now calling the Async version of ToList and awaiting on the result.</span></span>

<span data-ttu-id="99592-150">如需 System.web 命名空間中可用擴充方法的完整清單, 請參閱 QueryableExtensions 類別。</span><span class="sxs-lookup"><span data-stu-id="99592-150">For a comprehensive list of available extension methods in the System.Data.Entity namespace, refer to the QueryableExtensions class.</span></span> <span data-ttu-id="99592-151">*您也必須將「使用 system.string」新增至您的 using 語句。*</span><span class="sxs-lookup"><span data-stu-id="99592-151">*You’ll also need to add “using System.Data.Entity” to your using statements.*</span></span>

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

<span data-ttu-id="99592-152">既然程式碼是非同步, 我們就可以在執行程式時觀察到不同的執行流程:</span><span class="sxs-lookup"><span data-stu-id="99592-152">Now that the code is asynchronous, we can observe a different execution flow when we run the program:</span></span>

1.  <span data-ttu-id="99592-153">在命令傳送到資料庫之後, **SaveChanges**會開始*將新的**Blog**推送至資料庫, 目前的受控執行緒上不再需要計算時間。**PerformDatabaseOperations**方法會傳回 （即使它尚未完成執行時），在 Main 方法中的程式流程會繼續。*</span><span class="sxs-lookup"><span data-stu-id="99592-153">**SaveChanges** begins to push the new **Blog** to the database *Once the command is sent to the database no more compute time is needed on the current managed thread. The **PerformDatabaseOperations** method returns (even though it hasn't finished executing) and program flow in the Main method continues.*</span></span>
2.  <span data-ttu-id="99592-154">**每日寫入至主控台**
    因為沒有其他工作的 Main 方法中執行時，managed 的執行緒會封鎖在等候資料庫作業完成之前呼叫。完成後, 將會執行 **PerformDatabaseOperations** 的其餘部分。  \*</span><span class="sxs-lookup"><span data-stu-id="99592-154">**Quote of the day is written to Console**
*Since there is no more work to do in the Main method, the managed thread is blocked on the Wait call until the database operation completes. Once it completes, the remainder of our **PerformDatabaseOperations** will be executed.*</span></span>
3.  <span data-ttu-id="99592-155">**SaveChanges**完成</span><span class="sxs-lookup"><span data-stu-id="99592-155">**SaveChanges** completes</span></span>
4.  <span data-ttu-id="99592-156">所有**blog**的查詢都會再次傳送至資料庫 *, 而在資料庫中處理查詢時, managed 執行緒可以免費執行其他工作。由於所有其他的執行都已完成, 因此執行緒只會在等候呼叫時暫停。*</span><span class="sxs-lookup"><span data-stu-id="99592-156">Query for all **Blogs** is sent to the database *Again, the managed thread is free to do other work while the query is processed in the database. Since all other execution has completed, the thread will just halt on the Wait call though.*</span></span>
5.  <span data-ttu-id="99592-157">查詢傳回並將結果寫入**主控台**</span><span class="sxs-lookup"><span data-stu-id="99592-157">Query returns and results are written to **Console**</span></span>

![非同步輸出](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a><span data-ttu-id="99592-159">重點</span><span class="sxs-lookup"><span data-stu-id="99592-159">The takeaway</span></span>

<span data-ttu-id="99592-160">我們現在看到使用 EF 的非同步方法有多麼容易。</span><span class="sxs-lookup"><span data-stu-id="99592-160">We now saw how easy it is to make use of EF’s asynchronous methods.</span></span> <span data-ttu-id="99592-161">雖然透過簡單的主控台應用程式, 非同步優點可能不會很明顯, 但這些相同的策略也適用于長時間執行或網路系結活動可能會封鎖應用程式的情況, 或造成大量的執行緒增加記憶體使用量。</span><span class="sxs-lookup"><span data-stu-id="99592-161">Although the advantages of async may not be very apparent with a simple console app, these same strategies can be applied in situations where long-running or network-bound activities might otherwise block the application, or cause a large number of threads to increase the memory footprint.</span></span>
