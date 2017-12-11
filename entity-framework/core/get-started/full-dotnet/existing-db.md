---
title: ".NET Framework 快速入門 - 現有的資料庫 - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: a29a3d97-b2d8-4d33-9475-40ac67b3b2c6
ms.technology: entity-framework-core
uid: core/get-started/full-dotnet/existing-db
ms.openlocfilehash: 3cd69109e3cf8dbc103f9eea6e2553df17f29a98
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="getting-started-with-ef-core-on-net-framework-with-an-existing-database"></a><span data-ttu-id="d624f-102">在 .NET Framework 上使用 EF Core 搭配現有資料庫的使用者入門</span><span class="sxs-lookup"><span data-stu-id="d624f-102">Getting started with EF Core on .NET Framework with an Existing Database</span></span>

<span data-ttu-id="d624f-103">在本逐步解說中，您將建置主控台應用程式，該應用程式將使用 Entity Framework 對 Microsoft SQL Server 資料庫執行基本資料存取。</span><span class="sxs-lookup"><span data-stu-id="d624f-103">In this walkthrough, you will build a console application that performs basic data access against a Microsoft SQL Server database using Entity Framework.</span></span> <span data-ttu-id="d624f-104">您將會使用反向工程，根據現有的資料庫來建立 Entity Framework 模型。</span><span class="sxs-lookup"><span data-stu-id="d624f-104">You will use reverse engineering to create an Entity Framework model based on an existing database.</span></span>

> [!TIP]  
> <span data-ttu-id="d624f-105">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="d624f-105">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb) on GitHub.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d624f-106">必要條件</span><span class="sxs-lookup"><span data-stu-id="d624f-106">Prerequisites</span></span>

<span data-ttu-id="d624f-107">若要完成此逐步解說，必須符合下列必要條件：</span><span class="sxs-lookup"><span data-stu-id="d624f-107">The following prerequisites are needed to complete this walkthrough:</span></span>

* [<span data-ttu-id="d624f-108">Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="d624f-108">Visual Studio 2017</span></span>](https://www.visualstudio.com/downloads/)

* <span data-ttu-id="d624f-109">[最新版本的 NuGet 套件管理員](https://dist.nuget.org/index.html) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="d624f-109">[Latest version of NuGet Package Manager](https://dist.nuget.org/index.html)</span></span>

* [<span data-ttu-id="d624f-110">最新版本的 Windows PowerShell</span><span class="sxs-lookup"><span data-stu-id="d624f-110">Latest version of Windows PowerShell</span></span>](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell)

* [<span data-ttu-id="d624f-111">部落格資料庫</span><span class="sxs-lookup"><span data-stu-id="d624f-111">Blogging database</span></span>](#blogging-database)

### <a name="blogging-database"></a><span data-ttu-id="d624f-112">部落格資料庫</span><span class="sxs-lookup"><span data-stu-id="d624f-112">Blogging database</span></span>

<span data-ttu-id="d624f-113">本教學課程使用您 LocalDb 執行個體上的**部落格**資料庫作為現有的資料庫。</span><span class="sxs-lookup"><span data-stu-id="d624f-113">This tutorial uses a **Blogging** database on your LocalDb instance as the existing database.</span></span>

> [!TIP]  
> <span data-ttu-id="d624f-114">如果您在另一個教學課程中已經建立**部落格**資料庫，則可以跳過這些步驟。</span><span class="sxs-lookup"><span data-stu-id="d624f-114">If you have already created the **Blogging** database as part of another tutorial, you can skip these steps.</span></span>

* <span data-ttu-id="d624f-115">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d624f-115">Open Visual Studio</span></span>

* <span data-ttu-id="d624f-116">[工具] > [連線到資料庫]...</span><span class="sxs-lookup"><span data-stu-id="d624f-116">Tools > Connect to Database...</span></span>

* <span data-ttu-id="d624f-117">選取 [Microsoft SQL Server]，並按一下 [繼續]</span><span class="sxs-lookup"><span data-stu-id="d624f-117">Select **Microsoft SQL Server** and click **Continue**</span></span>

* <span data-ttu-id="d624f-118">輸入 **(localdb)\mssqllocaldb** 作為**伺服器名稱**</span><span class="sxs-lookup"><span data-stu-id="d624f-118">Enter **(localdb)\mssqllocaldb** as the **Server Name**</span></span>

* <span data-ttu-id="d624f-119">輸入 **master** 作為**資料庫名稱**，並按一下 [確定]</span><span class="sxs-lookup"><span data-stu-id="d624f-119">Enter **master** as the **Database Name** and click **OK**</span></span>

* <span data-ttu-id="d624f-120">master 資料庫現在會顯示在 [伺服器總管] 中的 [資料連線] 下</span><span class="sxs-lookup"><span data-stu-id="d624f-120">The master database is now displayed under **Data Connections** in **Server Explorer**</span></span>

* <span data-ttu-id="d624f-121">以滑鼠右鍵按一下 [伺服器總管] 中的資料庫，並選取 [新增查詢]</span><span class="sxs-lookup"><span data-stu-id="d624f-121">Right-click on the database in **Server Explorer** and select **New Query**</span></span>

* <span data-ttu-id="d624f-122">將下面列出的指令碼複製到查詢編輯器中</span><span class="sxs-lookup"><span data-stu-id="d624f-122">Copy the script, listed below, into the query editor</span></span>

* <span data-ttu-id="d624f-123">以滑鼠右鍵按一下查詢編輯器，然後選取 [執行]</span><span class="sxs-lookup"><span data-stu-id="d624f-123">Right-click on the query editor and select **Execute**</span></span>

[!code-sql[Main](../_shared/create-blogging-database-script.sql)]

## <a name="create-a-new-project"></a><span data-ttu-id="d624f-124">建立新專案</span><span class="sxs-lookup"><span data-stu-id="d624f-124">Create a new project</span></span>

* <span data-ttu-id="d624f-125">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d624f-125">Open Visual Studio</span></span>

* <span data-ttu-id="d624f-126">[檔案] > [新增] > [專案]...</span><span class="sxs-lookup"><span data-stu-id="d624f-126">File > New > Project...</span></span>

* <span data-ttu-id="d624f-127">從左側功能表選取 [範本] > [Visual C#] > [Windows]</span><span class="sxs-lookup"><span data-stu-id="d624f-127">From the left menu select Templates > Visual C# > Windows</span></span>

* <span data-ttu-id="d624f-128">選取 [主控台應用程式] 專案範本</span><span class="sxs-lookup"><span data-stu-id="d624f-128">Select the **Console Application** project template</span></span>

* <span data-ttu-id="d624f-129">請確認您的目標是 **.NET Framework 4.5.1** 或更新版本</span><span class="sxs-lookup"><span data-stu-id="d624f-129">Ensure you are targeting **.NET Framework 4.5.1** or later</span></span>

* <span data-ttu-id="d624f-130">提供專案名稱，然後按一下 [確定]</span><span class="sxs-lookup"><span data-stu-id="d624f-130">Give the project a name and click **OK**</span></span>

## <a name="install-entity-framework"></a><span data-ttu-id="d624f-131">安裝 Entity Framework</span><span class="sxs-lookup"><span data-stu-id="d624f-131">Install Entity Framework</span></span>

<span data-ttu-id="d624f-132">若要使用 EF Core，請針對您要作為目標的資料庫提供者來安裝套件。</span><span class="sxs-lookup"><span data-stu-id="d624f-132">To use EF Core, install the package for the database provider(s) you want to target.</span></span> <span data-ttu-id="d624f-133">本逐步解說會使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="d624f-133">This walkthrough uses SQL Server.</span></span> <span data-ttu-id="d624f-134">如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="d624f-134">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="d624f-135">[工具] > [NuGet 套件管理員] > [套件管理員主控台]</span><span class="sxs-lookup"><span data-stu-id="d624f-135">Tools > NuGet Package Manager > Package Manager Console</span></span>

* <span data-ttu-id="d624f-136">執行 `Install-Package Microsoft.EntityFrameworkCore.SqlServer`</span><span class="sxs-lookup"><span data-stu-id="d624f-136">Run `Install-Package Microsoft.EntityFrameworkCore.SqlServer`</span></span>

<span data-ttu-id="d624f-137">為了要從現有的資料庫啟用反向工程，我們也需要安裝幾個其他的套件。</span><span class="sxs-lookup"><span data-stu-id="d624f-137">To enable reverse engineering from an existing database we need to install a couple of other packages too.</span></span>

* <span data-ttu-id="d624f-138">執行 `Install-Package Microsoft.EntityFrameworkCore.Tools`</span><span class="sxs-lookup"><span data-stu-id="d624f-138">Run `Install-Package Microsoft.EntityFrameworkCore.Tools`</span></span>

## <a name="reverse-engineer-your-model"></a><span data-ttu-id="d624f-139">針對您的模型進行反向工程</span><span class="sxs-lookup"><span data-stu-id="d624f-139">Reverse engineer your model</span></span>

<span data-ttu-id="d624f-140">現在就可以根據您現有的資料庫來建立 EF 模型。</span><span class="sxs-lookup"><span data-stu-id="d624f-140">Now it's time to create the EF model based on your existing database.</span></span>

* <span data-ttu-id="d624f-141">[工具] –> [NuGet 套件管理員] –> [套件管理員主控台]</span><span class="sxs-lookup"><span data-stu-id="d624f-141">Tools –> NuGet Package Manager –> Package Manager Console</span></span>

* <span data-ttu-id="d624f-142">執行下列命令，以便從現有的資料庫來建立模型</span><span class="sxs-lookup"><span data-stu-id="d624f-142">Run the following command to create a model from the existing database</span></span>

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="d624f-143">反向工程程序會根據現有資料庫的結構描述，建立實體類別和衍生的內容。</span><span class="sxs-lookup"><span data-stu-id="d624f-143">The reverse engineer process created entity classes and a derived context based on the schema of the existing database.</span></span> <span data-ttu-id="d624f-144">實體類別是簡單的 C# 物件，代表您要查詢和儲存的資料。</span><span class="sxs-lookup"><span data-stu-id="d624f-144">The entity classes are simple C# objects that represent the data you will be querying and saving.</span></span>

<!-- [!code-csharp[Main](samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/Blog.cs)] -->
``` csharp
using System;
using System.Collections.Generic;

namespace EFGetStarted.ConsoleApp.ExistingDb
{
    public partial class Blog
    {
        public Blog()
        {
            Post = new HashSet<Post>();
        }

        public int BlogId { get; set; }
        public string Url { get; set; }

        public virtual ICollection<Post> Post { get; set; }
    }
}
```

<span data-ttu-id="d624f-145">內容代表資料庫的工作階段，並可讓您查詢和儲存實體類別的執行個體。</span><span class="sxs-lookup"><span data-stu-id="d624f-145">The context represents a session with the database and allows you to query and save instances of the entity classes.</span></span>

<!-- [!code-csharp[Main](samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/BloggingContext.cs)] -->
``` csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Metadata;

namespace EFGetStarted.ConsoleApp.ExistingDb
{
    public partial class BloggingContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            #warning To protect potentially sensitive information in your connection string, you should move it out of source code. See http://go.microsoft.com/fwlink/?LinkId=723263 for guidance on storing connection strings.
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;");
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

        public virtual DbSet<Blog> Blog { get; set; }
        public virtual DbSet<Post> Post { get; set; }
    }
}
```

## <a name="use-your-model"></a><span data-ttu-id="d624f-146">使用您的模型</span><span class="sxs-lookup"><span data-stu-id="d624f-146">Use your model</span></span>

<span data-ttu-id="d624f-147">您現在可以使用您的模型來執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="d624f-147">You can now use your model to perform data access.</span></span>

* <span data-ttu-id="d624f-148">開啟 *Program.cs*</span><span class="sxs-lookup"><span data-stu-id="d624f-148">Open *Program.cs*</span></span>

* <span data-ttu-id="d624f-149">以下列程式碼來取代檔案的內容</span><span class="sxs-lookup"><span data-stu-id="d624f-149">Replace the contents of the file with the following code</span></span>

<!-- [!code-csharp[Main](samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/Program.cs)] -->
``` csharp
using System;

namespace EFGetStarted.ConsoleApp.ExistingDb
{
    class Program
    {
        static void Main(string[] args)
        {
            using (var db = new BloggingContext())
            {
                db.Blog.Add(new Blog { Url = "http://blogs.msdn.com/adonet" });
                var count = db.SaveChanges();
                Console.WriteLine("{0} records saved to database", count);

                Console.WriteLine();
                Console.WriteLine("All blogs in database:");
                foreach (var blog in db.Blog)
                {
                    Console.WriteLine(" - {0}", blog.Url);
                }
            }
        }
    }
}
```

* <span data-ttu-id="d624f-150">偵錯 > 啟動但不偵錯</span><span class="sxs-lookup"><span data-stu-id="d624f-150">Debug > Start Without Debugging</span></span>

<span data-ttu-id="d624f-151">您會看到有一個部落格儲存至資料庫，然後所有部落格的詳細資料會列印至主控台。</span><span class="sxs-lookup"><span data-stu-id="d624f-151">You will see that one blog is saved to the database and then the details of all blogs are printed to the console.</span></span>

![image](_static/output-existing-db.png)
