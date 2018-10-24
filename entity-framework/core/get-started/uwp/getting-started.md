---
title: UWP 使用者入門 - 新資料庫 - EF Core
author: rowanmiller
ms.date: 10/13/2018
ms.assetid: a0ae2f21-1eef-43c6-83ad-92275f9c0727
uid: core/get-started/uwp/getting-started
ms.openlocfilehash: 48d26adbe17e4734753a7ada547b9c13317bef0d
ms.sourcegitcommit: 8b42045cd21f80f425a92f5e4e9dd4972a31720b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2018
ms.locfileid: "49315616"
---
# <a name="getting-started-with-ef-core-on-universal-windows-platform-uwp-with-a-new-database"></a><span data-ttu-id="76f2b-102">在通用 Windows 平台 (UWP) 上使用 EF Core 搭配新資料庫的使用者入門</span><span class="sxs-lookup"><span data-stu-id="76f2b-102">Getting Started with EF Core on Universal Windows Platform (UWP) with a New Database</span></span>

<span data-ttu-id="76f2b-103">在此教學課程中，您將建置通用 Windows 平台 (UWP) 應用程式，該應用程式將使用 Entity Framework Core 對本機 SQLite 資料庫執行基本資料存取。</span><span class="sxs-lookup"><span data-stu-id="76f2b-103">In this tutorial, you build a Universal Windows Platform (UWP) application that performs basic data access against a local SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="76f2b-104">[在 GitHub 上檢視此文章的範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/UWP)。</span><span class="sxs-lookup"><span data-stu-id="76f2b-104">[View this article's sample on GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/UWP).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="76f2b-105">必要條件</span><span class="sxs-lookup"><span data-stu-id="76f2b-105">Prerequisites</span></span>

* <span data-ttu-id="76f2b-106">[Windows 10 Fall Creators Update (10.0; Build 16299) 或更新版本](https://support.microsoft.com/en-us/help/4027667/windows-update-windows-10).</span><span class="sxs-lookup"><span data-stu-id="76f2b-106">[Windows 10 Fall Creators Update (10.0; Build 16299) or later](https://support.microsoft.com/en-us/help/4027667/windows-update-windows-10).</span></span>

* <span data-ttu-id="76f2b-107">[Visual Studio 2017](https://www.visualstudio.com/downloads/) 15.7 版或更新版本，搭配**通用 Windows 平台開發**工作負載。</span><span class="sxs-lookup"><span data-stu-id="76f2b-107">[Visual Studio 2017 version 15.7 or later](https://www.visualstudio.com/downloads/) with the **Universal Windows Platform Development** workload.</span></span>

* <span data-ttu-id="76f2b-108">[.NET Core 2.1 SDK 或更新版本](https://www.microsoft.com/net/core)或更新版本。</span><span class="sxs-lookup"><span data-stu-id="76f2b-108">[.NET Core 2.1 SDK or later](https://www.microsoft.com/net/core) or later.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="76f2b-109">本教學課程使用 Entity Framework Core [移轉](xref:core/managing-schemas/migrations/index)命令來建立和更新資料庫的結構描述。</span><span class="sxs-lookup"><span data-stu-id="76f2b-109">This tutorial uses Entity Framework Core [migrations](xref:core/managing-schemas/migrations/index) commands to create and update the schema of the database.</span></span>
> <span data-ttu-id="76f2b-110">這些命令不能直接搭配 UWP 專案使用。</span><span class="sxs-lookup"><span data-stu-id="76f2b-110">These commands don't work directly with UWP projects.</span></span>
> <span data-ttu-id="76f2b-111">因此，應用程式的資料模型會放在共用程式庫專案中，再使用不同的 .NET Core 主控台應用程式執行該命令。</span><span class="sxs-lookup"><span data-stu-id="76f2b-111">For this reason, the application's data model is placed in a shared library project, and a separate .NET Core console application is used to run the commands.</span></span>

## <a name="create-a-library-project-to-hold-the-data-model"></a><span data-ttu-id="76f2b-112">建立程式庫專案來保存資料模型</span><span class="sxs-lookup"><span data-stu-id="76f2b-112">Create a library project to hold the data model</span></span>

* <span data-ttu-id="76f2b-113">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76f2b-113">Open Visual Studio</span></span>

* <span data-ttu-id="76f2b-114">[檔案] > [新增] > [專案]</span><span class="sxs-lookup"><span data-stu-id="76f2b-114">**File > New > Project**</span></span>

* <span data-ttu-id="76f2b-115">從左側功能表選取 [已安裝] > [Visual C#] > [.NET Standard]。</span><span class="sxs-lookup"><span data-stu-id="76f2b-115">From the left menu select **Installed > Visual C# > .NET Standard**.</span></span>

* <span data-ttu-id="76f2b-116">選取 [類別庫 (.NET Standard)] 範本。</span><span class="sxs-lookup"><span data-stu-id="76f2b-116">Select the **Class Library (.NET Standard)** template.</span></span>

* <span data-ttu-id="76f2b-117">將專案命名為 *Blogging.Model*。</span><span class="sxs-lookup"><span data-stu-id="76f2b-117">Name the project *Blogging.Model*.</span></span>

* <span data-ttu-id="76f2b-118">將方案命名為 *Blogging*。</span><span class="sxs-lookup"><span data-stu-id="76f2b-118">Name the solution *Blogging*.</span></span>

* <span data-ttu-id="76f2b-119">按一下 [確定 **Deploying Office Solutions**]。</span><span class="sxs-lookup"><span data-stu-id="76f2b-119">Click **OK**.</span></span>

## <a name="install-entity-framework-core-runtime-in-the-data-model-project"></a><span data-ttu-id="76f2b-120">於資料模型專案中安裝 Entity Framework Core 執行階段</span><span class="sxs-lookup"><span data-stu-id="76f2b-120">Install Entity Framework Core runtime in the data model project</span></span>

<span data-ttu-id="76f2b-121">若要使用 EF Core，請針對您要作為目標的資料庫提供者來安裝套件。</span><span class="sxs-lookup"><span data-stu-id="76f2b-121">To use EF Core, install the package for the database provider(s) you want to target.</span></span> <span data-ttu-id="76f2b-122">此教學課程使用 SQLite。</span><span class="sxs-lookup"><span data-stu-id="76f2b-122">This tutorial uses SQLite.</span></span> <span data-ttu-id="76f2b-123">如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="76f2b-123">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="76f2b-124">**[工具] -> [NuGet 套件管理員] -> [套件管理員主控台]**。</span><span class="sxs-lookup"><span data-stu-id="76f2b-124">**Tools > NuGet Package Manager > Package Manager Console**.</span></span>

* <span data-ttu-id="76f2b-125">請確定已在套件管理員主控台中選取程式庫專案 *Blogging.Model* 作為**預設專案**。</span><span class="sxs-lookup"><span data-stu-id="76f2b-125">Make sure that the library project *Blogging.Model* is selected as the **Default Project** in the Package Manager Console.</span></span>

* <span data-ttu-id="76f2b-126">執行 `Install-Package Microsoft.EntityFrameworkCore.Sqlite`</span><span class="sxs-lookup"><span data-stu-id="76f2b-126">Run `Install-Package Microsoft.EntityFrameworkCore.Sqlite`</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="76f2b-127">建立資料模型</span><span class="sxs-lookup"><span data-stu-id="76f2b-127">Create the data model</span></span>

<span data-ttu-id="76f2b-128">現在開始定義組成模型的 *DbContext* 與實體類別。</span><span class="sxs-lookup"><span data-stu-id="76f2b-128">Now it's time to define the *DbContext* and entity classes that make up the model.</span></span>

* <span data-ttu-id="76f2b-129">刪除 *Class1.cs*。</span><span class="sxs-lookup"><span data-stu-id="76f2b-129">Delete *Class1.cs*.</span></span>

* <span data-ttu-id="76f2b-130">使用下列程式碼建立 *Model.cs*：</span><span class="sxs-lookup"><span data-stu-id="76f2b-130">Create *Model.cs* with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/UWP/Blogging.Model/Model.cs)]

## <a name="create-a-new-console-project-to-run-migrations-commands"></a><span data-ttu-id="76f2b-131">建立新的主控台專案以執行移轉命令</span><span class="sxs-lookup"><span data-stu-id="76f2b-131">Create a new console project to run migrations commands</span></span>

* <span data-ttu-id="76f2b-132">在 [方案總管] 中，以滑鼠右鍵按一下方案，然後選擇 [新增] > [新增專案]。</span><span class="sxs-lookup"><span data-stu-id="76f2b-132">In **Solution Explorer**, right-click the solution, and then choose **Add > New Project**.</span></span>

* <span data-ttu-id="76f2b-133">從左側功能表選取 [已安裝] > [Visual C#] > [.NET Core]。</span><span class="sxs-lookup"><span data-stu-id="76f2b-133">From the left menu select **Installed > Visual C# > .NET Core**.</span></span>

* <span data-ttu-id="76f2b-134">選取 [主控台應用程式 (.NET Core)] 專案範本。</span><span class="sxs-lookup"><span data-stu-id="76f2b-134">Select the **Console App (.NET Core)** project template.</span></span>

* <span data-ttu-id="76f2b-135">將專案命名為 *Blogging.Migrations.Startup*，然後按一下 [確定]。</span><span class="sxs-lookup"><span data-stu-id="76f2b-135">Name the project *Blogging.Migrations.Startup*, and click **OK**.</span></span>

* <span data-ttu-id="76f2b-136">新增從 *Blogging.Migrations.Startup* 專案到 *Blogging.Model* 專案的專案參考。</span><span class="sxs-lookup"><span data-stu-id="76f2b-136">Add a project reference from the *Blogging.Migrations.Startup* project to the *Blogging.Model* project.</span></span>

## <a name="install-entity-framework-core-tools-in-the-migrations-startup-project"></a><span data-ttu-id="76f2b-137">在移轉啟動專案中安裝 Entity Framework Core 工具</span><span class="sxs-lookup"><span data-stu-id="76f2b-137">Install Entity Framework Core tools in the migrations startup project</span></span>

<span data-ttu-id="76f2b-138">若要在套件管理員主控台中啟用 EF Core 移轉命令，請在主控台應用程式中安裝 EF Core 工具。</span><span class="sxs-lookup"><span data-stu-id="76f2b-138">To enable the EF Core migration commands in the Package Manager Console, install the EF Core tools package in the console application.</span></span>

* <span data-ttu-id="76f2b-139">[工具] > [NuGet 套件管理員] > [套件管理員主控台]</span><span class="sxs-lookup"><span data-stu-id="76f2b-139">**Tools > NuGet Package Manager > Package Manager Console**</span></span>

* <span data-ttu-id="76f2b-140">執行 `Install-Package Microsoft.EntityFrameworkCore.Tools -ProjectName Blogging.Migrations.Startup`</span><span class="sxs-lookup"><span data-stu-id="76f2b-140">Run `Install-Package Microsoft.EntityFrameworkCore.Tools -ProjectName Blogging.Migrations.Startup`</span></span>

## <a name="create-the-initial-migration"></a><span data-ttu-id="76f2b-141">建立初始移轉</span><span class="sxs-lookup"><span data-stu-id="76f2b-141">Create the initial migration</span></span>

 <span data-ttu-id="76f2b-142">建立初始遷移，並將主控台應用程式指定為啟動專案。</span><span class="sxs-lookup"><span data-stu-id="76f2b-142">Create the initial migration, specifying the console application as the startup project.</span></span>

* <span data-ttu-id="76f2b-143">執行 `Add-Migration InitialCreate -StartupProject Blogging.Migrations.Startup`</span><span class="sxs-lookup"><span data-stu-id="76f2b-143">Run `Add-Migration InitialCreate -StartupProject Blogging.Migrations.Startup`</span></span>

<span data-ttu-id="76f2b-144">此命令會建立移轉的支架，以針對您的資料模型建立一組初始資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="76f2b-144">This command scaffolds a migration that creates the initial set of database tables for your data model.</span></span>

## <a name="create-the-uwp-project"></a><span data-ttu-id="76f2b-145">建立 UWP 專案</span><span class="sxs-lookup"><span data-stu-id="76f2b-145">Create the UWP project</span></span>

* <span data-ttu-id="76f2b-146">在 [方案總管] 中，以滑鼠右鍵按一下方案，然後選擇 [新增] > [新增專案]。</span><span class="sxs-lookup"><span data-stu-id="76f2b-146">In **Solution Explorer**, right-click the solution, and then choose **Add > New Project**.</span></span>

* <span data-ttu-id="76f2b-147">從左側功能表中，選取 [已安裝] > [Visual C#] > [Windows 通用]。</span><span class="sxs-lookup"><span data-stu-id="76f2b-147">From the left menu select **Installed > Visual C# > Windows Universal**.</span></span>

* <span data-ttu-id="76f2b-148">選取 [空白應用程式 (通用 Windows)] 專案範本。</span><span class="sxs-lookup"><span data-stu-id="76f2b-148">Select the **Blank App (Universal Windows)** project template.</span></span>

* <span data-ttu-id="76f2b-149">將專案命名為 *Blogging.UWP*，然後按一下 [確定]</span><span class="sxs-lookup"><span data-stu-id="76f2b-149">Name the project *Blogging.UWP*, and click **OK**</span></span>

> [!IMPORTANT]
> <span data-ttu-id="76f2b-150">至少將目標與最低版本設定為 **Windows 10 Fall Creators Update (10.0; build 16299.0)**。</span><span class="sxs-lookup"><span data-stu-id="76f2b-150">Set the target and minimum versions to at least **Windows 10 Fall Creators Update (10.0; build 16299.0)**.</span></span>
> <span data-ttu-id="76f2b-151">舊版 Windows 10 不支援 Entity Framework Core 所需的 .NET Standard 2.0。</span><span class="sxs-lookup"><span data-stu-id="76f2b-151">Previous  versions of Windows 10 do not support .NET Standard 2.0, which is required by Entity Framework Core.</span></span>

## <a name="add-code-to-create-the-database-on-application-startup"></a><span data-ttu-id="76f2b-152">新增程式碼以在應用程式啟動時建立資料庫</span><span class="sxs-lookup"><span data-stu-id="76f2b-152">Add code to create the database on application startup</span></span>

<span data-ttu-id="76f2b-153">因為您要在執行應用程式的裝置上建立資料庫，請新增程式碼，在應用程式啟動時將任何擱置中的移轉套用至本機資料庫。</span><span class="sxs-lookup"><span data-stu-id="76f2b-153">Since you want the database to be created on the device that the app runs on, add code to apply any pending migrations to the local database on application startup.</span></span> <span data-ttu-id="76f2b-154">第一次執行應用程式時，這會負責建立本機資料庫。</span><span class="sxs-lookup"><span data-stu-id="76f2b-154">The first time that the app runs, this will take care of creating the local database.</span></span>

* <span data-ttu-id="76f2b-155">新增從 *Blogging.UWP* 專案到 *Blogging.Model* 專案的專案參考。</span><span class="sxs-lookup"><span data-stu-id="76f2b-155">Add a project reference from the *Blogging.UWP* project to the *Blogging.Model* project.</span></span>

* <span data-ttu-id="76f2b-156">開啟 *App.xaml.cs*。</span><span class="sxs-lookup"><span data-stu-id="76f2b-156">Open *App.xaml.cs*.</span></span>

* <span data-ttu-id="76f2b-157">加入醒目提示的程式碼，以套用任何擱置中的移轉。</span><span class="sxs-lookup"><span data-stu-id="76f2b-157">Add the highlighted code to apply any pending migrations.</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/UWP/Blogging.UWP/App.xaml.cs?highlight=1-2,26-29)]

> [!TIP]  
> <span data-ttu-id="76f2b-158">若您變更模型，請使用 `Add-Migration` 命令來建立新移轉的支架，以將相對應的變更套用到資料庫。</span><span class="sxs-lookup"><span data-stu-id="76f2b-158">If you change your model, use the `Add-Migration` command to scaffold a new migration to apply the corresponding changes to the database.</span></span> <span data-ttu-id="76f2b-159">應用程式啟動時，會將任何擱置中的移轉套用至每個裝置上的本機資料庫。</span><span class="sxs-lookup"><span data-stu-id="76f2b-159">Any pending migrations will be applied to the local database on each device when the application starts.</span></span>
>
><span data-ttu-id="76f2b-160">EF Core 在資料庫中使用 `__EFMigrationsHistory` 資料表，以追蹤已套用到資料庫的移轉。</span><span class="sxs-lookup"><span data-stu-id="76f2b-160">EF Core uses a `__EFMigrationsHistory` table in the database to keep track of which migrations have already been applied to the database.</span></span>

## <a name="use-the-data-model"></a><span data-ttu-id="76f2b-161">使用資料模型</span><span class="sxs-lookup"><span data-stu-id="76f2b-161">Use the data model</span></span>

<span data-ttu-id="76f2b-162">您現在可使用 EF Core 執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="76f2b-162">You can now use EF Core to perform data access.</span></span>

* <span data-ttu-id="76f2b-163">開啟 *MainPage.xaml*。</span><span class="sxs-lookup"><span data-stu-id="76f2b-163">Open *MainPage.xaml*.</span></span>

* <span data-ttu-id="76f2b-164">加入下列醒目提示的頁面載入處理常式和 UI 內容</span><span class="sxs-lookup"><span data-stu-id="76f2b-164">Add the page load handler and UI content highlighted below</span></span>

[!code-xml[Main](../../../../samples/core/GetStarted/UWP/Blogging.UWP/MainPage.xaml?highlight=9,11-23)]

<span data-ttu-id="76f2b-165">現在加入程式碼，以連接 UI 與資料庫</span><span class="sxs-lookup"><span data-stu-id="76f2b-165">Now add code to wire up the UI with the database</span></span>

* <span data-ttu-id="76f2b-166">開啟 *MainPage.xaml.cs*。</span><span class="sxs-lookup"><span data-stu-id="76f2b-166">Open *MainPage.xaml.cs*.</span></span>

* <span data-ttu-id="76f2b-167">從下列清單中加入醒目提示的程式碼：</span><span class="sxs-lookup"><span data-stu-id="76f2b-167">Add the highlighted code from the following listing:</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/UWP/Blogging.UWP/MainPage.xaml.cs?highlight=1,31-49)]

<span data-ttu-id="76f2b-168">您現在可以執行應用程式來查看運作狀況。</span><span class="sxs-lookup"><span data-stu-id="76f2b-168">You can now run the application to see it in action.</span></span>

* <span data-ttu-id="76f2b-169">在 [方案總管] 中，以滑鼠右鍵按一下 [Blogging.UWP] 專案，然後選取 [部署]。</span><span class="sxs-lookup"><span data-stu-id="76f2b-169">In **Solution Explorer**, right-click the *Blogging.UWP* project and then select **Deploy**.</span></span>

* <span data-ttu-id="76f2b-170">將 *Blogging.UWP* 設定為起始專案。</span><span class="sxs-lookup"><span data-stu-id="76f2b-170">Set *Blogging.UWP* as the startup project.</span></span>

* <span data-ttu-id="76f2b-171">[偵錯] > [啟動但不偵錯]</span><span class="sxs-lookup"><span data-stu-id="76f2b-171">**Debug > Start Without Debugging**</span></span>

  <span data-ttu-id="76f2b-172">應用程式會建置並執行。</span><span class="sxs-lookup"><span data-stu-id="76f2b-172">The app builds and runs.</span></span>

* <span data-ttu-id="76f2b-173">輸入 URL 並按一下 [加入] 按鈕</span><span class="sxs-lookup"><span data-stu-id="76f2b-173">Enter a URL and click the **Add** button</span></span>

  ![影像](_static/create.png)

  ![影像](_static/list.png)

  <span data-ttu-id="76f2b-176">大功告成！</span><span class="sxs-lookup"><span data-stu-id="76f2b-176">Tada!</span></span> <span data-ttu-id="76f2b-177">您現在有一個執行 Entity Framework Core 的簡單 UWP 應用程式了。</span><span class="sxs-lookup"><span data-stu-id="76f2b-177">You now have a simple UWP app running Entity Framework Core.</span></span>

## <a name="next-steps"></a><span data-ttu-id="76f2b-178">後續步驟</span><span class="sxs-lookup"><span data-stu-id="76f2b-178">Next steps</span></span>

<span data-ttu-id="76f2b-179">針對搭配 UWP 使用 EF Core 時應該知道的相容性與效能資訊，請參閱 [EF Core 提供的 .NET 實作支援](../../platforms/index.md#universal-windows-platform)。</span><span class="sxs-lookup"><span data-stu-id="76f2b-179">For compatibility and performance information that you should know when using EF Core with UWP, see [.NET implementations supported by EF Core](../../platforms/index.md#universal-windows-platform).</span></span>

<span data-ttu-id="76f2b-180">請參閱此文件中的其他文章，以深入了解 Entity Framework Core 的功能。</span><span class="sxs-lookup"><span data-stu-id="76f2b-180">Check out other articles in this documentation to learn more about Entity Framework Core features.</span></span>
