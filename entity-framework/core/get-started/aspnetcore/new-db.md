---
title: ASP.NET Core 使用者入門 - 新資料庫 - EF Core
author: rick-anderson
ms.author: riande
ms.date: 08/03/2018
ms.assetid: e153627f-f132-4c11-b13c-6c9a607addce
uid: core/get-started/aspnetcore/new-db
ms.openlocfilehash: 878478099878e4a0bc65c44fef0609d28f39f2b8
ms.sourcegitcommit: 7a7da65404c9338e1e3df42576a13be536a6f95f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2018
ms.locfileid: "48834769"
---
# <a name="getting-started-with-ef-core-on-aspnet-core-with-a-new-database"></a><span data-ttu-id="b7c3f-102">在 ASP.NET Core 上使用 EF Core 搭配新資料庫的使用者入門</span><span class="sxs-lookup"><span data-stu-id="b7c3f-102">Getting Started with EF Core on ASP.NET Core with a New database</span></span>

<span data-ttu-id="b7c3f-103">在本教學課程中，您將會建置 ASP.NET Core MVC 應用程式，而其使用 Entity Framework Core 執行基本的資料存取。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-103">In this tutorial, you build an ASP.NET Core MVC application that performs basic data access using Entity Framework Core.</span></span> <span data-ttu-id="b7c3f-104">本教學課程會使用移轉以從資料模型建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-104">The tutorial uses migrations to create the database from the data model.</span></span>

<span data-ttu-id="b7c3f-105">若要遵循本教學課程，您可以在 Windows 上使用 Visual Studio 2017 或在 Windows、macOS 或 Linux 上使用 .NET Core CLI。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-105">You can follow the tutorial by using Visual Studio 2017 on Windows, or by using the .NET Core CLI on Windows, macOS, or Linux.</span></span>

<span data-ttu-id="b7c3f-106">在 GitHub 上檢視此文章的範例：</span><span class="sxs-lookup"><span data-stu-id="b7c3f-106">View this article's sample on GitHub:</span></span>
* [<span data-ttu-id="b7c3f-107">搭配使用 Visual Studio 2017 與 SQL Server</span><span class="sxs-lookup"><span data-stu-id="b7c3f-107">Visual Studio 2017 with SQL Server</span></span>](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb)
* <span data-ttu-id="b7c3f-108">[搭配使用 .NET Core CLI 與 SQLite](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite)。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-108">[.NET Core CLI with SQLite](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b7c3f-109">必要條件</span><span class="sxs-lookup"><span data-stu-id="b7c3f-109">Prerequisites</span></span>

<span data-ttu-id="b7c3f-110">安裝下列軟體：</span><span class="sxs-lookup"><span data-stu-id="b7c3f-110">Install the following software:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b7c3f-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7c3f-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b7c3f-112">[Visual Studio 2017 15.7 版或更新版本](https://www.visualstudio.com/downloads/)以及下列工作負載：</span><span class="sxs-lookup"><span data-stu-id="b7c3f-112">[Visual Studio 2017 version 15.7 or later](https://www.visualstudio.com/downloads/) with these workloads:</span></span>
  * <span data-ttu-id="b7c3f-113">**ASP.NET 與網頁程式開發** (位在 [Web & Cloud] \(Web 與雲端) 下)</span><span class="sxs-lookup"><span data-stu-id="b7c3f-113">**ASP.NET and web development** (under **Web & Cloud**)</span></span>
  * <span data-ttu-id="b7c3f-114">**.NET Core 跨平台開發** (位在 [其他工具組] 下)</span><span class="sxs-lookup"><span data-stu-id="b7c3f-114">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>
* <span data-ttu-id="b7c3f-115">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core)。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-115">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core).</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b7c3f-116">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b7c3f-116">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="b7c3f-117">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core)。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-117">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core).</span></span>

---

## <a name="create-a-new-project"></a><span data-ttu-id="b7c3f-118">建立新專案</span><span class="sxs-lookup"><span data-stu-id="b7c3f-118">Create a new project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b7c3f-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7c3f-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b7c3f-120">開啟 Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="b7c3f-120">Open Visual Studio 2017</span></span>
* <span data-ttu-id="b7c3f-121">[檔案] > [新增] > [專案]</span><span class="sxs-lookup"><span data-stu-id="b7c3f-121">**File > New > Project**</span></span>
* <span data-ttu-id="b7c3f-122">從左側功能表選取 [已安裝] > [Visual C#] > [.NET Core]。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-122">From the left menu, select **Installed > Visual C# > .NET Core**.</span></span>
* <span data-ttu-id="b7c3f-123">選取 [ASP.NET Core Web 應用程式]。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-123">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="b7c3f-124">輸入 **EFGetStarted.AspNetCore.NewDb** 作為名稱，然後按一下 [確定]。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-124">Enter **EFGetStarted.AspNetCore.NewDb** for the name and click **OK**.</span></span>
* <span data-ttu-id="b7c3f-125">在 [新增 ASP.NET Core Web 應用程式] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="b7c3f-125">In the **New ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="b7c3f-126">確認下拉式清單中已選取 **NET Core** 和 **ASP.NET Core 2.1** 選項</span><span class="sxs-lookup"><span data-stu-id="b7c3f-126">Make sure that **.NET Core** and **ASP.NET Core 2.1** are selected in the drop-down lists</span></span>
  * <span data-ttu-id="b7c3f-127">選取 [Web 應用程式 (模型-檢視-控制器)] 專案範本</span><span class="sxs-lookup"><span data-stu-id="b7c3f-127">Select the **Web Application (Model-View-Controller)** project template</span></span>
  * <span data-ttu-id="b7c3f-128">確認 [驗證] 已設為 [無任何驗證]</span><span class="sxs-lookup"><span data-stu-id="b7c3f-128">Make sure that **Authentication** is set to **No Authentication**</span></span>
  * <span data-ttu-id="b7c3f-129">按一下 [確定] </span><span class="sxs-lookup"><span data-stu-id="b7c3f-129">Click **OK**</span></span>

<span data-ttu-id="b7c3f-130">警告：如果您針對 [驗證] 使用 [個別使用者帳戶] 而不是 [無]，則系統會將 Entity Framework Core 模型新增至 `Models\IdentityModel.cs` 中的專案。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-130">Warning: If you use **Individual User Accounts** instead of **None** for **Authentication** then an Entity Framework Core model will be added to the project in `Models\IdentityModel.cs`.</span></span> <span data-ttu-id="b7c3f-131">您可利用在本教學課程中所學到的技術，選擇新增第二個模型，或是擴充此現有模型以包含您的實體類別。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-131">Using the techniques you learn in this tutorial, you can choose to add a second model, or extend this existing model to contain your entity classes.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b7c3f-132">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b7c3f-132">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="b7c3f-133">請執行下列命令來建立 MVC 專案：</span><span class="sxs-lookup"><span data-stu-id="b7c3f-133">Run the following command to create an MVC project:</span></span>

   ```cli
   dotnet new mvc -n EFGetStarted.AspNetCore.NewDb
   ```
* <span data-ttu-id="b7c3f-134">變更為專案目錄。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-134">Change to the project directory.</span></span> <span data-ttu-id="b7c3f-135">針對新專案執行您輸入的後續命令。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-135">The next commands you enter need to run against the new project.</span></span>

   ```cli
   cd EFGetStarted.AspNetCore.NewDb
   ```
---

## <a name="install-entity-framework-core"></a><span data-ttu-id="b7c3f-136">安裝 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="b7c3f-136">Install Entity Framework Core</span></span>

<span data-ttu-id="b7c3f-137">若要安裝 EF Core，請為希望作為目標的 EF Core 資料庫提供者，安裝此套件。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-137">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="b7c3f-138">如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-138">For a list of available providers, see [Database Providers](../../providers/index.md).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b7c3f-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7c3f-139">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b7c3f-140">在本教學課程中，因為使用 SQL Server，所以無需安裝提供者套件。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-140">For this tutorial, you don't have to install a provider package because the tutorial uses SQL Server.</span></span> <span data-ttu-id="b7c3f-141">[Microsoft.AspnetCore.App 中繼套件](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1)包含 SQL Server 提供者套件。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-141">The SQL Server provider package is included in the [Microsoft.AspnetCore.App metapackage](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1).</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b7c3f-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b7c3f-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b7c3f-143">本教學課程會使用 SQLite，因為它可以在 .NET Core 支援的所有平台上執行。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-143">This tutorial uses SQLite because it runs on all platforms that .NET Core supports.</span></span>

* <span data-ttu-id="b7c3f-144">執行下列命令以安裝 SQLite 提供者：</span><span class="sxs-lookup"><span data-stu-id="b7c3f-144">Run the following command to install the SQLite provider:</span></span>

   ```cli
   dotnet add package Microsoft.EntityFrameworkCore.Sqlite
   ```

---

## <a name="create-the-model"></a><span data-ttu-id="b7c3f-145">建立模型</span><span class="sxs-lookup"><span data-stu-id="b7c3f-145">Create the model</span></span>

<span data-ttu-id="b7c3f-146">定義組成模型的內容類別與實體類別。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-146">Define a context class and entity classes that make up the model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b7c3f-147">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7c3f-147">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b7c3f-148">以滑鼠右鍵按一下 [Models] 資料夾，然後選取 [新增] > [類別]。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-148">Right-click on the **Models** folder and select **Add > Class**.</span></span>
* <span data-ttu-id="b7c3f-149">輸入 **Model.cs** 作為名稱，然後按一下 [確定]。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-149">Enter **Model.cs** as the name and click **OK**.</span></span>
* <span data-ttu-id="b7c3f-150">以下列程式碼取代檔案的內容：</span><span class="sxs-lookup"><span data-stu-id="b7c3f-150">Replace the contents of the file with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Models/Model.cs)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b7c3f-151">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b7c3f-151">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="b7c3f-152">在 **Models** 資料夾中，用下列程式碼建立 **Model.cs**：</span><span class="sxs-lookup"><span data-stu-id="b7c3f-152">In the **Models** folder create **Model.cs** with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite/Models/Model.cs)]

---

<span data-ttu-id="b7c3f-153">生產環境應用程式通常會將每個類別放在不同的檔案中。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-153">A production app would typically put each class in a separate file.</span></span> <span data-ttu-id="b7c3f-154">為了簡單起見，本教學課程會將這些類別置於同一個檔案中。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-154">For the sake of simplicity, this tutorial puts these classes in one file.</span></span>

## <a name="register-the-context-with-dependency-injection"></a><span data-ttu-id="b7c3f-155">使用相依性插入來註冊內容</span><span class="sxs-lookup"><span data-stu-id="b7c3f-155">Register the context with dependency injection</span></span>

<span data-ttu-id="b7c3f-156">服務 (例如 `BloggingContext`) 是在應用程式啟動期間使用[相依性插入](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html)來註冊。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-156">Services (such as `BloggingContext`) are registered with [dependency injection](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html) during application startup.</span></span> <span data-ttu-id="b7c3f-157">接著，系統會透過建構函式參數或屬性，將這些服務提供給需要的元件 (例如 MVC 控制器)。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-157">Components that require these services (such as MVC controllers) are provided these services via constructor parameters or properties.</span></span>

<span data-ttu-id="b7c3f-158">若要為 MVC 控制器提供 `BloggingContext`，請將其註冊為服務。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-158">To make `BloggingContext` available to MVC controllers, register it as a service.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b7c3f-159">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7c3f-159">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b7c3f-160">在 **Startup.cs** 中，新增下列 `using` 陳述式：</span><span class="sxs-lookup"><span data-stu-id="b7c3f-160">In **Startup.cs** add the following `using` statements:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs#AddedUsings)]

* <span data-ttu-id="b7c3f-161">將下列反白顯示的程式碼新增至 `ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="b7c3f-161">Add the following highlighted code to the `ConfigureServices` method:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs?name=ConfigureServices&highlight=12-14)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b7c3f-162">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b7c3f-162">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="b7c3f-163">在 **Startup.cs** 中，新增下列 `using` 陳述式：</span><span class="sxs-lookup"><span data-stu-id="b7c3f-163">In **Startup.cs** add the following `using` statements:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite/Startup.cs#AddedUsings)]

* <span data-ttu-id="b7c3f-164">將下列反白顯示的程式碼新增至 `ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="b7c3f-164">Add the following highlighted code to the `ConfigureServices` method:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite/Startup.cs?name=ConfigureServices&highlight=12-14)]

---

<span data-ttu-id="b7c3f-165">生產環境應用程式一般會將連接字串放在設定檔或環境變數中。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-165">A production app would typically put the connection string in a configuration file or environment variable.</span></span> <span data-ttu-id="b7c3f-166">為了簡單起見，本教學課程將其定義在程式碼中。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-166">For the sake of simplicity, this tutorial defines it in code.</span></span> <span data-ttu-id="b7c3f-167">如需詳細資訊，請參閱[連接字串](../../miscellaneous/connection-strings.md)。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-167">See [Connection Strings](../../miscellaneous/connection-strings.md) for more information.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="b7c3f-168">建立資料庫</span><span class="sxs-lookup"><span data-stu-id="b7c3f-168">Create the database</span></span>

<span data-ttu-id="b7c3f-169">下列步驟會使用[移轉](xref:core/managing-schemas/migrations/index)來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-169">The following steps use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b7c3f-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7c3f-170">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b7c3f-171">[工具] > [NuGet 套件管理員] > [套件管理員主控台]</span><span class="sxs-lookup"><span data-stu-id="b7c3f-171">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="b7c3f-172">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="b7c3f-172">Run the following commands:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

  <span data-ttu-id="b7c3f-173">如果您收到 `The term 'add-migration' is not recognized as the name of a cmdlet` 的錯誤，請關閉再重新開啟 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-173">If you get an error stating `The term 'add-migration' is not recognized as the name of a cmdlet`, close and reopen Visual Studio.</span></span>

  <span data-ttu-id="b7c3f-174">`Add-Migration` 命令會建立移轉的 Scaffolding，以針對模型建立一組初始資料表。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-174">The `Add-Migration` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="b7c3f-175">`Update-Database` 命令會建立資料庫，並對資料庫套用新的移轉。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-175">The `Update-Database` command creates the database and applies the new migration to it.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b7c3f-176">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b7c3f-176">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="b7c3f-177">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="b7c3f-177">Run the following commands:</span></span>

  ```cli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  <span data-ttu-id="b7c3f-178">`migrations` 命令會建立移轉的 Scaffolding，以針對模型建立一組初始資料表。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-178">The `migrations` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="b7c3f-179">`database update` 命令會建立資料庫，並對資料庫套用新的移轉。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-179">The `database update` command creates the database and applies the new migration to it.</span></span>

---

## <a name="create-a-controller"></a><span data-ttu-id="b7c3f-180">建立控制器</span><span class="sxs-lookup"><span data-stu-id="b7c3f-180">Create a controller</span></span>

<span data-ttu-id="b7c3f-181">為 `Blog` 實體設定控制器與檢視的結構。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-181">Scaffold a controller and views for the `Blog` entity.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b7c3f-182">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7c3f-182">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b7c3f-183">在 [方案總管] 中以滑鼠右鍵按一下 [Controllers] 資料夾，然後選取 [新增] > [控制器]。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-183">Right-click on the **Controllers** folder in **Solution Explorer** and select **Add > Controller**.</span></span>
* <span data-ttu-id="b7c3f-184">選取 [使用 Entity Framework 執行檢視的 MVC 控制器]，然後按一下 [新增]。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-184">Select **MVC Controller with views, using Entity Framework** and click **Add**.</span></span>
* <span data-ttu-id="b7c3f-185">將 [模型類別] 設為 [Blog]，並將 [資料內容類別] 設為 [BloggingContext]。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-185">Set **Model class** to **Blog** and **Data context class** to **BloggingContext**.</span></span>
* <span data-ttu-id="b7c3f-186">按一下 [加入] 。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-186">Click **Add**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b7c3f-187">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b7c3f-187">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="b7c3f-188">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="b7c3f-188">Run the following commands:</span></span>

  ```cli
  dotnet tool install -g dotnet-aspnet-codegenerator
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet restore
  dotnet aspnet-codegenerator controller -name BlogsController -m Blog -dc BloggingContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  <span data-ttu-id="b7c3f-189">`tool install` 和 `add package` 命令會安裝工具，該工具可以建立控制器和檢視的 Scaffolding。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-189">The `tool install` and `add package` commands install the tooling that can scaffold controllers and views.</span></span> <span data-ttu-id="b7c3f-190">`restore` 命令可確保下載所有專案的套件，而 `aspnet-codegenerator` 命令會執行 Scaffolding。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-190">The `restore` command ensures that all of the project's packages are downloaded, and the `aspnet-codegenerator` command does the scaffolding.</span></span>
---

<span data-ttu-id="b7c3f-191">Scaffolding 引擎會建立下列檔案：</span><span class="sxs-lookup"><span data-stu-id="b7c3f-191">The scaffolding engine creates the following files:</span></span>

* <span data-ttu-id="b7c3f-192">控制器 (*Controllers/BlogsController.cs*)</span><span class="sxs-lookup"><span data-stu-id="b7c3f-192">A controller (*Controllers/BlogsController.cs*)</span></span>
* <span data-ttu-id="b7c3f-193">[建立]、[刪除]、[詳細資料]、[編輯] 和 [索引] 頁面的 Razor 檢視 (_Views/Movies/\*.cshtml_)</span><span class="sxs-lookup"><span data-stu-id="b7c3f-193">Razor views for Create, Delete, Details, Edit, and Index pages (_Views/Movies/\*.cshtml_)</span></span>

## <a name="run-the-application"></a><span data-ttu-id="b7c3f-194">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="b7c3f-194">Run the application</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b7c3f-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7c3f-195">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b7c3f-196">**偵錯** > **啟動但不偵錯**</span><span class="sxs-lookup"><span data-stu-id="b7c3f-196">**Debug** > **Start Without Debugging**</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b7c3f-197">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b7c3f-197">.NET Core CLI</span></span>](#tab/netcore-cli)

```cli
dotnet run
```
---

* <span data-ttu-id="b7c3f-198">巡覽至 `/Blogs`</span><span class="sxs-lookup"><span data-stu-id="b7c3f-198">Navigate to `/Blogs`</span></span>

* <span data-ttu-id="b7c3f-199">使用 [建立新項目] 連結來建立一些部落格項目。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-199">Use the **Create New** link to create some blog entries.</span></span>

  ![建立頁面](_static/create.png)

* <span data-ttu-id="b7c3f-201">測試 [詳細資料]、[編輯] 和 [刪除] 連結。</span><span class="sxs-lookup"><span data-stu-id="b7c3f-201">Test the **Details**, **Edit**, and **Delete** links.</span></span>

  ![索引頁面](_static/index-new-db.png)

## <a name="additional-resources"></a><span data-ttu-id="b7c3f-203">其他資源</span><span class="sxs-lookup"><span data-stu-id="b7c3f-203">Additional Resources</span></span>

* [<span data-ttu-id="b7c3f-204">教學課程：開始使用 .NET Core 上的 EF Core 與使用 SQLite 的新資料庫</span><span class="sxs-lookup"><span data-stu-id="b7c3f-204">Tutorial: Get started with EF Core on .NET Core with a new database using SQLite</span></span>](xref:core/get-started/netcore/new-db-sqlite)
* [<span data-ttu-id="b7c3f-205">教學課程：開始使用 ASP.NET Core 中的 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="b7c3f-205">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="b7c3f-206">教學課程：ASP.NET Core 中的 Razor Pages 與 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="b7c3f-206">Tutorial: Razor Pages with Entity Framework Core in ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/data/ef-rp/intro)
