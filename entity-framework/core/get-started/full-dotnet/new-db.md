---
title: .NET Framework 快速入門 - 新資料庫 - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 52b69727-ded9-4a7b-b8d5-73f3acfbbad3
ms.technology: entity-framework-core
uid: core/get-started/full-dotnet/new-db
ms.openlocfilehash: bd7054c6834ae11bfdc352d63654e4304771e432
ms.sourcegitcommit: 507a40ed050fee957bcf8cf05f6e0ec8a3b1a363
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2018
---
# <a name="getting-started-with-ef-core-on-net-framework-with-a-new-database"></a><span data-ttu-id="cbde6-102">在 .NET Framework 上使用 EF Core 搭配新資料庫的使用者入門</span><span class="sxs-lookup"><span data-stu-id="cbde6-102">Getting started with EF Core on .NET Framework with a New Database</span></span>

<span data-ttu-id="cbde6-103">在本逐步解說中，您將建置主控台應用程式，該應用程式將使用 Entity Framework 對 Microsoft SQL Server 資料庫執行基本資料存取。</span><span class="sxs-lookup"><span data-stu-id="cbde6-103">In this walkthrough, you will build a console application that performs basic data access against a Microsoft SQL Server database using Entity Framework.</span></span> <span data-ttu-id="cbde6-104">您將使用移轉，以便從您的模型來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="cbde6-104">You will use migrations to create the database from your model.</span></span>

> [!TIP]  
> <span data-ttu-id="cbde6-105">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/FullNet/ConsoleApp.NewDb) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="cbde6-105">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/FullNet/ConsoleApp.NewDb) on GitHub.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cbde6-106">必要條件</span><span class="sxs-lookup"><span data-stu-id="cbde6-106">Prerequisites</span></span>

<span data-ttu-id="cbde6-107">若要完成此逐步解說，必須符合下列必要條件：</span><span class="sxs-lookup"><span data-stu-id="cbde6-107">The following prerequisites are needed to complete this walkthrough:</span></span>

* [<span data-ttu-id="cbde6-108">Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="cbde6-108">Visual Studio 2017</span></span>](https://www.visualstudio.com/downloads/)

* <span data-ttu-id="cbde6-109">[最新版本的 NuGet 套件管理員](https://dist.nuget.org/index.html) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="cbde6-109">[Latest version of NuGet Package Manager](https://dist.nuget.org/index.html)</span></span>

* [<span data-ttu-id="cbde6-110">最新版本的 Windows PowerShell</span><span class="sxs-lookup"><span data-stu-id="cbde6-110">Latest version of Windows PowerShell</span></span>](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell)

## <a name="create-a-new-project"></a><span data-ttu-id="cbde6-111">建立新專案</span><span class="sxs-lookup"><span data-stu-id="cbde6-111">Create a new project</span></span>

* <span data-ttu-id="cbde6-112">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cbde6-112">Open Visual Studio</span></span>

* <span data-ttu-id="cbde6-113">[檔案] > [新增] > [專案]...</span><span class="sxs-lookup"><span data-stu-id="cbde6-113">File > New > Project...</span></span>

* <span data-ttu-id="cbde6-114">從左側功能表選取 [範本] > [Visual C#] > [Windows 傳統桌面]</span><span class="sxs-lookup"><span data-stu-id="cbde6-114">From the left menu select Templates > Visual C# > Windows Classic Desktop</span></span>

* <span data-ttu-id="cbde6-115">選取 [主控台應用程式 (.NET Framework)] 專案範本</span><span class="sxs-lookup"><span data-stu-id="cbde6-115">Select the **Console App (.NET Framework)** project template</span></span>

* <span data-ttu-id="cbde6-116">請確認您的目標是 **.NET Framework 4.5.1** 或更新版本</span><span class="sxs-lookup"><span data-stu-id="cbde6-116">Ensure you are targeting **.NET Framework 4.5.1** or later</span></span>

* <span data-ttu-id="cbde6-117">提供專案名稱，然後按一下 [確定]</span><span class="sxs-lookup"><span data-stu-id="cbde6-117">Give the project a name and click **OK**</span></span>

## <a name="install-entity-framework"></a><span data-ttu-id="cbde6-118">安裝 Entity Framework</span><span class="sxs-lookup"><span data-stu-id="cbde6-118">Install Entity Framework</span></span>

<span data-ttu-id="cbde6-119">若要使用 EF Core，請針對您要作為目標的資料庫提供者來安裝套件。</span><span class="sxs-lookup"><span data-stu-id="cbde6-119">To use EF Core, install the package for the database provider(s) you want to target.</span></span> <span data-ttu-id="cbde6-120">本逐步解說會使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="cbde6-120">This walkthrough uses SQL Server.</span></span> <span data-ttu-id="cbde6-121">如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="cbde6-121">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="cbde6-122">[工具] > [NuGet 套件管理員] > [套件管理員主控台]</span><span class="sxs-lookup"><span data-stu-id="cbde6-122">Tools > NuGet Package Manager > Package Manager Console</span></span>

* <span data-ttu-id="cbde6-123">執行 `Install-Package Microsoft.EntityFrameworkCore.SqlServer`</span><span class="sxs-lookup"><span data-stu-id="cbde6-123">Run `Install-Package Microsoft.EntityFrameworkCore.SqlServer`</span></span>

<span data-ttu-id="cbde6-124">稍後在本逐步解說中，我們將使用部分 Entity Framework 工具來維護資料庫。</span><span class="sxs-lookup"><span data-stu-id="cbde6-124">Later in this walkthrough we will also be using some Entity Framework Tools to maintain the database.</span></span> <span data-ttu-id="cbde6-125">因此，我們也將安裝工具套件。</span><span class="sxs-lookup"><span data-stu-id="cbde6-125">So we will install the tools package as well.</span></span>

* <span data-ttu-id="cbde6-126">執行 `Install-Package Microsoft.EntityFrameworkCore.Tools`</span><span class="sxs-lookup"><span data-stu-id="cbde6-126">Run `Install-Package Microsoft.EntityFrameworkCore.Tools`</span></span>

## <a name="create-your-model"></a><span data-ttu-id="cbde6-127">建立您的模型</span><span class="sxs-lookup"><span data-stu-id="cbde6-127">Create your model</span></span>

<span data-ttu-id="cbde6-128">現在就來定義組成模型的內容和實體類別。</span><span class="sxs-lookup"><span data-stu-id="cbde6-128">Now it's time to define a context and entity classes that make up your model.</span></span>

* <span data-ttu-id="cbde6-129">[專案] > [加入類別]...</span><span class="sxs-lookup"><span data-stu-id="cbde6-129">Project > Add Class...</span></span>

* <span data-ttu-id="cbde6-130">輸入 *Model.cs* 作為名稱，然後按一下 [確定]</span><span class="sxs-lookup"><span data-stu-id="cbde6-130">Enter *Model.cs* as the name and click **OK**</span></span>

* <span data-ttu-id="cbde6-131">以下列程式碼來取代檔案的內容</span><span class="sxs-lookup"><span data-stu-id="cbde6-131">Replace the contents of the file with the following code</span></span>

<!-- [!code-csharp[Main](samples/core/GetStarted/FullNet/ConsoleApp.NewDb/Model.cs)] -->
``` csharp
using Microsoft.EntityFrameworkCore;
using System.Collections.Generic;

namespace EFGetStarted.ConsoleApp
{
    public class BloggingContext : DbContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;");
        }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Url { get; set; }

        public List<Post> Posts { get; set; }
    }

    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
}
```

> [!TIP]  
> <span data-ttu-id="cbde6-132">在實際的應用程式中，您會將每個類別放在個別的檔案中，然後將連接字串放在 `App.Config` 檔案中，並使用 `ConfigurationManager` 來讀取。</span><span class="sxs-lookup"><span data-stu-id="cbde6-132">In a real application you would put each class in a separate file and put the connection string in the `App.Config` file and read it out using `ConfigurationManager`.</span></span> <span data-ttu-id="cbde6-133">為了簡單起見，我們在此教學課程中將所有項目都放在單一程式碼檔案中。</span><span class="sxs-lookup"><span data-stu-id="cbde6-133">For the sake of simplicity, we are putting everything in a single code file for this tutorial.</span></span>

## <a name="create-your-database"></a><span data-ttu-id="cbde6-134">建立您的資料庫</span><span class="sxs-lookup"><span data-stu-id="cbde6-134">Create your database</span></span>

<span data-ttu-id="cbde6-135">一旦有模型之後，就可以使用移轉來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="cbde6-135">Now that you have a model, you can use migrations to create a database for you.</span></span>

* <span data-ttu-id="cbde6-136">[工具] –> [NuGet 套件管理員] –> [套件管理員主控台]</span><span class="sxs-lookup"><span data-stu-id="cbde6-136">Tools –> NuGet Package Manager –> Package Manager Console</span></span>

* <span data-ttu-id="cbde6-137">執行 `Add-Migration MyFirstMigration` 來建立移轉的結構，以針對您的模型建立一組初始的資料表。</span><span class="sxs-lookup"><span data-stu-id="cbde6-137">Run `Add-Migration MyFirstMigration` to scaffold a migration to create the initial set of tables for your model.</span></span>

* <span data-ttu-id="cbde6-138">執行 `Update-Database` 以將新的移轉套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="cbde6-138">Run `Update-Database` to apply the new migration to the database.</span></span> <span data-ttu-id="cbde6-139">因為您的資料庫尚未存在，因此會在套用移轉之前為您建立。</span><span class="sxs-lookup"><span data-stu-id="cbde6-139">Because your database doesn't exist yet, it will be created for you before the migration is applied.</span></span>

> [!TIP]  
> <span data-ttu-id="cbde6-140">如果日後要對您的模型進行變更，則可以使用 `Add-Migration` 命令來建立新移轉的結構，以對資料庫做出相對應的結構描述變更。</span><span class="sxs-lookup"><span data-stu-id="cbde6-140">If you make future changes to your model, you can use the `Add-Migration` command to scaffold a new migration to make the corresponding schema changes to the database.</span></span> <span data-ttu-id="cbde6-141">檢查完已建立結構的程式碼之後 (並進行任何必要的變更)，就可以使用 `Update-Database` 命令將變更套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="cbde6-141">Once you have checked the scaffolded code (and made any required changes), you can use the `Update-Database` command to apply the changes to the database.</span></span>
>
><span data-ttu-id="cbde6-142">EF 在資料庫中使用 `__EFMigrationsHistory` 資料表，以確認已經套用至資料庫的移轉。</span><span class="sxs-lookup"><span data-stu-id="cbde6-142">EF uses a `__EFMigrationsHistory` table in the database to keep track of which migrations have already been applied to the database.</span></span>

## <a name="use-your-model"></a><span data-ttu-id="cbde6-143">使用您的模型</span><span class="sxs-lookup"><span data-stu-id="cbde6-143">Use your model</span></span>

<span data-ttu-id="cbde6-144">您現在可以使用您的模型來執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="cbde6-144">You can now use your model to perform data access.</span></span>

* <span data-ttu-id="cbde6-145">開啟 *Program.cs*</span><span class="sxs-lookup"><span data-stu-id="cbde6-145">Open *Program.cs*</span></span>

* <span data-ttu-id="cbde6-146">以下列程式碼來取代檔案的內容</span><span class="sxs-lookup"><span data-stu-id="cbde6-146">Replace the contents of the file with the following code</span></span>

<!-- [!code-csharp[Main](samples/core/GetStarted/FullNet/ConsoleApp.NewDb/Program.cs)] -->
``` csharp
using System;

namespace EFGetStarted.ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            using (var db = new BloggingContext())
            {
                db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/adonet" });
                var count = db.SaveChanges();
                Console.WriteLine("{0} records saved to database", count);

                Console.WriteLine();
                Console.WriteLine("All blogs in database:");
                foreach (var blog in db.Blogs)
                {
                    Console.WriteLine(" - {0}", blog.Url);
                }
            }
        }
    }
}
```

* <span data-ttu-id="cbde6-147">偵錯 > 啟動但不偵錯</span><span class="sxs-lookup"><span data-stu-id="cbde6-147">Debug > Start Without Debugging</span></span>

<span data-ttu-id="cbde6-148">您會看到有一個部落格儲存至資料庫，然後所有部落格的詳細資料會列印至主控台。</span><span class="sxs-lookup"><span data-stu-id="cbde6-148">You will see that one blog is saved to the database and then the details of all blogs are printed to the console.</span></span>

![影像](_static/output-new-db.png)
