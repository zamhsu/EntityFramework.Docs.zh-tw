---
title: ASP.NET Core 使用者入門 - 現有的資料庫 - EF Core
author: rowanmiller
description: 在 ASP.NET Core 上使用 EF Core 搭配現有資料庫的使用者入門
ms.date: 08/02/2018
ms.assetid: 2bc68bea-ff77-4860-bf0b-cf00db6712a0
uid: core/get-started/aspnetcore/existing-db
ms.openlocfilehash: eeebd75bebe85994c6439e06243e113f2bda814c
ms.sourcegitcommit: 7b7f774a5966b20d2aed5435a672a1edbe73b6fb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2019
ms.locfileid: "69565236"
---
# <a name="get-started-with-ef-core-on-aspnet-core-with-an-existing-database"></a><span data-ttu-id="01070-103">在 ASP.NET Core 上使用 EF Core 搭配現有資料庫的使用者入門</span><span class="sxs-lookup"><span data-stu-id="01070-103">Get started with EF Core on ASP.NET Core with an existing database</span></span>

<span data-ttu-id="01070-104">在本教學課程中，您將會建置 ASP.NET Core MVC 應用程式，而其使用 Entity Framework Core 執行基本的資料存取。</span><span class="sxs-lookup"><span data-stu-id="01070-104">In this tutorial, you build an ASP.NET Core MVC application that performs basic data access using Entity Framework Core.</span></span> <span data-ttu-id="01070-105">您將會對現有的資料庫進行還原工程，建立 Entity Framework 模型。</span><span class="sxs-lookup"><span data-stu-id="01070-105">You reverse engineer an existing database to create an Entity Framework model.</span></span>

<span data-ttu-id="01070-106">[在 GitHub 上檢視此文章的範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb)。</span><span class="sxs-lookup"><span data-stu-id="01070-106">[View this article's sample on GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="01070-107">必要條件</span><span class="sxs-lookup"><span data-stu-id="01070-107">Prerequisites</span></span>

<span data-ttu-id="01070-108">安裝下列軟體：</span><span class="sxs-lookup"><span data-stu-id="01070-108">Install the following software:</span></span>

* <span data-ttu-id="01070-109">[Visual Studio 2017 15.7](https://www.visualstudio.com/downloads/) 與這些工作負載：</span><span class="sxs-lookup"><span data-stu-id="01070-109">[Visual Studio 2017 15.7](https://www.visualstudio.com/downloads/) with these workloads:</span></span>
  * <span data-ttu-id="01070-110">**ASP.NET 與網頁程式開發** (位在 [Web & Cloud] \(Web 與雲端)  下)</span><span class="sxs-lookup"><span data-stu-id="01070-110">**ASP.NET and web development** (under **Web & Cloud**)</span></span>
  * <span data-ttu-id="01070-111">**.NET Core 跨平台開發** (位在 [其他工具組]  下)</span><span class="sxs-lookup"><span data-stu-id="01070-111">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>
* <span data-ttu-id="01070-112">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core)。</span><span class="sxs-lookup"><span data-stu-id="01070-112">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core).</span></span>

## <a name="create-blogging-database"></a><span data-ttu-id="01070-113">建立部落格資料庫</span><span class="sxs-lookup"><span data-stu-id="01070-113">Create Blogging database</span></span>

<span data-ttu-id="01070-114">本教學課程使用您 LocalDb 執行個體上的**部落格**資料庫作為現有的資料庫。</span><span class="sxs-lookup"><span data-stu-id="01070-114">This tutorial uses a **Blogging** database on your LocalDb instance as the existing database.</span></span> <span data-ttu-id="01070-115">如果您在另一個教學課程中已建立了**部落格**資料庫，則可跳過這些步驟。</span><span class="sxs-lookup"><span data-stu-id="01070-115">If you have already created the **Blogging** database as part of another tutorial, skip these steps.</span></span>

* <span data-ttu-id="01070-116">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="01070-116">Open Visual Studio</span></span>
* <span data-ttu-id="01070-117">[工具] > [連線到資料庫] </span><span class="sxs-lookup"><span data-stu-id="01070-117">**Tools -> Connect to Database...**</span></span>
* <span data-ttu-id="01070-118">選取 [Microsoft SQL Server]  ，並按一下 [繼續] </span><span class="sxs-lookup"><span data-stu-id="01070-118">Select **Microsoft SQL Server** and click **Continue**</span></span>
* <span data-ttu-id="01070-119">輸入 **(localdb)\mssqllocaldb** 作為**伺服器名稱**</span><span class="sxs-lookup"><span data-stu-id="01070-119">Enter **(localdb)\mssqllocaldb** as the **Server Name**</span></span>
* <span data-ttu-id="01070-120">輸入 **master** 作為**資料庫名稱**，並按一下 [確定] </span><span class="sxs-lookup"><span data-stu-id="01070-120">Enter **master** as the **Database Name** and click **OK**</span></span>
* <span data-ttu-id="01070-121">master 資料庫現在會顯示在 [伺服器總管]  中的 [資料連線]  下</span><span class="sxs-lookup"><span data-stu-id="01070-121">The master database is now displayed under **Data Connections** in **Server Explorer**</span></span>
* <span data-ttu-id="01070-122">以滑鼠右鍵按一下 [伺服器總管]  中的資料庫，並選取 [新增查詢] </span><span class="sxs-lookup"><span data-stu-id="01070-122">Right-click on the database in **Server Explorer** and select **New Query**</span></span>
* <span data-ttu-id="01070-123">將以下列出的指令碼，複製到查詢編輯器中</span><span class="sxs-lookup"><span data-stu-id="01070-123">Copy the script listed below into the query editor</span></span>
* <span data-ttu-id="01070-124">以滑鼠右鍵按一下查詢編輯器，然後選取 [執行] </span><span class="sxs-lookup"><span data-stu-id="01070-124">Right-click on the query editor and select **Execute**</span></span>

[!code-sql[Main](../_shared/create-blogging-database-script.sql)]

## <a name="create-a-new-project"></a><span data-ttu-id="01070-125">建立新專案</span><span class="sxs-lookup"><span data-stu-id="01070-125">Create a new project</span></span>

* <span data-ttu-id="01070-126">開啟 Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="01070-126">Open Visual Studio 2017</span></span>
* <span data-ttu-id="01070-127">[檔案] > [新增] > [專案...] </span><span class="sxs-lookup"><span data-stu-id="01070-127">**File > New > Project...**</span></span>
* <span data-ttu-id="01070-128">從左側功能表中，選取 **[已安裝] > [Visual C#] > [Web]**</span><span class="sxs-lookup"><span data-stu-id="01070-128">From the left menu select **Installed > Visual C# > Web**</span></span>
* <span data-ttu-id="01070-129">選取 [ASP.NET Core Web 應用程式]  專案範本</span><span class="sxs-lookup"><span data-stu-id="01070-129">Select the **ASP.NET Core Web Application** project template</span></span>
* <span data-ttu-id="01070-130">輸入 **EFGetStarted.AspNetCore.ExistingDb** 做為名稱 (它必須與程式碼中稍後使用的命名空間完全相符) 並按一下 [確定] </span><span class="sxs-lookup"><span data-stu-id="01070-130">Enter **EFGetStarted.AspNetCore.ExistingDb** as the name (it has to match exactly the namespace later used in the code) and click **OK**</span></span> 
* <span data-ttu-id="01070-131">等候 [新增 ASP.NET Core Web 應用程式]  對話方塊出現</span><span class="sxs-lookup"><span data-stu-id="01070-131">Wait for the **New ASP.NET Core Web Application** dialog to appear</span></span>
* <span data-ttu-id="01070-132">請確定目標架構下拉式清單設定為 **.NET Core**，然後版本下拉式清單設定為 **ASP.NET Core 2.1**</span><span class="sxs-lookup"><span data-stu-id="01070-132">Make sure that the target framework dropdown is set to **.NET Core**, and the version dropdown is set to **ASP.NET Core 2.1**</span></span>
* <span data-ttu-id="01070-133">選取 [Web 應用程式 (模型-檢視-控制器)]  範本</span><span class="sxs-lookup"><span data-stu-id="01070-133">Select the **Web Application (Model-View-Controller)** template</span></span>
* <span data-ttu-id="01070-134">確認 [驗證]  已設為 [無驗證] </span><span class="sxs-lookup"><span data-stu-id="01070-134">Ensure that **Authentication** is set to **No Authentication**</span></span>
* <span data-ttu-id="01070-135">按一下 [確定] </span><span class="sxs-lookup"><span data-stu-id="01070-135">Click **OK**</span></span>

## <a name="install-entity-framework-core"></a><span data-ttu-id="01070-136">安裝 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="01070-136">Install Entity Framework Core</span></span>

<span data-ttu-id="01070-137">若要安裝 EF Core，請為希望作為目標的 EF Core 資料庫提供者，安裝此套件。</span><span class="sxs-lookup"><span data-stu-id="01070-137">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="01070-138">如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="01070-138">For a list of available providers see [Database Providers](../../providers/index.md).</span></span> 

<span data-ttu-id="01070-139">在本教學課程中，因為使用 SQL Server，所以無需安裝提供者套件。</span><span class="sxs-lookup"><span data-stu-id="01070-139">For this tutorial, you don't have to install a provider package because the tutorial uses SQL Server.</span></span> <span data-ttu-id="01070-140">[Microsoft.AspnetCore.App 中繼套件](https://docs.microsoft.com/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1)包含 SQL Server 提供者套件。</span><span class="sxs-lookup"><span data-stu-id="01070-140">The SQL Server provider package is included in the [Microsoft.AspnetCore.App metapackage](https://docs.microsoft.com/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1).</span></span>

## <a name="reverse-engineer-your-model"></a><span data-ttu-id="01070-141">針對您的模型進行反向工程</span><span class="sxs-lookup"><span data-stu-id="01070-141">Reverse engineer your model</span></span>

<span data-ttu-id="01070-142">現在就可以根據您現有的資料庫來建立 EF 模型。</span><span class="sxs-lookup"><span data-stu-id="01070-142">Now it's time to create the EF model based on your existing database.</span></span>

* <span data-ttu-id="01070-143">[工具] –> [NuGet 套件管理員] –> [套件管理員主控台] </span><span class="sxs-lookup"><span data-stu-id="01070-143">**Tools –> NuGet Package Manager –> Package Manager Console**</span></span>
* <span data-ttu-id="01070-144">執行下列命令，以便從現有的資料庫來建立模型：</span><span class="sxs-lookup"><span data-stu-id="01070-144">Run the following command to create a model from the existing database:</span></span>

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

<span data-ttu-id="01070-145">如果您收到 `The term 'Scaffold-DbContext' is not recognized as the name of a cmdlet` 的錯誤，請關閉再重新開啟 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="01070-145">If you receive an error stating `The term 'Scaffold-DbContext' is not recognized as the name of a cmdlet`, then close and reopen Visual Studio.</span></span>

> [!TIP]  
> <span data-ttu-id="01070-146">您可以將 `-Tables` 引數加入上述命令，以指定要為哪些資料表產生實體。</span><span class="sxs-lookup"><span data-stu-id="01070-146">You can specify which tables you want to generate entities for by adding the `-Tables` argument to the command above.</span></span> <span data-ttu-id="01070-147">例如，`-Tables Blog,Post`。</span><span class="sxs-lookup"><span data-stu-id="01070-147">For example, `-Tables Blog,Post`.</span></span>

<span data-ttu-id="01070-148">反向工程程序會根據現有資料庫的結構描述，建立實體類別 (`Blog.cs` & `Post.cs`) 和衍生的內容 (`BloggingContext.cs`)。</span><span class="sxs-lookup"><span data-stu-id="01070-148">The reverse engineer process created entity classes (`Blog.cs` & `Post.cs`) and a derived context (`BloggingContext.cs`) based on the schema of the existing database.</span></span>

 <span data-ttu-id="01070-149">實體類別是簡單的 C# 物件，代表您要查詢和儲存的資料。</span><span class="sxs-lookup"><span data-stu-id="01070-149">The entity classes are simple C# objects that represent the data you will be querying and saving.</span></span> <span data-ttu-id="01070-150">以下為 `Blog` 與 `Post` 實體類別：</span><span class="sxs-lookup"><span data-stu-id="01070-150">Here are the `Blog` and `Post` entity classes:</span></span>

 [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Models/Blog.cs)]

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Models/Post.cs)]

> [!TIP]  
> <span data-ttu-id="01070-151">若要啟用延遲載入，可以設定瀏覽屬性 `virtual` (Blog.Post 與 Post.Blog)。</span><span class="sxs-lookup"><span data-stu-id="01070-151">To enable lazy loading, you can make navigation properties `virtual` (Blog.Post and Post.Blog).</span></span>

 <span data-ttu-id="01070-152">內容代表資料庫的工作階段，並可讓您查詢和儲存實體類別的執行個體。</span><span class="sxs-lookup"><span data-stu-id="01070-152">The context represents a session with the database and allows you to query and save instances of the entity classes.</span></span>

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

## <a name="register-your-context-with-dependency-injection"></a><span data-ttu-id="01070-153">使用相依性插入來註冊內容</span><span class="sxs-lookup"><span data-stu-id="01070-153">Register your context with dependency injection</span></span>

<span data-ttu-id="01070-154">相依性插入的概念是 ASP.NET Core 的核心。</span><span class="sxs-lookup"><span data-stu-id="01070-154">The concept of dependency injection is central to ASP.NET Core.</span></span> <span data-ttu-id="01070-155">服務 (例如 `BloggingContext`) 是在應用程式啟動期間使用相依性插入來註冊。</span><span class="sxs-lookup"><span data-stu-id="01070-155">Services (such as `BloggingContext`) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="01070-156">接著，會透過建構函式參數或屬性，針對需要這些服務的元件 (例如 MVC 控制器) 來提供服務。</span><span class="sxs-lookup"><span data-stu-id="01070-156">Components that require these services (such as your MVC controllers) are then provided these services via constructor parameters or properties.</span></span> <span data-ttu-id="01070-157">如需相依性插入的詳細資訊，請參閱 ASP.NET 網站上的[相依性插入](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html)文章。</span><span class="sxs-lookup"><span data-stu-id="01070-157">For more information on dependency injection see the [Dependency Injection](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html) article on the ASP.NET site.</span></span>

### <a name="register-and-configure-your-context-in-startupcs"></a><span data-ttu-id="01070-158">在 Startup.cs 中註冊並設定內容</span><span class="sxs-lookup"><span data-stu-id="01070-158">Register and configure your context in Startup.cs</span></span>

<span data-ttu-id="01070-159">若要為 MVC 控制器提供 `BloggingContext`，請將其註冊為服務。</span><span class="sxs-lookup"><span data-stu-id="01070-159">To make `BloggingContext` available to MVC controllers, register it as a service.</span></span>

* <span data-ttu-id="01070-160">開啟 **Startup.cs**</span><span class="sxs-lookup"><span data-stu-id="01070-160">Open **Startup.cs**</span></span>
* <span data-ttu-id="01070-161">在檔案開頭加入下列 `using` 陳述式</span><span class="sxs-lookup"><span data-stu-id="01070-161">Add the following `using` statements at the start of the file</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Startup.cs#AddedUsings)]

<span data-ttu-id="01070-162">您現已可使用 `AddDbContext(...)` 方法，將其註冊為服務。</span><span class="sxs-lookup"><span data-stu-id="01070-162">Now you can use the `AddDbContext(...)` method to register it as a service.</span></span>
* <span data-ttu-id="01070-163">找出 `ConfigureServices(...)` 方法</span><span class="sxs-lookup"><span data-stu-id="01070-163">Locate the `ConfigureServices(...)` method</span></span>
* <span data-ttu-id="01070-164">新增下列反白顯示的程式碼，將內容註冊為服務</span><span class="sxs-lookup"><span data-stu-id="01070-164">Add the following highlighted code to register the context as a service</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Startup.cs?name=ConfigureServices&highlight=14-15)]

> [!TIP]  
> <span data-ttu-id="01070-165">在實際的應用程式中，一般會將連接字串置於組態檔或環境變數中。</span><span class="sxs-lookup"><span data-stu-id="01070-165">In a real application you would typically put the connection string in a configuration file or environment variable.</span></span> <span data-ttu-id="01070-166">為了簡單起見，本教學課程將會請您將其定義在程式碼中。</span><span class="sxs-lookup"><span data-stu-id="01070-166">For the sake of simplicity, this tutorial has you define it in code.</span></span> <span data-ttu-id="01070-167">如需詳細資訊，請參閱[連接字串](../../miscellaneous/connection-strings.md)。</span><span class="sxs-lookup"><span data-stu-id="01070-167">For more information, see [Connection Strings](../../miscellaneous/connection-strings.md).</span></span>

## <a name="create-a-controller-and-views"></a><span data-ttu-id="01070-168">建立控制器和檢視</span><span class="sxs-lookup"><span data-stu-id="01070-168">Create a controller and views</span></span>

* <span data-ttu-id="01070-169">在 [方案總管]  中以滑鼠右鍵按一下 [Controllers]  資料夾，然後選取 [新增] > [控制器] </span><span class="sxs-lookup"><span data-stu-id="01070-169">Right-click on the **Controllers** folder in **Solution Explorer** and select **Add -> Controller...**</span></span>
* <span data-ttu-id="01070-170">選取 [使用 Entity Framework 執行檢視的 MVC 控制器]  並按一下 [確定] </span><span class="sxs-lookup"><span data-stu-id="01070-170">Select **MVC Controller with views, using Entity Framework** and click **Ok**</span></span>
* <span data-ttu-id="01070-171">將 [模型類別]  設為 [Blog]  ，並將 [資料內容類別]  設為 [BloggingContext] </span><span class="sxs-lookup"><span data-stu-id="01070-171">Set **Model class** to **Blog** and **Data context class** to **BloggingContext**</span></span>
* <span data-ttu-id="01070-172">按一下 [新增] </span><span class="sxs-lookup"><span data-stu-id="01070-172">Click **Add**</span></span>

## <a name="run-the-application"></a><span data-ttu-id="01070-173">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="01070-173">Run the application</span></span>

<span data-ttu-id="01070-174">您現在可以執行應用程式來查看運作狀況。</span><span class="sxs-lookup"><span data-stu-id="01070-174">You can now run the application to see it in action.</span></span>

* <span data-ttu-id="01070-175">[偵錯] > [啟動但不偵錯] </span><span class="sxs-lookup"><span data-stu-id="01070-175">**Debug -> Start Without Debugging**</span></span>
* <span data-ttu-id="01070-176">應用程式會在網頁瀏覽器中建置及開啟</span><span class="sxs-lookup"><span data-stu-id="01070-176">The application builds and opens in a web browser</span></span>
* <span data-ttu-id="01070-177">巡覽至 `/Blogs`</span><span class="sxs-lookup"><span data-stu-id="01070-177">Navigate to `/Blogs`</span></span>
* <span data-ttu-id="01070-178">按一下 [新建] </span><span class="sxs-lookup"><span data-stu-id="01070-178">Click **Create New**</span></span>
* <span data-ttu-id="01070-179">輸入新部落格的 **URL**，然後按一下 [建立] </span><span class="sxs-lookup"><span data-stu-id="01070-179">Enter a **Url** for the new blog and click **Create**</span></span>

  ![建立頁面](_static/create.png)

  ![索引頁面](_static/index-existing-db.png)

## <a name="next-steps"></a><span data-ttu-id="01070-182">後續步驟</span><span class="sxs-lookup"><span data-stu-id="01070-182">Next steps</span></span>

<span data-ttu-id="01070-183">如需如何建立內容和實體類別的詳細資訊，請參閱下列文章：</span><span class="sxs-lookup"><span data-stu-id="01070-183">For more information about how to scaffold a context and entity classes, see the following articles:</span></span>
* [<span data-ttu-id="01070-184">反向工程</span><span class="sxs-lookup"><span data-stu-id="01070-184">Reverse Engineering</span></span>](xref:core/managing-schemas/scaffolding)
* [<span data-ttu-id="01070-185">Entity Framework Core 工具參考 - .NET CLI</span><span class="sxs-lookup"><span data-stu-id="01070-185">Entity Framework Core tools reference - .NET CLI</span></span>](xref:core/miscellaneous/cli/dotnet#dotnet-ef-dbcontext-scaffold)
* [<span data-ttu-id="01070-186">Entity Framework Core 工具參考 - 套件管理員主控台</span><span class="sxs-lookup"><span data-stu-id="01070-186">Entity Framework Core tools reference - Package Manager Console</span></span>](xref:core/miscellaneous/cli/powershell#scaffold-dbcontext)
