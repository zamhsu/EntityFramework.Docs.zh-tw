---
title: .NET Core 使用者入門 - 新資料庫 - EF Core
author: rick-anderson
ms.author: riande
ms.author2: tdykstra
description: 使用 .NET Core 搭配 Entity Framework Core 的使用者入門
keywords: .NET Core, Entity Framework Core, VS Code, Visual Studio Code, Mac, Linux
ms.date: 06/05/2018
ms.assetid: 099d179e-dd7b-4755-8f3c-fcde914bf50b
ms.technology: entity-framework-core
uid: core/get-started/netcore/new-db-sqlite
ms.openlocfilehash: e4eafed037325237345efbc3d7d42b32270a54e3
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2018
ms.locfileid: "37911498"
---
# <a name="getting-started-with-ef-core-on-net-core-console-app-with-a-new-database"></a><span data-ttu-id="df589-104">在 .NET Core 主控台應用程式上使用 EF Core 搭配新資料庫的使用者入門</span><span class="sxs-lookup"><span data-stu-id="df589-104">Getting Started with EF Core on .NET Core Console App with a New database</span></span>

<span data-ttu-id="df589-105">在本逐步解說中，您會建置 .NET Core 主控台應用程式，其會使用 Entity Framework Core 依 SQLite 資料庫執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="df589-105">In this walkthrough, you create a .NET Core console app that performs data access against a SQLite database using Entity Framework Core.</span></span> <span data-ttu-id="df589-106">您會使用移轉從模型建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="df589-106">You use migrations to create the database from the model.</span></span> <span data-ttu-id="df589-107">如需使用 ASP.NET Core MVC 的 Visual Studio 版本，請參閱 [ASP.NET Core - 新資料庫](xref:core/get-started/aspnetcore/new-db)。</span><span class="sxs-lookup"><span data-stu-id="df589-107">See [ASP.NET Core - New database](xref:core/get-started/aspnetcore/new-db) for a Visual Studio version using ASP.NET Core MVC.</span></span>

> [!TIP]  
> <span data-ttu-id="df589-108">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/NetCore/ConsoleApp.SQLite) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="df589-108">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/NetCore/ConsoleApp.SQLite) on GitHub.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="df589-109">必要條件</span><span class="sxs-lookup"><span data-stu-id="df589-109">Prerequisites</span></span>

<span data-ttu-id="df589-110">[.NET Core SDK](https://www.microsoft.com/net/core) 2.1</span><span class="sxs-lookup"><span data-stu-id="df589-110">[The .NET Core SDK](https://www.microsoft.com/net/core) 2.1</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="df589-111">建立新專案</span><span class="sxs-lookup"><span data-stu-id="df589-111">Create a new project</span></span>

* <span data-ttu-id="df589-112">建立新的主控台專案：</span><span class="sxs-lookup"><span data-stu-id="df589-112">Create a new console project:</span></span>

``` Console
dotnet new console -o ConsoleApp.SQLite
cd ConsoleApp.SQLite/
```

## <a name="install-entity-framework-core"></a><span data-ttu-id="df589-113">安裝 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="df589-113">Install Entity Framework Core</span></span>

<span data-ttu-id="df589-114">若要使用 EF Core，請針對您要作為目標的資料庫提供者來安裝套件。</span><span class="sxs-lookup"><span data-stu-id="df589-114">To use EF Core, install the package for the database provider(s) you want to target.</span></span> <span data-ttu-id="df589-115">本逐步解說使用 SQLite。</span><span class="sxs-lookup"><span data-stu-id="df589-115">This walkthrough uses SQLite.</span></span> <span data-ttu-id="df589-116">如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="df589-116">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="df589-117">安裝 Microsoft.EntityFrameworkCore.Sqlite 和 Microsoft.EntityFrameworkCore.Design</span><span class="sxs-lookup"><span data-stu-id="df589-117">Install Microsoft.EntityFrameworkCore.Sqlite and Microsoft.EntityFrameworkCore.Design</span></span>

``` Console
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
dotnet add package Microsoft.EntityFrameworkCore.Design
```

* <span data-ttu-id="df589-118">執行 `dotnet restore` 以安裝新的套件。</span><span class="sxs-lookup"><span data-stu-id="df589-118">Run `dotnet restore` to install the new packages.</span></span>

## <a name="create-the-model"></a><span data-ttu-id="df589-119">建立模型</span><span class="sxs-lookup"><span data-stu-id="df589-119">Create the model</span></span>

<span data-ttu-id="df589-120">定義組成模型的內容和實體類別。</span><span class="sxs-lookup"><span data-stu-id="df589-120">Define a context and entity classes that make up your model.</span></span>

* <span data-ttu-id="df589-121">建立具有下列內容的新 *Model.cs* 檔案。</span><span class="sxs-lookup"><span data-stu-id="df589-121">Create a new *Model.cs* file with the following contents.</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Model.cs)]

<span data-ttu-id="df589-122">秘訣：在實際的應用程式中，您要將每個類別放在個別檔案中，然後將連接字串放在組態檔中。</span><span class="sxs-lookup"><span data-stu-id="df589-122">Tip: In a real application, you put each class in a separate file and put the connection string in a configuration file.</span></span> <span data-ttu-id="df589-123">為了簡化教學課程，所有項目會統一放在一個檔案中。</span><span class="sxs-lookup"><span data-stu-id="df589-123">To keep the tutorial simple, everything is contained in one file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="df589-124">建立資料庫</span><span class="sxs-lookup"><span data-stu-id="df589-124">Create the database</span></span>

<span data-ttu-id="df589-125">一旦有模型後，您就可以使用[移轉](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="df589-125">Once you have a model, you use [migrations](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations) to create a database.</span></span>

* <span data-ttu-id="df589-126">執行 `dotnet ef migrations add InitialCreate` 以建立移轉的結構，並針對模型建立一組初始的資料表。</span><span class="sxs-lookup"><span data-stu-id="df589-126">Run `dotnet ef migrations add InitialCreate` to scaffold a migration and create the initial set of tables for the model.</span></span>
* <span data-ttu-id="df589-127">執行 `dotnet ef database update` 以將新的移轉套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="df589-127">Run `dotnet ef database update` to apply the new migration to the database.</span></span> <span data-ttu-id="df589-128">此命令會建立資料庫，然後才套用移轉。</span><span class="sxs-lookup"><span data-stu-id="df589-128">This command creates the database before applying migrations.</span></span>

<span data-ttu-id="df589-129">*blogging.db*\* SQLite DB 位於專案目錄中。</span><span class="sxs-lookup"><span data-stu-id="df589-129">The *blogging.db*\* SQLite DB is in the project directory.</span></span>

## <a name="use-your-model"></a><span data-ttu-id="df589-130">使用您的模型</span><span class="sxs-lookup"><span data-stu-id="df589-130">Use your model</span></span>

* <span data-ttu-id="df589-131">開啟 *Program.cs* 並使用下列程式碼來取代內容：</span><span class="sxs-lookup"><span data-stu-id="df589-131">Open *Program.cs* and replace the contents with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Program.cs)]

* <span data-ttu-id="df589-132">測試應用程式：</span><span class="sxs-lookup"><span data-stu-id="df589-132">Test the app:</span></span>

  `dotnet run`

  <span data-ttu-id="df589-133">有一個部落格儲存至資料庫，且所有部落格的詳細資料會在主控台中顯示。</span><span class="sxs-lookup"><span data-stu-id="df589-133">One blog is saved to the database and the details of all blogs are displayed in the console.</span></span>

  ``` Console
  ConsoleApp.SQLite>dotnet run
  1 records saved to database

  All blogs in database:
   - http://blogs.msdn.com/adonet
  ```

### <a name="changing-the-model"></a><span data-ttu-id="df589-134">變更模型：</span><span class="sxs-lookup"><span data-stu-id="df589-134">Changing the model:</span></span>

- <span data-ttu-id="df589-135">如果您變更了您的模型，則可以使用 `dotnet ef migrations add` 命令來建立新[移轉](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)的結構，以對資料庫做出相對應的結構描述變更。</span><span class="sxs-lookup"><span data-stu-id="df589-135">If you make changes to your model, you can use the `dotnet ef migrations add` command to scaffold a new [migration](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)  to make the corresponding schema changes to the database.</span></span> <span data-ttu-id="df589-136">檢查完已建立結構的程式碼之後 (並進行任何必要的變更)，就可以使用 `dotnet ef database update` 命令將變更套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="df589-136">Once you have checked the scaffolded code (and made any required changes), you can use the `dotnet ef database update` command to apply the changes to the database.</span></span>
- <span data-ttu-id="df589-137">EF 在資料庫中使用 `__EFMigrationsHistory` 資料表，以確認已經套用至資料庫的移轉。</span><span class="sxs-lookup"><span data-stu-id="df589-137">EF uses a `__EFMigrationsHistory` table in the database to keep track of which migrations have already been applied to the database.</span></span>
- <span data-ttu-id="df589-138">由於 SQLite 中的限制，SQLite 不支援所有移轉 (結構描述變更)。</span><span class="sxs-lookup"><span data-stu-id="df589-138">SQLite does not support all migrations (schema changes) due to limitations in SQLite.</span></span> <span data-ttu-id="df589-139">請參閱 [SQLite 限制](../../providers/sqlite/limitations.md)。</span><span class="sxs-lookup"><span data-stu-id="df589-139">See [SQLite Limitations](../../providers/sqlite/limitations.md).</span></span> <span data-ttu-id="df589-140">針對新的開發，當模型變更時，請考慮捨棄資料庫並建立一個新的資料庫，而不是使用移轉。</span><span class="sxs-lookup"><span data-stu-id="df589-140">For new development, consider dropping the database and creating a new one rather than using migrations when your model changes.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="df589-141">其他資源</span><span class="sxs-lookup"><span data-stu-id="df589-141">Additional Resources</span></span>

* <span data-ttu-id="df589-142">[.NET Core - 使用 SQLite 的新資料庫](xref:core/get-started/netcore/new-db-sqlite) - 跨平台的主控台 EF 教學課程。</span><span class="sxs-lookup"><span data-stu-id="df589-142">[.NET Core - New database with SQLite](xref:core/get-started/netcore/new-db-sqlite) -  a cross-platform console EF tutorial.</span></span>
* [<span data-ttu-id="df589-143">Mac 或 Linux 上的 ASP.NET Core MVC 簡介</span><span class="sxs-lookup"><span data-stu-id="df589-143">Introduction to ASP.NET Core MVC on Mac or Linux</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app-xplat/index)
* [<span data-ttu-id="df589-144">使用 Visual Studio 的 ASP.NET Core MVC 簡介</span><span class="sxs-lookup"><span data-stu-id="df589-144">Introduction to ASP.NET Core MVC with Visual Studio</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/index)
* [<span data-ttu-id="df589-145">使用 Visual Studio 的 ASP.NET Core 與 Entity Framework Core 的使用者入門</span><span class="sxs-lookup"><span data-stu-id="df589-145">Getting started with ASP.NET Core and Entity Framework Core using Visual Studio</span></span>](https://docs.microsoft.com/aspnet/core/data/ef-mvc/index)
