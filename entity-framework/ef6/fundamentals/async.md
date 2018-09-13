---
title: 非同步查詢並儲存-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d56e6f1d-4bd1-4b50-9558-9a30e04a8ec3
ms.openlocfilehash: a6f49f3c31601ab04a3c04c074ce8fddfc6fe301
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45489683"
---
# <a name="async-query-and-save"></a><span data-ttu-id="df386-102">非同步查詢和儲存</span><span class="sxs-lookup"><span data-stu-id="df386-102">Async query and save</span></span>
> [!NOTE]
> <span data-ttu-id="df386-103">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="df386-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="df386-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="df386-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="df386-105">EF6 導入了非同步查詢並儲存使用的支援[async 和 await 關鍵字](https://msdn.microsoft.com/library/vstudio/hh191443.aspx).NET 4.5 中所導入。</span><span class="sxs-lookup"><span data-stu-id="df386-105">EF6 introduced support for asynchronous query and save using the [async and await keywords](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) that were introduced in .NET 4.5.</span></span> <span data-ttu-id="df386-106">雖然並非所有應用程式可能會受益於非同步功能，它可用來處理長時間執行、 網路或 I/O 繫結工作時，改善用戶端的回應性和伺服器的延展性。</span><span class="sxs-lookup"><span data-stu-id="df386-106">While not all applications may benefit from asynchrony, it can be used to improve client responsiveness and server scalability when handling long-running, network or I/O-bound tasks.</span></span>

## <a name="when-to-really-use-async"></a><span data-ttu-id="df386-107">真正使用非同步的時機</span><span class="sxs-lookup"><span data-stu-id="df386-107">When to really use async</span></span>

<span data-ttu-id="df386-108">本逐步解說的目的是介紹非同步概念，可讓您更容易觀察非同步和同步程式執行之間的差異的方式。</span><span class="sxs-lookup"><span data-stu-id="df386-108">The purpose of this walkthrough is to introduce the async concepts in a way that makes it easy to observe the difference between asynchronous and synchronous program execution.</span></span> <span data-ttu-id="df386-109">本逐步解說不是為了說明任何索引鍵的情況下，非同步程式設計的優點。</span><span class="sxs-lookup"><span data-stu-id="df386-109">This walkthrough is not intended to illustrate any of the key scenarios where async programming provides benefits.</span></span>

<span data-ttu-id="df386-110">非同步程式設計主要著重於釋出其等候的作業，不需要任何計算時間時執行其他工作的目前 managed 執行緒 （執行緒執行.NET 程式碼） 從受管理的執行緒。</span><span class="sxs-lookup"><span data-stu-id="df386-110">Async programming is primarily focused on freeing up the current managed thread (thread running .NET code) to do other work while it waits for an operation that does not require any compute time from a managed thread.</span></span> <span data-ttu-id="df386-111">比方說，儘管資料庫引擎在處理查詢沒有要進行.NET 程式碼。</span><span class="sxs-lookup"><span data-stu-id="df386-111">For example, whilst the database engine is processing a query there is nothing to be done by .NET code.</span></span>

<span data-ttu-id="df386-112">用戶端應用程式 （WinForms、 WPF 等等） 目前的執行緒可用來執行非同步作業時保留 UI 回應性。</span><span class="sxs-lookup"><span data-stu-id="df386-112">In client applications (WinForms, WPF, etc.) the current thread can be used to keep the UI responsive while the async operation is performed.</span></span> <span data-ttu-id="df386-113">在 伺服器應用程式 （ASP.NET 等） 的執行緒可用來處理其他連入要求數-這可以減少記憶體使用量和/或增加輸送量的伺服器。</span><span class="sxs-lookup"><span data-stu-id="df386-113">In server applications (ASP.NET etc.) the thread can be used to process other incoming requests - this can reduce memory usage and/or increase throughput of the server.</span></span>

<span data-ttu-id="df386-114">在大部分的應用程式使用 async 會有任何明顯的好處，甚至可能會危害。</span><span class="sxs-lookup"><span data-stu-id="df386-114">In most applications using async will have no noticeable benefits and even could be detrimental.</span></span> <span data-ttu-id="df386-115">使用測試、 分析以及相關常識，來測量特定實例中的非同步處理影響之前認可。</span><span class="sxs-lookup"><span data-stu-id="df386-115">Use tests, profiling and common sense to measure the impact of async in your particular scenario before committing to it.</span></span>

<span data-ttu-id="df386-116">以下是一些更多的資源，了解非同步：</span><span class="sxs-lookup"><span data-stu-id="df386-116">Here are some more resources to learn about async:</span></span>

-   [<span data-ttu-id="df386-117">Async/await.NET 4.5 中的 Brandon bray 所發表的概觀</span><span class="sxs-lookup"><span data-stu-id="df386-117">Brandon Bray’s overview of async/await in .NET 4.5</span></span>](http://blogs.msdn.com/b/dotnet/archive/2012/04/03/async-in-4-5-worth-the-await.aspx)
-   <span data-ttu-id="df386-118">[非同步程式設計](https://msdn.microsoft.com/library/hh191443.aspx)MSDN Library 中的頁面</span><span class="sxs-lookup"><span data-stu-id="df386-118">[Asynchronous Programming](https://msdn.microsoft.com/library/hh191443.aspx) pages in the MSDN Library</span></span>
-   <span data-ttu-id="df386-119">[如何建置 ASP.NET Web 應用程式使用 Async](http://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) （包括提高的伺服器輸送量的示範）</span><span class="sxs-lookup"><span data-stu-id="df386-119">[How to Build ASP.NET Web Applications Using Async](http://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (includes a demo of increased server throughput)</span></span>

## <a name="create-the-model"></a><span data-ttu-id="df386-120">建立模型</span><span class="sxs-lookup"><span data-stu-id="df386-120">Create the model</span></span>

<span data-ttu-id="df386-121">我們將使用[Code First 的工作流程](~/ef6/modeling/code-first/workflows/new-database.md)建立我們的模型，並產生資料庫，但非同步功能將會搭配所有的 EF 模型，包括使用 EF 設計工具建立。</span><span class="sxs-lookup"><span data-stu-id="df386-121">We’ll be using the [Code First workflow](~/ef6/modeling/code-first/workflows/new-database.md) to create our model and generate the database, however the asynchronous functionality will work with all EF models including those created with the EF Designer.</span></span>

-   <span data-ttu-id="df386-122">建立主控台應用程式並為它**AsyncDemo**</span><span class="sxs-lookup"><span data-stu-id="df386-122">Create a Console Application and call it **AsyncDemo**</span></span>
-   <span data-ttu-id="df386-123">新增 EntityFramework NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="df386-123">Add the EntityFramework NuGet package</span></span>
    -   <span data-ttu-id="df386-124">在 [方案總管] 中，以滑鼠右鍵按一下**AsyncDemo**專案</span><span class="sxs-lookup"><span data-stu-id="df386-124">In Solution Explorer, right-click on the **AsyncDemo** project</span></span>
    -   <span data-ttu-id="df386-125">選取**管理 NuGet 封裝...**</span><span class="sxs-lookup"><span data-stu-id="df386-125">Select **Manage NuGet Packages…**</span></span>
    -   <span data-ttu-id="df386-126">在 [管理 NuGet 套件] 對話方塊中，選取**線上**索引標籤，然後選擇**EntityFramework**封裝</span><span class="sxs-lookup"><span data-stu-id="df386-126">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
    -   <span data-ttu-id="df386-127">按一下 **安裝**</span><span class="sxs-lookup"><span data-stu-id="df386-127">Click **Install**</span></span>
-   <span data-ttu-id="df386-128">新增**Model.cs**替換為下列實作的類別</span><span class="sxs-lookup"><span data-stu-id="df386-128">Add a **Model.cs** class with the following implementation</span></span>

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

 

## <a name="create-a-synchronous-program"></a><span data-ttu-id="df386-129">建立同步的程式</span><span class="sxs-lookup"><span data-stu-id="df386-129">Create a synchronous program</span></span>

<span data-ttu-id="df386-130">既然我們已經 EF 模型，讓我們撰寫一些程式碼，使用它來執行某些資料存取。</span><span class="sxs-lookup"><span data-stu-id="df386-130">Now that we have an EF model, let's write some code that uses it to perform some data access.</span></span>

-   <span data-ttu-id="df386-131">內容取代**Program.cs**為下列程式碼</span><span class="sxs-lookup"><span data-stu-id="df386-131">Replace the contents of **Program.cs** with the following code</span></span>

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

<span data-ttu-id="df386-132">此程式碼會呼叫**PerformDatabaseOperations**方法，進而省下新**部落格**到資料庫，然後再擷取所有**部落格**從資料庫並列印至**主控台**。</span><span class="sxs-lookup"><span data-stu-id="df386-132">This code calls the **PerformDatabaseOperations** method which saves a new **Blog** to the database and then retrieves all **Blogs** from the database and prints them to the **Console**.</span></span> <span data-ttu-id="df386-133">在此之後，程式會寫入到一天中的報價**主控台**。</span><span class="sxs-lookup"><span data-stu-id="df386-133">After this, the program writes a quote of the day to the **Console**.</span></span>

<span data-ttu-id="df386-134">程式碼是同步的因為我們可以發現下列的執行流程，當我們執行程式：</span><span class="sxs-lookup"><span data-stu-id="df386-134">Since the code is synchronous, we can observe the following execution flow when we run the program:</span></span>

1.  <span data-ttu-id="df386-135">**SaveChanges**開始新的推播**部落格**資料庫</span><span class="sxs-lookup"><span data-stu-id="df386-135">**SaveChanges** begins to push the new **Blog** to the database</span></span>
2.  <span data-ttu-id="df386-136">**SaveChanges**完成</span><span class="sxs-lookup"><span data-stu-id="df386-136">**SaveChanges** completes</span></span>
3.  <span data-ttu-id="df386-137">所有的查詢**部落格**傳送至資料庫</span><span class="sxs-lookup"><span data-stu-id="df386-137">Query for all **Blogs** is sent to the database</span></span>
4.  <span data-ttu-id="df386-138">查詢會傳回，而且結果會寫入**主控台**</span><span class="sxs-lookup"><span data-stu-id="df386-138">Query returns and results are written to **Console**</span></span>
5.  <span data-ttu-id="df386-139">每日會寫入至**主控台**</span><span class="sxs-lookup"><span data-stu-id="df386-139">Quote of the day is written to **Console**</span></span>

![同步處理輸出](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a><span data-ttu-id="df386-141">讓非同步</span><span class="sxs-lookup"><span data-stu-id="df386-141">Making it asynchronous</span></span>

<span data-ttu-id="df386-142">既然我們已啟動並執行程式，我們可以開始進行使用新的 async 和 await 關鍵字。</span><span class="sxs-lookup"><span data-stu-id="df386-142">Now that we have our program up and running, we can begin making use of the new async and await keywords.</span></span> <span data-ttu-id="df386-143">我們已對 Program.cs 中的下列變更</span><span class="sxs-lookup"><span data-stu-id="df386-143">We've made the following changes to Program.cs</span></span>

1.  <span data-ttu-id="df386-144">第 2 行： Using 陳述式**System.Data.Entity**命名空間可讓我們存取 EF 的非同步擴充方法。</span><span class="sxs-lookup"><span data-stu-id="df386-144">Line 2: The using statement for the **System.Data.Entity** namespace gives us access to the EF async extension methods.</span></span>
2.  <span data-ttu-id="df386-145">第 4 行： Using 陳述式**System.Threading.Tasks**命名空間可讓我們使用**工作**型別。</span><span class="sxs-lookup"><span data-stu-id="df386-145">Line 4: The using statement for the **System.Threading.Tasks** namespace allows us to use the **Task** type.</span></span>
3.  <span data-ttu-id="df386-146">行 12 & 18： 我們會擷取以監視進度的工作**PerformSomeDatabaseOperations** （第 12 行），然後封鎖程式執行此工作完成一次的所有工作的程式完成 （第 18 行）。</span><span class="sxs-lookup"><span data-stu-id="df386-146">Line 12 & 18: We are capturing as task that monitors the progress of **PerformSomeDatabaseOperations** (line 12) and then blocking program execution for this task to complete once all the work for the program is done (line 18).</span></span>
4.  <span data-ttu-id="df386-147">第 25 行： 我們已更新**PerformSomeDatabaseOperations**標示為**非同步**，並傳回**工作**。</span><span class="sxs-lookup"><span data-stu-id="df386-147">Line 25: We've update **PerformSomeDatabaseOperations** to be marked as **async** and return a **Task**.</span></span>
5.  <span data-ttu-id="df386-148">第 35 行： 正在現在呼叫 SaveChanges 的非同步版本，並等候其完成。</span><span class="sxs-lookup"><span data-stu-id="df386-148">Line 35: We're now calling the Async version of SaveChanges and awaiting it's completion.</span></span>
6.  <span data-ttu-id="df386-149">列 42： 正在現在呼叫 ToList 的非同步版本，並等候結果。</span><span class="sxs-lookup"><span data-stu-id="df386-149">Line 42: We're now calling hte Async version of ToList and awaiting on the result.</span></span>

<span data-ttu-id="df386-150">如 System.Data.Entity 命名空間中可用的擴充方法的完整清單，請參閱 QueryableExtensions 類別。</span><span class="sxs-lookup"><span data-stu-id="df386-150">For a comprehensive list of available extension methods in the System.Data.Entity namespace, refer to the QueryableExtensions class.</span></span> <span data-ttu-id="df386-151">*您也需要將 「 使用 System.Data.Entity 」 使用陳述式。*</span><span class="sxs-lookup"><span data-stu-id="df386-151">*You’ll also need to add “using System.Data.Entity” to your using statements.*</span></span>

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

<span data-ttu-id="df386-152">現在，程式碼是 asyncronous，我們可以發現不同的執行流程，當我們執行程式：</span><span class="sxs-lookup"><span data-stu-id="df386-152">Now that the code is asyncronous, we can observe a different execution flow when we run the program:</span></span>

1.  <span data-ttu-id="df386-153">**SaveChanges**開始新的推播**部落格**資料庫\*之後傳送命令到資料庫不再計算目前 managed 執行緒上所需時間。**PerformDatabaseOperations**方法會傳回 （即使它尚未完成執行時），在 Main 方法中的程式流程會繼續。</span><span class="sxs-lookup"><span data-stu-id="df386-153">**SaveChanges** begins to push the new **Blog** to the database *Once the command is sent to the database no more compute time is needed on the current managed thread. The **PerformDatabaseOperations** method returns (even though it hasn't finished executing) and program flow in the Main method continues.*</span></span>
2.  <span data-ttu-id="df386-154">**每日寫入至主控台**
    因為沒有其他工作的 Main 方法中執行時，managed 的執行緒會封鎖在等候資料庫作業完成之前呼叫。完成後的其餘部分我們**PerformDatabaseOperations** \*就會執行。</span><span class="sxs-lookup"><span data-stu-id="df386-154">**Quote of the day is written to Console**
*Since there is no more work to do in the Main method, the managed thread is blocked on the Wait call until the database operation completes. Once it completes, the remainder of our **PerformDatabaseOperations*** will be executed.</span></span>
3.  <span data-ttu-id="df386-155">**SaveChanges**完成</span><span class="sxs-lookup"><span data-stu-id="df386-155">**SaveChanges** completes</span></span>
4.  <span data-ttu-id="df386-156">所有的查詢**部落格**傳送至資料庫*同樣地，managed 的執行緒沒有執行其他工作，而查詢則會處理在資料庫中。因為所有其他執行完成之後，執行緒將會就中止等候呼叫不過。*</span><span class="sxs-lookup"><span data-stu-id="df386-156">Query for all **Blogs** is sent to the database *Again, the managed thread is free to do other work while the query is processed in the database. Since all other execution has completed, the thread will just halt on the Wait call though.*</span></span>
5.  <span data-ttu-id="df386-157">查詢會傳回，而且結果會寫入**主控台**</span><span class="sxs-lookup"><span data-stu-id="df386-157">Query returns and results are written to **Console**</span></span>

![非同步輸出](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a><span data-ttu-id="df386-159">因此請了解</span><span class="sxs-lookup"><span data-stu-id="df386-159">The takeaway</span></span>

<span data-ttu-id="df386-160">我們現在看到多麼輕鬆容易讓使用 EF 的非同步方法。</span><span class="sxs-lookup"><span data-stu-id="df386-160">We now saw how easy it is to make use of EF’s asynchronous methods.</span></span> <span data-ttu-id="df386-161">雖然非同步的優點，可能不會使用簡單的主控台應用程式很明顯，可以在其中長時間執行或網路繫結的活動可能會否則封鎖應用程式，或是造成大量執行緒的情況下套用這些相同的策略增加記憶體使用量。</span><span class="sxs-lookup"><span data-stu-id="df386-161">Although the advantages of async may not be very apparent with a simple console app, these same strategies can be applied in situations where long-running or network-bound activities might otherwise block the application, or cause a large number of threads to increase the memory footprint.</span></span>
