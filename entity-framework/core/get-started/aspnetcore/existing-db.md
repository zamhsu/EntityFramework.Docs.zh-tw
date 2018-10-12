---
title: ASP.NET Core 使用者入門 - 現有的資料庫 - EF Core
author: rowanmiller
ms.date: 08/02/2018
ms.assetid: 2bc68bea-ff77-4860-bf0b-cf00db6712a0
uid: core/get-started/aspnetcore/existing-db
ms.openlocfilehash: 84e2e4bc1bdc774fa059fa893e0f8ac128931feb
ms.sourcegitcommit: ad1bdea58ed35d0f19791044efe9f72f94189c18
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/28/2018
ms.locfileid: "47447179"
---
# <a name="getting-started-with-ef-core-on-aspnet-core-with-an-existing-database"></a><span data-ttu-id="25d31-102">在 ASP.NET Core 上使用 EF Core 搭配現有資料庫的使用者入門</span><span class="sxs-lookup"><span data-stu-id="25d31-102">Getting Started with EF Core on ASP.NET Core with an Existing Database</span></span>

<span data-ttu-id="25d31-103">在本教學課程中，您將會建置 ASP.NET Core MVC 應用程式，而其使用 Entity Framework Core 執行基本的資料存取。</span><span class="sxs-lookup"><span data-stu-id="25d31-103">In this tutorial, you build an ASP.NET Core MVC application that performs basic data access using Entity Framework Core.</span></span> <span data-ttu-id="25d31-104">您將會對現有的資料庫進行還原工程，建立 Entity Framework 模型。</span><span class="sxs-lookup"><span data-stu-id="25d31-104">You reverse engineer an existing database to create an Entity Framework model.</span></span>

<span data-ttu-id="25d31-105">[在 GitHub 上檢視此文章的範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb)。</span><span class="sxs-lookup"><span data-stu-id="25d31-105">[View this article's sample on GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="25d31-106">必要條件</span><span class="sxs-lookup"><span data-stu-id="25d31-106">Prerequisites</span></span>

<span data-ttu-id="25d31-107">安裝下列軟體：</span><span class="sxs-lookup"><span data-stu-id="25d31-107">Install the following software:</span></span>

* <span data-ttu-id="25d31-108">[Visual Studio 2017 15.7](https://www.visualstudio.com/downloads/) 與這些工作負載：</span><span class="sxs-lookup"><span data-stu-id="25d31-108">[Visual Studio 2017 15.7](https://www.visualstudio.com/downloads/) with these workloads:</span></span>
  * <span data-ttu-id="25d31-109">**ASP.NET 與網頁程式開發** (位在 [Web & Cloud] \(Web 與雲端) 下)</span><span class="sxs-lookup"><span data-stu-id="25d31-109">**ASP.NET and web development** (under **Web & Cloud**)</span></span>
  * <span data-ttu-id="25d31-110">**.NET Core 跨平台開發** (位在 [其他工具組] 下)</span><span class="sxs-lookup"><span data-stu-id="25d31-110">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>
* <span data-ttu-id="25d31-111">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core)。</span><span class="sxs-lookup"><span data-stu-id="25d31-111">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core).</span></span>

## <a name="create-blogging-database"></a><span data-ttu-id="25d31-112">建立部落格資料庫</span><span class="sxs-lookup"><span data-stu-id="25d31-112">Create Blogging database</span></span>

<span data-ttu-id="25d31-113">本教學課程使用您 LocalDb 執行個體上的**部落格**資料庫作為現有的資料庫。</span><span class="sxs-lookup"><span data-stu-id="25d31-113">This tutorial uses a **Blogging** database on your LocalDb instance as the existing database.</span></span> <span data-ttu-id="25d31-114">如果您在另一個教學課程中已建立了**部落格**資料庫，則可跳過這些步驟。</span><span class="sxs-lookup"><span data-stu-id="25d31-114">If you have already created the **Blogging** database as part of another tutorial, skip these steps.</span></span>

* <span data-ttu-id="25d31-115">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="25d31-115">Open Visual Studio</span></span>
* <span data-ttu-id="25d31-116">[工具] > [連線到資料庫]</span><span class="sxs-lookup"><span data-stu-id="25d31-116">**Tools -> Connect to Database...**</span></span>
* <span data-ttu-id="25d31-117">選取 [Microsoft SQL Server]，並按一下 [繼續]</span><span class="sxs-lookup"><span data-stu-id="25d31-117">Select **Microsoft SQL Server** and click **Continue**</span></span>
* <span data-ttu-id="25d31-118">輸入 **(localdb)\mssqllocaldb** 作為**伺服器名稱**</span><span class="sxs-lookup"><span data-stu-id="25d31-118">Enter **(localdb)\mssqllocaldb** as the **Server Name**</span></span>
* <span data-ttu-id="25d31-119">輸入 **master** 作為**資料庫名稱**，並按一下 [確定]</span><span class="sxs-lookup"><span data-stu-id="25d31-119">Enter **master** as the **Database Name** and click **OK**</span></span>
* <span data-ttu-id="25d31-120">master 資料庫現在會顯示在 [伺服器總管] 中的 [資料連線] 下</span><span class="sxs-lookup"><span data-stu-id="25d31-120">The master database is now displayed under **Data Connections** in **Server Explorer**</span></span>
* <span data-ttu-id="25d31-121">以滑鼠右鍵按一下 [伺服器總管] 中的資料庫，並選取 [新增查詢]</span><span class="sxs-lookup"><span data-stu-id="25d31-121">Right-click on the database in **Server Explorer** and select **New Query**</span></span>
* <span data-ttu-id="25d31-122">將以下列出的指令碼，複製到查詢編輯器中</span><span class="sxs-lookup"><span data-stu-id="25d31-122">Copy the script listed below into the query editor</span></span>
* <span data-ttu-id="25d31-123">以滑鼠右鍵按一下查詢編輯器，然後選取 [執行]</span><span class="sxs-lookup"><span data-stu-id="25d31-123">Right-click on the query editor and select **Execute**</span></span>

[!code-sql[Main](../_shared/create-blogging-database-script.sql)]

## <a name="create-a-new-project"></a><span data-ttu-id="25d31-124">建立新專案</span><span class="sxs-lookup"><span data-stu-id="25d31-124">Create a new project</span></span>

* <span data-ttu-id="25d31-125">開啟 Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="25d31-125">Open Visual Studio 2017</span></span>
* <span data-ttu-id="25d31-126">[檔案] > [新增] > [專案...]</span><span class="sxs-lookup"><span data-stu-id="25d31-126">**File > New > Project...**</span></span>
* <span data-ttu-id="25d31-127">從左側功能表中，選取 **[已安裝] > [Visual C#] > [Web]**</span><span class="sxs-lookup"><span data-stu-id="25d31-127">From the left menu select **Installed > Visual C# > Web**</span></span>
* <span data-ttu-id="25d31-128">選取 [ASP.NET Core Web 應用程式] 專案範本</span><span class="sxs-lookup"><span data-stu-id="25d31-128">Select the **ASP.NET Core Web Application** project template</span></span>
* <span data-ttu-id="25d31-129">輸入 **EFGetStarted.AspNetCore.ExistingDb** 作為名稱，然後按一下 [確定]</span><span class="sxs-lookup"><span data-stu-id="25d31-129">Enter **EFGetStarted.AspNetCore.ExistingDb** as the name and click **OK**</span></span>
* <span data-ttu-id="25d31-130">等候 [新增 ASP.NET Core Web 應用程式] 對話方塊出現</span><span class="sxs-lookup"><span data-stu-id="25d31-130">Wait for the **New ASP.NET Core Web Application** dialog to appear</span></span>
* <span data-ttu-id="25d31-131">請確定目標架構下拉式清單設定為 **.NET Core**，然後版本下拉式清單設定為 **ASP.NET Core 2.1**</span><span class="sxs-lookup"><span data-stu-id="25d31-131">Make sure that the target framework dropdown is set to **.NET Core**, and the version dropdown is set to **ASP.NET Core 2.1**</span></span>
* <span data-ttu-id="25d31-132">選取 [Web 應用程式 (模型-檢視-控制器)] 範本</span><span class="sxs-lookup"><span data-stu-id="25d31-132">Select the **Web Application (Model-View-Controller)** template</span></span>
* <span data-ttu-id="25d31-133">確認 [驗證] 已設為 [無驗證]</span><span class="sxs-lookup"><span data-stu-id="25d31-133">Ensure that **Authentication** is set to **No Authentication**</span></span>
* <span data-ttu-id="25d31-134">按一下 [確定] </span><span class="sxs-lookup"><span data-stu-id="25d31-134">Click **OK**</span></span>

## <a name="install-entity-framework-core"></a><span data-ttu-id="25d31-135">安裝 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="25d31-135">Install Entity Framework Core</span></span>

<span data-ttu-id="25d31-136">若要安裝 EF Core，請為希望作為目標的 EF Core 資料庫提供者，安裝此套件。</span><span class="sxs-lookup"><span data-stu-id="25d31-136">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="25d31-137">如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="25d31-137">For a list of available providers see [Database Providers](../../providers/index.md).</span></span> 

<span data-ttu-id="25d31-138">在本教學課程中，因為使用 SQL Server，所以無需安裝提供者套件。</span><span class="sxs-lookup"><span data-stu-id="25d31-138">For this tutorial, you don't have to install a provider package because the tutorial uses SQL Server.</span></span> <span data-ttu-id="25d31-139">[Microsoft.AspnetCore.App 中繼套件](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1)包含 SQL Server 提供者套件。</span><span class="sxs-lookup"><span data-stu-id="25d31-139">The SQL Server provider package is included in the [Microsoft.AspnetCore.App metapackage](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1).</span></span>

## <a name="reverse-engineer-your-model"></a><span data-ttu-id="25d31-140">針對您的模型進行反向工程</span><span class="sxs-lookup"><span data-stu-id="25d31-140">Reverse engineer your model</span></span>

<span data-ttu-id="25d31-141">現在就可以根據您現有的資料庫來建立 EF 模型。</span><span class="sxs-lookup"><span data-stu-id="25d31-141">Now it's time to create the EF model based on your existing database.</span></span>

* <span data-ttu-id="25d31-142">[工具] –> [NuGet 套件管理員] –> [套件管理員主控台]</span><span class="sxs-lookup"><span data-stu-id="25d31-142">**Tools –> NuGet Package Manager –> Package Manager Console**</span></span>
* <span data-ttu-id="25d31-143">執行下列命令，以便從現有的資料庫來建立模型：</span><span class="sxs-lookup"><span data-stu-id="25d31-143">Run the following command to create a model from the existing database:</span></span>

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

<span data-ttu-id="25d31-144">如果您收到 `The term 'Scaffold-DbContext' is not recognized as the name of a cmdlet` 的錯誤，請關閉再重新開啟 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="25d31-144">If you receive an error stating `The term 'Scaffold-DbContext' is not recognized as the name of a cmdlet`, then close and reopen Visual Studio.</span></span>

> [!TIP]  
> <span data-ttu-id="25d31-145">您可以將 `-Tables` 引數加入上述命令，以指定要為哪些資料表產生實體。</span><span class="sxs-lookup"><span data-stu-id="25d31-145">You can specify which tables you want to generate entities for by adding the `-Tables` argument to the command above.</span></span> <span data-ttu-id="25d31-146">例如，`-Tables Blog,Post`。</span><span class="sxs-lookup"><span data-stu-id="25d31-146">For example, `-Tables Blog,Post`.</span></span>

<span data-ttu-id="25d31-147">反向工程程序會根據現有資料庫的結構描述，建立實體類別 (`Blog.cs` & `Post.cs`) 和衍生的內容 (`BloggingContext.cs`)。</span><span class="sxs-lookup"><span data-stu-id="25d31-147">The reverse engineer process created entity classes (`Blog.cs` & `Post.cs`) and a derived context (`BloggingContext.cs`) based on the schema of the existing database.</span></span>

 <span data-ttu-id="25d31-148">實體類別是簡單的 C# 物件，代表您要查詢和儲存的資料。</span><span class="sxs-lookup"><span data-stu-id="25d31-148">The entity classes are simple C# objects that represent the data you will be querying and saving.</span></span> <span data-ttu-id="25d31-149">以下為 `Blog` 與 `Post` 實體類別：</span><span class="sxs-lookup"><span data-stu-id="25d31-149">Here are the `Blog` and `Post` entity classes:</span></span>

 [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Models/Blog.cs)]

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Models/Post.cs)]

> [!TIP]  
> <span data-ttu-id="25d31-150">若要啟用延遲載入，可以設定瀏覽屬性 `virtual` (Blog.Post 與 Post.Blog)。</span><span class="sxs-lookup"><span data-stu-id="25d31-150">To enable lazy loading, you can make navigation properties `virtual` (Blog.Post and Post.Blog).</span></span>

 <span data-ttu-id="25d31-151">內容代表資料庫的工作階段，並可讓您查詢和儲存實體類別的執行個體。</span><span class="sxs-lookup"><span data-stu-id="25d31-151">The context represents a session with the database and allows you to query and save instances of the entity classes.</span></span>

<!-- Static code listing, rather than a linked file, because the tutorial modifies the context file heavily -->
 ``` csharp
public partial class BloggingContext : DbContext
{
    public BloggingContext()
    {
    }

    public BloggingContext(DbContextOptions<BloggingContext> options)
        : base(options)
    {
    }

    public virtual DbSet<Blog> Blog { get; set; }
    public virtual DbSet<Post> Post { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        if (!optionsBuilder.IsConfigured)
        {
            #warning To protect potentially sensitive information in your connection string, you should move it out of source code. See http://go.microsoft.com/fwlink/?LinkId=723263 for guidance on storing connection strings.
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;");
        }
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>(entity =>
        {
            entity.Property(e => e.Url).IsRequired();
        });

        modelBuilder.Entity<Post>(entity =>
        {
            entity.HasOne(d => d.Blog)
                .WithMany(p => p.Post)
                .HasForeignKey(d => d.BlogId);
        });
    }
}
```

## <a name="register-your-context-with-dependency-injection"></a><span data-ttu-id="25d31-152">使用相依性插入來註冊內容</span><span class="sxs-lookup"><span data-stu-id="25d31-152">Register your context with dependency injection</span></span>

<span data-ttu-id="25d31-153">相依性插入的概念是 ASP.NET Core 的核心。</span><span class="sxs-lookup"><span data-stu-id="25d31-153">The concept of dependency injection is central to ASP.NET Core.</span></span> <span data-ttu-id="25d31-154">服務 (例如 `BloggingContext`) 是在應用程式啟動期間使用相依性插入來註冊。</span><span class="sxs-lookup"><span data-stu-id="25d31-154">Services (such as `BloggingContext`) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="25d31-155">接著，會透過建構函式參數或屬性，針對需要這些服務的元件 (例如 MVC 控制器) 來提供服務。</span><span class="sxs-lookup"><span data-stu-id="25d31-155">Components that require these services (such as your MVC controllers) are then provided these services via constructor parameters or properties.</span></span> <span data-ttu-id="25d31-156">如需相依性插入的詳細資訊，請參閱 ASP.NET 網站上的[相依性插入](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html)文章。</span><span class="sxs-lookup"><span data-stu-id="25d31-156">For more information on dependency injection see the [Dependency Injection](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html) article on the ASP.NET site.</span></span>

### <a name="register-and-configure-your-context-in-startupcs"></a><span data-ttu-id="25d31-157">在 Startup.cs 中註冊並設定內容</span><span class="sxs-lookup"><span data-stu-id="25d31-157">Register and configure your context in Startup.cs</span></span>

<span data-ttu-id="25d31-158">若要為 MVC 控制器提供 `BloggingContext`，請將其註冊為服務。</span><span class="sxs-lookup"><span data-stu-id="25d31-158">To make `BloggingContext` available to MVC controllers, register it as a service.</span></span>

* <span data-ttu-id="25d31-159">開啟 **Startup.cs**</span><span class="sxs-lookup"><span data-stu-id="25d31-159">Open **Startup.cs**</span></span>
* <span data-ttu-id="25d31-160">在檔案開頭加入下列 `using` 陳述式</span><span class="sxs-lookup"><span data-stu-id="25d31-160">Add the following `using` statements at the start of the file</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Startup.cs#AddedUsings)]

<span data-ttu-id="25d31-161">您現已可使用 `AddDbContext(...)` 方法，將其註冊為服務。</span><span class="sxs-lookup"><span data-stu-id="25d31-161">Now you can use the `AddDbContext(...)` method to register it as a service.</span></span>
* <span data-ttu-id="25d31-162">找出 `ConfigureServices(...)` 方法</span><span class="sxs-lookup"><span data-stu-id="25d31-162">Locate the `ConfigureServices(...)` method</span></span>
* <span data-ttu-id="25d31-163">新增下列反白顯示的程式碼，將內容註冊為服務</span><span class="sxs-lookup"><span data-stu-id="25d31-163">Add the following highlighted code to register the context as a service</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Startup.cs?name=ConfigureServices&highlight=14-15)]

> [!TIP]  
> <span data-ttu-id="25d31-164">在實際的應用程式中，一般會將連接字串置於組態檔或環境變數中。</span><span class="sxs-lookup"><span data-stu-id="25d31-164">In a real application you would typically put the connection string in a configuration file or environment variable.</span></span> <span data-ttu-id="25d31-165">為了簡單起見，本教學課程將會請您將其定義在程式碼中。</span><span class="sxs-lookup"><span data-stu-id="25d31-165">For the sake of simplicity, this tutorial has you define it in code.</span></span> <span data-ttu-id="25d31-166">如需詳細資訊，請參閱[連接字串](../../miscellaneous/connection-strings.md)。</span><span class="sxs-lookup"><span data-stu-id="25d31-166">For more information, see [Connection Strings](../../miscellaneous/connection-strings.md).</span></span>

## <a name="create-a-controller-and-views"></a><span data-ttu-id="25d31-167">建立控制器和檢視</span><span class="sxs-lookup"><span data-stu-id="25d31-167">Create a controller and views</span></span>

* <span data-ttu-id="25d31-168">在 [方案總管] 中以滑鼠右鍵按一下 [Controllers] 資料夾，然後選取 [新增] > [控制器]</span><span class="sxs-lookup"><span data-stu-id="25d31-168">Right-click on the **Controllers** folder in **Solution Explorer** and select **Add -> Controller...**</span></span>
* <span data-ttu-id="25d31-169">選取 [使用 Entity Framework 執行檢視的 MVC 控制器] 並按一下 [確定]</span><span class="sxs-lookup"><span data-stu-id="25d31-169">Select **MVC Controller with views, using Entity Framework** and click **Ok**</span></span>
* <span data-ttu-id="25d31-170">將 [模型類別] 設為 [Blog]，並將 [資料內容類別] 設為 [BloggingContext]</span><span class="sxs-lookup"><span data-stu-id="25d31-170">Set **Model class** to **Blog** and **Data context class** to **BloggingContext**</span></span>
* <span data-ttu-id="25d31-171">按一下 [新增]</span><span class="sxs-lookup"><span data-stu-id="25d31-171">Click **Add**</span></span>

## <a name="run-the-application"></a><span data-ttu-id="25d31-172">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="25d31-172">Run the application</span></span>

<span data-ttu-id="25d31-173">您現在可以執行應用程式來查看運作狀況。</span><span class="sxs-lookup"><span data-stu-id="25d31-173">You can now run the application to see it in action.</span></span>

* <span data-ttu-id="25d31-174">[偵錯] > [啟動但不偵錯]</span><span class="sxs-lookup"><span data-stu-id="25d31-174">**Debug -> Start Without Debugging**</span></span>
* <span data-ttu-id="25d31-175">應用程式會在網頁瀏覽器中建置及開啟</span><span class="sxs-lookup"><span data-stu-id="25d31-175">The application builds and opens in a web browser</span></span>
* <span data-ttu-id="25d31-176">巡覽至 `/Blogs`</span><span class="sxs-lookup"><span data-stu-id="25d31-176">Navigate to `/Blogs`</span></span>
* <span data-ttu-id="25d31-177">按一下 [新建]</span><span class="sxs-lookup"><span data-stu-id="25d31-177">Click **Create New**</span></span>
* <span data-ttu-id="25d31-178">輸入新部落格的 **URL**，然後按一下 [建立]</span><span class="sxs-lookup"><span data-stu-id="25d31-178">Enter a **Url** for the new blog and click **Create**</span></span>

  ![建立頁面](_static/create.png)

  ![索引頁面](_static/index-existing-db.png)

## <a name="next-steps"></a><span data-ttu-id="25d31-181">後續步驟</span><span class="sxs-lookup"><span data-stu-id="25d31-181">Next steps</span></span>

<span data-ttu-id="25d31-182">如需如何建立內容和實體類別的詳細資訊，請參閱下列文章：</span><span class="sxs-lookup"><span data-stu-id="25d31-182">For more information about how to scaffold a context and entity classes, see the following articles:</span></span>
* [<span data-ttu-id="25d31-183">Entity Framework Core 工具參考 - .NET CLI</span><span class="sxs-lookup"><span data-stu-id="25d31-183">Entity Framework Core tools reference - .NET CLI</span></span>](xref:core/miscellaneous/cli/dotnet#dotnet-ef-dbcontext-scaffold)
* [<span data-ttu-id="25d31-184">Entity Framework Core 工具參考 - 套件管理員主控台</span><span class="sxs-lookup"><span data-stu-id="25d31-184">Entity Framework Core tools reference - Package Manager Console</span></span>](xref:core/miscellaneous/cli/powershell#scaffold-dbcontext)