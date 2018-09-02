---
title: ASP.NET Core 使用者入門 - 新資料庫 - EF Core
author: rick-anderson
ms.author: riande
ms.date: 08/03/2018
ms.assetid: e153627f-f132-4c11-b13c-6c9a607addce
uid: core/get-started/aspnetcore/new-db
ms.openlocfilehash: c6a86dd943dc7fe6f600455fe6743ea01a062aab
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996060"
---
# <a name="getting-started-with-ef-core-on-aspnet-core-with-a-new-database"></a><span data-ttu-id="5164d-102">在 ASP.NET Core 上使用 EF Core 搭配新資料庫的使用者入門</span><span class="sxs-lookup"><span data-stu-id="5164d-102">Getting Started with EF Core on ASP.NET Core with a New database</span></span>

<span data-ttu-id="5164d-103">在本教學課程中，您將會建置 ASP.NET Core MVC 應用程式，而其使用 Entity Framework Core 執行基本的資料存取。</span><span class="sxs-lookup"><span data-stu-id="5164d-103">In this tutorial, you build an ASP.NET Core MVC application that performs basic data access using Entity Framework Core.</span></span> <span data-ttu-id="5164d-104">您將會使用移轉，從您的 EF Core 模型建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="5164d-104">You use migrations to create the database from your EF Core model.</span></span>

<span data-ttu-id="5164d-105">[在 GitHub 上檢視此文章的範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb)。</span><span class="sxs-lookup"><span data-stu-id="5164d-105">[View this article's sample on GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5164d-106">必要條件</span><span class="sxs-lookup"><span data-stu-id="5164d-106">Prerequisites</span></span>

<span data-ttu-id="5164d-107">安裝下列軟體：</span><span class="sxs-lookup"><span data-stu-id="5164d-107">Install the following software:</span></span>

* <span data-ttu-id="5164d-108">[Visual Studio 2017 15.7](https://www.visualstudio.com/downloads/) 與這些工作負載：</span><span class="sxs-lookup"><span data-stu-id="5164d-108">[Visual Studio 2017 15.7](https://www.visualstudio.com/downloads/) with these workloads:</span></span>
  * <span data-ttu-id="5164d-109">**ASP.NET 與網頁程式開發** (位在 [Web & Cloud] \(Web 與雲端) 下)</span><span class="sxs-lookup"><span data-stu-id="5164d-109">**ASP.NET and web development** (under **Web & Cloud**)</span></span>
  * <span data-ttu-id="5164d-110">**.NET Core 跨平台開發** (位在 [其他工具組] 下)</span><span class="sxs-lookup"><span data-stu-id="5164d-110">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>
* <span data-ttu-id="5164d-111">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core)。</span><span class="sxs-lookup"><span data-stu-id="5164d-111">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core).</span></span>

## <a name="create-a-new-project-in-visual-studio-2017"></a><span data-ttu-id="5164d-112">在 Visual Studio 2017 中建立新專案</span><span class="sxs-lookup"><span data-stu-id="5164d-112">Create a new project in Visual Studio 2017</span></span>

* <span data-ttu-id="5164d-113">開啟 Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="5164d-113">Open Visual Studio 2017</span></span>
* <span data-ttu-id="5164d-114">[檔案] > [新增] > [專案]</span><span class="sxs-lookup"><span data-stu-id="5164d-114">**File > New > Project**</span></span>
* <span data-ttu-id="5164d-115">從左側功能表選取 [已安裝] > [Visual C#] > [.NET Core]。</span><span class="sxs-lookup"><span data-stu-id="5164d-115">From the left menu select **Installed > Visual C# > .NET Core**.</span></span>
* <span data-ttu-id="5164d-116">選取 [ASP.NET Core Web 應用程式]。</span><span class="sxs-lookup"><span data-stu-id="5164d-116">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="5164d-117">輸入 **EFGetStarted.AspNetCore.NewDb** 作為名稱，然後按一下 [確定]。</span><span class="sxs-lookup"><span data-stu-id="5164d-117">Enter **EFGetStarted.AspNetCore.NewDb** for the name and click **OK**.</span></span>
* <span data-ttu-id="5164d-118">在 [新增 ASP.NET Core Web 應用程式] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="5164d-118">In the **New ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="5164d-119">確認下拉式清單中已選取 [.NET Core] 和 [ASP.NET Core 2.1] 選項</span><span class="sxs-lookup"><span data-stu-id="5164d-119">Ensure the options **.NET Core** and **ASP.NET Core 2.1** are selected in the drop down lists</span></span>
  * <span data-ttu-id="5164d-120">選取 [Web 應用程式 (模型-檢視-控制器)] 專案範本</span><span class="sxs-lookup"><span data-stu-id="5164d-120">Select the **Web Application (Model-View-Controller)** project template</span></span>
  * <span data-ttu-id="5164d-121">確認 [驗證] 已設為 [無驗證]</span><span class="sxs-lookup"><span data-stu-id="5164d-121">Ensure that **Authentication** is set to **No Authentication**</span></span>
  * <span data-ttu-id="5164d-122">按一下 [確定] </span><span class="sxs-lookup"><span data-stu-id="5164d-122">Click **OK**</span></span>

<span data-ttu-id="5164d-123">警告：如果您針對 [驗證] 使用 [個別使用者帳戶] 而不是 [無]，則會將 Entity Framework Core 模型新增至您專案中的 `Models\IdentityModel.cs`。</span><span class="sxs-lookup"><span data-stu-id="5164d-123">Warning: If you use **Individual User Accounts** instead of **None** for **Authentication** then an Entity Framework Core model will be added to your project in `Models\IdentityModel.cs`.</span></span> <span data-ttu-id="5164d-124">您可利用在本教學課程中所學到的技術，選擇新增第二個模型，或是擴充此現有模型以包含您的實體類別。</span><span class="sxs-lookup"><span data-stu-id="5164d-124">Using the techniques you learn in this tutorial, you can choose to add a second model, or extend this existing model to contain your entity classes.</span></span>

## <a name="install-entity-framework-core"></a><span data-ttu-id="5164d-125">安裝 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="5164d-125">Install Entity Framework Core</span></span>

<span data-ttu-id="5164d-126">若要安裝 EF Core，請為希望作為目標的 EF Core 資料庫提供者，安裝此套件。</span><span class="sxs-lookup"><span data-stu-id="5164d-126">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="5164d-127">如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="5164d-127">For a list of available providers see [Database Providers](../../providers/index.md).</span></span> 

<span data-ttu-id="5164d-128">在本教學課程中，因為使用 SQL Server，所以無需安裝提供者套件。</span><span class="sxs-lookup"><span data-stu-id="5164d-128">For this tutorial, you don't have to install a provider package because the tutorial uses SQL Server.</span></span> <span data-ttu-id="5164d-129">[Microsoft.AspnetCore.App 中繼套件](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1)包含 SQL Server 提供者套件。</span><span class="sxs-lookup"><span data-stu-id="5164d-129">The SQL Server provider package is included in the [Microsoft.AspnetCore.App metapackage](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1).</span></span>

## <a name="create-the-model"></a><span data-ttu-id="5164d-130">建立模型</span><span class="sxs-lookup"><span data-stu-id="5164d-130">Create the model</span></span>

<span data-ttu-id="5164d-131">定義組成模型的內容類別與實體類別：</span><span class="sxs-lookup"><span data-stu-id="5164d-131">Define a context class and entity classes that make up the model:</span></span>

* <span data-ttu-id="5164d-132">以滑鼠右鍵按一下 [Models] 資料夾，然後選取 [新增] > [類別]。</span><span class="sxs-lookup"><span data-stu-id="5164d-132">Right-click on the **Models** folder and select **Add > Class**.</span></span>
* <span data-ttu-id="5164d-133">輸入 **Model.cs** 作為名稱，然後按一下 [確定]。</span><span class="sxs-lookup"><span data-stu-id="5164d-133">Enter **Model.cs** as the name and click **OK**.</span></span>
* <span data-ttu-id="5164d-134">以下列程式碼取代檔案的內容：</span><span class="sxs-lookup"><span data-stu-id="5164d-134">Replace the contents of the file with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Models/Model.cs)]

<span data-ttu-id="5164d-135">在實際的應用程式中，一般會將模型中的每個類別置於不同的檔案中。</span><span class="sxs-lookup"><span data-stu-id="5164d-135">In a real app you would typically put each class from your model in a separate file.</span></span> <span data-ttu-id="5164d-136">為了簡單起見，本教學課程將所有類別皆置於同一個檔案中。</span><span class="sxs-lookup"><span data-stu-id="5164d-136">For the sake of simplicity, this tutorial puts all the classes in one file.</span></span>

## <a name="register-your-context-with-dependency-injection"></a><span data-ttu-id="5164d-137">使用相依性插入來註冊內容</span><span class="sxs-lookup"><span data-stu-id="5164d-137">Register your context with dependency injection</span></span>

<span data-ttu-id="5164d-138">服務 (例如 `BloggingContext`) 是在應用程式啟動期間使用[相依性插入](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html)來註冊。</span><span class="sxs-lookup"><span data-stu-id="5164d-138">Services (such as `BloggingContext`) are registered with [dependency injection](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html) during application startup.</span></span> <span data-ttu-id="5164d-139">接著，會透過建構函式參數或屬性，針對需要這些服務的元件 (例如 MVC 控制器) 來提供服務。</span><span class="sxs-lookup"><span data-stu-id="5164d-139">Components that require these services (such as your MVC controllers) are then provided these services via constructor parameters or properties.</span></span>

<span data-ttu-id="5164d-140">若要為 MVC 控制器提供 `BloggingContext`，請將其註冊為服務。</span><span class="sxs-lookup"><span data-stu-id="5164d-140">To make `BloggingContext` available to MVC controllers, register it as a service.</span></span>

* <span data-ttu-id="5164d-141">開啟 **Startup.cs**</span><span class="sxs-lookup"><span data-stu-id="5164d-141">Open **Startup.cs**</span></span>
* <span data-ttu-id="5164d-142">加入下列 `using` 陳述式：</span><span class="sxs-lookup"><span data-stu-id="5164d-142">Add the following `using` statements:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs#AddedUsings)]

<span data-ttu-id="5164d-143">呼叫 `AddDbContext` 方法，將該內容註冊為服務。</span><span class="sxs-lookup"><span data-stu-id="5164d-143">Call the `AddDbContext` method to register the context as a service.</span></span>

* <span data-ttu-id="5164d-144">將下列反白顯示的程式碼新增至 `ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="5164d-144">Add the following highlighted code to the `ConfigureServices` method:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs?name=ConfigureServices&highlight=13-14)]

<span data-ttu-id="5164d-145">注意：在實際的應用程式中，一般會將連接字串置於設定檔或環境變數中。</span><span class="sxs-lookup"><span data-stu-id="5164d-145">Note: A real app would generally put the connection string in a configuration file or environment variable.</span></span> <span data-ttu-id="5164d-146">為了簡單起見，本教學課程將其定義在程式碼中。</span><span class="sxs-lookup"><span data-stu-id="5164d-146">For the sake of simplicity, this tutorial defines it in code.</span></span> <span data-ttu-id="5164d-147">如需詳細資訊，請參閱[連接字串](../../miscellaneous/connection-strings.md)。</span><span class="sxs-lookup"><span data-stu-id="5164d-147">See [Connection Strings](../../miscellaneous/connection-strings.md) for more information.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="5164d-148">建立資料庫</span><span class="sxs-lookup"><span data-stu-id="5164d-148">Create the database</span></span>

<span data-ttu-id="5164d-149">一旦有模型之後，就可以使用[移轉](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="5164d-149">Once you have a model, you can use [migrations](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations) to create a database.</span></span>

* <span data-ttu-id="5164d-150">[工具] > [NuGet 套件管理員] > [套件管理員主控台]</span><span class="sxs-lookup"><span data-stu-id="5164d-150">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="5164d-151">執行 `Add-Migration InitialCreate` 來建立移轉的結構，以針對您的模型建立一組初始的資料表。</span><span class="sxs-lookup"><span data-stu-id="5164d-151">Run `Add-Migration InitialCreate` to scaffold a migration to create the initial set of tables for your model.</span></span> <span data-ttu-id="5164d-152">如果您收到 `The term 'add-migration' is not recognized as the name of a cmdlet` 的錯誤，請關閉再重新開啟 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="5164d-152">If you receive an error stating `The term 'add-migration' is not recognized as the name of a cmdlet`, close and reopen Visual Studio.</span></span>
* <span data-ttu-id="5164d-153">執行 `Update-Database` 以將新的移轉套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="5164d-153">Run `Update-Database` to apply the new migration to the database.</span></span> <span data-ttu-id="5164d-154">此命令會建立資料庫，然後才套用移轉。</span><span class="sxs-lookup"><span data-stu-id="5164d-154">This command creates the database before applying migrations.</span></span>

## <a name="create-a-controller"></a><span data-ttu-id="5164d-155">建立控制器</span><span class="sxs-lookup"><span data-stu-id="5164d-155">Create a controller</span></span>

<span data-ttu-id="5164d-156">為 `Blog` 實體設定控制器與檢視的結構。</span><span class="sxs-lookup"><span data-stu-id="5164d-156">Scaffold a controller and views for the `Blog` entity.</span></span>

* <span data-ttu-id="5164d-157">在 [方案總管] 中以滑鼠右鍵按一下 [Controllers] 資料夾，然後選取 [新增] > [控制器]。</span><span class="sxs-lookup"><span data-stu-id="5164d-157">Right-click on the **Controllers** folder in **Solution Explorer** and select **Add > Controller**.</span></span>
* <span data-ttu-id="5164d-158">選取 [使用 Entity Framework 執行檢視的 MVC 控制器]，然後按一下 [新增]。</span><span class="sxs-lookup"><span data-stu-id="5164d-158">Select **MVC Controller with views, using Entity Framework** and click **Add**.</span></span>
* <span data-ttu-id="5164d-159">將 [模型類別] 設為 [Blog]，並將 [資料內容類別] 設為 [BloggingContext]。</span><span class="sxs-lookup"><span data-stu-id="5164d-159">Set **Model class** to **Blog** and **Data context class** to **BloggingContext**.</span></span>
* <span data-ttu-id="5164d-160">按一下 [加入] 。</span><span class="sxs-lookup"><span data-stu-id="5164d-160">Click **Add**.</span></span>


## <a name="run-the-application"></a><span data-ttu-id="5164d-161">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="5164d-161">Run the application</span></span>

<span data-ttu-id="5164d-162">按 F5 來執行和測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="5164d-162">Press F5 to run and test the app.</span></span>

* <span data-ttu-id="5164d-163">巡覽至 `/Blogs`</span><span class="sxs-lookup"><span data-stu-id="5164d-163">Navigate to `/Blogs`</span></span>
* <span data-ttu-id="5164d-164">使用建立連結來建立一些部落格項目。</span><span class="sxs-lookup"><span data-stu-id="5164d-164">Use the create link to create some blog entries.</span></span> <span data-ttu-id="5164d-165">測試詳細資料和刪除連結。</span><span class="sxs-lookup"><span data-stu-id="5164d-165">Test the details and delete links.</span></span>

![影像](_static/create.png)

![影像](_static/index-new-db.png)

## <a name="additional-resources"></a><span data-ttu-id="5164d-168">其他資源</span><span class="sxs-lookup"><span data-stu-id="5164d-168">Additional Resources</span></span>

* <span data-ttu-id="5164d-169">[EF - 使用 SQLite 的新資料庫](xref:core/get-started/netcore/new-db-sqlite) - 跨平台的主控台 EF 教學課程。</span><span class="sxs-lookup"><span data-stu-id="5164d-169">[EF - New database with SQLite](xref:core/get-started/netcore/new-db-sqlite) -  a cross-platform console EF tutorial.</span></span>
* [<span data-ttu-id="5164d-170">Mac 或 Linux 上的 ASP.NET Core MVC 簡介</span><span class="sxs-lookup"><span data-stu-id="5164d-170">Introduction to ASP.NET Core MVC on Mac or Linux</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app-xplat/index)
* [<span data-ttu-id="5164d-171">使用 Visual Studio 的 ASP.NET Core MVC 簡介</span><span class="sxs-lookup"><span data-stu-id="5164d-171">Introduction to ASP.NET Core MVC with Visual Studio</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/index)
* [<span data-ttu-id="5164d-172">使用 Visual Studio 的 ASP.NET Core 與 Entity Framework Core 的使用者入門</span><span class="sxs-lookup"><span data-stu-id="5164d-172">Getting started with ASP.NET Core and Entity Framework Core using Visual Studio</span></span>](https://docs.microsoft.com/aspnet/core/data/ef-mvc/index)
