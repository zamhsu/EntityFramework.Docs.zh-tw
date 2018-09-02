---
title: .NET Framework 快速入門 - 新資料庫 - EF Core
author: rowanmiller
ms.date: 08/06/2018
ms.assetid: 52b69727-ded9-4a7b-b8d5-73f3acfbbad3
uid: core/get-started/full-dotnet/new-db
ms.openlocfilehash: 66d9011a5978fc3d8253a963bdb9df27848e9ff9
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997583"
---
# <a name="getting-started-with-ef-core-on-net-framework-with-a-new-database"></a><span data-ttu-id="fe0b8-102">在 .NET Framework 上使用 EF Core 搭配新資料庫的使用者入門</span><span class="sxs-lookup"><span data-stu-id="fe0b8-102">Getting started with EF Core on .NET Framework with a New Database</span></span>

<span data-ttu-id="fe0b8-103">在本教學課程中，您將會建置主控台應用程式，而其使用 Entity Framework 對 Microsoft SQL Server 資料庫執行基本的資料存取。</span><span class="sxs-lookup"><span data-stu-id="fe0b8-103">In this tutorial, you build a console application that performs basic data access against a Microsoft SQL Server database using Entity Framework.</span></span> <span data-ttu-id="fe0b8-104">您會使用移轉從模型建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="fe0b8-104">You use migrations to create the database from a model.</span></span>

<span data-ttu-id="fe0b8-105">[在 GitHub 上檢視此文章的範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/FullNet/ConsoleApp.NewDb)。</span><span class="sxs-lookup"><span data-stu-id="fe0b8-105">[View this article's sample on GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/FullNet/ConsoleApp.NewDb).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fe0b8-106">必要條件</span><span class="sxs-lookup"><span data-stu-id="fe0b8-106">Prerequisites</span></span>

* [<span data-ttu-id="fe0b8-107">Visual Studio 2017 15.7 版 (或更新版本)</span><span class="sxs-lookup"><span data-stu-id="fe0b8-107">Visual Studio 2017 version 15.7 or later</span></span>](https://www.visualstudio.com/downloads/)

## <a name="create-a-new-project"></a><span data-ttu-id="fe0b8-108">建立新專案</span><span class="sxs-lookup"><span data-stu-id="fe0b8-108">Create a new project</span></span>

* <span data-ttu-id="fe0b8-109">開啟 Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="fe0b8-109">Open Visual Studio 2017</span></span>

* <span data-ttu-id="fe0b8-110">[檔案] > [新增] > [專案...]</span><span class="sxs-lookup"><span data-stu-id="fe0b8-110">**File > New > Project...**</span></span>

* <span data-ttu-id="fe0b8-111">從左側功能表中，選取 **[已安裝] > [Visual C#] > [Windows Desktop]**</span><span class="sxs-lookup"><span data-stu-id="fe0b8-111">From the left menu select **Installed > Visual C# > Windows Desktop**</span></span>

* <span data-ttu-id="fe0b8-112">選取 [主控台應用程式 (.NET Framework)] 專案範本</span><span class="sxs-lookup"><span data-stu-id="fe0b8-112">Select the **Console App (.NET Framework)** project template</span></span>

* <span data-ttu-id="fe0b8-113">請確定專案的目標為 **.NET Framework 4.6.1** 或更新版本</span><span class="sxs-lookup"><span data-stu-id="fe0b8-113">Make sure that the project targets **.NET Framework 4.6.1** or later</span></span>

* <span data-ttu-id="fe0b8-114">將專案命名為 *ConsoleApp.NewDb*，然後按一下 [確定]</span><span class="sxs-lookup"><span data-stu-id="fe0b8-114">Name the project *ConsoleApp.NewDb* and click **OK**</span></span>

## <a name="install-entity-framework"></a><span data-ttu-id="fe0b8-115">安裝 Entity Framework</span><span class="sxs-lookup"><span data-stu-id="fe0b8-115">Install Entity Framework</span></span>

<span data-ttu-id="fe0b8-116">若要使用 EF Core，請針對您要作為目標的資料庫提供者來安裝套件。</span><span class="sxs-lookup"><span data-stu-id="fe0b8-116">To use EF Core, install the package for the database provider(s) you want to target.</span></span> <span data-ttu-id="fe0b8-117">本教學課程使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="fe0b8-117">This tutorial uses SQL Server.</span></span> <span data-ttu-id="fe0b8-118">如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="fe0b8-118">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="fe0b8-119">[工具] > [NuGet 套件管理員] > [套件管理員主控台]</span><span class="sxs-lookup"><span data-stu-id="fe0b8-119">Tools > NuGet Package Manager > Package Manager Console</span></span>

* <span data-ttu-id="fe0b8-120">執行 `Install-Package Microsoft.EntityFrameworkCore.SqlServer`</span><span class="sxs-lookup"><span data-stu-id="fe0b8-120">Run `Install-Package Microsoft.EntityFrameworkCore.SqlServer`</span></span>

<span data-ttu-id="fe0b8-121">您在本教學課程稍後將會使用一些 Entity Framework 工具，來維護該資料庫。</span><span class="sxs-lookup"><span data-stu-id="fe0b8-121">Later in this tutorial you use some Entity Framework Tools to maintain the database.</span></span> <span data-ttu-id="fe0b8-122">因此，也請安裝工具套件。</span><span class="sxs-lookup"><span data-stu-id="fe0b8-122">So install the tools package as well.</span></span>

* <span data-ttu-id="fe0b8-123">執行 `Install-Package Microsoft.EntityFrameworkCore.Tools`</span><span class="sxs-lookup"><span data-stu-id="fe0b8-123">Run `Install-Package Microsoft.EntityFrameworkCore.Tools`</span></span>

## <a name="create-the-model"></a><span data-ttu-id="fe0b8-124">建立模型</span><span class="sxs-lookup"><span data-stu-id="fe0b8-124">Create the model</span></span>

<span data-ttu-id="fe0b8-125">您現已可定義組成模型的內容與實體類別。</span><span class="sxs-lookup"><span data-stu-id="fe0b8-125">Now it's time to define a context and entity classes that make up the model.</span></span>

* <span data-ttu-id="fe0b8-126">[專案] > [新增類別...]</span><span class="sxs-lookup"><span data-stu-id="fe0b8-126">**Project > Add Class...**</span></span>

* <span data-ttu-id="fe0b8-127">輸入 *Model.cs* 作為名稱，然後按一下 [確定]</span><span class="sxs-lookup"><span data-stu-id="fe0b8-127">Enter *Model.cs* as the name and click **OK**</span></span>

* <span data-ttu-id="fe0b8-128">以下列程式碼來取代檔案的內容</span><span class="sxs-lookup"><span data-stu-id="fe0b8-128">Replace the contents of the file with the following code</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.NewDb/Model.cs)] 

> [!TIP]  
> <span data-ttu-id="fe0b8-129">在實際的應用程式中，要將每個類別置於不同的檔案中，然後將連接字串置於組態檔或環境變數中。</span><span class="sxs-lookup"><span data-stu-id="fe0b8-129">In a real application you would put each class in a separate file and put the connection string in a configuration file or environment variable.</span></span> <span data-ttu-id="fe0b8-130">為了簡單起見，在本教學課程中會將所有項目都置於單一個程式碼檔案中。</span><span class="sxs-lookup"><span data-stu-id="fe0b8-130">For the sake of simplicity, everything is in a single code file for this tutorial.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="fe0b8-131">建立資料庫</span><span class="sxs-lookup"><span data-stu-id="fe0b8-131">Create the database</span></span>

<span data-ttu-id="fe0b8-132">一旦有了模型之後，即可使用移轉來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="fe0b8-132">Now that you have a model, you can use migrations to create a database.</span></span>

* <span data-ttu-id="fe0b8-133">[工具] > [NuGet 套件管理員] > [套件管理員主控台]</span><span class="sxs-lookup"><span data-stu-id="fe0b8-133">**Tools > NuGet Package Manager > Package Manager Console**</span></span>

* <span data-ttu-id="fe0b8-134">執行 `Add-Migration InitialCreate` 可建立移轉的結構，為模型建立一組初始資料表。</span><span class="sxs-lookup"><span data-stu-id="fe0b8-134">Run `Add-Migration InitialCreate` to scaffold a migration to create the initial set of tables for the model.</span></span>

* <span data-ttu-id="fe0b8-135">執行 `Update-Database` 以將新的移轉套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="fe0b8-135">Run `Update-Database` to apply the new migration to the database.</span></span> <span data-ttu-id="fe0b8-136">因為您的資料庫尚未存在，因此會在套用移轉之前為您建立。</span><span class="sxs-lookup"><span data-stu-id="fe0b8-136">Because the database doesn't exist yet, it will be created before the migration is applied.</span></span>

> [!TIP]  
> <span data-ttu-id="fe0b8-137">若您變更了您的模型，則可使用 `Add-Migration` 命令來建立新移轉的結構，以對資料庫做出相對應的結構描述變更。</span><span class="sxs-lookup"><span data-stu-id="fe0b8-137">If you make changes to the model, you can use the `Add-Migration` command to scaffold a new migration to make the corresponding schema changes to the database.</span></span> <span data-ttu-id="fe0b8-138">檢查完已建立結構的程式碼之後 (並進行任何必要的變更)，就可以使用 `Update-Database` 命令將變更套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="fe0b8-138">Once you have checked the scaffolded code (and made any required changes), you can use the `Update-Database` command to apply the changes to the database.</span></span>
>
> <span data-ttu-id="fe0b8-139">EF 在資料庫中使用 `__EFMigrationsHistory` 資料表，以確認已經套用至資料庫的移轉。</span><span class="sxs-lookup"><span data-stu-id="fe0b8-139">EF uses a `__EFMigrationsHistory` table in the database to keep track of which migrations have already been applied to the database.</span></span>

## <a name="use-the-model"></a><span data-ttu-id="fe0b8-140">使用模型</span><span class="sxs-lookup"><span data-stu-id="fe0b8-140">Use the model</span></span>

<span data-ttu-id="fe0b8-141">您現已可使用模型來執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="fe0b8-141">You can now use the model to perform data access.</span></span>

* <span data-ttu-id="fe0b8-142">開啟 *Program.cs*</span><span class="sxs-lookup"><span data-stu-id="fe0b8-142">Open *Program.cs*</span></span>

* <span data-ttu-id="fe0b8-143">以下列程式碼來取代檔案的內容</span><span class="sxs-lookup"><span data-stu-id="fe0b8-143">Replace the contents of the file with the following code</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.NewDb/Program.cs)]

* <span data-ttu-id="fe0b8-144">[偵錯] > [啟動但不偵錯]</span><span class="sxs-lookup"><span data-stu-id="fe0b8-144">**Debug > Start Without Debugging**</span></span>

  <span data-ttu-id="fe0b8-145">您會看到有一個部落格儲存至資料庫，然後所有部落格的詳細資料皆會列印至主控台。</span><span class="sxs-lookup"><span data-stu-id="fe0b8-145">You see that one blog is saved to the database and then the details of all blogs are printed to the console.</span></span>

  ![影像](_static/output-new-db.png)

## <a name="additional-resources"></a><span data-ttu-id="fe0b8-147">其他資源</span><span class="sxs-lookup"><span data-stu-id="fe0b8-147">Additional Resources</span></span>

* [<span data-ttu-id="fe0b8-148">.NET Framework 上的 EF Core (附現有資料庫)</span><span class="sxs-lookup"><span data-stu-id="fe0b8-148">EF Core on .NET Framework with an existing database</span></span>](xref:core/get-started/full-dotnet/existing-db)
* <span data-ttu-id="fe0b8-149">[.NET Framework 上的 EF Core (附新資料庫) - SQLite](xref:core/get-started/netcore/new-db-sqlite) - 跨平台主控台 EF 教學課程。</span><span class="sxs-lookup"><span data-stu-id="fe0b8-149">[EF Core on .NET Core with a new database - SQLite](xref:core/get-started/netcore/new-db-sqlite) -  a cross-platform console EF tutorial.</span></span>
