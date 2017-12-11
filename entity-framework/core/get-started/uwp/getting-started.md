---
title: "UWP 使用者入門 - 新資料庫 - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.topic: get-started-article
ms.assetid: a0ae2f21-1eef-43c6-83ad-92275f9c0727
ms.technology: entity-framework-core
uid: core/get-started/uwp/getting-started
ms.openlocfilehash: f743ff5392d1f30283a13d2e7fb8029be88387aa
ms.sourcegitcommit: 96324e58c02b97277395ed43173bf13ac80d2012
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2017
---
# <a name="getting-started-with-ef-core-on-universal-windows-platform-uwp-with-a-new-database"></a><span data-ttu-id="a54c8-102">在通用 Windows 平台 (UWP) 上使用 EF Core 搭配新資料庫的使用者入門</span><span class="sxs-lookup"><span data-stu-id="a54c8-102">Getting Started with EF Core on Universal Windows Platform (UWP) with a New Database</span></span>

> [!NOTE]
> <span data-ttu-id="a54c8-103">本教學課程使用 EF Core 2.0.1 (連同 ASP.NET Core 和 .NET Core SDK 2.0.3 一起發行)。</span><span class="sxs-lookup"><span data-stu-id="a54c8-103">This tutorial uses EF Core 2.0.1 (released alongside ASP.NET Core and .NET Core SDK 2.0.3).</span></span> <span data-ttu-id="a54c8-104">EF Core 2.0.0 缺少良好 UWP 體驗所需的某些重要錯誤 (bug) 修正。</span><span class="sxs-lookup"><span data-stu-id="a54c8-104">EF Core 2.0.0 lacks some crucial bug fixes required for a good UWP experience.</span></span>

<span data-ttu-id="a54c8-105">在本逐步解說中，您將建置通用 Windows 平台 (UWP) 應用程式，該應用程式將使用 Entity Framework 對本機 SQLite 資料庫執行基本資料存取。</span><span class="sxs-lookup"><span data-stu-id="a54c8-105">In this walkthrough, you will build a Universal Windows Platform (UWP) application that performs basic data access against a local SQLite database using Entity Framework.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a54c8-106">**請考慮避免在 UWP 上的 LINQ 查詢中使用匿名型別**。</span><span class="sxs-lookup"><span data-stu-id="a54c8-106">**Consider avoiding anonymous types in LINQ queries on UWP**.</span></span> <span data-ttu-id="a54c8-107">若要將 UWP 應用程式部署至應用程式市集，您的應用程式需要使用 .NET Native 來編譯。</span><span class="sxs-lookup"><span data-stu-id="a54c8-107">Deploying a UWP application to the app store requires your application to be compiled with .NET Native.</span></span> <span data-ttu-id="a54c8-108">使用匿名型別的查詢在 .NET Native 上會有較差的效能。</span><span class="sxs-lookup"><span data-stu-id="a54c8-108">Queries with anonymous types have worse performance on .NET Native.</span></span>

> [!TIP]
> <span data-ttu-id="a54c8-109">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/UWP/UWP.SQLite) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="a54c8-109">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/UWP/UWP.SQLite) on GitHub.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a54c8-110">必要條件</span><span class="sxs-lookup"><span data-stu-id="a54c8-110">Prerequisites</span></span>

<span data-ttu-id="a54c8-111">若要完成此逐步解說，必須要有下列項目：</span><span class="sxs-lookup"><span data-stu-id="a54c8-111">The following items are required to complete this walkthrough:</span></span>

* <span data-ttu-id="a54c8-112">[Windows 10 Fall Creators Update](https://support.microsoft.com/en-us/help/4027667/windows-update-windows-10) (10.0.16299.0)</span><span class="sxs-lookup"><span data-stu-id="a54c8-112">[Windows 10 Fall Creators Update](https://support.microsoft.com/en-us/help/4027667/windows-update-windows-10) (10.0.16299.0)</span></span>

* <span data-ttu-id="a54c8-113">[.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="a54c8-113">[.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) or later.</span></span>

* <span data-ttu-id="a54c8-114">[Visual Studio 2017](https://www.visualstudio.com/downloads/) 15.4 版或更新版本，包含**通用 Windows 平台開發**工作負載。</span><span class="sxs-lookup"><span data-stu-id="a54c8-114">[Visual Studio 2017](https://www.visualstudio.com/downloads/) version 15.4 or later with the **Universal Windows Platform Development** workload.</span></span>

## <a name="create-a-new-model-project"></a><span data-ttu-id="a54c8-115">建立新模型專案</span><span class="sxs-lookup"><span data-stu-id="a54c8-115">Create a new model project</span></span>

> [!WARNING]
> <span data-ttu-id="a54c8-116">由於 .NET Core 工具與 UWP 專案互動方式的限制，模型需要放置在非 UWP 專案中，才能在套件管理員主控台中執行移轉命令</span><span class="sxs-lookup"><span data-stu-id="a54c8-116">Due to limitations in the way .NET Core tools interact with UWP projects the model needs to be placed in a non-UWP project to be able to run migrations commands in the Package Manager Console</span></span>

* <span data-ttu-id="a54c8-117">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a54c8-117">Open Visual Studio</span></span>

* <span data-ttu-id="a54c8-118">[檔案] > [新增] > [專案]...</span><span class="sxs-lookup"><span data-stu-id="a54c8-118">File > New > Project...</span></span>

* <span data-ttu-id="a54c8-119">從左側功能表選取 [範本] > [Visual C#]</span><span class="sxs-lookup"><span data-stu-id="a54c8-119">From the left menu select Templates > Visual C#</span></span>

* <span data-ttu-id="a54c8-120">選取 [類別庫 (.NET Standard)] 專案範本</span><span class="sxs-lookup"><span data-stu-id="a54c8-120">Select the **Class Library (.NET Standard)** project template</span></span>

* <span data-ttu-id="a54c8-121">提供專案名稱，然後按一下 [確定]</span><span class="sxs-lookup"><span data-stu-id="a54c8-121">Give the project a name and click **OK**</span></span>

## <a name="install-entity-framework"></a><span data-ttu-id="a54c8-122">安裝 Entity Framework</span><span class="sxs-lookup"><span data-stu-id="a54c8-122">Install Entity Framework</span></span>

<span data-ttu-id="a54c8-123">若要使用 EF Core，請針對您要作為目標的資料庫提供者來安裝套件。</span><span class="sxs-lookup"><span data-stu-id="a54c8-123">To use EF Core, install the package for the database provider(s) you want to target.</span></span> <span data-ttu-id="a54c8-124">本逐步解說使用 SQLite。</span><span class="sxs-lookup"><span data-stu-id="a54c8-124">This walkthrough uses SQLite.</span></span> <span data-ttu-id="a54c8-125">如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="a54c8-125">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="a54c8-126">[工具] > [NuGet 套件管理員] > [套件管理員主控台]</span><span class="sxs-lookup"><span data-stu-id="a54c8-126">Tools > NuGet Package Manager > Package Manager Console</span></span>

* <span data-ttu-id="a54c8-127">執行 `Install-Package Microsoft.EntityFrameworkCore.Sqlite`</span><span class="sxs-lookup"><span data-stu-id="a54c8-127">Run `Install-Package Microsoft.EntityFrameworkCore.Sqlite`</span></span>

<span data-ttu-id="a54c8-128">稍後在本逐步解說中，我們將使用部分 Entity Framework 工具來維護資料庫。</span><span class="sxs-lookup"><span data-stu-id="a54c8-128">Later in this walkthrough we will also be using some Entity Framework Tools to maintain the database.</span></span> <span data-ttu-id="a54c8-129">因此，我們也將安裝工具套件。</span><span class="sxs-lookup"><span data-stu-id="a54c8-129">So we will install the tools package as well.</span></span>

* <span data-ttu-id="a54c8-130">執行 `Install-Package Microsoft.EntityFrameworkCore.Tools`</span><span class="sxs-lookup"><span data-stu-id="a54c8-130">Run `Install-Package Microsoft.EntityFrameworkCore.Tools`</span></span>

* <span data-ttu-id="a54c8-131">編輯 .csproj 檔案，並以 `<TargetFrameworks>netcoreapp2.0;netstandard2.0</TargetFrameworks>` 取代 `<TargetFramework>netstandard2.0</TargetFramework>`</span><span class="sxs-lookup"><span data-stu-id="a54c8-131">Edit the .csproj file and replace `<TargetFramework>netstandard2.0</TargetFramework>` with `<TargetFrameworks>netcoreapp2.0;netstandard2.0</TargetFrameworks>`</span></span>

## <a name="create-your-model"></a><span data-ttu-id="a54c8-132">建立您的模型</span><span class="sxs-lookup"><span data-stu-id="a54c8-132">Create your model</span></span>

<span data-ttu-id="a54c8-133">現在就來定義組成模型的內容和實體類別。</span><span class="sxs-lookup"><span data-stu-id="a54c8-133">Now it's time to define a context and entity classes that make up your model.</span></span>

* <span data-ttu-id="a54c8-134">[專案] > [加入類別]...</span><span class="sxs-lookup"><span data-stu-id="a54c8-134">Project > Add Class...</span></span>

* <span data-ttu-id="a54c8-135">輸入 *Model.cs* 作為名稱，然後按一下 [確定]</span><span class="sxs-lookup"><span data-stu-id="a54c8-135">Enter *Model.cs* as the name and click **OK**</span></span>

* <span data-ttu-id="a54c8-136">以下列程式碼來取代檔案的內容</span><span class="sxs-lookup"><span data-stu-id="a54c8-136">Replace the contents of the file with the following code</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/UWP/UWP.Model/Model.cs)]

## <a name="create-a-new-uwp-project"></a><span data-ttu-id="a54c8-137">建立新 UWP 專案</span><span class="sxs-lookup"><span data-stu-id="a54c8-137">Create a new UWP project</span></span>

* <span data-ttu-id="a54c8-138">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a54c8-138">Open Visual Studio</span></span>

* <span data-ttu-id="a54c8-139">[檔案] > [新增] > [專案]...</span><span class="sxs-lookup"><span data-stu-id="a54c8-139">File > New > Project...</span></span>

* <span data-ttu-id="a54c8-140">從左側功能表選取 [範本] > [Visual C#] > [Windows 通用]</span><span class="sxs-lookup"><span data-stu-id="a54c8-140">From the left menu select Templates > Visual C# > Windows Universal</span></span>

* <span data-ttu-id="a54c8-141">選取 [空白應用程式 (通用 Windows)] 專案範本</span><span class="sxs-lookup"><span data-stu-id="a54c8-141">Select the **Blank App (Universal Windows)** project template</span></span>

* <span data-ttu-id="a54c8-142">提供專案名稱，然後按一下 [確定]</span><span class="sxs-lookup"><span data-stu-id="a54c8-142">Give the project a name and click **OK**</span></span>

* <span data-ttu-id="a54c8-143">將目標和最小版本至少設定為 `Windows 10 Fall Creators Update (10.0; build 16299.0)`</span><span class="sxs-lookup"><span data-stu-id="a54c8-143">Set the target and minimum versions to at least `Windows 10 Fall Creators Update (10.0; build 16299.0)`</span></span>

## <a name="create-your-database"></a><span data-ttu-id="a54c8-144">建立您的資料庫</span><span class="sxs-lookup"><span data-stu-id="a54c8-144">Create your database</span></span>

<span data-ttu-id="a54c8-145">一旦有模型之後，就可以使用移轉來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="a54c8-145">Now that you have a model, you can use migrations to create a database for you.</span></span>

* <span data-ttu-id="a54c8-146">[工具] –> [NuGet 套件管理員] –> [套件管理員主控台]</span><span class="sxs-lookup"><span data-stu-id="a54c8-146">Tools –> NuGet Package Manager –> Package Manager Console</span></span>

* <span data-ttu-id="a54c8-147">選取模型專案作為預設專案，並將它設定為啟始專案</span><span class="sxs-lookup"><span data-stu-id="a54c8-147">Select the model project as the Default project and set it as the startup project</span></span>

* <span data-ttu-id="a54c8-148">執行 `Add-Migration MyFirstMigration` 來建立移轉的結構，以針對您的模型建立一組初始的資料表。</span><span class="sxs-lookup"><span data-stu-id="a54c8-148">Run `Add-Migration MyFirstMigration` to scaffold a migration to create the initial set of tables for your model.</span></span>

<span data-ttu-id="a54c8-149">因為我們要在執行應用程式的裝置上建立資料庫，所以我們將加入一些程式碼，在應用程式啟動時將任何擱置中的移轉套用至本機資料庫。</span><span class="sxs-lookup"><span data-stu-id="a54c8-149">Since we want the database to be created on the device that the app runs on, we will add some code to apply any pending migrations to the local database on application startup.</span></span> <span data-ttu-id="a54c8-150">第一次執行應用程式時，這會負責為我們建立本機資料庫。</span><span class="sxs-lookup"><span data-stu-id="a54c8-150">The first time that the app runs, this will take care of creating the local database for us.</span></span>

* <span data-ttu-id="a54c8-151">在 [方案總管] 中，以滑鼠右鍵按一下 [App.xaml]，然後選取 [檢視程式碼]</span><span class="sxs-lookup"><span data-stu-id="a54c8-151">Right-click on **App.xaml** in **Solution Explorer** and select **View Code**</span></span>

* <span data-ttu-id="a54c8-152">將醒目提示的 using 加入檔案的開頭</span><span class="sxs-lookup"><span data-stu-id="a54c8-152">Add the highlighted using to the start of the file</span></span>

* <span data-ttu-id="a54c8-153">加入醒目提示的程式碼，以套用擱置中的移轉</span><span class="sxs-lookup"><span data-stu-id="a54c8-153">Add the highlighted code to apply any pending migrations</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/UWP/UWP.SQLite/App.xaml.cs?highlight=1,25-28)]

> [!TIP]  
> <span data-ttu-id="a54c8-154">如果日後要對您的模型進行變更，則可以使用 `Add-Migration` 命令來建立新移轉的結構，以將相對應的變更套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="a54c8-154">If you make future changes to your model, you can use the `Add-Migration` command to scaffold a new migration to apply the corresponding changes to the database.</span></span> <span data-ttu-id="a54c8-155">應用程式啟動時，會將任何擱置中的移轉套用至每個裝置上的本機資料庫。</span><span class="sxs-lookup"><span data-stu-id="a54c8-155">Any pending migrations will be applied to the local database on each device when the application starts.</span></span>
>
><span data-ttu-id="a54c8-156">EF 在資料庫中使用 `__EFMigrationsHistory` 資料表，以確認已經套用至資料庫的移轉。</span><span class="sxs-lookup"><span data-stu-id="a54c8-156">EF uses a `__EFMigrationsHistory` table in the database to keep track of which migrations have already been applied to the database.</span></span>

## <a name="use-your-model"></a><span data-ttu-id="a54c8-157">使用您的模型</span><span class="sxs-lookup"><span data-stu-id="a54c8-157">Use your model</span></span>

<span data-ttu-id="a54c8-158">您現在可以使用您的模型來執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="a54c8-158">You can now use your model to perform data access.</span></span>

* <span data-ttu-id="a54c8-159">開啟 *MainPage.xaml*</span><span class="sxs-lookup"><span data-stu-id="a54c8-159">Open *MainPage.xaml*</span></span>

* <span data-ttu-id="a54c8-160">加入下列醒目提示的頁面載入處理常式和 UI 內容</span><span class="sxs-lookup"><span data-stu-id="a54c8-160">Add the page load handler and UI content highlighted below</span></span>

[!code-xml[Main](../../../../samples/core/GetStarted/UWP/UWP.SQLite/MainPage.xaml?highlight=9,11-23)]

<span data-ttu-id="a54c8-161">現在我們將加入程式碼，以連接 UI 與資料庫</span><span class="sxs-lookup"><span data-stu-id="a54c8-161">Now we'll add code to wire up the UI with the database</span></span>

* <span data-ttu-id="a54c8-162">在 [方案總管] 中，以滑鼠右鍵按一下 [MainPage.xaml]，然後選取 [檢視程式碼]</span><span class="sxs-lookup"><span data-stu-id="a54c8-162">Right-click **MainPage.xaml** in **Solution Explorer** and select **View Code**</span></span>

* <span data-ttu-id="a54c8-163">從下列清單中加入醒目提示的程式碼</span><span class="sxs-lookup"><span data-stu-id="a54c8-163">Add the highlighted code from the following listing</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/UWP/UWP.SQLite/MainPage.xaml.cs?highlight=30-48)]

<span data-ttu-id="a54c8-164">您現在可以執行應用程式來查看運作狀況。</span><span class="sxs-lookup"><span data-stu-id="a54c8-164">You can now run the application to see it in action.</span></span>

* <span data-ttu-id="a54c8-165">偵錯 > 啟動但不偵錯</span><span class="sxs-lookup"><span data-stu-id="a54c8-165">Debug > Start Without Debugging</span></span>

* <span data-ttu-id="a54c8-166">系統將會建置並啟動應用程式</span><span class="sxs-lookup"><span data-stu-id="a54c8-166">The application will build and launch</span></span>

* <span data-ttu-id="a54c8-167">輸入 URL 並按一下 [加入] 按鈕</span><span class="sxs-lookup"><span data-stu-id="a54c8-167">Enter a URL and click the **Add** button</span></span>

![image](_static/create.png)

![image](_static/list.png)

## <a name="next-steps"></a><span data-ttu-id="a54c8-170">後續步驟</span><span class="sxs-lookup"><span data-stu-id="a54c8-170">Next steps</span></span>

> [!TIP]
> <span data-ttu-id="a54c8-171">在您的實體類型中實作 [`INotifyPropertyChanged`](https://msdn.microsoft.com/en-us/library/system.componentmodel.inotifypropertychanged.aspx)、[`INotifyPropertyChanging`](https://msdn.microsoft.com/en-us/library/system.componentmodel.inotifypropertychanging.aspx)、[`INotifyCollectionChanged`](https://msdn.microsoft.com/en-us/library/system.collections.specialized.inotifycollectionchanged.aspx) 和使用 `ChangeTrackingStrategy.ChangingAndChangedNotifications`，可以改善 `SaveChanges()` 的效能。</span><span class="sxs-lookup"><span data-stu-id="a54c8-171">`SaveChanges()` performance can be improved by implementing [`INotifyPropertyChanged`](https://msdn.microsoft.com/en-us/library/system.componentmodel.inotifypropertychanged.aspx), [`INotifyPropertyChanging`](https://msdn.microsoft.com/en-us/library/system.componentmodel.inotifypropertychanging.aspx), [`INotifyCollectionChanged`](https://msdn.microsoft.com/en-us/library/system.collections.specialized.inotifycollectionchanged.aspx) in your entity types and using `ChangeTrackingStrategy.ChangingAndChangedNotifications`.</span></span>

<span data-ttu-id="a54c8-172">大功告成！</span><span class="sxs-lookup"><span data-stu-id="a54c8-172">Tada!</span></span> <span data-ttu-id="a54c8-173">您現在有一個執行 Entity Framework 的簡單 UWP 應用程式了。</span><span class="sxs-lookup"><span data-stu-id="a54c8-173">You now have a simple UWP app running Entity Framework.</span></span>

<span data-ttu-id="a54c8-174">請參閱此文件中的其他文章，以深入了解 Entity Framework 的功能。</span><span class="sxs-lookup"><span data-stu-id="a54c8-174">Check out other articles in this documentation to learn more about Entity Framework's features.</span></span>
