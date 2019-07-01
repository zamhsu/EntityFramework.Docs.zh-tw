---
title: .NET Core 使用者入門 - 新資料庫 - EF Core
author: rick-anderson
ms.author: riande
description: 使用 .NET Core 搭配 Entity Framework Core 的使用者入門
ms.date: 08/03/2018
ms.assetid: 099d179e-dd7b-4755-8f3c-fcde914bf50b
uid: core/get-started/netcore/new-db-sqlite
ms.openlocfilehash: e6996630e399659807d23304993c8e19c11ca6f5
ms.sourcegitcommit: 83c1e2fc034e5eb1fec1ebabc8d629ffcc7c0632
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2019
ms.locfileid: "67351332"
---
# <a name="getting-started-with-ef-core-on-net-core-console-app-with-a-new-database"></a><span data-ttu-id="c7b18-103">在 .NET Core 主控台應用程式上使用 EF Core 搭配新資料庫的使用者入門</span><span class="sxs-lookup"><span data-stu-id="c7b18-103">Getting Started with EF Core on .NET Core Console App with a New database</span></span>

<span data-ttu-id="c7b18-104">在此教學課程中，您會建置 .NET Core 主控台應用程式，它會使用 Entity Framework Core 對 SQLite 資料庫執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="c7b18-104">In this tutorial, you create a .NET Core console app that performs data access against a SQLite database using Entity Framework Core.</span></span> <span data-ttu-id="c7b18-105">您會使用[移轉](xref:core/managing-schemas/migrations/index)從模型建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="c7b18-105">You use [migrations](xref:core/managing-schemas/migrations/index) to create the database from the model.</span></span> <span data-ttu-id="c7b18-106">如需使用 ASP.NET Core MVC 的 Visual Studio 版本，請參閱 [ASP.NET Core - 新資料庫](xref:core/get-started/aspnetcore/new-db)。</span><span class="sxs-lookup"><span data-stu-id="c7b18-106">See [ASP.NET Core - New database](xref:core/get-started/aspnetcore/new-db) for a Visual Studio version using ASP.NET Core MVC.</span></span>

<span data-ttu-id="c7b18-107">[在 GitHub 上檢視此文章的範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/NetCore/ConsoleApp.SQLite)。</span><span class="sxs-lookup"><span data-stu-id="c7b18-107">[View this article's sample on GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/NetCore/ConsoleApp.SQLite).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c7b18-108">必要條件</span><span class="sxs-lookup"><span data-stu-id="c7b18-108">Prerequisites</span></span>

* [<span data-ttu-id="c7b18-109">.NET Core 2.1 SDK</span><span class="sxs-lookup"><span data-stu-id="c7b18-109">The .NET Core 2.1 SDK</span></span>](https://www.microsoft.com/net/core)

## <a name="create-a-new-project"></a><span data-ttu-id="c7b18-110">建立新專案</span><span class="sxs-lookup"><span data-stu-id="c7b18-110">Create a new project</span></span>

* <span data-ttu-id="c7b18-111">建立新的主控台專案：</span><span class="sxs-lookup"><span data-stu-id="c7b18-111">Create a new console project:</span></span>

  ``` Console
  dotnet new console -o ConsoleApp.SQLite
  ```
## <a name="change-the-current-directory"></a><span data-ttu-id="c7b18-112">變更目前的目錄</span><span class="sxs-lookup"><span data-stu-id="c7b18-112">Change the current directory</span></span>

<span data-ttu-id="c7b18-113">在後續步驟中，我們需要對應用程式發出 `dotnet` 命令。</span><span class="sxs-lookup"><span data-stu-id="c7b18-113">In subsequent steps, we need to issue `dotnet` commands against the application.</span></span>

* <span data-ttu-id="c7b18-114">我們會將目前的目錄變更為應用程式目錄，如下所示：</span><span class="sxs-lookup"><span data-stu-id="c7b18-114">We change the current directory to the application's directory like this:</span></span>

  ``` Console
  cd ConsoleApp.SQLite/
  ```
## <a name="install-entity-framework-core"></a><span data-ttu-id="c7b18-115">安裝 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="c7b18-115">Install Entity Framework Core</span></span>

<span data-ttu-id="c7b18-116">若要使用 EF Core，請針對您要作為目標的資料庫提供者來安裝套件。</span><span class="sxs-lookup"><span data-stu-id="c7b18-116">To use EF Core, install the package for the database provider(s) you want to target.</span></span> <span data-ttu-id="c7b18-117">本逐步解說使用 SQLite。</span><span class="sxs-lookup"><span data-stu-id="c7b18-117">This walkthrough uses SQLite.</span></span> <span data-ttu-id="c7b18-118">如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="c7b18-118">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="c7b18-119">安裝 Microsoft.EntityFrameworkCore.Sqlite 和 Microsoft.EntityFrameworkCore.Design</span><span class="sxs-lookup"><span data-stu-id="c7b18-119">Install Microsoft.EntityFrameworkCore.Sqlite and Microsoft.EntityFrameworkCore.Design</span></span>

  ```Console
  dotnet add package Microsoft.EntityFrameworkCore.Sqlite
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

* <span data-ttu-id="c7b18-120">執行 `dotnet restore` 以安裝新的套件。</span><span class="sxs-lookup"><span data-stu-id="c7b18-120">Run `dotnet restore` to install the new packages.</span></span>

## <a name="create-the-model"></a><span data-ttu-id="c7b18-121">建立模型</span><span class="sxs-lookup"><span data-stu-id="c7b18-121">Create the model</span></span>

<span data-ttu-id="c7b18-122">定義組成模型的內容和實體類別。</span><span class="sxs-lookup"><span data-stu-id="c7b18-122">Define a context and entity classes that make up your model.</span></span>

* <span data-ttu-id="c7b18-123">建立具有下列內容的新 *Model.cs* 檔案。</span><span class="sxs-lookup"><span data-stu-id="c7b18-123">Create a new *Model.cs* file with the following contents.</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Model.cs)]

<span data-ttu-id="c7b18-124">提示：在實際的應用程式中，您會將每個類別放在不同的檔案中，然後將連接字串放在設定檔或環境變數中。</span><span class="sxs-lookup"><span data-stu-id="c7b18-124">Tip: In a real application, you put each class in a separate file and put the connection string in a configuration file or environment variable.</span></span> <span data-ttu-id="c7b18-125">為了簡化教學課程，所有項目會統一放在一個檔案中。</span><span class="sxs-lookup"><span data-stu-id="c7b18-125">To keep the tutorial simple, everything is contained in one file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="c7b18-126">建立資料庫</span><span class="sxs-lookup"><span data-stu-id="c7b18-126">Create the database</span></span>

<span data-ttu-id="c7b18-127">一旦有模型後，您就可以使用[移轉](xref:core/managing-schemas/migrations/index)來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="c7b18-127">Once you have a model, you use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

* <span data-ttu-id="c7b18-128">執行 `dotnet ef migrations add InitialCreate` 以建立移轉的結構，並針對模型建立一組初始的資料表。</span><span class="sxs-lookup"><span data-stu-id="c7b18-128">Run `dotnet ef migrations add InitialCreate` to scaffold a migration and create the initial set of tables for the model.</span></span>
* <span data-ttu-id="c7b18-129">執行 `dotnet ef database update` 以將新的移轉套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="c7b18-129">Run `dotnet ef database update` to apply the new migration to the database.</span></span> <span data-ttu-id="c7b18-130">此命令會建立資料庫，然後才套用移轉。</span><span class="sxs-lookup"><span data-stu-id="c7b18-130">This command creates the database before applying migrations.</span></span>

<span data-ttu-id="c7b18-131">*blogging.db* SQLite DB 位於專案目錄中。</span><span class="sxs-lookup"><span data-stu-id="c7b18-131">The *blogging.db* SQLite DB is in the project directory.</span></span>

## <a name="use-the-model"></a><span data-ttu-id="c7b18-132">使用模型</span><span class="sxs-lookup"><span data-stu-id="c7b18-132">Use the model</span></span>

* <span data-ttu-id="c7b18-133">開啟 *Program.cs* 並使用下列程式碼來取代內容：</span><span class="sxs-lookup"><span data-stu-id="c7b18-133">Open *Program.cs* and replace the contents with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Program.cs)]

* <span data-ttu-id="c7b18-134">從主控台測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="c7b18-134">Test the app from the console.</span></span> <span data-ttu-id="c7b18-135">請參閱 [Visual Studio 注意事項](#vs) 以透過 Visual Studio 執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="c7b18-135">See the [Visual Studio note](#vs) to run the app from Visual Studio.</span></span>

  `dotnet run`

  <span data-ttu-id="c7b18-136">有一個部落格儲存至資料庫，且所有部落格的詳細資料會在主控台中顯示。</span><span class="sxs-lookup"><span data-stu-id="c7b18-136">One blog is saved to the database and the details of all blogs are displayed in the console.</span></span>

  ```Console
  ConsoleApp.SQLite>dotnet run
  1 records saved to database

  All blogs in database:
   - http://blogs.msdn.com/adonet
  ```

### <a name="changing-the-model"></a><span data-ttu-id="c7b18-137">變更模型：</span><span class="sxs-lookup"><span data-stu-id="c7b18-137">Changing the model:</span></span>

- <span data-ttu-id="c7b18-138">若要變更模型，您可以使用 `dotnet ef migrations add` 命令來建立新的[移轉](xref:core/managing-schemas/migrations/index)支架。</span><span class="sxs-lookup"><span data-stu-id="c7b18-138">If you make changes to the model, you can use the `dotnet ef migrations add` command to scaffold a new [migration](xref:core/managing-schemas/migrations/index).</span></span> <span data-ttu-id="c7b18-139">一旦檢查支架程式碼 (並進行任何必要的變更)，您就可以使用 `dotnet ef database update` 命令將結構描述變更套用到資料庫。</span><span class="sxs-lookup"><span data-stu-id="c7b18-139">Once you have checked the scaffolded code (and made any required changes), you can use the `dotnet ef database update` command to apply the schema changes to the database.</span></span>
- <span data-ttu-id="c7b18-140">EF Core 在資料庫中使用 `__EFMigrationsHistory` 資料表，以追蹤已套用到資料庫的移轉。</span><span class="sxs-lookup"><span data-stu-id="c7b18-140">EF Core uses a `__EFMigrationsHistory` table in the database to keep track of which migrations have already been applied to the database.</span></span>
- <span data-ttu-id="c7b18-141">SQLite 資料庫引擎不支援大部分其他關聯式資料庫所支援的特定結構描述變更。</span><span class="sxs-lookup"><span data-stu-id="c7b18-141">The SQLite database engine doesn't support certain schema changes that are supported by most other relational databases.</span></span> <span data-ttu-id="c7b18-142">例如，不支援 `DropColumn` 作業。</span><span class="sxs-lookup"><span data-stu-id="c7b18-142">For example, the `DropColumn` operation is not supported.</span></span> <span data-ttu-id="c7b18-143">EF Core 移轉將會產生這些作業的程式碼。</span><span class="sxs-lookup"><span data-stu-id="c7b18-143">EF Core Migrations will generate code for these operations.</span></span> <span data-ttu-id="c7b18-144">但若您嘗試將它們套用到資料庫或產生指令碼，EF Core 會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="c7b18-144">But if you try to apply them to a database or generate a script, EF Core throws exceptions.</span></span> <span data-ttu-id="c7b18-145">請參閱 [SQLite 限制](../../providers/sqlite/limitations.md)。</span><span class="sxs-lookup"><span data-stu-id="c7b18-145">See [SQLite Limitations](../../providers/sqlite/limitations.md).</span></span> <span data-ttu-id="c7b18-146">針對新的開發，當模型變更時，請考慮捨棄資料庫並建立一個新的資料庫，而不是使用移轉。</span><span class="sxs-lookup"><span data-stu-id="c7b18-146">For new development, consider dropping the database and creating a new one rather than using migrations when the model changes.</span></span>

<a name="vs"></a>
### <a name="run-from-visual-studio"></a><span data-ttu-id="c7b18-147">透過 Visual Studio 來執行</span><span class="sxs-lookup"><span data-stu-id="c7b18-147">Run from Visual Studio</span></span>

<span data-ttu-id="c7b18-148">若要透過 Visual Studio 執行此範例，您必須將工作目錄手動設定為專案的根目錄。</span><span class="sxs-lookup"><span data-stu-id="c7b18-148">To run this sample from Visual Studio, you must set the working directory manually to be the root of the project.</span></span> <span data-ttu-id="c7b18-149">如果您未設定工作目錄，就會擲回下列 `Microsoft.Data.Sqlite.SqliteException`：`SQLite Error 1: 'no such table: Blogs'`。</span><span class="sxs-lookup"><span data-stu-id="c7b18-149">If  you don't set the working directory, the following `Microsoft.Data.Sqlite.SqliteException` is thrown: `SQLite Error 1: 'no such table: Blogs'`.</span></span>

<span data-ttu-id="c7b18-150">若要設定工作目錄：</span><span class="sxs-lookup"><span data-stu-id="c7b18-150">To set the working directory:</span></span>

* <span data-ttu-id="c7b18-151">在 [方案總管]  中，以滑鼠右鍵按一下專案，然後選取 [屬性]  。</span><span class="sxs-lookup"><span data-stu-id="c7b18-151">In **Solution Explorer**, right click the project and then select **Properties**.</span></span>
* <span data-ttu-id="c7b18-152">選取左窗格中的 [偵錯]  索引標籤。</span><span class="sxs-lookup"><span data-stu-id="c7b18-152">Select the **Debug** tab in the left pane.</span></span>
* <span data-ttu-id="c7b18-153">將**工作目錄**設定為專案目錄。</span><span class="sxs-lookup"><span data-stu-id="c7b18-153">Set **Working directory** to the project directory.</span></span>
* <span data-ttu-id="c7b18-154">儲存變更。</span><span class="sxs-lookup"><span data-stu-id="c7b18-154">Save the changes.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c7b18-155">其他資源</span><span class="sxs-lookup"><span data-stu-id="c7b18-155">Additional Resources</span></span>

* [<span data-ttu-id="c7b18-156">教學課程：在 ASP.NET Core 上使用 EF Core 搭配新資料庫 (使用 SQLite) 的使用者入門</span><span class="sxs-lookup"><span data-stu-id="c7b18-156">Tutorial: Get started with EF Core on ASP.NET Core with a new database using SQLite</span></span>](xref:core/get-started/aspnetcore/new-db)
* [<span data-ttu-id="c7b18-157">教學課程：開始使用 ASP.NET Core 中的 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="c7b18-157">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="c7b18-158">教學課程：ASP.NET Core 中的 Razor Pages 與 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="c7b18-158">Tutorial: Razor Pages with Entity Framework Core in ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/data/ef-rp/intro)
