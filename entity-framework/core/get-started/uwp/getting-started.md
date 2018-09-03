---
title: UWP 使用者入門 - 新資料庫 - EF Core
author: rowanmiller
ms.date: 08/08/2018
ms.assetid: a0ae2f21-1eef-43c6-83ad-92275f9c0727
uid: core/get-started/uwp/getting-started
ms.openlocfilehash: c243ef2a1940af9bf4f4b32f17acfcce7f972862
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996906"
---
# <a name="getting-started-with-ef-core-on-universal-windows-platform-uwp-with-a-new-database"></a><span data-ttu-id="2c03c-102">在通用 Windows 平台 (UWP) 上使用 EF Core 搭配新資料庫的使用者入門</span><span class="sxs-lookup"><span data-stu-id="2c03c-102">Getting Started with EF Core on Universal Windows Platform (UWP) with a New Database</span></span>

<span data-ttu-id="2c03c-103">在此教學課程中，您將建置通用 Windows 平台 (UWP) 應用程式，該應用程式將使用 Entity Framework Core 對本機 SQLite 資料庫執行基本資料存取。</span><span class="sxs-lookup"><span data-stu-id="2c03c-103">In this tutorial, you build a Universal Windows Platform (UWP) application that performs basic data access against a local SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="2c03c-104">[在 GitHub 上檢視此文章的範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/UWP)。</span><span class="sxs-lookup"><span data-stu-id="2c03c-104">[View this article's sample on GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/UWP).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2c03c-105">必要條件</span><span class="sxs-lookup"><span data-stu-id="2c03c-105">Prerequisites</span></span>

* <span data-ttu-id="2c03c-106">[Windows 10 Fall Creators Update (10.0; Build 16299) 或更新版本](https://support.microsoft.com/en-us/help/4027667/windows-update-windows-10).</span><span class="sxs-lookup"><span data-stu-id="2c03c-106">[Windows 10 Fall Creators Update (10.0; Build 16299) or later](https://support.microsoft.com/en-us/help/4027667/windows-update-windows-10).</span></span>

* <span data-ttu-id="2c03c-107">[Visual Studio 2017](https://www.visualstudio.com/downloads/) 15.7 版或更新版本，搭配**通用 Windows 平台開發**工作負載。</span><span class="sxs-lookup"><span data-stu-id="2c03c-107">[Visual Studio 2017 version 15.7 or later](https://www.visualstudio.com/downloads/) with the **Universal Windows Platform Development** workload.</span></span>

* <span data-ttu-id="2c03c-108">[.NET Core 2.1 SDK 或更新版本](https://www.microsoft.com/net/core)或更新版本。</span><span class="sxs-lookup"><span data-stu-id="2c03c-108">[.NET Core 2.1 SDK or later](https://www.microsoft.com/net/core) or later.</span></span>

## <a name="create-a-model-project"></a><span data-ttu-id="2c03c-109">建立模型專案</span><span class="sxs-lookup"><span data-stu-id="2c03c-109">Create a model project</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2c03c-110">由於 .NET Core 工具與 UWP 專案互動方式的限制，模型需要放置在非 UWP 專案中，才能在套件管理員主控台 (PMC) 中執行移轉命令</span><span class="sxs-lookup"><span data-stu-id="2c03c-110">Due to limitations in the way .NET Core tools interact with UWP projects the model needs to be placed in a non-UWP project to be able to run migrations commands in the **Package Manager Console** (PMC)</span></span>

* <span data-ttu-id="2c03c-111">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c03c-111">Open Visual Studio</span></span>

* <span data-ttu-id="2c03c-112">[檔案] > [新增] > [專案]</span><span class="sxs-lookup"><span data-stu-id="2c03c-112">**File > New > Project**</span></span>

* <span data-ttu-id="2c03c-113">從左側功能表選取 [已安裝] > [Visual C#] > [.NET Standard]。</span><span class="sxs-lookup"><span data-stu-id="2c03c-113">From the left menu select **Installed > Visual C# > .NET Standard**.</span></span>

* <span data-ttu-id="2c03c-114">選取 [類別庫 (.NET Standard)] 範本。</span><span class="sxs-lookup"><span data-stu-id="2c03c-114">Select the **Class Library (.NET Standard)** template.</span></span>

* <span data-ttu-id="2c03c-115">將專案命名為 *Blogging.Model*。</span><span class="sxs-lookup"><span data-stu-id="2c03c-115">Name the project *Blogging.Model*.</span></span>

* <span data-ttu-id="2c03c-116">將方案命名為 *Blogging*。</span><span class="sxs-lookup"><span data-stu-id="2c03c-116">Name the solution *Blogging*.</span></span>

* <span data-ttu-id="2c03c-117">按一下 [確定]。</span><span class="sxs-lookup"><span data-stu-id="2c03c-117">Click **OK**.</span></span>

## <a name="install-entity-framework-core"></a><span data-ttu-id="2c03c-118">安裝 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="2c03c-118">Install Entity Framework Core</span></span>

<span data-ttu-id="2c03c-119">若要使用 EF Core，請針對您要作為目標的資料庫提供者來安裝套件。</span><span class="sxs-lookup"><span data-stu-id="2c03c-119">To use EF Core, install the package for the database provider(s) you want to target.</span></span> <span data-ttu-id="2c03c-120">此教學課程使用 SQLite。</span><span class="sxs-lookup"><span data-stu-id="2c03c-120">This tutorial uses SQLite.</span></span> <span data-ttu-id="2c03c-121">如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="2c03c-121">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="2c03c-122">**[工具] -> [NuGet 套件管理員] -> [套件管理員主控台]**。</span><span class="sxs-lookup"><span data-stu-id="2c03c-122">**Tools > NuGet Package Manager > Package Manager Console**.</span></span>

* <span data-ttu-id="2c03c-123">執行 `Install-Package Microsoft.EntityFrameworkCore.Sqlite`</span><span class="sxs-lookup"><span data-stu-id="2c03c-123">Run `Install-Package Microsoft.EntityFrameworkCore.Sqlite`</span></span>

<span data-ttu-id="2c03c-124">您在本教學課程稍後將會使用一些 Entity Framework Core 工具來維護該資料庫。</span><span class="sxs-lookup"><span data-stu-id="2c03c-124">Later in this tutorial you will be using some Entity Framework Core tools to maintain the database.</span></span> <span data-ttu-id="2c03c-125">因此，也請安裝工具套件。</span><span class="sxs-lookup"><span data-stu-id="2c03c-125">So install the tools package as well.</span></span>

* <span data-ttu-id="2c03c-126">執行 `Install-Package Microsoft.EntityFrameworkCore.Tools`</span><span class="sxs-lookup"><span data-stu-id="2c03c-126">Run `Install-Package Microsoft.EntityFrameworkCore.Tools`</span></span>

## <a name="create-the-model"></a><span data-ttu-id="2c03c-127">建立模型</span><span class="sxs-lookup"><span data-stu-id="2c03c-127">Create the model</span></span>

<span data-ttu-id="2c03c-128">您現已可定義組成模型的內容與實體類別。</span><span class="sxs-lookup"><span data-stu-id="2c03c-128">Now it's time to define a context and entity classes that make up the model.</span></span>

* <span data-ttu-id="2c03c-129">刪除 *Class1.cs*。</span><span class="sxs-lookup"><span data-stu-id="2c03c-129">Delete *Class1.cs*.</span></span>

* <span data-ttu-id="2c03c-130">使用下列程式碼建立 *Model.cs*：</span><span class="sxs-lookup"><span data-stu-id="2c03c-130">Create *Model.cs* with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/UWP/Blogging.Model/Model.cs)]

## <a name="create-a-new-uwp-project"></a><span data-ttu-id="2c03c-131">建立新 UWP 專案</span><span class="sxs-lookup"><span data-stu-id="2c03c-131">Create a new UWP project</span></span>

* <span data-ttu-id="2c03c-132">在 [方案總管] 中，以滑鼠右鍵按一下方案，然後選擇 [新增] > [新增專案]。</span><span class="sxs-lookup"><span data-stu-id="2c03c-132">In **Solution Explorer**, right-click the solution, and then choose **Add > New Project**.</span></span>

* <span data-ttu-id="2c03c-133">從左側功能表中，選取 [已安裝] > [Visual C#] > [Windows 通用]。</span><span class="sxs-lookup"><span data-stu-id="2c03c-133">From the left menu select **Installed > Visual C# > Windows Universal**.</span></span>

* <span data-ttu-id="2c03c-134">選取 [空白應用程式 (通用 Windows)] 專案範本。</span><span class="sxs-lookup"><span data-stu-id="2c03c-134">Select the **Blank App (Universal Windows)** project template.</span></span>

* <span data-ttu-id="2c03c-135">將專案命名為 *Blogging.UWP*，然後按一下 [確定]</span><span class="sxs-lookup"><span data-stu-id="2c03c-135">Name the project *Blogging.UWP*, and click **OK**</span></span>

* <span data-ttu-id="2c03c-136">至少將目標與最低版本設定為 **Windows 10 Fall Creators Update (10.0; build 16299.0)**。</span><span class="sxs-lookup"><span data-stu-id="2c03c-136">Set the target and minimum versions to at least **Windows 10 Fall Creators Update (10.0; build 16299.0)**.</span></span>

## <a name="create-the-initial-migration"></a><span data-ttu-id="2c03c-137">建立初始移轉</span><span class="sxs-lookup"><span data-stu-id="2c03c-137">Create the initial migration</span></span>

<span data-ttu-id="2c03c-138">現在您已經有模型，請設定應用程式以在第一次執行時建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="2c03c-138">Now that you have a model, set up the app to create a database the first time it runs.</span></span> <span data-ttu-id="2c03c-139">自此節中，您將會建立初始移轉。</span><span class="sxs-lookup"><span data-stu-id="2c03c-139">In this section, you create the initial migration.</span></span> <span data-ttu-id="2c03c-140">在下一節中，您將會新增會在應用程式啟動時套用此移轉的程式碼。</span><span class="sxs-lookup"><span data-stu-id="2c03c-140">In the following section, you add code that applies this migration when the app starts.</span></span>

<span data-ttu-id="2c03c-141">移轉工具需要非 UWP 起始專案，因此請先建議該專案。</span><span class="sxs-lookup"><span data-stu-id="2c03c-141">Migrations tools require a non-UWP startup project, so create that first.</span></span>

* <span data-ttu-id="2c03c-142">在 [方案總管] 中，以滑鼠右鍵按一下方案，然後選擇 [新增] > [新增專案]。</span><span class="sxs-lookup"><span data-stu-id="2c03c-142">In **Solution Explorer**, right-click the solution, and then choose **Add > New Project**.</span></span>

* <span data-ttu-id="2c03c-143">從左側功能表選取 [已安裝] > [Visual C#] > [.NET Core]。</span><span class="sxs-lookup"><span data-stu-id="2c03c-143">From the left menu select **Installed > Visual C# > .NET Core**.</span></span>

* <span data-ttu-id="2c03c-144">選取 [主控台應用程式 (.NET Core)] 專案範本。</span><span class="sxs-lookup"><span data-stu-id="2c03c-144">Select the **Console App (.NET Core)** project template.</span></span>

* <span data-ttu-id="2c03c-145">將專案命名為 *Blogging.Migrations.Startup*，然後按一下 [確定]。</span><span class="sxs-lookup"><span data-stu-id="2c03c-145">Name the project *Blogging.Migrations.Startup*, and click **OK**.</span></span>

* <span data-ttu-id="2c03c-146">新增從 *Blogging.Migrations.Startup* 專案到 *Blogging.Model* 專案的專案參考。</span><span class="sxs-lookup"><span data-stu-id="2c03c-146">Add a project reference from the *Blogging.Migrations.Startup* project to the *Blogging.Model* project.</span></span>

<span data-ttu-id="2c03c-147">現在您可以建立初始移轉。</span><span class="sxs-lookup"><span data-stu-id="2c03c-147">Now you can create your initial migration.</span></span>

* <span data-ttu-id="2c03c-148">[工具] > [NuGet 套件管理員] > [套件管理員主控台]</span><span class="sxs-lookup"><span data-stu-id="2c03c-148">**Tools > NuGet Package Manager > Package Manager Console**</span></span>

* <span data-ttu-id="2c03c-149">選取 *Blogging.Model* 專案做為 [預設專案]。</span><span class="sxs-lookup"><span data-stu-id="2c03c-149">Select the *Blogging.Model* project as the **Default project**.</span></span>

* <span data-ttu-id="2c03c-150">在 [方案總管] 中，將 *Blogging.Migrations.Startup* 專案設定為啟始專案。</span><span class="sxs-lookup"><span data-stu-id="2c03c-150">In **Solution Explorer**, set the *Blogging.Migrations.Startup* project as the startup project.</span></span>

* <span data-ttu-id="2c03c-151">執行 `Add-Migration InitialCreate`。</span><span class="sxs-lookup"><span data-stu-id="2c03c-151">Run `Add-Migration InitialCreate`.</span></span>

  <span data-ttu-id="2c03c-152">此命令會建立移轉的支架，以針對您的模型建立一組初始資料表。</span><span class="sxs-lookup"><span data-stu-id="2c03c-152">This command scaffolds a migration that creates the initial set of tables for your model.</span></span>

## <a name="create-the-database-on-app-startup"></a><span data-ttu-id="2c03c-153">在應用程式啟動時建立資料庫</span><span class="sxs-lookup"><span data-stu-id="2c03c-153">Create the database on app startup</span></span>

<span data-ttu-id="2c03c-154">因為您要在執行應用程式的裝置上建立資料庫，請新增程式碼，在應用程式啟動時將任何擱置中的移轉套用至本機資料庫。</span><span class="sxs-lookup"><span data-stu-id="2c03c-154">Since you want the database to be created on the device that the app runs on, add code to apply any pending migrations to the local database on application startup.</span></span> <span data-ttu-id="2c03c-155">第一次執行應用程式時，這會負責建立本機資料庫。</span><span class="sxs-lookup"><span data-stu-id="2c03c-155">The first time that the app runs, this will take care of creating the local database.</span></span>

* <span data-ttu-id="2c03c-156">新增從 *Blogging.UWP* 專案到 *Blogging.Model* 專案的專案參考。</span><span class="sxs-lookup"><span data-stu-id="2c03c-156">Add a project reference from the *Blogging.UWP* project to the *Blogging.Model* project.</span></span>

* <span data-ttu-id="2c03c-157">開啟 *App.xaml.cs*。</span><span class="sxs-lookup"><span data-stu-id="2c03c-157">Open *App.xaml.cs*.</span></span>

* <span data-ttu-id="2c03c-158">加入醒目提示的程式碼，以套用任何擱置中的移轉。</span><span class="sxs-lookup"><span data-stu-id="2c03c-158">Add the highlighted code to apply any pending migrations.</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/UWP/Blogging.UWP/App.xaml.cs?highlight=1-2,26-29)]

> [!TIP]  
> <span data-ttu-id="2c03c-159">若您變更模型，請使用 `Add-Migration` 命令來建立新移轉的支架，以將相對應的變更套用到資料庫。</span><span class="sxs-lookup"><span data-stu-id="2c03c-159">If you change your model, use the `Add-Migration` command to scaffold a new migration to apply the corresponding changes to the database.</span></span> <span data-ttu-id="2c03c-160">應用程式啟動時，會將任何擱置中的移轉套用至每個裝置上的本機資料庫。</span><span class="sxs-lookup"><span data-stu-id="2c03c-160">Any pending migrations will be applied to the local database on each device when the application starts.</span></span>
>
><span data-ttu-id="2c03c-161">EF 在資料庫中使用 `__EFMigrationsHistory` 資料表，以確認已經套用至資料庫的移轉。</span><span class="sxs-lookup"><span data-stu-id="2c03c-161">EF uses a `__EFMigrationsHistory` table in the database to keep track of which migrations have already been applied to the database.</span></span>

## <a name="use-the-model"></a><span data-ttu-id="2c03c-162">使用模型</span><span class="sxs-lookup"><span data-stu-id="2c03c-162">Use the model</span></span>

<span data-ttu-id="2c03c-163">您現已可使用模型來執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="2c03c-163">You can now use the model to perform data access.</span></span>

* <span data-ttu-id="2c03c-164">開啟 *MainPage.xaml*。</span><span class="sxs-lookup"><span data-stu-id="2c03c-164">Open *MainPage.xaml*.</span></span>

* <span data-ttu-id="2c03c-165">加入下列醒目提示的頁面載入處理常式和 UI 內容</span><span class="sxs-lookup"><span data-stu-id="2c03c-165">Add the page load handler and UI content highlighted below</span></span>

[!code-xml[Main](../../../../samples/core/GetStarted/UWP/Blogging.UWP/MainPage.xaml?highlight=9,11-23)]

<span data-ttu-id="2c03c-166">現在加入程式碼，以連接 UI 與資料庫</span><span class="sxs-lookup"><span data-stu-id="2c03c-166">Now add code to wire up the UI with the database</span></span>

* <span data-ttu-id="2c03c-167">開啟 *MainPage.xaml.cs*。</span><span class="sxs-lookup"><span data-stu-id="2c03c-167">Open *MainPage.xaml.cs*.</span></span>

* <span data-ttu-id="2c03c-168">從下列清單中加入醒目提示的程式碼：</span><span class="sxs-lookup"><span data-stu-id="2c03c-168">Add the highlighted code from the following listing:</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/UWP/Blogging.UWP/MainPage.xaml.cs?highlight=1,31-49)]

<span data-ttu-id="2c03c-169">您現在可以執行應用程式來查看運作狀況。</span><span class="sxs-lookup"><span data-stu-id="2c03c-169">You can now run the application to see it in action.</span></span>

* <span data-ttu-id="2c03c-170">在 [方案總管] 中，以滑鼠右鍵按一下 [Blogging.UWP] 專案，然後選取 [部署]。</span><span class="sxs-lookup"><span data-stu-id="2c03c-170">In **Solution Explorer**, right-click the *Blogging.UWP* project and then select **Deploy**.</span></span>

* <span data-ttu-id="2c03c-171">將 *Blogging.UWP* 設定為起始專案。</span><span class="sxs-lookup"><span data-stu-id="2c03c-171">Set *Blogging.UWP* as the startup project.</span></span>

* <span data-ttu-id="2c03c-172">[偵錯] > [啟動但不偵錯]</span><span class="sxs-lookup"><span data-stu-id="2c03c-172">**Debug > Start Without Debugging**</span></span>

  <span data-ttu-id="2c03c-173">應用程式會建置並執行。</span><span class="sxs-lookup"><span data-stu-id="2c03c-173">The app builds and runs.</span></span>

* <span data-ttu-id="2c03c-174">輸入 URL 並按一下 [加入] 按鈕</span><span class="sxs-lookup"><span data-stu-id="2c03c-174">Enter a URL and click the **Add** button</span></span>

  ![影像](_static/create.png)

  ![影像](_static/list.png)

  <span data-ttu-id="2c03c-177">大功告成！</span><span class="sxs-lookup"><span data-stu-id="2c03c-177">Tada!</span></span> <span data-ttu-id="2c03c-178">您現在有一個執行 Entity Framework Core 的簡單 UWP 應用程式了。</span><span class="sxs-lookup"><span data-stu-id="2c03c-178">You now have a simple UWP app running Entity Framework Core.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2c03c-179">後續步驟</span><span class="sxs-lookup"><span data-stu-id="2c03c-179">Next steps</span></span>

<span data-ttu-id="2c03c-180">針對搭配 UWP 使用 EF Core 時應該知道的相容性與效能資訊，請參閱 [EF Core 提供的 .NET 實作支援](../../platforms/index.md#universal-windows-platform)。</span><span class="sxs-lookup"><span data-stu-id="2c03c-180">For compatibility and performance information that you should know when using EF Core with UWP, see [.NET implementations supported by EF Core](../../platforms/index.md#universal-windows-platform).</span></span>

<span data-ttu-id="2c03c-181">請參閱此文件中的其他文章，以深入了解 Entity Framework Core 的功能。</span><span class="sxs-lookup"><span data-stu-id="2c03c-181">Check out other articles in this documentation to learn more about Entity Framework Core features.</span></span>
