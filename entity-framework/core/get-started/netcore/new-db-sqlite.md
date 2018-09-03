---
title: .NET Core 使用者入門 - 新資料庫 - EF Core
author: rick-anderson
ms.author: riande
description: 使用 .NET Core 搭配 Entity Framework Core 的使用者入門
ms.date: 08/03/2018
ms.assetid: 099d179e-dd7b-4755-8f3c-fcde914bf50b
uid: core/get-started/netcore/new-db-sqlite
ms.openlocfilehash: 51f5752eebce5603c663072f7b36dfecd4ddf227
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993688"
---
# <a name="getting-started-with-ef-core-on-net-core-console-app-with-a-new-database"></a><span data-ttu-id="f3f3b-103">在 .NET Core 主控台應用程式上使用 EF Core 搭配新資料庫的使用者入門</span><span class="sxs-lookup"><span data-stu-id="f3f3b-103">Getting Started with EF Core on .NET Core Console App with a New database</span></span>

<span data-ttu-id="f3f3b-104">在此教學課程中，您會建置 .NET Core 主控台應用程式，它會使用 Entity Framework Core 對 SQLite 資料庫執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="f3f3b-104">In this tutorial, you create a .NET Core console app that performs data access against a SQLite database using Entity Framework Core.</span></span> <span data-ttu-id="f3f3b-105">您會使用移轉從模型建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="f3f3b-105">You use migrations to create the database from the model.</span></span> <span data-ttu-id="f3f3b-106">如需使用 ASP.NET Core MVC 的 Visual Studio 版本，請參閱 [ASP.NET Core - 新資料庫](xref:core/get-started/aspnetcore/new-db)。</span><span class="sxs-lookup"><span data-stu-id="f3f3b-106">See [ASP.NET Core - New database](xref:core/get-started/aspnetcore/new-db) for a Visual Studio version using ASP.NET Core MVC.</span></span>

<span data-ttu-id="f3f3b-107">在 GitHub 上檢視此文章的範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/NetCore/ConsoleApp.SQLite)。</span><span class="sxs-lookup"><span data-stu-id="f3f3b-107">View this article's sample on GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/NetCore/ConsoleApp.SQLite).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f3f3b-108">必要條件</span><span class="sxs-lookup"><span data-stu-id="f3f3b-108">Prerequisites</span></span>

* [<span data-ttu-id="f3f3b-109">.NET Core 2.1 SDK</span><span class="sxs-lookup"><span data-stu-id="f3f3b-109">The .NET Core 2.1 SDK</span></span>](https://www.microsoft.com/net/core)

## <a name="create-a-new-project"></a><span data-ttu-id="f3f3b-110">建立新專案</span><span class="sxs-lookup"><span data-stu-id="f3f3b-110">Create a new project</span></span>

* <span data-ttu-id="f3f3b-111">建立新的主控台專案：</span><span class="sxs-lookup"><span data-stu-id="f3f3b-111">Create a new console project:</span></span>

  ``` Console
  dotnet new console -o ConsoleApp.SQLite
  cd ConsoleApp.SQLite/
  ```

## <a name="install-entity-framework-core"></a><span data-ttu-id="f3f3b-112">安裝 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="f3f3b-112">Install Entity Framework Core</span></span>

<span data-ttu-id="f3f3b-113">若要使用 EF Core，請針對您要作為目標的資料庫提供者來安裝套件。</span><span class="sxs-lookup"><span data-stu-id="f3f3b-113">To use EF Core, install the package for the database provider(s) you want to target.</span></span> <span data-ttu-id="f3f3b-114">此逐步解說使用 SQLite。</span><span class="sxs-lookup"><span data-stu-id="f3f3b-114">This walkthrough uses SQLite.</span></span> <span data-ttu-id="f3f3b-115">如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="f3f3b-115">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="f3f3b-116">安裝 Microsoft.EntityFrameworkCore.Sqlite 和 Microsoft.EntityFrameworkCore.Design</span><span class="sxs-lookup"><span data-stu-id="f3f3b-116">Install Microsoft.EntityFrameworkCore.Sqlite and Microsoft.EntityFrameworkCore.Design</span></span>

  ```Console
  dotnet add package Microsoft.EntityFrameworkCore.Sqlite
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

* <span data-ttu-id="f3f3b-117">執行 `dotnet restore` 以安裝新的套件。</span><span class="sxs-lookup"><span data-stu-id="f3f3b-117">Run `dotnet restore` to install the new packages.</span></span>

## <a name="create-the-model"></a><span data-ttu-id="f3f3b-118">建立模型</span><span class="sxs-lookup"><span data-stu-id="f3f3b-118">Create the model</span></span>

<span data-ttu-id="f3f3b-119">定義組成模型的內容和實體類別。</span><span class="sxs-lookup"><span data-stu-id="f3f3b-119">Define a context and entity classes that make up your model.</span></span>

* <span data-ttu-id="f3f3b-120">建立具有下列內容的新 *Model.cs* 檔案。</span><span class="sxs-lookup"><span data-stu-id="f3f3b-120">Create a new *Model.cs* file with the following contents.</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Model.cs)]

<span data-ttu-id="f3f3b-121">祕訣：在實際的應用程式中，您會將每個類別放在不同的檔案中，然後將連接字串放在設定檔或環境變數中。</span><span class="sxs-lookup"><span data-stu-id="f3f3b-121">Tip: In a real application, you put each class in a separate file and put the connection string in a configuration file or environment variable.</span></span> <span data-ttu-id="f3f3b-122">為了簡化教學課程，所有項目會統一放在一個檔案中。</span><span class="sxs-lookup"><span data-stu-id="f3f3b-122">To keep the tutorial simple, everything is contained in one file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="f3f3b-123">建立資料庫</span><span class="sxs-lookup"><span data-stu-id="f3f3b-123">Create the database</span></span>

<span data-ttu-id="f3f3b-124">一旦有模型後，您就可以使用[移轉](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="f3f3b-124">Once you have a model, you use [migrations](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations) to create a database.</span></span>

* <span data-ttu-id="f3f3b-125">執行 `dotnet ef migrations add InitialCreate` 以建立移轉的結構，並針對模型建立一組初始的資料表。</span><span class="sxs-lookup"><span data-stu-id="f3f3b-125">Run `dotnet ef migrations add InitialCreate` to scaffold a migration and create the initial set of tables for the model.</span></span>
* <span data-ttu-id="f3f3b-126">執行 `dotnet ef database update` 以將新的移轉套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="f3f3b-126">Run `dotnet ef database update` to apply the new migration to the database.</span></span> <span data-ttu-id="f3f3b-127">此命令會建立資料庫，然後才套用移轉。</span><span class="sxs-lookup"><span data-stu-id="f3f3b-127">This command creates the database before applying migrations.</span></span>

<span data-ttu-id="f3f3b-128">*blogging.db*\* SQLite DB 位於專案目錄中。</span><span class="sxs-lookup"><span data-stu-id="f3f3b-128">The *blogging.db*\* SQLite DB is in the project directory.</span></span>

## <a name="use-the-model"></a><span data-ttu-id="f3f3b-129">使用模型</span><span class="sxs-lookup"><span data-stu-id="f3f3b-129">Use the model</span></span>

* <span data-ttu-id="f3f3b-130">開啟 *Program.cs* 並使用下列程式碼來取代內容：</span><span class="sxs-lookup"><span data-stu-id="f3f3b-130">Open *Program.cs* and replace the contents with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Program.cs)]

* <span data-ttu-id="f3f3b-131">測試應用程式：</span><span class="sxs-lookup"><span data-stu-id="f3f3b-131">Test the app:</span></span>

  `dotnet run`

  <span data-ttu-id="f3f3b-132">有一個部落格儲存至資料庫，且所有部落格的詳細資料會在主控台中顯示。</span><span class="sxs-lookup"><span data-stu-id="f3f3b-132">One blog is saved to the database and the details of all blogs are displayed in the console.</span></span>

  ```Console
  ConsoleApp.SQLite>dotnet run
  1 records saved to database

  All blogs in database:
   - http://blogs.msdn.com/adonet
  ```

### <a name="changing-the-model"></a><span data-ttu-id="f3f3b-133">變更模型：</span><span class="sxs-lookup"><span data-stu-id="f3f3b-133">Changing the model:</span></span>

- <span data-ttu-id="f3f3b-134">若要變更模型，您可以使用 `dotnet ef migrations add` 命令來建立新的[移轉](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)支架。</span><span class="sxs-lookup"><span data-stu-id="f3f3b-134">If you make changes to the model, you can use the `dotnet ef migrations add` command to scaffold a new [migration](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations).</span></span> <span data-ttu-id="f3f3b-135">一旦檢查支架程式碼 (並進行任何必要的變更)，您就可以使用 `dotnet ef database update` 命令將結構描述變更套用到資料庫。</span><span class="sxs-lookup"><span data-stu-id="f3f3b-135">Once you have checked the scaffolded code (and made any required changes), you can use the `dotnet ef database update` command to apply the schema changes to the database.</span></span>
- <span data-ttu-id="f3f3b-136">EF Core 在資料庫中使用 `__EFMigrationsHistory` 資料表，以追蹤已套用到資料庫的移轉。</span><span class="sxs-lookup"><span data-stu-id="f3f3b-136">EF Core uses a `__EFMigrationsHistory` table in the database to keep track of which migrations have already been applied to the database.</span></span>
- <span data-ttu-id="f3f3b-137">SQLite 資料庫引擎不支援大部分其他關聯式資料庫所支援的特定結構描述變更。</span><span class="sxs-lookup"><span data-stu-id="f3f3b-137">The SQLite database engine doesn't support certain schema changes that are supported by most other relational databases.</span></span> <span data-ttu-id="f3f3b-138">例如，不支援 `DropColumn` 作業。</span><span class="sxs-lookup"><span data-stu-id="f3f3b-138">For example, the `DropColumn` operation is not supported.</span></span> <span data-ttu-id="f3f3b-139">EF Core 移轉將會產生這些作業的程式碼。</span><span class="sxs-lookup"><span data-stu-id="f3f3b-139">EF Core Migrations will generate code for these operations.</span></span> <span data-ttu-id="f3f3b-140">但若您嘗試將它們套用到資料庫或產生指令碼，EF Core 會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="f3f3b-140">But if you try to apply them to a database or generate a script, EF Core throws exceptions.</span></span> <span data-ttu-id="f3f3b-141">請參閱 [SQLite 限制](../../providers/sqlite/limitations.md)。</span><span class="sxs-lookup"><span data-stu-id="f3f3b-141">See [SQLite Limitations](../../providers/sqlite/limitations.md).</span></span> <span data-ttu-id="f3f3b-142">針對新的開發，當模型變更時，請考慮捨棄資料庫並建立一個新的資料庫，而不是使用移轉。</span><span class="sxs-lookup"><span data-stu-id="f3f3b-142">For new development, consider dropping the database and creating a new one rather than using migrations when the model changes.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f3f3b-143">其他資源</span><span class="sxs-lookup"><span data-stu-id="f3f3b-143">Additional Resources</span></span>

* [<span data-ttu-id="f3f3b-144">Mac 或 Linux 上的 ASP.NET Core MVC 簡介</span><span class="sxs-lookup"><span data-stu-id="f3f3b-144">Introduction to ASP.NET Core MVC on Mac or Linux</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app-xplat/index)
* [<span data-ttu-id="f3f3b-145">使用 Visual Studio 的 ASP.NET Core MVC 簡介</span><span class="sxs-lookup"><span data-stu-id="f3f3b-145">Introduction to ASP.NET Core MVC with Visual Studio</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/index)
* [<span data-ttu-id="f3f3b-146">使用 Visual Studio 的 ASP.NET Core 與 Entity Framework Core 的使用者入門</span><span class="sxs-lookup"><span data-stu-id="f3f3b-146">Getting started with ASP.NET Core and Entity Framework Core using Visual Studio</span></span>](https://docs.microsoft.com/aspnet/core/data/ef-mvc/index)
