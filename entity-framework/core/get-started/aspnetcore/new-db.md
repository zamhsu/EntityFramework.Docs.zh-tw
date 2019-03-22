---
title: ASP.NET Core 使用者入門 - 新資料庫 - EF Core
author: rick-anderson
ms.author: riande
ms.date: 08/03/2018
ms.assetid: e153627f-f132-4c11-b13c-6c9a607addce
uid: core/get-started/aspnetcore/new-db
ms.openlocfilehash: 25e5a683acf4bbed0b978cc6a80f1b50a0b64ca1
ms.sourcegitcommit: eb8359b7ab3b0a1a08522faf67b703a00ecdcefd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2019
ms.locfileid: "58319175"
---
# <a name="getting-started-with-ef-core-on-aspnet-core-with-a-new-database"></a><span data-ttu-id="58d78-102">在 ASP.NET Core 上使用 EF Core 搭配新資料庫的使用者入門</span><span class="sxs-lookup"><span data-stu-id="58d78-102">Getting Started with EF Core on ASP.NET Core with a New database</span></span>

<span data-ttu-id="58d78-103">在本教學課程中，您將會建置 ASP.NET Core MVC 應用程式，而其使用 Entity Framework Core 執行基本的資料存取。</span><span class="sxs-lookup"><span data-stu-id="58d78-103">In this tutorial, you build an ASP.NET Core MVC application that performs basic data access using Entity Framework Core.</span></span> <span data-ttu-id="58d78-104">本教學課程會使用移轉以從資料模型建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="58d78-104">The tutorial uses migrations to create the database from the data model.</span></span>

<span data-ttu-id="58d78-105">若要遵循本教學課程，您可以在 Windows 上使用 Visual Studio 2017 或在 Windows、macOS 或 Linux 上使用 .NET Core CLI。</span><span class="sxs-lookup"><span data-stu-id="58d78-105">You can follow the tutorial by using Visual Studio 2017 on Windows, or by using the .NET Core CLI on Windows, macOS, or Linux.</span></span>

<span data-ttu-id="58d78-106">在 GitHub 上檢視此文章的範例：</span><span class="sxs-lookup"><span data-stu-id="58d78-106">View this article's sample on GitHub:</span></span>
* [<span data-ttu-id="58d78-107">搭配使用 Visual Studio 2017 與 SQL Server</span><span class="sxs-lookup"><span data-stu-id="58d78-107">Visual Studio 2017 with SQL Server</span></span>](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb)
* <span data-ttu-id="58d78-108">[搭配使用 .NET Core CLI 與 SQLite](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite)。</span><span class="sxs-lookup"><span data-stu-id="58d78-108">[.NET Core CLI with SQLite](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="58d78-109">必要條件</span><span class="sxs-lookup"><span data-stu-id="58d78-109">Prerequisites</span></span>

<span data-ttu-id="58d78-110">安裝下列軟體：</span><span class="sxs-lookup"><span data-stu-id="58d78-110">Install the following software:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="58d78-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58d78-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="58d78-112">[Visual Studio 2017 15.7 版或更新版本](https://www.visualstudio.com/downloads/)以及下列工作負載：</span><span class="sxs-lookup"><span data-stu-id="58d78-112">[Visual Studio 2017 version 15.7 or later](https://www.visualstudio.com/downloads/) with these workloads:</span></span>
  * <span data-ttu-id="58d78-113">**ASP.NET 與網頁程式開發** (位在 [Web & Cloud] \(Web 與雲端) 下)</span><span class="sxs-lookup"><span data-stu-id="58d78-113">**ASP.NET and web development** (under **Web & Cloud**)</span></span>
  * <span data-ttu-id="58d78-114">**.NET Core 跨平台開發** (位在 [其他工具組] 下)</span><span class="sxs-lookup"><span data-stu-id="58d78-114">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>
* <span data-ttu-id="58d78-115">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core)。</span><span class="sxs-lookup"><span data-stu-id="58d78-115">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core).</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="58d78-116">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="58d78-116">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="58d78-117">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core)。</span><span class="sxs-lookup"><span data-stu-id="58d78-117">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core).</span></span>

---

## <a name="create-a-new-project"></a><span data-ttu-id="58d78-118">建立新專案</span><span class="sxs-lookup"><span data-stu-id="58d78-118">Create a new project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="58d78-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58d78-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="58d78-120">開啟 Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="58d78-120">Open Visual Studio 2017</span></span>
* <span data-ttu-id="58d78-121">[檔案] > [新增] > [專案]</span><span class="sxs-lookup"><span data-stu-id="58d78-121">**File > New > Project**</span></span>
* <span data-ttu-id="58d78-122">從左側功能表選取 [已安裝] > [Visual C#] > [.NET Core]。</span><span class="sxs-lookup"><span data-stu-id="58d78-122">From the left menu, select **Installed > Visual C# > .NET Core**.</span></span>
* <span data-ttu-id="58d78-123">選取 [ASP.NET Core Web 應用程式]。</span><span class="sxs-lookup"><span data-stu-id="58d78-123">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="58d78-124">輸入 **EFGetStarted.AspNetCore.NewDb** 作為名稱，然後按一下 [確定]。</span><span class="sxs-lookup"><span data-stu-id="58d78-124">Enter **EFGetStarted.AspNetCore.NewDb** for the name and click **OK**.</span></span>
* <span data-ttu-id="58d78-125">在 [新增 ASP.NET Core Web 應用程式] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="58d78-125">In the **New ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="58d78-126">確認下拉式清單中已選取 **NET Core** 和 **ASP.NET Core 2.1** 選項</span><span class="sxs-lookup"><span data-stu-id="58d78-126">Make sure that **.NET Core** and **ASP.NET Core 2.1** are selected in the drop-down lists</span></span>
  * <span data-ttu-id="58d78-127">選取 [Web 應用程式 (模型-檢視-控制器)] 專案範本</span><span class="sxs-lookup"><span data-stu-id="58d78-127">Select the **Web Application (Model-View-Controller)** project template</span></span>
  * <span data-ttu-id="58d78-128">確認 [驗證] 已設為 [無任何驗證]</span><span class="sxs-lookup"><span data-stu-id="58d78-128">Make sure that **Authentication** is set to **No Authentication**</span></span>
  * <span data-ttu-id="58d78-129">按一下 [確定] </span><span class="sxs-lookup"><span data-stu-id="58d78-129">Click **OK**</span></span>

<span data-ttu-id="58d78-130">警告：如果您針對 [驗證] 使用 [個別使用者帳戶] 而不是 [無]，則系統會將 Entity Framework Core 模型新增至 `Models\IdentityModel.cs` 中的專案。</span><span class="sxs-lookup"><span data-stu-id="58d78-130">Warning: If you use **Individual User Accounts** instead of **None** for **Authentication** then an Entity Framework Core model will be added to the project in `Models\IdentityModel.cs`.</span></span> <span data-ttu-id="58d78-131">您可利用在本教學課程中所學到的技術，選擇新增第二個模型，或是擴充此現有模型以包含您的實體類別。</span><span class="sxs-lookup"><span data-stu-id="58d78-131">Using the techniques you learn in this tutorial, you can choose to add a second model, or extend this existing model to contain your entity classes.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="58d78-132">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="58d78-132">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="58d78-133">請執行下列命令來建立 MVC 專案：</span><span class="sxs-lookup"><span data-stu-id="58d78-133">Run the following command to create an MVC project:</span></span>

   ```cli
   dotnet new mvc -n EFGetStarted.AspNetCore.NewDb
   ```
* <span data-ttu-id="58d78-134">變更為專案目錄。</span><span class="sxs-lookup"><span data-stu-id="58d78-134">Change to the project directory.</span></span> <span data-ttu-id="58d78-135">針對新專案執行您輸入的後續命令。</span><span class="sxs-lookup"><span data-stu-id="58d78-135">The next commands you enter need to run against the new project.</span></span>

   ```cli
   cd EFGetStarted.AspNetCore.NewDb
   ```
---

## <a name="install-entity-framework-core"></a><span data-ttu-id="58d78-136">安裝 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="58d78-136">Install Entity Framework Core</span></span>

<span data-ttu-id="58d78-137">若要安裝 EF Core，請為希望作為目標的 EF Core 資料庫提供者，安裝此套件。</span><span class="sxs-lookup"><span data-stu-id="58d78-137">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="58d78-138">如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="58d78-138">For a list of available providers, see [Database Providers](../../providers/index.md).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="58d78-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58d78-139">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="58d78-140">在本教學課程中，因為使用 SQL Server，所以無需安裝提供者套件。</span><span class="sxs-lookup"><span data-stu-id="58d78-140">For this tutorial, you don't have to install a provider package because the tutorial uses SQL Server.</span></span> <span data-ttu-id="58d78-141">[Microsoft.AspnetCore.App 中繼套件](https://docs.microsoft.com/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1)包含 SQL Server 提供者套件。</span><span class="sxs-lookup"><span data-stu-id="58d78-141">The SQL Server provider package is included in the [Microsoft.AspnetCore.App metapackage](https://docs.microsoft.com/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1).</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="58d78-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="58d78-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="58d78-143">本教學課程會使用 SQLite，因為它可以在 .NET Core 支援的所有平台上執行。</span><span class="sxs-lookup"><span data-stu-id="58d78-143">This tutorial uses SQLite because it runs on all platforms that .NET Core supports.</span></span>

* <span data-ttu-id="58d78-144">執行下列命令以安裝 SQLite 提供者：</span><span class="sxs-lookup"><span data-stu-id="58d78-144">Run the following command to install the SQLite provider:</span></span>

   ```cli
   dotnet add package Microsoft.EntityFrameworkCore.Sqlite
   ```

---

## <a name="create-the-model"></a><span data-ttu-id="58d78-145">建立模型</span><span class="sxs-lookup"><span data-stu-id="58d78-145">Create the model</span></span>

<span data-ttu-id="58d78-146">定義組成模型的內容類別與實體類別。</span><span class="sxs-lookup"><span data-stu-id="58d78-146">Define a context class and entity classes that make up the model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="58d78-147">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58d78-147">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="58d78-148">以滑鼠右鍵按一下 [Models] 資料夾，然後選取 [新增] > [類別]。</span><span class="sxs-lookup"><span data-stu-id="58d78-148">Right-click on the **Models** folder and select **Add > Class**.</span></span>
* <span data-ttu-id="58d78-149">輸入 **Model.cs** 作為名稱，然後按一下 [確定]。</span><span class="sxs-lookup"><span data-stu-id="58d78-149">Enter **Model.cs** as the name and click **OK**.</span></span>
* <span data-ttu-id="58d78-150">以下列程式碼取代檔案的內容：</span><span class="sxs-lookup"><span data-stu-id="58d78-150">Replace the contents of the file with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Models/Model.cs)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="58d78-151">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="58d78-151">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="58d78-152">在 **Models** 資料夾中，用下列程式碼建立 **Model.cs**：</span><span class="sxs-lookup"><span data-stu-id="58d78-152">In the **Models** folder create **Model.cs** with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite/Models/Model.cs)]

---

<span data-ttu-id="58d78-153">生產環境應用程式通常會將每個類別放在不同的檔案中。</span><span class="sxs-lookup"><span data-stu-id="58d78-153">A production app would typically put each class in a separate file.</span></span> <span data-ttu-id="58d78-154">為了簡單起見，本教學課程會將這些類別置於同一個檔案中。</span><span class="sxs-lookup"><span data-stu-id="58d78-154">For the sake of simplicity, this tutorial puts these classes in one file.</span></span>

## <a name="register-the-context-with-dependency-injection"></a><span data-ttu-id="58d78-155">使用相依性插入來註冊內容</span><span class="sxs-lookup"><span data-stu-id="58d78-155">Register the context with dependency injection</span></span>

<span data-ttu-id="58d78-156">若要為 MVC 控制器提供 `BloggingContext`，請在 `Startup.cs` 中將其註冊為服務。</span><span class="sxs-lookup"><span data-stu-id="58d78-156">To make `BloggingContext` available to MVC controllers, register it as a service in `Startup.cs`.</span></span>

<span data-ttu-id="58d78-157">服務 (例如 `BloggingContext`) 均會在應用程式啟動期間以[相依性插入](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html)註冊，使其能自動獲提供透過建構函式參數與屬性取用服務 (例如 MVC 控制器) 的元件。</span><span class="sxs-lookup"><span data-stu-id="58d78-157">Services (such as `BloggingContext`) are registered with [dependency injection](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html) during application startup so that they can be provided automatically to components that consume services (such as MVC controllers) via constructor parameters and properties.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="58d78-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58d78-158">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="58d78-159">在 **Startup.cs** 中，新增下列 `using` 陳述式：</span><span class="sxs-lookup"><span data-stu-id="58d78-159">In **Startup.cs** add the following `using` statements:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs#AddedUsings)]

* <span data-ttu-id="58d78-160">將下列反白顯示的程式碼新增至 `ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="58d78-160">Add the following highlighted code to the `ConfigureServices` method:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs?name=ConfigureServices&highlight=12-14)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="58d78-161">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="58d78-161">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="58d78-162">在 **Startup.cs** 中，新增下列 `using` 陳述式：</span><span class="sxs-lookup"><span data-stu-id="58d78-162">In **Startup.cs** add the following `using` statements:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite/Startup.cs#AddedUsings)]

* <span data-ttu-id="58d78-163">將下列反白顯示的程式碼新增至 `ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="58d78-163">Add the following highlighted code to the `ConfigureServices` method:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite/Startup.cs?name=ConfigureServices&highlight=12-14)]

---

<span data-ttu-id="58d78-164">生產環境應用程式一般會將連接字串放在設定檔或環境變數中。</span><span class="sxs-lookup"><span data-stu-id="58d78-164">A production app would typically put the connection string in a configuration file or environment variable.</span></span> <span data-ttu-id="58d78-165">為了簡單起見，本教學課程將其定義在程式碼中。</span><span class="sxs-lookup"><span data-stu-id="58d78-165">For the sake of simplicity, this tutorial defines it in code.</span></span> <span data-ttu-id="58d78-166">如需詳細資訊，請參閱[連接字串](../../miscellaneous/connection-strings.md)。</span><span class="sxs-lookup"><span data-stu-id="58d78-166">See [Connection Strings](../../miscellaneous/connection-strings.md) for more information.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="58d78-167">建立資料庫</span><span class="sxs-lookup"><span data-stu-id="58d78-167">Create the database</span></span>

<span data-ttu-id="58d78-168">下列步驟會使用[移轉](xref:core/managing-schemas/migrations/index)來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="58d78-168">The following steps use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="58d78-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58d78-169">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="58d78-170">[工具] > [NuGet 套件管理員] > [套件管理員主控台]</span><span class="sxs-lookup"><span data-stu-id="58d78-170">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="58d78-171">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="58d78-171">Run the following commands:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

  <span data-ttu-id="58d78-172">如果您收到 `The term 'add-migration' is not recognized as the name of a cmdlet` 的錯誤，請關閉再重新開啟 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="58d78-172">If you get an error stating `The term 'add-migration' is not recognized as the name of a cmdlet`, close and reopen Visual Studio.</span></span>

  <span data-ttu-id="58d78-173">`Add-Migration` 命令會建立移轉的 Scaffolding，以針對模型建立一組初始資料表。</span><span class="sxs-lookup"><span data-stu-id="58d78-173">The `Add-Migration` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="58d78-174">`Update-Database` 命令會建立資料庫，並對資料庫套用新的移轉。</span><span class="sxs-lookup"><span data-stu-id="58d78-174">The `Update-Database` command creates the database and applies the new migration to it.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="58d78-175">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="58d78-175">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="58d78-176">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="58d78-176">Run the following commands:</span></span>

  ```cli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  <span data-ttu-id="58d78-177">`migrations` 命令會建立移轉的 Scaffolding，以針對模型建立一組初始資料表。</span><span class="sxs-lookup"><span data-stu-id="58d78-177">The `migrations` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="58d78-178">`database update` 命令會建立資料庫，並對資料庫套用新的移轉。</span><span class="sxs-lookup"><span data-stu-id="58d78-178">The `database update` command creates the database and applies the new migration to it.</span></span>

---

## <a name="create-a-controller"></a><span data-ttu-id="58d78-179">建立控制器</span><span class="sxs-lookup"><span data-stu-id="58d78-179">Create a controller</span></span>

<span data-ttu-id="58d78-180">為 `Blog` 實體設定控制器與檢視的結構。</span><span class="sxs-lookup"><span data-stu-id="58d78-180">Scaffold a controller and views for the `Blog` entity.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="58d78-181">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58d78-181">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="58d78-182">在 [方案總管] 中以滑鼠右鍵按一下 [Controllers] 資料夾，然後選取 [新增] > [控制器]。</span><span class="sxs-lookup"><span data-stu-id="58d78-182">Right-click on the **Controllers** folder in **Solution Explorer** and select **Add > Controller**.</span></span>
* <span data-ttu-id="58d78-183">選取 [使用 Entity Framework 執行檢視的 MVC 控制器]，然後按一下 [新增]。</span><span class="sxs-lookup"><span data-stu-id="58d78-183">Select **MVC Controller with views, using Entity Framework** and click **Add**.</span></span>
* <span data-ttu-id="58d78-184">將 [模型類別] 設為 [Blog]，並將 [資料內容類別] 設為 [BloggingContext]。</span><span class="sxs-lookup"><span data-stu-id="58d78-184">Set **Model class** to **Blog** and **Data context class** to **BloggingContext**.</span></span>
* <span data-ttu-id="58d78-185">按一下 [加入] 。</span><span class="sxs-lookup"><span data-stu-id="58d78-185">Click **Add**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="58d78-186">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="58d78-186">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="58d78-187">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="58d78-187">Run the following commands:</span></span>

  ```cli
  dotnet tool install -g dotnet-aspnet-codegenerator
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet restore
  dotnet aspnet-codegenerator controller -name BlogsController -m Blog -dc BloggingContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  <span data-ttu-id="58d78-188">`tool install` 和 `add package` 命令會安裝工具，該工具可以建立控制器和檢視的 Scaffolding。</span><span class="sxs-lookup"><span data-stu-id="58d78-188">The `tool install` and `add package` commands install the tooling that can scaffold controllers and views.</span></span> <span data-ttu-id="58d78-189">`restore` 命令可確保下載所有專案的套件，而 `aspnet-codegenerator` 命令會執行 Scaffolding。</span><span class="sxs-lookup"><span data-stu-id="58d78-189">The `restore` command ensures that all of the project's packages are downloaded, and the `aspnet-codegenerator` command does the scaffolding.</span></span>
---

<span data-ttu-id="58d78-190">Scaffolding 引擎會建立下列檔案：</span><span class="sxs-lookup"><span data-stu-id="58d78-190">The scaffolding engine creates the following files:</span></span>

* <span data-ttu-id="58d78-191">控制器 (*Controllers/BlogsController.cs*)</span><span class="sxs-lookup"><span data-stu-id="58d78-191">A controller (*Controllers/BlogsController.cs*)</span></span>
* <span data-ttu-id="58d78-192">[建立]、[刪除]、[詳細資料]、[編輯] 和 [索引] 頁面的 Razor 檢視 (_Views/Blogs/\*.cshtml_)</span><span class="sxs-lookup"><span data-stu-id="58d78-192">Razor views for Create, Delete, Details, Edit, and Index pages (_Views/Blogs/\*.cshtml_)</span></span>

## <a name="run-the-application"></a><span data-ttu-id="58d78-193">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="58d78-193">Run the application</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="58d78-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58d78-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="58d78-195">**偵錯** > **啟動但不偵錯**</span><span class="sxs-lookup"><span data-stu-id="58d78-195">**Debug** > **Start Without Debugging**</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="58d78-196">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="58d78-196">.NET Core CLI</span></span>](#tab/netcore-cli)

```cli
dotnet run
```
---

* <span data-ttu-id="58d78-197">巡覽至 `/Blogs`</span><span class="sxs-lookup"><span data-stu-id="58d78-197">Navigate to `/Blogs`</span></span>

* <span data-ttu-id="58d78-198">使用 [建立新項目] 連結來建立一些部落格項目。</span><span class="sxs-lookup"><span data-stu-id="58d78-198">Use the **Create New** link to create some blog entries.</span></span>

  ![建立頁面](_static/create.png)

* <span data-ttu-id="58d78-200">測試 [詳細資料]、[編輯] 和 [刪除] 連結。</span><span class="sxs-lookup"><span data-stu-id="58d78-200">Test the **Details**, **Edit**, and **Delete** links.</span></span>

  ![索引頁面](_static/index-new-db.png)

## <a name="additional-resources"></a><span data-ttu-id="58d78-202">其他資源</span><span class="sxs-lookup"><span data-stu-id="58d78-202">Additional Resources</span></span>

* [<span data-ttu-id="58d78-203">教學課程：在 ASP.NET Core 上使用 EF Core 搭配新資料庫的使用者入門</span><span class="sxs-lookup"><span data-stu-id="58d78-203">Tutorial: Get started with EF Core on .NET Core with a new database using SQLite</span></span>](xref:core/get-started/netcore/new-db-sqlite)
* [<span data-ttu-id="58d78-204">教學課程：開始使用 ASP.NET Core 中的 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="58d78-204">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="58d78-205">教學課程：ASP.NET Core 中的 Razor Pages 與 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="58d78-205">Tutorial: Razor Pages with Entity Framework Core in ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/data/ef-rp/intro)
