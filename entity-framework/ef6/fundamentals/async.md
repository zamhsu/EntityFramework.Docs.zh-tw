---
title: 非同步查詢和儲存-EF6
description: Entity Framework 6 中的非同步查詢和儲存
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/async
ms.openlocfilehash: 2b5f6f868cbf2e0699a943cf68c8568550f4ba36
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063396"
---
# <a name="async-query-and-save"></a><span data-ttu-id="85a74-103">非同步查詢並儲存</span><span class="sxs-lookup"><span data-stu-id="85a74-103">Async query and save</span></span>
> [!NOTE]
> <span data-ttu-id="85a74-104">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="85a74-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="85a74-105">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="85a74-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="85a74-106">EF6 引進了非同步查詢的支援，並使用 .NET 4.5 中引進的 [async 和 await 關鍵字](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) 來儲存。</span><span class="sxs-lookup"><span data-stu-id="85a74-106">EF6 introduced support for asynchronous query and save using the [async and await keywords](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) that were introduced in .NET 4.5.</span></span> <span data-ttu-id="85a74-107">雖然並非所有應用程式都能從非同步中獲益，但是在處理長時間執行、網路或 i/o 系結的工作時，也可以使用它來改善用戶端回應能力和伺服器的擴充性。</span><span class="sxs-lookup"><span data-stu-id="85a74-107">While not all applications may benefit from asynchrony, it can be used to improve client responsiveness and server scalability when handling long-running, network or I/O-bound tasks.</span></span>

## <a name="when-to-really-use-async"></a><span data-ttu-id="85a74-108">真正使用 async 的時機</span><span class="sxs-lookup"><span data-stu-id="85a74-108">When to really use async</span></span>

<span data-ttu-id="85a74-109">本逐步解說的目的是要以可讓您輕鬆觀察非同步和同步程式執行之間差異的方式，來引入非同步概念。</span><span class="sxs-lookup"><span data-stu-id="85a74-109">The purpose of this walkthrough is to introduce the async concepts in a way that makes it easy to observe the difference between asynchronous and synchronous program execution.</span></span> <span data-ttu-id="85a74-110">本逐步解說的目的不是要說明非同步程式設計提供優點的任何重要案例。</span><span class="sxs-lookup"><span data-stu-id="85a74-110">This walkthrough is not intended to illustrate any of the key scenarios where async programming provides benefits.</span></span>

<span data-ttu-id="85a74-111">非同步程式設計主要是用來釋出目前的 managed 執行緒 (執行 .NET 程式碼) 的執行緒，以執行其他工作，同時等待不需要來自 managed 執行緒的計算時間。</span><span class="sxs-lookup"><span data-stu-id="85a74-111">Async programming is primarily focused on freeing up the current managed thread (thread running .NET code) to do other work while it waits for an operation that does not require any compute time from a managed thread.</span></span> <span data-ttu-id="85a74-112">例如，在資料庫引擎處理查詢時，.NET 程式碼並不會執行任何動作。</span><span class="sxs-lookup"><span data-stu-id="85a74-112">For example, whilst the database engine is processing a query there is nothing to be done by .NET code.</span></span>

<span data-ttu-id="85a74-113">在用戶端應用程式中 (WinForms、WPF 等等 ) 在執行非同步作業時，可使用目前的執行緒讓 UI 保持回應。</span><span class="sxs-lookup"><span data-stu-id="85a74-113">In client applications (WinForms, WPF, etc.) the current thread can be used to keep the UI responsive while the async operation is performed.</span></span> <span data-ttu-id="85a74-114">在 [伺服器應用程式] (ASP.NET 等 ) 執行緒可以用來處理其他傳入的要求，這可能會減少記憶體使用量及/或增加伺服器的輸送量。</span><span class="sxs-lookup"><span data-stu-id="85a74-114">In server applications (ASP.NET etc.) the thread can be used to process other incoming requests - this can reduce memory usage and/or increase throughput of the server.</span></span>

<span data-ttu-id="85a74-115">在大部分使用 async 的應用程式中，不會有明顯的好處，甚至可能會造成損害。</span><span class="sxs-lookup"><span data-stu-id="85a74-115">In most applications using async will have no noticeable benefits and even could be detrimental.</span></span> <span data-ttu-id="85a74-116">您可以使用測試、分析和一般意義來測量非同步在您特定案例中的影響，然後再對其進行認可。</span><span class="sxs-lookup"><span data-stu-id="85a74-116">Use tests, profiling and common sense to measure the impact of async in your particular scenario before committing to it.</span></span>

<span data-ttu-id="85a74-117">以下是一些可深入瞭解 async 的資源：</span><span class="sxs-lookup"><span data-stu-id="85a74-117">Here are some more resources to learn about async:</span></span>

-   [<span data-ttu-id="85a74-118">Brandon Bray 在 .NET 4.5 中的 async/await 總覽</span><span class="sxs-lookup"><span data-stu-id="85a74-118">Brandon Bray’s overview of async/await in .NET 4.5</span></span>](https://devblogs.microsoft.com/dotnet/async-in-4-5-worth-the-await/)
-   <span data-ttu-id="85a74-119">MSDN Library 中的[非同步程式設計](https://msdn.microsoft.com/library/hh191443.aspx)頁面</span><span class="sxs-lookup"><span data-stu-id="85a74-119">[Asynchronous Programming](https://msdn.microsoft.com/library/hh191443.aspx) pages in the MSDN Library</span></span>
-   <span data-ttu-id="85a74-120">[如何使用 Async (建立 ASP.NET Web 應用程式](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) ，包括增加伺服器輸送量的示範) </span><span class="sxs-lookup"><span data-stu-id="85a74-120">[How to Build ASP.NET Web Applications Using Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (includes a demo of increased server throughput)</span></span>

## <a name="create-the-model"></a><span data-ttu-id="85a74-121">建立模型</span><span class="sxs-lookup"><span data-stu-id="85a74-121">Create the model</span></span>

<span data-ttu-id="85a74-122">我們將使用 [Code First 工作流程](xref:ef6/modeling/code-first/workflows/new-database) 來建立我們的模型並產生資料庫，但非同步功能可用於所有 ef 模型，包括使用 ef 設計工具建立的模型。</span><span class="sxs-lookup"><span data-stu-id="85a74-122">We’ll be using the [Code First workflow](xref:ef6/modeling/code-first/workflows/new-database) to create our model and generate the database, however the asynchronous functionality will work with all EF models including those created with the EF Designer.</span></span>

-   <span data-ttu-id="85a74-123">建立主控台應用程式並呼叫它 **AsyncDemo**</span><span class="sxs-lookup"><span data-stu-id="85a74-123">Create a Console Application and call it **AsyncDemo**</span></span>
-   <span data-ttu-id="85a74-124">新增 EntityFramework NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="85a74-124">Add the EntityFramework NuGet package</span></span>
    -   <span data-ttu-id="85a74-125">在方案總管中，以滑鼠右鍵按一下 **AsyncDemo** 專案</span><span class="sxs-lookup"><span data-stu-id="85a74-125">In Solution Explorer, right-click on the **AsyncDemo** project</span></span>
    -   <span data-ttu-id="85a74-126">選取 [**管理 NuGet 套件 ...** ]</span><span class="sxs-lookup"><span data-stu-id="85a74-126">Select **Manage NuGet Packages…**</span></span>
    -   <span data-ttu-id="85a74-127">在 [管理 NuGet 封裝] 對話方塊中，選取 [ **線上** ] 索引標籤，然後選擇 **EntityFramework** 套件</span><span class="sxs-lookup"><span data-stu-id="85a74-127">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
    -   <span data-ttu-id="85a74-128">按一下 [安裝]</span><span class="sxs-lookup"><span data-stu-id="85a74-128">Click **Install**</span></span>
-   <span data-ttu-id="85a74-129">新增具有下列執行的 **Model.cs** 類別</span><span class="sxs-lookup"><span data-stu-id="85a74-129">Add a **Model.cs** class with the following implementation</span></span>

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

 

## <a name="create-a-synchronous-program"></a><span data-ttu-id="85a74-130">建立同步程式</span><span class="sxs-lookup"><span data-stu-id="85a74-130">Create a synchronous program</span></span>

<span data-ttu-id="85a74-131">現在有了 EF 模型，讓我們撰寫一些程式碼來使用它來執行一些資料存取。</span><span class="sxs-lookup"><span data-stu-id="85a74-131">Now that we have an EF model, let's write some code that uses it to perform some data access.</span></span>

-   <span data-ttu-id="85a74-132">以下列程式碼取代 **Program.cs** 的內容</span><span class="sxs-lookup"><span data-stu-id="85a74-132">Replace the contents of **Program.cs** with the following code</span></span>

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

<span data-ttu-id="85a74-133">此程式碼會呼叫 **PerformDatabaseOperations** 方法，該方法會將新的 **blog** 儲存至資料庫，然後從資料庫中取出所有的 **blog** ，然後將其列印至 **主控台**。</span><span class="sxs-lookup"><span data-stu-id="85a74-133">This code calls the **PerformDatabaseOperations** method which saves a new **Blog** to the database and then retrieves all **Blogs** from the database and prints them to the **Console**.</span></span> <span data-ttu-id="85a74-134">之後，程式會將一天的報價寫入 **主控台**。</span><span class="sxs-lookup"><span data-stu-id="85a74-134">After this, the program writes a quote of the day to the **Console**.</span></span>

<span data-ttu-id="85a74-135">由於程式碼是同步的，我們可以在執行程式時觀察到下列執行流程：</span><span class="sxs-lookup"><span data-stu-id="85a74-135">Since the code is synchronous, we can observe the following execution flow when we run the program:</span></span>

1.  <span data-ttu-id="85a74-136">**SaveChanges** 開始將新的 **Blog** 推送至資料庫</span><span class="sxs-lookup"><span data-stu-id="85a74-136">**SaveChanges** begins to push the new **Blog** to the database</span></span>
2.  <span data-ttu-id="85a74-137">**SaveChanges** 完成</span><span class="sxs-lookup"><span data-stu-id="85a74-137">**SaveChanges** completes</span></span>
3.  <span data-ttu-id="85a74-138">所有 **blog** 的查詢都會傳送至資料庫</span><span class="sxs-lookup"><span data-stu-id="85a74-138">Query for all **Blogs** is sent to the database</span></span>
4.  <span data-ttu-id="85a74-139">查詢傳回和結果會寫入 **主控台**</span><span class="sxs-lookup"><span data-stu-id="85a74-139">Query returns and results are written to **Console**</span></span>
5.  <span data-ttu-id="85a74-140">日期的報價會寫入 **主控台**</span><span class="sxs-lookup"><span data-stu-id="85a74-140">Quote of the day is written to **Console**</span></span>

![同步輸出](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a><span data-ttu-id="85a74-142">使其變成非同步</span><span class="sxs-lookup"><span data-stu-id="85a74-142">Making it asynchronous</span></span>

<span data-ttu-id="85a74-143">既然我們已啟動並執行程式，就可以開始使用新的 async 和 await 關鍵字。</span><span class="sxs-lookup"><span data-stu-id="85a74-143">Now that we have our program up and running, we can begin making use of the new async and await keywords.</span></span> <span data-ttu-id="85a74-144">我們已對 Program.cs 進行下列變更</span><span class="sxs-lookup"><span data-stu-id="85a74-144">We've made the following changes to Program.cs</span></span>

1.  <span data-ttu-id="85a74-145">第2行： **system.object** 命名空間的 using 語句可讓我們存取 EF async 擴充方法。</span><span class="sxs-lookup"><span data-stu-id="85a74-145">Line 2: The using statement for the **System.Data.Entity** namespace gives us access to the EF async extension methods.</span></span>
2.  <span data-ttu-id="85a74-146">第4行： [ **system.object** ] 命名空間的 using 語句可讓我們使用**工作類型。**</span><span class="sxs-lookup"><span data-stu-id="85a74-146">Line 4: The using statement for the **System.Threading.Tasks** namespace allows us to use the **Task** type.</span></span>
3.  <span data-ttu-id="85a74-147">第12行 & 18：我們正在作為工作來監視 **PerformSomeDatabaseOperations** (第) 12 行的進度，然後在程式的所有工作完成後，封鎖此工作的程式執行 (第18行) 。</span><span class="sxs-lookup"><span data-stu-id="85a74-147">Line 12 & 18: We are capturing as task that monitors the progress of **PerformSomeDatabaseOperations** (line 12) and then blocking program execution for this task to complete once all the work for the program is done (line 18).</span></span>
4.  <span data-ttu-id="85a74-148">第25行：我們已將**PerformSomeDatabaseOperations**更新為以**非同步**方式標記，並**傳回工作。**</span><span class="sxs-lookup"><span data-stu-id="85a74-148">Line 25: We've update **PerformSomeDatabaseOperations** to be marked as **async** and return a **Task**.</span></span>
5.  <span data-ttu-id="85a74-149">第35行：我們現在呼叫了 SaveChanges 的非同步版本，並等候它完成。</span><span class="sxs-lookup"><span data-stu-id="85a74-149">Line 35: We're now calling the Async version of SaveChanges and awaiting it's completion.</span></span>
6.  <span data-ttu-id="85a74-150">行42：我們目前正在呼叫 ToList 的非同步版本，並等候結果。</span><span class="sxs-lookup"><span data-stu-id="85a74-150">Line 42: We're now calling the Async version of ToList and awaiting on the result.</span></span>

<span data-ttu-id="85a74-151">如需 system.string 命名空間中可用擴充方法的完整清單，請參閱 QueryableExtensions 類別。</span><span class="sxs-lookup"><span data-stu-id="85a74-151">For a comprehensive list of available extension methods in the System.Data.Entity namespace, refer to the QueryableExtensions class.</span></span> <span data-ttu-id="85a74-152">*您也需要將「使用 system.string」新增至您的 using 語句。*</span><span class="sxs-lookup"><span data-stu-id="85a74-152">*You’ll also need to add “using System.Data.Entity” to your using statements.*</span></span>

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

<span data-ttu-id="85a74-153">現在程式碼是非同步，我們可以在執行程式時觀察到不同的執行流程：</span><span class="sxs-lookup"><span data-stu-id="85a74-153">Now that the code is asynchronous, we can observe a different execution flow when we run the program:</span></span>

1. <span data-ttu-id="85a74-154">**SaveChanges** 開始將新的 **Blog** 推送至資料庫</span><span class="sxs-lookup"><span data-stu-id="85a74-154">**SaveChanges** begins to push the new **Blog** to the database</span></span>  
    <span data-ttu-id="85a74-155">*將命令傳送至資料庫之後，目前的 managed 執行緒上不需要有更多的計算時間。 **PerformDatabaseOperations** 方法會傳回 (，即使它尚未完成執行) 和 Main 方法中的程式流程仍繼續。*</span><span class="sxs-lookup"><span data-stu-id="85a74-155">*Once the command is sent to the database no more compute time is needed on the current managed thread. The **PerformDatabaseOperations** method returns (even though it hasn't finished executing) and program flow in the Main method continues.*</span></span>
2. <span data-ttu-id="85a74-156">**日期的報價會寫入主控台**</span><span class="sxs-lookup"><span data-stu-id="85a74-156">**Quote of the day is written to Console**</span></span>  
    <span data-ttu-id="85a74-157">*由於 Main 方法中沒有其他要執行的工作，因此在資料庫作業完成之前，會封鎖等候呼叫上的 managed 執行緒。完成之後，我們將會執行 **PerformDatabaseOperations** 的其餘部分。*</span><span class="sxs-lookup"><span data-stu-id="85a74-157">*Since there is no more work to do in the Main method, the managed thread is blocked on the Wait call until the database operation completes. Once it completes, the remainder of our **PerformDatabaseOperations** will be executed.*</span></span>
3.  <span data-ttu-id="85a74-158">**SaveChanges** 完成</span><span class="sxs-lookup"><span data-stu-id="85a74-158">**SaveChanges** completes</span></span>  
4.  <span data-ttu-id="85a74-159">所有 **blog** 的查詢都會傳送至資料庫</span><span class="sxs-lookup"><span data-stu-id="85a74-159">Query for all **Blogs** is sent to the database</span></span>  
    <span data-ttu-id="85a74-160">*同樣地，在資料庫中處理查詢時，managed 執行緒可以自由地執行其他工作。因為所有其他執行都已完成，所以執行緒只會在等候呼叫上停止。*</span><span class="sxs-lookup"><span data-stu-id="85a74-160">*Again, the managed thread is free to do other work while the query is processed in the database. Since all other execution has completed, the thread will just halt on the Wait call though.*</span></span>
5.  <span data-ttu-id="85a74-161">查詢傳回和結果會寫入 **主控台**</span><span class="sxs-lookup"><span data-stu-id="85a74-161">Query returns and results are written to **Console**</span></span>  

![非同步輸出](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a><span data-ttu-id="85a74-163">重點</span><span class="sxs-lookup"><span data-stu-id="85a74-163">The takeaway</span></span>

<span data-ttu-id="85a74-164">我們現在看到使用 EF 的非同步方法有多麼容易。</span><span class="sxs-lookup"><span data-stu-id="85a74-164">We now saw how easy it is to make use of EF’s asynchronous methods.</span></span> <span data-ttu-id="85a74-165">雖然 async 的優點可能不會與簡單的主控台應用程式很明顯，但是這些相同的策略也適用于長時間執行或網路系結活動可能會封鎖應用程式的情況，或導致大量的執行緒提高記憶體使用量。</span><span class="sxs-lookup"><span data-stu-id="85a74-165">Although the advantages of async may not be very apparent with a simple console app, these same strategies can be applied in situations where long-running or network-bound activities might otherwise block the application, or cause a large number of threads to increase the memory footprint.</span></span>
