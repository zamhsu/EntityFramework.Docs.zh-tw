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
# <a name="async-query-and-save"></a><span data-ttu-id="79593-102">非同步查詢及儲存</span><span class="sxs-lookup"><span data-stu-id="79593-102">Async query and save</span></span>
> [!NOTE]
> <span data-ttu-id="79593-103">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="79593-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="79593-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="79593-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="79593-105">EF6 引入了對異步查詢的支援,並使用 .NET 4.5 中引入[的異步和等待關鍵字](https://msdn.microsoft.com/library/vstudio/hh191443.aspx)進行保存。</span><span class="sxs-lookup"><span data-stu-id="79593-105">EF6 introduced support for asynchronous query and save using the [async and await keywords](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) that were introduced in .NET 4.5.</span></span> <span data-ttu-id="79593-106">雖然並非所有應用程式都可能從非同步中受益,但它可用於在處理長時間運行、網路或 I/O 綁定任務時提高用戶端回應能力和伺服器可擴充性。</span><span class="sxs-lookup"><span data-stu-id="79593-106">While not all applications may benefit from asynchrony, it can be used to improve client responsiveness and server scalability when handling long-running, network or I/O-bound tasks.</span></span>

## <a name="when-to-really-use-async"></a><span data-ttu-id="79593-107">何時真正使用非同步</span><span class="sxs-lookup"><span data-stu-id="79593-107">When to really use async</span></span>

<span data-ttu-id="79593-108">本演練的目的是以一種易於觀察非同步程式執行之間的區別的方式介紹異步概念。</span><span class="sxs-lookup"><span data-stu-id="79593-108">The purpose of this walkthrough is to introduce the async concepts in a way that makes it easy to observe the difference between asynchronous and synchronous program execution.</span></span> <span data-ttu-id="79593-109">本演練的目的不是說明非同步程式設計帶來好處的任何關鍵方案。</span><span class="sxs-lookup"><span data-stu-id="79593-109">This walkthrough is not intended to illustrate any of the key scenarios where async programming provides benefits.</span></span>

<span data-ttu-id="79593-110">非同步編程主要側重於釋放當前託管線程(線程執行 .NET 代碼),以執行其他工作,同時等待不需要從託管線程進行任何計算時間的操作。</span><span class="sxs-lookup"><span data-stu-id="79593-110">Async programming is primarily focused on freeing up the current managed thread (thread running .NET code) to do other work while it waits for an operation that does not require any compute time from a managed thread.</span></span> <span data-ttu-id="79593-111">例如,當資料庫引擎正在處理查詢時,.NET代碼將執行任何操作。</span><span class="sxs-lookup"><span data-stu-id="79593-111">For example, whilst the database engine is processing a query there is nothing to be done by .NET code.</span></span>

<span data-ttu-id="79593-112">在用戶端應用程式(WinForms、WPF等)中,當前線程可用於執行非同步操作時保持 UI 回應。</span><span class="sxs-lookup"><span data-stu-id="79593-112">In client applications (WinForms, WPF, etc.) the current thread can be used to keep the UI responsive while the async operation is performed.</span></span> <span data-ttu-id="79593-113">在伺服器應用程式(ASP.NET等)中,線程可用於處理其他傳入請求 - 這可以減少記憶體使用量和/或提高伺服器的輸送量。</span><span class="sxs-lookup"><span data-stu-id="79593-113">In server applications (ASP.NET etc.) the thread can be used to process other incoming requests - this can reduce memory usage and/or increase throughput of the server.</span></span>

<span data-ttu-id="79593-114">在大多數使用異步的應用程式將沒有明顯的好處,甚至可能是有害的。</span><span class="sxs-lookup"><span data-stu-id="79593-114">In most applications using async will have no noticeable benefits and even could be detrimental.</span></span> <span data-ttu-id="79593-115">在提交特定方案之前,使用測試、分析和常識來測量非同步對特定方案的影響。</span><span class="sxs-lookup"><span data-stu-id="79593-115">Use tests, profiling and common sense to measure the impact of async in your particular scenario before committing to it.</span></span>

<span data-ttu-id="79593-116">以下是一些更多有關非同步的資源:</span><span class="sxs-lookup"><span data-stu-id="79593-116">Here are some more resources to learn about async:</span></span>

-   [<span data-ttu-id="79593-117">布蘭登·佈雷在 .NET 4.5 中的異步/等待概述</span><span class="sxs-lookup"><span data-stu-id="79593-117">Brandon Bray’s overview of async/await in .NET 4.5</span></span>](https://devblogs.microsoft.com/dotnet/async-in-4-5-worth-the-await/)
-   <span data-ttu-id="79593-118">MSDN 函式庫中的[非同步編程](https://msdn.microsoft.com/library/hh191443.aspx)頁面</span><span class="sxs-lookup"><span data-stu-id="79593-118">[Asynchronous Programming](https://msdn.microsoft.com/library/hh191443.aspx) pages in the MSDN Library</span></span>
-   <span data-ttu-id="79593-119">[如何使用 Async 建構 ASP.NET Web 應用程式](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337)(包括伺服器輸送量增加的展示)</span><span class="sxs-lookup"><span data-stu-id="79593-119">[How to Build ASP.NET Web Applications Using Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (includes a demo of increased server throughput)</span></span>

## <a name="create-the-model"></a><span data-ttu-id="79593-120">建立模型</span><span class="sxs-lookup"><span data-stu-id="79593-120">Create the model</span></span>

<span data-ttu-id="79593-121">我們將使用[Code First 工作流](~/ef6/modeling/code-first/workflows/new-database.md)創建模型並生成資料庫,但是非同步功能將適用於所有 EF 模型,包括使用 EF 設計器創建的模型。</span><span class="sxs-lookup"><span data-stu-id="79593-121">We’ll be using the [Code First workflow](~/ef6/modeling/code-first/workflows/new-database.md) to create our model and generate the database, however the asynchronous functionality will work with all EF models including those created with the EF Designer.</span></span>

-   <span data-ttu-id="79593-122">建立主控台應用程式並稱之為**AsyncDemo**</span><span class="sxs-lookup"><span data-stu-id="79593-122">Create a Console Application and call it **AsyncDemo**</span></span>
-   <span data-ttu-id="79593-123">新增實體框架 NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="79593-123">Add the EntityFramework NuGet package</span></span>
    -   <span data-ttu-id="79593-124">在解決方案資源管理員中,右鍵按**下 AsyncDemo**專案</span><span class="sxs-lookup"><span data-stu-id="79593-124">In Solution Explorer, right-click on the **AsyncDemo** project</span></span>
    -   <span data-ttu-id="79593-125">選擇 **"管理 NuGet 包..."...**</span><span class="sxs-lookup"><span data-stu-id="79593-125">Select **Manage NuGet Packages…**</span></span>
    -   <span data-ttu-id="79593-126">在「管理 NuGet」 套件「對話框中,選擇 **」連線**「工具」選項卡並選擇 **「實體框架**」套件</span><span class="sxs-lookup"><span data-stu-id="79593-126">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
    -   <span data-ttu-id="79593-127">單擊"**安裝"**</span><span class="sxs-lookup"><span data-stu-id="79593-127">Click **Install**</span></span>
-   <span data-ttu-id="79593-128">新增具有以下實現**Model.cs**類別</span><span class="sxs-lookup"><span data-stu-id="79593-128">Add a **Model.cs** class with the following implementation</span></span>

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

 

## <a name="create-a-synchronous-program"></a><span data-ttu-id="79593-129">建立同步程式</span><span class="sxs-lookup"><span data-stu-id="79593-129">Create a synchronous program</span></span>

<span data-ttu-id="79593-130">現在,我們有一個 EF 模型,讓我們編寫一些代碼,用它來執行一些數據訪問。</span><span class="sxs-lookup"><span data-stu-id="79593-130">Now that we have an EF model, let's write some code that uses it to perform some data access.</span></span>

-   <span data-ttu-id="79593-131">將**Program.cs**的內容取代為以下代碼</span><span class="sxs-lookup"><span data-stu-id="79593-131">Replace the contents of **Program.cs** with the following code</span></span>

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

<span data-ttu-id="79593-132">此程式呼叫**執行資料庫管理**方法,該方法將新**部落格**儲存到資料庫中,然後從資料庫中檢索所有**部落格**並將其列印到**主控台**。</span><span class="sxs-lookup"><span data-stu-id="79593-132">This code calls the **PerformDatabaseOperations** method which saves a new **Blog** to the database and then retrieves all **Blogs** from the database and prints them to the **Console**.</span></span> <span data-ttu-id="79593-133">此之後,程式將當天的報價寫入**主控台**。</span><span class="sxs-lookup"><span data-stu-id="79593-133">After this, the program writes a quote of the day to the **Console**.</span></span>

<span data-ttu-id="79593-134">由於代碼是同步的,因此在運行程式時,我們可以觀察到以下執行流:</span><span class="sxs-lookup"><span data-stu-id="79593-134">Since the code is synchronous, we can observe the following execution flow when we run the program:</span></span>

1.  <span data-ttu-id="79593-135">**儲存變更**開始將新**部落格**推送到資料庫</span><span class="sxs-lookup"><span data-stu-id="79593-135">**SaveChanges** begins to push the new **Blog** to the database</span></span>
2.  <span data-ttu-id="79593-136">**儲存變更**完成</span><span class="sxs-lookup"><span data-stu-id="79593-136">**SaveChanges** completes</span></span>
3.  <span data-ttu-id="79593-137">查詢所有**部落格**送出到資料庫</span><span class="sxs-lookup"><span data-stu-id="79593-137">Query for all **Blogs** is sent to the database</span></span>
4.  <span data-ttu-id="79593-138">查詢傳回及結果寫入**主控台**</span><span class="sxs-lookup"><span data-stu-id="79593-138">Query returns and results are written to **Console**</span></span>
5.  <span data-ttu-id="79593-139">當天的報價寫入**主控台**</span><span class="sxs-lookup"><span data-stu-id="79593-139">Quote of the day is written to **Console**</span></span>

![同步輸出](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a><span data-ttu-id="79593-141">使其非同步</span><span class="sxs-lookup"><span data-stu-id="79593-141">Making it asynchronous</span></span>

<span data-ttu-id="79593-142">現在,我們已經啟動和運行我們的程序,我們可以開始使用新的非同步和等待關鍵字。</span><span class="sxs-lookup"><span data-stu-id="79593-142">Now that we have our program up and running, we can begin making use of the new async and await keywords.</span></span> <span data-ttu-id="79593-143">我們對Program.cs進行了以下更改</span><span class="sxs-lookup"><span data-stu-id="79593-143">We've made the following changes to Program.cs</span></span>

1.  <span data-ttu-id="79593-144">第 2 行 **:System.Data.Entity**命名空間的 using 語句允許我們存取 EF 非同步擴充方法。</span><span class="sxs-lookup"><span data-stu-id="79593-144">Line 2: The using statement for the **System.Data.Entity** namespace gives us access to the EF async extension methods.</span></span>
2.  <span data-ttu-id="79593-145">第 4 行:**系統.執行緒的**using 語句,任務命名空間允許我們使用**任務**類型。</span><span class="sxs-lookup"><span data-stu-id="79593-145">Line 4: The using statement for the **System.Threading.Tasks** namespace allows us to use the **Task** type.</span></span>
3.  <span data-ttu-id="79593-146">第 12 行 & 18:我們將捕獲為任務,用於監視**執行某些資料庫操作**(第 12 行)的進度,然後在完成程式的所有工作(第 18 行)後阻止此任務的程式執行完成。</span><span class="sxs-lookup"><span data-stu-id="79593-146">Line 12 & 18: We are capturing as task that monitors the progress of **PerformSomeDatabaseOperations** (line 12) and then blocking program execution for this task to complete once all the work for the program is done (line 18).</span></span>
4.  <span data-ttu-id="79593-147">第 25 行:我們更新了**執行一些資料庫操作**,以標記為**非同步**並傳回**工作**。</span><span class="sxs-lookup"><span data-stu-id="79593-147">Line 25: We've update **PerformSomeDatabaseOperations** to be marked as **async** and return a **Task**.</span></span>
5.  <span data-ttu-id="79593-148">第 35 行:我們現在正在調用"保存更改"的 Async 版本,等待它完成。</span><span class="sxs-lookup"><span data-stu-id="79593-148">Line 35: We're now calling the Async version of SaveChanges and awaiting it's completion.</span></span>
6.  <span data-ttu-id="79593-149">第 42 行:我們現在正在調用 ToList 的 Async 版本,等待結果。</span><span class="sxs-lookup"><span data-stu-id="79593-149">Line 42: We're now calling the Async version of ToList and awaiting on the result.</span></span>

<span data-ttu-id="79593-150">有關 System.Data.Entity 命名空間中可用擴充方法的完整清單,請參閱可查詢擴展類。</span><span class="sxs-lookup"><span data-stu-id="79593-150">For a comprehensive list of available extension methods in the System.Data.Entity namespace, refer to the QueryableExtensions class.</span></span> <span data-ttu-id="79593-151">*您還需要將「使用系統.Data.Entity」添加到您的使用語句中。*</span><span class="sxs-lookup"><span data-stu-id="79593-151">*You’ll also need to add “using System.Data.Entity” to your using statements.*</span></span>

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

<span data-ttu-id="79593-152">現在代碼是非同步的,我們可以在執行程式時觀察到不同的執行流:</span><span class="sxs-lookup"><span data-stu-id="79593-152">Now that the code is asynchronous, we can observe a different execution flow when we run the program:</span></span>

1. <span data-ttu-id="79593-153">**儲存變更**開始將新**部落格**推送到資料庫</span><span class="sxs-lookup"><span data-stu-id="79593-153">**SaveChanges** begins to push the new **Blog** to the database</span></span>  
    <span data-ttu-id="79593-154">*將命令發送到資料庫後,當前託管線程不需要更多的計算時間。**執行資料庫操作**方法返回(即使它尚未完成執行),並且 Main 方法中的程式流將繼續。*</span><span class="sxs-lookup"><span data-stu-id="79593-154">*Once the command is sent to the database no more compute time is needed on the current managed thread. The **PerformDatabaseOperations** method returns (even though it hasn't finished executing) and program flow in the Main method continues.*</span></span>
2. <span data-ttu-id="79593-155">**當天的報價寫入主控台**</span><span class="sxs-lookup"><span data-stu-id="79593-155">**Quote of the day is written to Console**</span></span>  
    <span data-ttu-id="79593-156">*由於 Main 方法中沒有更多工作要做,因此在 Wait 調用上阻止託管線程,直到資料庫操作完成。完成後,將執行**其餘執行資料庫操作**。*</span><span class="sxs-lookup"><span data-stu-id="79593-156">*Since there is no more work to do in the Main method, the managed thread is blocked on the Wait call until the database operation completes. Once it completes, the remainder of our **PerformDatabaseOperations** will be executed.*</span></span>
3.  <span data-ttu-id="79593-157">**儲存變更**完成</span><span class="sxs-lookup"><span data-stu-id="79593-157">**SaveChanges** completes</span></span>  
4.  <span data-ttu-id="79593-158">查詢所有**部落格**送出到資料庫</span><span class="sxs-lookup"><span data-stu-id="79593-158">Query for all **Blogs** is sent to the database</span></span>  
    <span data-ttu-id="79593-159">*同樣,託管線程在資料庫中處理查詢時可以自由地執行其他工作。由於所有其他執行已完成,線程將在"等待"調用時停止。*</span><span class="sxs-lookup"><span data-stu-id="79593-159">*Again, the managed thread is free to do other work while the query is processed in the database. Since all other execution has completed, the thread will just halt on the Wait call though.*</span></span>
5.  <span data-ttu-id="79593-160">查詢傳回及結果寫入**主控台**</span><span class="sxs-lookup"><span data-stu-id="79593-160">Query returns and results are written to **Console**</span></span>  

![非同步輸出](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a><span data-ttu-id="79593-162">外賣</span><span class="sxs-lookup"><span data-stu-id="79593-162">The takeaway</span></span>

<span data-ttu-id="79593-163">現在,我們看到使用 EF 的非同步方法是多麼容易。</span><span class="sxs-lookup"><span data-stu-id="79593-163">We now saw how easy it is to make use of EF’s asynchronous methods.</span></span> <span data-ttu-id="79593-164">儘管非同步的優點在簡單的控制台應用中可能並不十分明顯,但在長時間運行或網路綁定活動可能會阻塞應用程式或導致大量線程增加記憶體佔用的情況下,可以應用這些相同的策略。</span><span class="sxs-lookup"><span data-stu-id="79593-164">Although the advantages of async may not be very apparent with a simple console app, these same strategies can be applied in situations where long-running or network-bound activities might otherwise block the application, or cause a large number of threads to increase the memory footprint.</span></span>
