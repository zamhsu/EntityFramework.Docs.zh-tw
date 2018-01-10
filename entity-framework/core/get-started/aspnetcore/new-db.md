---
title: "ASP.NET Core 使用者入門 - 新資料庫 - EF Core"
author: rick-anderson
ms.author: riande
ms.author2: tdykstra
ms.date: 04/07/2017
ms.topic: get-started-article
ms.assetid: e153627f-f132-4c11-b13c-6c9a607addce
ms.technology: entity-framework-core
uid: core/get-started/aspnetcore/new-db
ms.openlocfilehash: 7e7ecaff29e9830bf3bcf742e6a5d54e1ced24de
ms.sourcegitcommit: 860ec5d047342fbc4063a0de881c9861cc1f8813
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2017
---
# <a name="getting-started-with-ef-core-on-aspnet-core-with-a-new-database"></a><span data-ttu-id="b1b2e-102">在 ASP.NET Core 上使用 EF Core 搭配新資料庫的使用者入門</span><span class="sxs-lookup"><span data-stu-id="b1b2e-102">Getting Started with EF Core on ASP.NET Core with a New database</span></span>

<span data-ttu-id="b1b2e-103">在本逐步解說中，您將建置 ASP.NET Core MVC 應用程式，該應用程式將使用 Entity Framework Core 執行基本的資料存取。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-103">In this walkthrough, you will build an ASP.NET Core MVC application that performs basic data access using Entity Framework Core.</span></span> <span data-ttu-id="b1b2e-104">您將使用移轉，以便從您的 EF Core 模型來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-104">You will use migrations to create the database from your EF Core model.</span></span> <span data-ttu-id="b1b2e-105">如需 Entity Framework Core 的教學課程，請參閱[其他資源](#additional-resources)。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-105">See [Additional Resources](#additional-resources) for more Entity Framework Core tutorials.</span></span>

<span data-ttu-id="b1b2e-106">本教學課程需要：</span><span class="sxs-lookup"><span data-stu-id="b1b2e-106">This tutorial requires:</span></span>
* <span data-ttu-id="b1b2e-107">[Visual Studio 2017 15.3](https://www.visualstudio.com/downloads/) 與這些工作負載：</span><span class="sxs-lookup"><span data-stu-id="b1b2e-107">[Visual Studio 2017 15.3](https://www.visualstudio.com/downloads/) with these workloads:</span></span>
  * <span data-ttu-id="b1b2e-108">**ASP.NET 與網頁程式開發** (位在 [Web & Cloud] \(Web 與雲端) 下)</span><span class="sxs-lookup"><span data-stu-id="b1b2e-108">**ASP.NET and web development** (under **Web & Cloud**)</span></span>
  * <span data-ttu-id="b1b2e-109">**.NET Core 跨平台開發** (位在 [其他工具組] 下)</span><span class="sxs-lookup"><span data-stu-id="b1b2e-109">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>
* <span data-ttu-id="b1b2e-110">[.NET Core 2.0 SDK](https://www.microsoft.com/net/download/core)。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-110">[.NET Core 2.0 SDK](https://www.microsoft.com/net/download/core).</span></span>

> [!TIP]  
> <span data-ttu-id="b1b2e-111">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-111">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb) on GitHub.</span></span>

## <a name="create-a-new-project-in-visual-studio-2017"></a><span data-ttu-id="b1b2e-112">在 Visual Studio 2017 中建立新專案</span><span class="sxs-lookup"><span data-stu-id="b1b2e-112">Create a new project in Visual Studio 2017</span></span>

* <span data-ttu-id="b1b2e-113">[檔案] > [新增] > [專案]</span><span class="sxs-lookup"><span data-stu-id="b1b2e-113">**File > New > Project**</span></span>
* <span data-ttu-id="b1b2e-114">從左側功能表選取 [已安裝] > [範本] > [Visual C#] > [.NET Core]。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-114">From the left menu select **Installed > Templates > Visual C# > .NET Core**.</span></span>
* <span data-ttu-id="b1b2e-115">選取 [ASP.NET Core Web 應用程式]。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-115">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="b1b2e-116">輸入 **EFGetStarted.AspNetCore.NewDb** 作為名稱，然後按一下 [確定]。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-116">Enter **EFGetStarted.AspNetCore.NewDb** for the name and click **OK**.</span></span>
* <span data-ttu-id="b1b2e-117">在 [新增 ASP.NET Core Web 應用程式] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="b1b2e-117">In the **New ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="b1b2e-118">確認下拉式清單中已選取 [.NET Core] 和 [ASP.NET Core 2.0] 選項</span><span class="sxs-lookup"><span data-stu-id="b1b2e-118">Ensure the options **.NET Core** and **ASP.NET Core 2.0** are selected in the drop down lists</span></span>
  * <span data-ttu-id="b1b2e-119">選取 [Web 應用程式 (模型-檢視-控制器)] 專案範本</span><span class="sxs-lookup"><span data-stu-id="b1b2e-119">Select the **Web Application (Model-View-Controller)** project template</span></span>
  * <span data-ttu-id="b1b2e-120">確認 [驗證] 已設為 [無驗證]</span><span class="sxs-lookup"><span data-stu-id="b1b2e-120">Ensure that **Authentication** is set to **No Authentication**</span></span>
  * <span data-ttu-id="b1b2e-121">按一下 [確定] </span><span class="sxs-lookup"><span data-stu-id="b1b2e-121">Click **OK**</span></span>

<span data-ttu-id="b1b2e-122">警告：如果您針對 [驗證] 使用 [個別使用者帳戶] 而不是 [無]，則會將 Entity Framework Core 模型新增至您專案中的 `Models\IdentityModel.cs`。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-122">Warning: If you use **Individual User Accounts** instead of **None** for **Authentication** then an Entity Framework Core model will be added to your project in `Models\IdentityModel.cs`.</span></span> <span data-ttu-id="b1b2e-123">您可以利用在本逐步解說中學到的技術，選擇新增第二個模型，或是擴充此現有模型以包含您的實體類別。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-123">Using the techniques you will learn in this walkthrough, you can choose to add a second model, or extend this existing model to contain your entity classes.</span></span>

## <a name="install-entity-framework-core"></a><span data-ttu-id="b1b2e-124">安裝 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="b1b2e-124">Install Entity Framework Core</span></span>

<span data-ttu-id="b1b2e-125">針對您要作為目標的 EF Core 資料庫提供者來安裝套件。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-125">Install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="b1b2e-126">本逐步解說會使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-126">This walkthrough uses SQL Server.</span></span> <span data-ttu-id="b1b2e-127">如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-127">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="b1b2e-128">[工具] > [NuGet 套件管理員] > [套件管理員主控台]</span><span class="sxs-lookup"><span data-stu-id="b1b2e-128">**Tools > NuGet Package Manager > Package Manager Console**</span></span>

* <span data-ttu-id="b1b2e-129">執行 `Install-Package Microsoft.EntityFrameworkCore.SqlServer`</span><span class="sxs-lookup"><span data-stu-id="b1b2e-129">Run `Install-Package Microsoft.EntityFrameworkCore.SqlServer`</span></span>

<span data-ttu-id="b1b2e-130">我們將使用一些 Entity Framework Core 工具，以便從您的 EF Core 模型來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-130">We will be using some Entity Framework Core Tools to create a database from your EF Core model.</span></span> <span data-ttu-id="b1b2e-131">因此，我們也將安裝工具套件：</span><span class="sxs-lookup"><span data-stu-id="b1b2e-131">So we will install the tools package as well:</span></span>

* <span data-ttu-id="b1b2e-132">執行 `Install-Package Microsoft.EntityFrameworkCore.Tools`</span><span class="sxs-lookup"><span data-stu-id="b1b2e-132">Run `Install-Package Microsoft.EntityFrameworkCore.Tools`</span></span>

<span data-ttu-id="b1b2e-133">我們將使用一些 ASP.NET Core Scaffolding 工具，於稍後建立控制器與檢視。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-133">We will be using some ASP.NET Core Scaffolding tools to create controllers and views later on.</span></span> <span data-ttu-id="b1b2e-134">因此，我們也將安裝此設計套件：</span><span class="sxs-lookup"><span data-stu-id="b1b2e-134">So we will install this design package as well:</span></span>

* <span data-ttu-id="b1b2e-135">執行 `Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design`</span><span class="sxs-lookup"><span data-stu-id="b1b2e-135">Run `Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design`</span></span>

## <a name="create-the-model"></a><span data-ttu-id="b1b2e-136">建立模型</span><span class="sxs-lookup"><span data-stu-id="b1b2e-136">Create the model</span></span>

<span data-ttu-id="b1b2e-137">定義組成模型的內容和實體類別：</span><span class="sxs-lookup"><span data-stu-id="b1b2e-137">Define a context and entity classes that make up the model:</span></span>

* <span data-ttu-id="b1b2e-138">以滑鼠右鍵按一下 [Models] 資料夾，然後選取 [新增] > [類別]。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-138">Right-click on the **Models** folder and select **Add > Class**.</span></span>
* <span data-ttu-id="b1b2e-139">輸入 **Model.cs** 作為名稱，然後按一下 [確定]。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-139">Enter **Model.cs** as the name and click **OK**.</span></span>
* <span data-ttu-id="b1b2e-140">以下列程式碼取代檔案的內容：</span><span class="sxs-lookup"><span data-stu-id="b1b2e-140">Replace the contents of the file with the following code:</span></span>

 [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Models/Model.cs)]

<span data-ttu-id="b1b2e-141">注意：在實際的應用程式中，您通常會將模型中的每個類別放在不同的檔案中。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-141">Note: In a real app you would typically put each class from your model in a separate file.</span></span> <span data-ttu-id="b1b2e-142">為了簡單起見，我們在此教學課程中將所有類別放在同一個檔案中。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-142">For the sake of simplicity, we are putting all the classes in one file for this tutorial.</span></span>

## <a name="register-your-context-with-dependency-injection"></a><span data-ttu-id="b1b2e-143">使用相依性插入來註冊內容</span><span class="sxs-lookup"><span data-stu-id="b1b2e-143">Register your context with dependency injection</span></span>

<span data-ttu-id="b1b2e-144">服務 (例如 `BloggingContext`) 是在應用程式啟動期間使用[相依性插入](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html)來註冊。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-144">Services (such as `BloggingContext`) are registered with [dependency injection](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html) during application startup.</span></span> <span data-ttu-id="b1b2e-145">接著，會透過建構函式參數或屬性，針對需要這些服務的元件 (例如 MVC 控制器) 來提供服務。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-145">Components that require these services (such as your MVC controllers) are then provided these services via constructor parameters or properties.</span></span>

<span data-ttu-id="b1b2e-146">為了讓 MVC 控制器能使用 `BloggingContext`，我們要將它註冊為服務。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-146">In order for our MVC controllers to make use of `BloggingContext` we will register it as a service.</span></span>

* <span data-ttu-id="b1b2e-147">開啟 **Startup.cs**</span><span class="sxs-lookup"><span data-stu-id="b1b2e-147">Open **Startup.cs**</span></span>
* <span data-ttu-id="b1b2e-148">加入下列 `using` 陳述式：</span><span class="sxs-lookup"><span data-stu-id="b1b2e-148">Add the following `using` statements:</span></span>

 [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs#AddedUsings)]

<span data-ttu-id="b1b2e-149">加入 `AddDbContext` 方法以將它註冊為服務：</span><span class="sxs-lookup"><span data-stu-id="b1b2e-149">Add the `AddDbContext` method to register it as a service:</span></span>

* <span data-ttu-id="b1b2e-150">將下列程式碼加入 `ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="b1b2e-150">Add the following code to the `ConfigureServices` method:</span></span>

 [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs?name=ConfigureServices&highlight=7-8)]

<span data-ttu-id="b1b2e-151">注意：實際的應用程式通常會將連接字串放入到設定檔中。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-151">Note: A real app would gennerally put the connection string in a configuration file.</span></span> <span data-ttu-id="b1b2e-152">為了簡單起見，我們在程式碼中加以定義。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-152">For the sake of simplicity, we are defining it in code.</span></span> <span data-ttu-id="b1b2e-153">如需詳細資訊，請參閱[連接字串](../../miscellaneous/connection-strings.md)。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-153">See [Connection Strings](../../miscellaneous/connection-strings.md) for more information.</span></span>

## <a name="create-your-database"></a><span data-ttu-id="b1b2e-154">建立您的資料庫</span><span class="sxs-lookup"><span data-stu-id="b1b2e-154">Create your database</span></span>

<span data-ttu-id="b1b2e-155">一旦有模型之後，就可以使用[移轉](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-155">Once you have a model, you can use [migrations](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations) to create a database.</span></span>

* <span data-ttu-id="b1b2e-156">開啟 PMC：</span><span class="sxs-lookup"><span data-stu-id="b1b2e-156">Open the PMC:</span></span>

  <span data-ttu-id="b1b2e-157">[工具] –> [NuGet 套件管理員] –> [套件管理員主控台]</span><span class="sxs-lookup"><span data-stu-id="b1b2e-157">**Tools –> NuGet Package Manager –> Package Manager Console**</span></span>
* <span data-ttu-id="b1b2e-158">執行 `Add-Migration InitialCreate` 來建立移轉的結構，以針對您的模型建立一組初始的資料表。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-158">Run `Add-Migration InitialCreate` to scaffold a migration to create the initial set of tables for your model.</span></span> <span data-ttu-id="b1b2e-159">如果您收到 `The term 'add-migration' is not recognized as the name of a cmdlet` 的錯誤，請關閉再重新開啟 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-159">If you receive an error stating `The term 'add-migration' is not recognized as the name of a cmdlet`, close and reopen Visual Studio.</span></span>
* <span data-ttu-id="b1b2e-160">執行 `Update-Database` 以將新的移轉套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-160">Run `Update-Database` to apply the new migration to the database.</span></span> <span data-ttu-id="b1b2e-161">此命令會建立資料庫，然後才套用移轉。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-161">This command creates the database before applying migrations.</span></span>

## <a name="create-a-controller"></a><span data-ttu-id="b1b2e-162">建立控制器</span><span class="sxs-lookup"><span data-stu-id="b1b2e-162">Create a controller</span></span>

<span data-ttu-id="b1b2e-163">在專案中啟用 Scaffolding：</span><span class="sxs-lookup"><span data-stu-id="b1b2e-163">Enable scaffolding in the project:</span></span>

* <span data-ttu-id="b1b2e-164">在 [方案總管] 中以滑鼠右鍵按一下 [Controllers] 資料夾，然後選取 [新增] > [控制器]。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-164">Right-click on the **Controllers** folder in **Solution Explorer** and select **Add > Controller**.</span></span>
* <span data-ttu-id="b1b2e-165">選取 [基本相依性]，然後按一下 [新增]。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-165">Select **Minimal Dependencies** and click **Add**.</span></span>
* <span data-ttu-id="b1b2e-166">您可以忽略或刪除 *ScaffoldingReadMe.txt* 檔案。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-166">You can ignore or delete the *ScaffoldingReadMe.txt* file.</span></span>

<span data-ttu-id="b1b2e-167">啟用 Scaffolding 之後，就可以建立 `Blog` 實體的控制器結構。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-167">Now that scaffolding is enabled, we can scaffold a controller for the `Blog` entity.</span></span>

* <span data-ttu-id="b1b2e-168">在 [方案總管] 中以滑鼠右鍵按一下 [Controllers] 資料夾，然後選取 [新增] > [控制器]。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-168">Right-click on the **Controllers** folder in **Solution Explorer** and select **Add > Controller**.</span></span>
* <span data-ttu-id="b1b2e-169">選取 [使用 Entity Framework 執行檢視的 MVC 控制器] 並按一下 [確定]。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-169">Select **MVC Controller with views, using Entity Framework** and click **Ok**.</span></span>
* <span data-ttu-id="b1b2e-170">將 [模型類別] 設為 [Blog]，並將 [資料內容類別] 設為 [BloggingContext]。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-170">Set **Model class** to **Blog** and **Data context class** to **BloggingContext**.</span></span>
* <span data-ttu-id="b1b2e-171">按一下 [加入] 。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-171">Click **Add**.</span></span>


## <a name="run-the-application"></a><span data-ttu-id="b1b2e-172">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="b1b2e-172">Run the application</span></span>

<span data-ttu-id="b1b2e-173">按 F5 來執行和測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-173">Press F5 to run and test the app.</span></span>

* <span data-ttu-id="b1b2e-174">巡覽至 `/Blogs`</span><span class="sxs-lookup"><span data-stu-id="b1b2e-174">Navigate to `/Blogs`</span></span>
* <span data-ttu-id="b1b2e-175">使用建立連結來建立一些部落格項目。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-175">Use the create link to create some blog entries.</span></span> <span data-ttu-id="b1b2e-176">測試詳細資料和刪除連結。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-176">Test the details and delete links.</span></span>

![image](_static/create.png)

![image](_static/index-new-db.png)

## <a name="additional-resources"></a><span data-ttu-id="b1b2e-179">其他資源</span><span class="sxs-lookup"><span data-stu-id="b1b2e-179">Additional Resources</span></span>

* <span data-ttu-id="b1b2e-180">[EF - 使用 SQLite 的新資料庫](xref:core/get-started/netcore/new-db-sqlite) - 跨平台的主控台 EF 教學課程。</span><span class="sxs-lookup"><span data-stu-id="b1b2e-180">[EF - New database with SQLite](xref:core/get-started/netcore/new-db-sqlite) -  a cross-platform console EF tutorial.</span></span>
* [<span data-ttu-id="b1b2e-181">Mac 或 Linux 上的 ASP.NET Core MVC 簡介</span><span class="sxs-lookup"><span data-stu-id="b1b2e-181">Introduction to ASP.NET Core MVC on Mac or Linux</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app-xplat/index)
* [<span data-ttu-id="b1b2e-182">使用 Visual Studio 的 ASP.NET Core MVC 簡介</span><span class="sxs-lookup"><span data-stu-id="b1b2e-182">Introduction to ASP.NET Core MVC with Visual Studio</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/index)
* [<span data-ttu-id="b1b2e-183">使用 Visual Studio 的 ASP.NET Core 與 Entity Framework Core 的使用者入門</span><span class="sxs-lookup"><span data-stu-id="b1b2e-183">Getting started with ASP.NET Core and Entity Framework Core using Visual Studio</span></span>](https://docs.microsoft.com/aspnet/core/data/ef-mvc/index)
