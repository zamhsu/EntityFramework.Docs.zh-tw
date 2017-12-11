---
title: ".NET Core 使用者入門 - 新資料庫 - EF Core"
author: rick-anderson
ms.author: riande
ms.author2: tdykstra
description: "使用 .NET Core 搭配 Entity Framework Core 的使用者入門"
keywords: .NET Core, Entity Framework Core, VS Code, Visual Studio Code, Mac, Linux
ms.date: 04/05/2017
ms.assetid: 099d179e-dd7b-4755-8f3c-fcde914bf50b
ms.technology: entity-framework-core
uid: core/get-started/netcore/new-db-sqlite
ms.openlocfilehash: 22fc0446dee71dd0d2402b47d76cc8b7307fbe5f
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="getting-started-with-ef-core-on-net-core-console-app-with-a-new-database"></a><span data-ttu-id="d0074-104">在 .NET Core 主控台應用程式上使用 EF Core 搭配新資料庫的使用者入門</span><span class="sxs-lookup"><span data-stu-id="d0074-104">Getting Started with EF Core on .NET Core Console App with a New database</span></span>

<span data-ttu-id="d0074-105">在本逐步解說中，您將建置 .NET Core 主控台應用程式，該應用程式將使用 Entity Framework Core 對 SQLite 資料庫執行基本資料存取。</span><span class="sxs-lookup"><span data-stu-id="d0074-105">In this walkthrough, you will create a .NET Core console app that performs basic data access against a SQLite database using Entity Framework Core.</span></span> <span data-ttu-id="d0074-106">您將使用移轉，以便從您的模型來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="d0074-106">You will use migrations to create the database from your model.</span></span> <span data-ttu-id="d0074-107">如需使用 ASP.NET Core MVC 的 Visual Studio 版本，請參閱 [ASP.NET Core - 新資料庫](xref:core/get-started/aspnetcore/new-db)。</span><span class="sxs-lookup"><span data-stu-id="d0074-107">See [ASP.NET Core - New database](xref:core/get-started/aspnetcore/new-db) for a Visual Studio version using ASP.NET Core MVC.</span></span>

> [!NOTE]  
> <span data-ttu-id="d0074-108">[.NET Core SDK](https://www.microsoft.com/net/download/core) 已不再支援 `project.json` 或 Visual Studio 2015。</span><span class="sxs-lookup"><span data-stu-id="d0074-108">The [.NET Core SDK](https://www.microsoft.com/net/download/core) no longer supports `project.json` or Visual Studio 2015.</span></span> <span data-ttu-id="d0074-109">建議您[從 project.json 移轉至 csproj](https://docs.microsoft.com/dotnet/articles/core/migration/)。</span><span class="sxs-lookup"><span data-stu-id="d0074-109">We recommend you [migrate from project.json to csproj](https://docs.microsoft.com/dotnet/articles/core/migration/).</span></span> <span data-ttu-id="d0074-110">如果您使用 Visual Studio，建議您移轉至 [Visual Studio 2017](https://www.visualstudio.com/downloads/)。</span><span class="sxs-lookup"><span data-stu-id="d0074-110">If you are using Visual Studio, we recommend you migrate to [Visual Studio 2017](https://www.visualstudio.com/downloads/).</span></span>

> [!TIP]  
> <span data-ttu-id="d0074-111">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/NetCore/ConsoleApp.SQLite) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="d0074-111">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/NetCore/ConsoleApp.SQLite) on GitHub.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d0074-112">必要條件</span><span class="sxs-lookup"><span data-stu-id="d0074-112">Prerequisites</span></span>

<span data-ttu-id="d0074-113">若要完成此逐步解說，必須符合下列必要條件：</span><span class="sxs-lookup"><span data-stu-id="d0074-113">The following prerequisites are needed to complete this walkthrough:</span></span>
* <span data-ttu-id="d0074-114">支援 .NET Core 的作業系統。</span><span class="sxs-lookup"><span data-stu-id="d0074-114">An operating system that supports .NET Core.</span></span>
* <span data-ttu-id="d0074-115">[.NET Core SDK](https://www.microsoft.com/net/core) 2.0 (不過，指示也可用來建立使用先前版的應用程式，且幾乎不必修改)。</span><span class="sxs-lookup"><span data-stu-id="d0074-115">[The .NET Core SDK](https://www.microsoft.com/net/core) 2.0 (although the instructions can be used to create an application with a previous version with very few modifications).</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="d0074-116">建立新專案</span><span class="sxs-lookup"><span data-stu-id="d0074-116">Create a new project</span></span>

* <span data-ttu-id="d0074-117">為您的專案建立新的 `ConsoleApp.SQLite` 資料夾，並使用 `dotnet` 命令將 .NET Core 應用程式填入該資料夾。</span><span class="sxs-lookup"><span data-stu-id="d0074-117">Create a new `ConsoleApp.SQLite` folder for your project and use the `dotnet` command to populate it with a .NET Core app.</span></span>

``` Console
mkdir ConsoleApp.SQLite
cd ConsoleApp.SQLite/
dotnet new console
```

## <a name="install-entity-framework-core"></a><span data-ttu-id="d0074-118">安裝 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="d0074-118">Install Entity Framework Core</span></span>

<span data-ttu-id="d0074-119">若要使用 EF Core，請針對您要作為目標的資料庫提供者來安裝套件。</span><span class="sxs-lookup"><span data-stu-id="d0074-119">To use EF Core, install the package for the database provider(s) you want to target.</span></span> <span data-ttu-id="d0074-120">本逐步解說使用 SQLite。</span><span class="sxs-lookup"><span data-stu-id="d0074-120">This walkthrough uses SQLite.</span></span> <span data-ttu-id="d0074-121">如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="d0074-121">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="d0074-122">安裝 Microsoft.EntityFrameworkCore.Sqlite 和 Microsoft.EntityFrameworkCore.Design</span><span class="sxs-lookup"><span data-stu-id="d0074-122">Install Microsoft.EntityFrameworkCore.Sqlite and Microsoft.EntityFrameworkCore.Design</span></span>

``` Console
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
dotnet add package Microsoft.EntityFrameworkCore.Design
```

* <span data-ttu-id="d0074-123">手動編輯 `ConsoleApp.SQLite.csproj`，以將 DotNetCliToolReference 加入 Microsoft.EntityFrameworkCore.Tools.DotNet：</span><span class="sxs-lookup"><span data-stu-id="d0074-123">Manually edit `ConsoleApp.SQLite.csproj` to add a DotNetCliToolReference to Microsoft.EntityFrameworkCore.Tools.DotNet:</span></span>

  ``` xml
  <ItemGroup>
    <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.0" />
  </ItemGroup>
  ```

 <span data-ttu-id="d0074-124">注意：未來版本的 `dotnet` 將透過 `dotnet add tool` 來支援 DotNetCliToolReferences</span><span class="sxs-lookup"><span data-stu-id="d0074-124">Note: A future version of `dotnet` will support DotNetCliToolReferences via `dotnet add tool`</span></span>

<span data-ttu-id="d0074-125">`ConsoleApp.SQLite.csproj` 現在應該會包含下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="d0074-125">`ConsoleApp.SQLite.csproj` should now contain the following:</span></span>

[!code[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/ConsoleApp.SQLite.csproj)]

 <span data-ttu-id="d0074-126">注意：上列所使用的版本號碼，為發行時正確的版本號碼。</span><span class="sxs-lookup"><span data-stu-id="d0074-126">Note: The version numbers used above were correct at the time of publishing.</span></span>

*  <span data-ttu-id="d0074-127">執行 `dotnet restore` 以安裝新的套件。</span><span class="sxs-lookup"><span data-stu-id="d0074-127">Run `dotnet restore` to install the new packages.</span></span>

## <a name="create-the-model"></a><span data-ttu-id="d0074-128">建立模型</span><span class="sxs-lookup"><span data-stu-id="d0074-128">Create the model</span></span>

<span data-ttu-id="d0074-129">定義組成模型的內容和實體類別。</span><span class="sxs-lookup"><span data-stu-id="d0074-129">Define a context and entity classes that make up your model.</span></span>

* <span data-ttu-id="d0074-130">建立具有下列內容的新 *Model.cs* 檔案。</span><span class="sxs-lookup"><span data-stu-id="d0074-130">Create a new *Model.cs* file with the following contents.</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Model.cs)]

<span data-ttu-id="d0074-131">秘訣：在實際的應用程式中，您會將每個類別放在個別的檔案中，然後將連接字串放在組態檔中。</span><span class="sxs-lookup"><span data-stu-id="d0074-131">Tip: In a real application you would put each class in a separate file and put the connection string in a configuration file.</span></span> <span data-ttu-id="d0074-132">為了簡化教學課程，我們會將所有項目放在一個檔案中。</span><span class="sxs-lookup"><span data-stu-id="d0074-132">To keep the tutorial simple, we are putting everything in one file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="d0074-133">建立資料庫</span><span class="sxs-lookup"><span data-stu-id="d0074-133">Create the database</span></span>

<span data-ttu-id="d0074-134">一旦有模型之後，就可以使用[移轉](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="d0074-134">Once you have a model, you can use [migrations](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations) to create a database.</span></span>

* <span data-ttu-id="d0074-135">執行 `dotnet ef migrations add InitialCreate` 以建立移轉的結構，並針對模型建立一組初始的資料表。</span><span class="sxs-lookup"><span data-stu-id="d0074-135">Run `dotnet ef migrations add InitialCreate` to scaffold a migration and create the initial set of tables for the model.</span></span>
* <span data-ttu-id="d0074-136">執行 `dotnet ef database update` 以將新的移轉套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="d0074-136">Run `dotnet ef database update` to apply the new migration to the database.</span></span> <span data-ttu-id="d0074-137">此命令會建立資料庫，然後才套用移轉。</span><span class="sxs-lookup"><span data-stu-id="d0074-137">This command creates the database before applying migrations.</span></span>

> [!NOTE]  
> <span data-ttu-id="d0074-138">在 SQLite 中使用相對路徑時，路徑會相對於應用程式的主要組件。</span><span class="sxs-lookup"><span data-stu-id="d0074-138">When using relative paths with SQLite, the path will be relative to the application's main assembly.</span></span> <span data-ttu-id="d0074-139">在此範例中，主要的二進位檔是 `bin/Debug/netcoreapp2.0/ConsoleApp.SQLite.dll`，因此 SQLite 資料庫將位於 `bin/Debug/netcoreapp2.0/blogging.db` 中。</span><span class="sxs-lookup"><span data-stu-id="d0074-139">In this sample, the main binary is `bin/Debug/netcoreapp2.0/ConsoleApp.SQLite.dll`, so the SQLite database will be in `bin/Debug/netcoreapp2.0/blogging.db`.</span></span>

## <a name="use-your-model"></a><span data-ttu-id="d0074-140">使用您的模型</span><span class="sxs-lookup"><span data-stu-id="d0074-140">Use your model</span></span>

* <span data-ttu-id="d0074-141">開啟 *Program.cs* 並使用下列程式碼來取代內容：</span><span class="sxs-lookup"><span data-stu-id="d0074-141">Open *Program.cs* and replace the contents with the following code:</span></span>

 [!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Program.cs)]

* <span data-ttu-id="d0074-142">測試應用程式：</span><span class="sxs-lookup"><span data-stu-id="d0074-142">Test the app:</span></span>

 `dotnet run`

 <span data-ttu-id="d0074-143">有一個部落格儲存至資料庫，且所有部落格的詳細資料會在主控台中顯示。</span><span class="sxs-lookup"><span data-stu-id="d0074-143">One blog is saved to the database and the details of all blogs are displayed in the console.</span></span>

  ``` Console
  ConsoleApp.SQLite>dotnet run
  1 records saved to database

  All blogs in database:
   - http://blogs.msdn.com/adonet
  ```

### <a name="changing-the-model"></a><span data-ttu-id="d0074-144">變更模型：</span><span class="sxs-lookup"><span data-stu-id="d0074-144">Changing the model:</span></span>

- <span data-ttu-id="d0074-145">如果您變更了您的模型，則可以使用 `dotnet ef migrations add` 命令來建立新[移轉](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)的結構，以對資料庫做出相對應的結構描述變更。</span><span class="sxs-lookup"><span data-stu-id="d0074-145">If you make changes to your model, you can use the `dotnet ef migrations add` command to scaffold a new [migration](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)  to make the corresponding schema changes to the database.</span></span> <span data-ttu-id="d0074-146">檢查完已建立結構的程式碼之後 (並進行任何必要的變更)，就可以使用 `dotnet ef database update` 命令將變更套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="d0074-146">Once you have checked the scaffolded code (and made any required changes), you can use the `dotnet ef database update` command to apply the changes to the database.</span></span>
- <span data-ttu-id="d0074-147">EF 在資料庫中使用 `__EFMigrationsHistory` 資料表，以確認已經套用至資料庫的移轉。</span><span class="sxs-lookup"><span data-stu-id="d0074-147">EF uses a `__EFMigrationsHistory` table in the database to keep track of which migrations have already been applied to the database.</span></span>
- <span data-ttu-id="d0074-148">由於 SQLite 中的限制，SQLite 不支援所有移轉 (結構描述變更)。</span><span class="sxs-lookup"><span data-stu-id="d0074-148">SQLite does not support all migrations (schema changes) due to limitations in SQLite.</span></span> <span data-ttu-id="d0074-149">請參閱 [SQLite 限制](../../providers/sqlite/limitations.md)。</span><span class="sxs-lookup"><span data-stu-id="d0074-149">See [SQLite Limitations](../../providers/sqlite/limitations.md).</span></span> <span data-ttu-id="d0074-150">針對新的開發，當模型變更時，請考慮捨棄資料庫並建立一個新的資料庫，而不是使用移轉。</span><span class="sxs-lookup"><span data-stu-id="d0074-150">For new development, consider dropping the database and creating a new one rather than using migrations when your model changes.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d0074-151">其他資源</span><span class="sxs-lookup"><span data-stu-id="d0074-151">Additional Resources</span></span>

* <span data-ttu-id="d0074-152">[.NET Core - 使用 SQLite 的新資料庫](xref:core/get-started/netcore/new-db-sqlite) - 跨平台的主控台 EF 教學課程。</span><span class="sxs-lookup"><span data-stu-id="d0074-152">[.NET Core - New database with SQLite](xref:core/get-started/netcore/new-db-sqlite) -  a cross-platform console EF tutorial.</span></span>
* [<span data-ttu-id="d0074-153">Mac 或 Linux 上的 ASP.NET Core MVC 簡介</span><span class="sxs-lookup"><span data-stu-id="d0074-153">Introduction to ASP.NET Core MVC on Mac or Linux</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app-xplat/index)
* [<span data-ttu-id="d0074-154">使用 Visual Studio 的 ASP.NET Core MVC 簡介</span><span class="sxs-lookup"><span data-stu-id="d0074-154">Introduction to ASP.NET Core MVC with Visual Studio</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/index)
* [<span data-ttu-id="d0074-155">使用 Visual Studio 的 ASP.NET Core 與 Entity Framework Core 的使用者入門</span><span class="sxs-lookup"><span data-stu-id="d0074-155">Getting started with ASP.NET Core and Entity Framework Core using Visual Studio</span></span>](https://docs.microsoft.com/aspnet/core/data/ef-mvc/index)
