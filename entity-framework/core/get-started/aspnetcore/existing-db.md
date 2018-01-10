---
title: "ASP.NET Core 使用者入門 - 現有的資料庫 - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 2bc68bea-ff77-4860-bf0b-cf00db6712a0
ms.technology: entity-framework-core
uid: core/get-started/aspnetcore/existing-db
ms.openlocfilehash: afd99d68d2ba25ce58a21dc48d2c7ce27f208807
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="getting-started-with-ef-core-on-aspnet-core-with-an-existing-database"></a><span data-ttu-id="cd963-102">在 ASP.NET Core 上使用 EF Core 搭配現有資料庫的使用者入門</span><span class="sxs-lookup"><span data-stu-id="cd963-102">Getting Started with EF Core on ASP.NET Core with an Existing Database</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="cd963-103">[.NET Core SDK](https://www.microsoft.com/net/download/core) 已不再支援 `project.json` 或 Visual Studio 2015。</span><span class="sxs-lookup"><span data-stu-id="cd963-103">The [.NET Core SDK](https://www.microsoft.com/net/download/core) no longer supports `project.json` or Visual Studio 2015.</span></span> <span data-ttu-id="cd963-104">針對所有進行 .NET Core 開發的人員，建議[從 project.json 移轉至 csproj](https://docs.microsoft.com/dotnet/articles/core/migration/) 和 [Visual Studio 2017](https://www.visualstudio.com/downloads/)。</span><span class="sxs-lookup"><span data-stu-id="cd963-104">Everyone doing .NET Core development is encouraged to [migrate from project.json to csproj](https://docs.microsoft.com/dotnet/articles/core/migration/) and [Visual Studio 2017](https://www.visualstudio.com/downloads/).</span></span>

<span data-ttu-id="cd963-105">在本逐步解說中，您將建置 ASP.NET Core MVC 應用程式，該應用程式將使用 Entity Framework 執行基本的資料存取。</span><span class="sxs-lookup"><span data-stu-id="cd963-105">In this walkthrough, you will build an ASP.NET Core MVC application that performs basic data access using Entity Framework.</span></span> <span data-ttu-id="cd963-106">您將會使用反向工程，根據現有的資料庫來建立 Entity Framework 模型。</span><span class="sxs-lookup"><span data-stu-id="cd963-106">You will use reverse engineering to create an Entity Framework model based on an existing database.</span></span>

> [!TIP]  
> <span data-ttu-id="cd963-107">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="cd963-107">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb) on GitHub.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cd963-108">必要條件</span><span class="sxs-lookup"><span data-stu-id="cd963-108">Prerequisites</span></span>

<span data-ttu-id="cd963-109">若要完成此逐步解說，必須符合下列必要條件：</span><span class="sxs-lookup"><span data-stu-id="cd963-109">The following prerequisites are needed to complete this walkthrough:</span></span>

* <span data-ttu-id="cd963-110">[Visual Studio 2017 15.3](https://www.visualstudio.com/downloads/) 與這些工作負載：</span><span class="sxs-lookup"><span data-stu-id="cd963-110">[Visual Studio 2017 15.3](https://www.visualstudio.com/downloads/) with these workloads:</span></span>
  * <span data-ttu-id="cd963-111">**ASP.NET 與網頁程式開發** (位在 [Web & Cloud] \(Web 與雲端) 下)</span><span class="sxs-lookup"><span data-stu-id="cd963-111">**ASP.NET and web development** (under **Web & Cloud**)</span></span>
  * <span data-ttu-id="cd963-112">**.NET Core 跨平台開發** (位在 [其他工具組] 下)</span><span class="sxs-lookup"><span data-stu-id="cd963-112">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>
* <span data-ttu-id="cd963-113">[.NET Core 2.0 SDK](https://www.microsoft.com/net/download/core)。</span><span class="sxs-lookup"><span data-stu-id="cd963-113">[.NET Core 2.0 SDK](https://www.microsoft.com/net/download/core).</span></span>
* [<span data-ttu-id="cd963-114">部落格資料庫</span><span class="sxs-lookup"><span data-stu-id="cd963-114">Blogging database</span></span>](#blogging-database)

### <a name="blogging-database"></a><span data-ttu-id="cd963-115">部落格資料庫</span><span class="sxs-lookup"><span data-stu-id="cd963-115">Blogging database</span></span>

<span data-ttu-id="cd963-116">本教學課程使用您 LocalDb 執行個體上的**部落格**資料庫作為現有的資料庫。</span><span class="sxs-lookup"><span data-stu-id="cd963-116">This tutorial uses a **Blogging** database on your LocalDb instance as the existing database.</span></span>

> [!TIP]  
> <span data-ttu-id="cd963-117">如果您在另一個教學課程中已經建立**部落格**資料庫，則可以跳過這些步驟。</span><span class="sxs-lookup"><span data-stu-id="cd963-117">If you have already created the **Blogging** database as part of another tutorial, you can skip these steps.</span></span>

* <span data-ttu-id="cd963-118">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd963-118">Open Visual Studio</span></span>
* <span data-ttu-id="cd963-119">[工具] > [連線到資料庫]</span><span class="sxs-lookup"><span data-stu-id="cd963-119">**Tools -> Connect to Database...**</span></span>
* <span data-ttu-id="cd963-120">選取 [Microsoft SQL Server]，並按一下 [繼續]</span><span class="sxs-lookup"><span data-stu-id="cd963-120">Select **Microsoft SQL Server** and click **Continue**</span></span>
* <span data-ttu-id="cd963-121">輸入 **(localdb)\mssqllocaldb** 作為**伺服器名稱**</span><span class="sxs-lookup"><span data-stu-id="cd963-121">Enter **(localdb)\mssqllocaldb** as the **Server Name**</span></span>
* <span data-ttu-id="cd963-122">輸入 **master** 作為**資料庫名稱**，並按一下 [確定]</span><span class="sxs-lookup"><span data-stu-id="cd963-122">Enter **master** as the **Database Name** and click **OK**</span></span>
* <span data-ttu-id="cd963-123">master 資料庫現在會顯示在 [伺服器總管] 中的 [資料連線] 下</span><span class="sxs-lookup"><span data-stu-id="cd963-123">The master database is now displayed under **Data Connections** in **Server Explorer**</span></span>
* <span data-ttu-id="cd963-124">以滑鼠右鍵按一下 [伺服器總管] 中的資料庫，並選取 [新增查詢]</span><span class="sxs-lookup"><span data-stu-id="cd963-124">Right-click on the database in **Server Explorer** and select **New Query**</span></span>
* <span data-ttu-id="cd963-125">將下面列出的指令碼複製到查詢編輯器中</span><span class="sxs-lookup"><span data-stu-id="cd963-125">Copy the script, listed below, into the query editor</span></span>
* <span data-ttu-id="cd963-126">以滑鼠右鍵按一下查詢編輯器，然後選取 [執行]</span><span class="sxs-lookup"><span data-stu-id="cd963-126">Right-click on the query editor and select **Execute**</span></span>

[!code-sql[Main](../_shared/create-blogging-database-script.sql)]

## <a name="create-a-new-project"></a><span data-ttu-id="cd963-127">建立新專案</span><span class="sxs-lookup"><span data-stu-id="cd963-127">Create a new project</span></span>

* <span data-ttu-id="cd963-128">開啟 Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="cd963-128">Open Visual Studio 2017</span></span>
* <span data-ttu-id="cd963-129">[檔案] > [新增] > [專案]</span><span class="sxs-lookup"><span data-stu-id="cd963-129">**File -> New -> Project...**</span></span>
* <span data-ttu-id="cd963-130">從左側功能表選取 [已安裝] > [範本] > [Visual C#] > [Web]</span><span class="sxs-lookup"><span data-stu-id="cd963-130">From the left menu select **Installed -> Templates -> Visual C# -> Web**</span></span>
* <span data-ttu-id="cd963-131">選取 [ASP.NET Core Web 應用程式 (.NET Core)] 專案範本</span><span class="sxs-lookup"><span data-stu-id="cd963-131">Select the **ASP.NET Core Web Application (.NET Core)** project template</span></span>
* <span data-ttu-id="cd963-132">輸入 **EFGetStarted.AspNetCore.ExistingDb** 作為名稱，然後按一下 [確定]</span><span class="sxs-lookup"><span data-stu-id="cd963-132">Enter **EFGetStarted.AspNetCore.ExistingDb** as the name and click **OK**</span></span>
* <span data-ttu-id="cd963-133">等候 [新增 ASP.NET Core Web 應用程式] 對話方塊出現</span><span class="sxs-lookup"><span data-stu-id="cd963-133">Wait for the **New ASP.NET Core Web Application** dialog to appear</span></span>
* <span data-ttu-id="cd963-134">在 [ASP.NET Core 範本 2.0] 下，選取 [Web 應用程式 (模型-檢視-控制器)]</span><span class="sxs-lookup"><span data-stu-id="cd963-134">Under **ASP.NET Core Templates 2.0** select the **Web Application (Model-View-Controller)**</span></span>
* <span data-ttu-id="cd963-135">確認 [驗證] 已設為 [無驗證]</span><span class="sxs-lookup"><span data-stu-id="cd963-135">Ensure that **Authentication** is set to **No Authentication**</span></span>
* <span data-ttu-id="cd963-136">按一下 [確定] </span><span class="sxs-lookup"><span data-stu-id="cd963-136">Click **OK**</span></span>

## <a name="install-entity-framework"></a><span data-ttu-id="cd963-137">安裝 Entity Framework</span><span class="sxs-lookup"><span data-stu-id="cd963-137">Install Entity Framework</span></span>

<span data-ttu-id="cd963-138">若要使用 EF Core，請針對您要作為目標的資料庫提供者來安裝套件。</span><span class="sxs-lookup"><span data-stu-id="cd963-138">To use EF Core, install the package for the database provider(s) you want to target.</span></span> <span data-ttu-id="cd963-139">本逐步解說會使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="cd963-139">This walkthrough uses SQL Server.</span></span> <span data-ttu-id="cd963-140">如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="cd963-140">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="cd963-141">[工具] > [NuGet 套件管理員] > [套件管理員主控台]</span><span class="sxs-lookup"><span data-stu-id="cd963-141">**Tools > NuGet Package Manager > Package Manager Console**</span></span>

* <span data-ttu-id="cd963-142">執行 `Install-Package Microsoft.EntityFrameworkCore.SqlServer`</span><span class="sxs-lookup"><span data-stu-id="cd963-142">Run `Install-Package Microsoft.EntityFrameworkCore.SqlServer`</span></span>

<span data-ttu-id="cd963-143">我們將使用一些 Entity Framework Core 工具，以便從資料庫來建立模型。</span><span class="sxs-lookup"><span data-stu-id="cd963-143">We will be using some Entity Framework Tools to create a model from the database.</span></span> <span data-ttu-id="cd963-144">因此，我們也將安裝工具套件：</span><span class="sxs-lookup"><span data-stu-id="cd963-144">So we will install the tools package as well:</span></span>

* <span data-ttu-id="cd963-145">執行 `Install-Package Microsoft.EntityFrameworkCore.Tools`</span><span class="sxs-lookup"><span data-stu-id="cd963-145">Run `Install-Package Microsoft.EntityFrameworkCore.Tools`</span></span>

<span data-ttu-id="cd963-146">我們將使用一些 ASP.NET Core Scaffolding 工具，於稍後建立控制器與檢視。</span><span class="sxs-lookup"><span data-stu-id="cd963-146">We will be using some ASP.NET Core Scaffolding tools to create controllers and views later on.</span></span> <span data-ttu-id="cd963-147">因此，我們也將安裝此設計套件：</span><span class="sxs-lookup"><span data-stu-id="cd963-147">So we will install this design package as well:</span></span>

* <span data-ttu-id="cd963-148">執行 `Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design`</span><span class="sxs-lookup"><span data-stu-id="cd963-148">Run `Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design`</span></span>

## <a name="reverse-engineer-your-model"></a><span data-ttu-id="cd963-149">針對您的模型進行反向工程</span><span class="sxs-lookup"><span data-stu-id="cd963-149">Reverse engineer your model</span></span>

<span data-ttu-id="cd963-150">現在就可以根據您現有的資料庫來建立 EF 模型。</span><span class="sxs-lookup"><span data-stu-id="cd963-150">Now it's time to create the EF model based on your existing database.</span></span>

* <span data-ttu-id="cd963-151">[工具] –> [NuGet 套件管理員] –> [套件管理員主控台]</span><span class="sxs-lookup"><span data-stu-id="cd963-151">**Tools –> NuGet Package Manager –> Package Manager Console**</span></span>
* <span data-ttu-id="cd963-152">執行下列命令，以便從現有的資料庫來建立模型：</span><span class="sxs-lookup"><span data-stu-id="cd963-152">Run the following command to create a model from the existing database:</span></span>

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

<span data-ttu-id="cd963-153">如果您收到 `The term 'Scaffold-DbContext' is not recognized as the name of a cmdlet` 的錯誤，請關閉再重新開啟 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="cd963-153">If you receive an error stating `The term 'Scaffold-DbContext' is not recognized as the name of a cmdlet`, then close and reopen Visual Studio.</span></span>

> [!TIP]  
> <span data-ttu-id="cd963-154">您可以將 `-Tables` 引數加入上述命令，以指定要為哪些資料表產生實體。</span><span class="sxs-lookup"><span data-stu-id="cd963-154">You can specify which tables you want to generate entities for by adding the `-Tables` argument to the command above.</span></span> <span data-ttu-id="cd963-155">例如</span><span class="sxs-lookup"><span data-stu-id="cd963-155">E.g.</span></span> <span data-ttu-id="cd963-156">`-Tables Blog,Post`.</span><span class="sxs-lookup"><span data-stu-id="cd963-156">`-Tables Blog,Post`.</span></span>

<span data-ttu-id="cd963-157">反向工程程序會根據現有資料庫的結構描述，建立實體類別 (`Blog.cs` & `Post.cs`) 和衍生的內容 (`BloggingContext.cs`)。</span><span class="sxs-lookup"><span data-stu-id="cd963-157">The reverse engineer process created entity classes (`Blog.cs` & `Post.cs`) and a derived context (`BloggingContext.cs`) based on the schema of the existing database.</span></span>

 <span data-ttu-id="cd963-158">實體類別是簡單的 C# 物件，代表您要查詢和儲存的資料。</span><span class="sxs-lookup"><span data-stu-id="cd963-158">The entity classes are simple C# objects that represent the data you will be querying and saving.</span></span>

 [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Models/Blog.cs)]

 <span data-ttu-id="cd963-159">內容代表資料庫的工作階段，並可讓您查詢和儲存實體類別的執行個體。</span><span class="sxs-lookup"><span data-stu-id="cd963-159">The context represents a session with the database and allows you to query and save instances of the entity classes.</span></span>

<!-- Static code listing, rather than a linked file, because the walkthrough modifies the context file heavily -->
 ``` csharp
public partial class BloggingContext : DbContext
{
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

## <a name="register-your-context-with-dependency-injection"></a><span data-ttu-id="cd963-160">使用相依性插入來註冊內容</span><span class="sxs-lookup"><span data-stu-id="cd963-160">Register your context with dependency injection</span></span>

<span data-ttu-id="cd963-161">相依性插入的概念是 ASP.NET Core 的核心。</span><span class="sxs-lookup"><span data-stu-id="cd963-161">The concept of dependency injection is central to ASP.NET Core.</span></span> <span data-ttu-id="cd963-162">服務 (例如 `BloggingContext`) 是在應用程式啟動期間使用相依性插入來註冊。</span><span class="sxs-lookup"><span data-stu-id="cd963-162">Services (such as `BloggingContext`) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="cd963-163">接著，會透過建構函式參數或屬性，針對需要這些服務的元件 (例如 MVC 控制器) 來提供服務。</span><span class="sxs-lookup"><span data-stu-id="cd963-163">Components that require these services (such as your MVC controllers) are then provided these services via constructor parameters or properties.</span></span> <span data-ttu-id="cd963-164">如需相依性插入的詳細資訊，請參閱 ASP.NET 網站上的[相依性插入](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html)文章。</span><span class="sxs-lookup"><span data-stu-id="cd963-164">For more information on dependency injection see the [Dependency Injection](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html) article on the ASP.NET site.</span></span>

### <a name="remove-inline-context-configuration"></a><span data-ttu-id="cd963-165">移除內嵌內容設定</span><span class="sxs-lookup"><span data-stu-id="cd963-165">Remove inline context configuration</span></span>

<span data-ttu-id="cd963-166">在 ASP.NET Core 中，通常是在 **Startup.cs** 中執行設定。</span><span class="sxs-lookup"><span data-stu-id="cd963-166">In ASP.NET Core, configuration is generally performed in **Startup.cs**.</span></span> <span data-ttu-id="cd963-167">為了遵循此模式，我們要將資料庫提供者的設定移至 **Startup.cs**。</span><span class="sxs-lookup"><span data-stu-id="cd963-167">To conform to this pattern, we will move configuration of the database provider to **Startup.cs**.</span></span>

* <span data-ttu-id="cd963-168">開啟 `Models\BloggingContext.cs`</span><span class="sxs-lookup"><span data-stu-id="cd963-168">Open `Models\BloggingContext.cs`</span></span>
* <span data-ttu-id="cd963-169">刪除 `OnConfiguring(...)` 方法</span><span class="sxs-lookup"><span data-stu-id="cd963-169">Delete the `OnConfiguring(...)` method</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    #warning To protect potentially sensitive information in your connection string, you should move it out of source code. See http://go.microsoft.com/fwlink/?LinkId=723263 for guidance on storing connection strings.
    optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;");
}
```

* <span data-ttu-id="cd963-170">新增下列建構函式，以藉由相依性插入將設定傳遞到內容中</span><span class="sxs-lookup"><span data-stu-id="cd963-170">Add the following constructor, which will allow configuration to be passed into the context by dependency injection</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Models/BloggingContext.cs#Constructor)]

### <a name="register-and-configure-your-context-in-startupcs"></a><span data-ttu-id="cd963-171">在 Startup.cs 中註冊並設定內容</span><span class="sxs-lookup"><span data-stu-id="cd963-171">Register and configure your context in Startup.cs</span></span>

<span data-ttu-id="cd963-172">為了讓 MVC 控制器能使用 `BloggingContext`，我們要將它註冊為服務。</span><span class="sxs-lookup"><span data-stu-id="cd963-172">In order for our MVC controllers to make use of `BloggingContext` we are going to register it as a service.</span></span>

* <span data-ttu-id="cd963-173">開啟 **Startup.cs**</span><span class="sxs-lookup"><span data-stu-id="cd963-173">Open **Startup.cs**</span></span>
* <span data-ttu-id="cd963-174">在檔案開頭加入下列 `using` 陳述式</span><span class="sxs-lookup"><span data-stu-id="cd963-174">Add the following `using` statements at the start of the file</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Startup.cs#AddedUsings)]

<span data-ttu-id="cd963-175">現在，我們可以使用 `AddDbContext(...)` 方法以將它註冊為服務。</span><span class="sxs-lookup"><span data-stu-id="cd963-175">Now we can use the `AddDbContext(...)` method to register it as a service.</span></span>
* <span data-ttu-id="cd963-176">找出 `ConfigureServices(...)` 方法</span><span class="sxs-lookup"><span data-stu-id="cd963-176">Locate the `ConfigureServices(...)` method</span></span>
* <span data-ttu-id="cd963-177">加入下列程式碼以將內容註冊為服務</span><span class="sxs-lookup"><span data-stu-id="cd963-177">Add the following code to register the context as a service</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Startup.cs?name=ConfigureServices&highlight=7-8)]

> [!TIP]  
> <span data-ttu-id="cd963-178">在實際的應用程式中，您通常會將連接字串放到設定檔中。</span><span class="sxs-lookup"><span data-stu-id="cd963-178">In a real application you would typically put the connection string in a configuration file.</span></span> <span data-ttu-id="cd963-179">為了簡單起見，我們在程式碼中加以定義。</span><span class="sxs-lookup"><span data-stu-id="cd963-179">For the sake of simplicity, we are defining it in code.</span></span> <span data-ttu-id="cd963-180">如需詳細資訊，請參閱[連接字串](../../miscellaneous/connection-strings.md)。</span><span class="sxs-lookup"><span data-stu-id="cd963-180">For more information, see [Connection Strings](../../miscellaneous/connection-strings.md).</span></span>

## <a name="create-a-controller"></a><span data-ttu-id="cd963-181">建立控制器</span><span class="sxs-lookup"><span data-stu-id="cd963-181">Create a controller</span></span>

<span data-ttu-id="cd963-182">接下來，我們將在專案中啟用 Scaffolding。</span><span class="sxs-lookup"><span data-stu-id="cd963-182">Next, we'll enable scaffolding in our project.</span></span>

* <span data-ttu-id="cd963-183">在 [方案總管] 中以滑鼠右鍵按一下 [Controllers] 資料夾，然後選取 [新增] > [控制器]</span><span class="sxs-lookup"><span data-stu-id="cd963-183">Right-click on the **Controllers** folder in **Solution Explorer** and select **Add -> Controller...**</span></span>
* <span data-ttu-id="cd963-184">選取 [完整相依性]，然後按一下 [新增]</span><span class="sxs-lookup"><span data-stu-id="cd963-184">Select **Full Dependencies** and click **Add**</span></span>
* <span data-ttu-id="cd963-185">在開啟的 `ScaffoldingReadMe.txt` 檔案中，您可以忽略其中的指示</span><span class="sxs-lookup"><span data-stu-id="cd963-185">You can ignore the instructions in the `ScaffoldingReadMe.txt` file that opens</span></span>

<span data-ttu-id="cd963-186">啟用 Scaffolding 之後，就可以建立 `Blog` 實體的控制器結構。</span><span class="sxs-lookup"><span data-stu-id="cd963-186">Now that scaffolding is enabled, we can scaffold a controller for the `Blog` entity.</span></span>

* <span data-ttu-id="cd963-187">在 [方案總管] 中以滑鼠右鍵按一下 [Controllers] 資料夾，然後選取 [新增] > [控制器]</span><span class="sxs-lookup"><span data-stu-id="cd963-187">Right-click on the **Controllers** folder in **Solution Explorer** and select **Add -> Controller...**</span></span>
* <span data-ttu-id="cd963-188">選取 [使用 Entity Framework 執行檢視的 MVC 控制器] 並按一下 [確定]</span><span class="sxs-lookup"><span data-stu-id="cd963-188">Select **MVC Controller with views, using Entity Framework** and click **Ok**</span></span>
* <span data-ttu-id="cd963-189">將 [模型類別] 設為 [Blog]，並將 [資料內容類別] 設為 [BloggingContext]</span><span class="sxs-lookup"><span data-stu-id="cd963-189">Set **Model class** to **Blog** and **Data context class** to **BloggingContext**</span></span>
* <span data-ttu-id="cd963-190">按一下 [新增]</span><span class="sxs-lookup"><span data-stu-id="cd963-190">Click **Add**</span></span>

## <a name="run-the-application"></a><span data-ttu-id="cd963-191">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="cd963-191">Run the application</span></span>

<span data-ttu-id="cd963-192">您現在可以執行應用程式來查看運作狀況。</span><span class="sxs-lookup"><span data-stu-id="cd963-192">You can now run the application to see it in action.</span></span>

* <span data-ttu-id="cd963-193">[偵錯] > [啟動但不偵錯]</span><span class="sxs-lookup"><span data-stu-id="cd963-193">**Debug -> Start Without Debugging**</span></span>
* <span data-ttu-id="cd963-194">系統將會建置應用程式並在網頁瀏覽器中開啟</span><span class="sxs-lookup"><span data-stu-id="cd963-194">The application will build and open in a web browser</span></span>
* <span data-ttu-id="cd963-195">巡覽至 `/Blogs`</span><span class="sxs-lookup"><span data-stu-id="cd963-195">Navigate to `/Blogs`</span></span>
* <span data-ttu-id="cd963-196">按一下 [新建]</span><span class="sxs-lookup"><span data-stu-id="cd963-196">Click **Create New**</span></span>
* <span data-ttu-id="cd963-197">輸入新部落格的 **URL**，然後按一下 [建立]</span><span class="sxs-lookup"><span data-stu-id="cd963-197">Enter a **Url** for the new blog and click **Create**</span></span>

![image](_static/create.png)

![image](_static/index-existing-db.png)
