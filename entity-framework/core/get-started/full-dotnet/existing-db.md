---
title: .NET Framework 快速入門 - 現有的資料庫 - EF Core
author: rowanmiller
ms.date: 08/06/2018
ms.assetid: a29a3d97-b2d8-4d33-9475-40ac67b3b2c6
uid: core/get-started/full-dotnet/existing-db
ms.openlocfilehash: b9e079f88dd35016407b19bb627f8bd46edb3d4c
ms.sourcegitcommit: ad1bdea58ed35d0f19791044efe9f72f94189c18
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/28/2018
ms.locfileid: "47447153"
---
# <a name="getting-started-with-ef-core-on-net-framework-with-an-existing-database"></a><span data-ttu-id="73fc7-102">在 .NET Framework 上使用 EF Core 搭配現有資料庫的使用者入門</span><span class="sxs-lookup"><span data-stu-id="73fc7-102">Getting started with EF Core on .NET Framework with an Existing Database</span></span>

<span data-ttu-id="73fc7-103">在本教學課程中，您將會建置主控台應用程式，而其使用 Entity Framework 對 Microsoft SQL Server 資料庫執行基本的資料存取。</span><span class="sxs-lookup"><span data-stu-id="73fc7-103">In this tutorial, you build a console application that performs basic data access against a Microsoft SQL Server database using Entity Framework.</span></span> <span data-ttu-id="73fc7-104">您會對現有的資料庫進行還原工程，以建立 Entity Framework 模型。</span><span class="sxs-lookup"><span data-stu-id="73fc7-104">You create an Entity Framework model by reverse engineering an existing database.</span></span>

<span data-ttu-id="73fc7-105">[在 GitHub 上檢視此文章的範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb)。</span><span class="sxs-lookup"><span data-stu-id="73fc7-105">[View this article's sample on GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="73fc7-106">必要條件</span><span class="sxs-lookup"><span data-stu-id="73fc7-106">Prerequisites</span></span>

* [<span data-ttu-id="73fc7-107">Visual Studio 2017 15.7 版 (或更新版本)</span><span class="sxs-lookup"><span data-stu-id="73fc7-107">Visual Studio 2017 version 15.7 or later</span></span>](https://www.visualstudio.com/downloads/)

## <a name="create-blogging-database"></a><span data-ttu-id="73fc7-108">建立部落格資料庫</span><span class="sxs-lookup"><span data-stu-id="73fc7-108">Create Blogging database</span></span>

<span data-ttu-id="73fc7-109">本教學課程使用 LocalDb 執行個體上的**部落格**資料庫，作為現有的資料庫。</span><span class="sxs-lookup"><span data-stu-id="73fc7-109">This tutorial uses a **Blogging** database on the LocalDb instance as the existing database.</span></span> <span data-ttu-id="73fc7-110">如果您在另一個教學課程中已建立了**部落格**資料庫，則可跳過這些步驟。</span><span class="sxs-lookup"><span data-stu-id="73fc7-110">If you have already created the **Blogging** database as part of another tutorial, skip these steps.</span></span>

* <span data-ttu-id="73fc7-111">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73fc7-111">Open Visual Studio</span></span>

* <span data-ttu-id="73fc7-112">[工具] > [連線到資料庫...]</span><span class="sxs-lookup"><span data-stu-id="73fc7-112">**Tools > Connect to Database...**</span></span>

* <span data-ttu-id="73fc7-113">選取 [Microsoft SQL Server]，並按一下 [繼續]</span><span class="sxs-lookup"><span data-stu-id="73fc7-113">Select **Microsoft SQL Server** and click **Continue**</span></span>

* <span data-ttu-id="73fc7-114">輸入 **(localdb)\mssqllocaldb** 作為**伺服器名稱**</span><span class="sxs-lookup"><span data-stu-id="73fc7-114">Enter **(localdb)\mssqllocaldb** as the **Server Name**</span></span>

* <span data-ttu-id="73fc7-115">輸入 **master** 作為**資料庫名稱**，並按一下 [確定]</span><span class="sxs-lookup"><span data-stu-id="73fc7-115">Enter **master** as the **Database Name** and click **OK**</span></span>

* <span data-ttu-id="73fc7-116">master 資料庫現在會顯示在 [伺服器總管] 中的 [資料連線] 下</span><span class="sxs-lookup"><span data-stu-id="73fc7-116">The master database is now displayed under **Data Connections** in **Server Explorer**</span></span>

* <span data-ttu-id="73fc7-117">以滑鼠右鍵按一下 [伺服器總管] 中的資料庫，並選取 [新增查詢]</span><span class="sxs-lookup"><span data-stu-id="73fc7-117">Right-click on the database in **Server Explorer** and select **New Query**</span></span>

* <span data-ttu-id="73fc7-118">將以下列出的指令碼，複製到查詢編輯器中</span><span class="sxs-lookup"><span data-stu-id="73fc7-118">Copy the script listed below into the query editor</span></span>

* <span data-ttu-id="73fc7-119">以滑鼠右鍵按一下查詢編輯器，然後選取 [執行]</span><span class="sxs-lookup"><span data-stu-id="73fc7-119">Right-click on the query editor and select **Execute**</span></span>

[!code-sql[Main](../_shared/create-blogging-database-script.sql)]

## <a name="create-a-new-project"></a><span data-ttu-id="73fc7-120">建立新專案</span><span class="sxs-lookup"><span data-stu-id="73fc7-120">Create a new project</span></span>

* <span data-ttu-id="73fc7-121">開啟 Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="73fc7-121">Open Visual Studio 2017</span></span>

* <span data-ttu-id="73fc7-122">[檔案] > [新增] > [專案...]</span><span class="sxs-lookup"><span data-stu-id="73fc7-122">**File > New > Project...**</span></span>

* <span data-ttu-id="73fc7-123">從左側功能表中，選取 **[已安裝] > [Visual C#] > [Windows Desktop]**</span><span class="sxs-lookup"><span data-stu-id="73fc7-123">From the left menu select **Installed > Visual C# > Windows Desktop**</span></span>

* <span data-ttu-id="73fc7-124">選取 [主控台應用程式 (.NET Framework)] 專案範本</span><span class="sxs-lookup"><span data-stu-id="73fc7-124">Select the **Console App (.NET Framework)** project template</span></span>

* <span data-ttu-id="73fc7-125">請確定專案的目標為 **.NET Framework 4.6.1** 或更新版本</span><span class="sxs-lookup"><span data-stu-id="73fc7-125">Make sure that the project targets **.NET Framework 4.6.1** or later</span></span>

* <span data-ttu-id="73fc7-126">將專案命名為 *ConsoleApp.ExistingDb*，然後按一下 [確定]</span><span class="sxs-lookup"><span data-stu-id="73fc7-126">Name the project *ConsoleApp.ExistingDb* and click **OK**</span></span>

## <a name="install-entity-framework"></a><span data-ttu-id="73fc7-127">安裝 Entity Framework</span><span class="sxs-lookup"><span data-stu-id="73fc7-127">Install Entity Framework</span></span>

<span data-ttu-id="73fc7-128">若要使用 EF Core，請針對您要作為目標的資料庫提供者來安裝套件。</span><span class="sxs-lookup"><span data-stu-id="73fc7-128">To use EF Core, install the package for the database provider(s) you want to target.</span></span> <span data-ttu-id="73fc7-129">本教學課程使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="73fc7-129">This tutorial uses SQL Server.</span></span> <span data-ttu-id="73fc7-130">如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="73fc7-130">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="73fc7-131">[工具] > [NuGet 套件管理員] > [套件管理員主控台]</span><span class="sxs-lookup"><span data-stu-id="73fc7-131">**Tools > NuGet Package Manager > Package Manager Console**</span></span>

* <span data-ttu-id="73fc7-132">執行 `Install-Package Microsoft.EntityFrameworkCore.SqlServer`</span><span class="sxs-lookup"><span data-stu-id="73fc7-132">Run `Install-Package Microsoft.EntityFrameworkCore.SqlServer`</span></span>

<span data-ttu-id="73fc7-133">在下一個步驟中，您會用到一些 Entity Framework Tools 對資料庫進行還原工程。</span><span class="sxs-lookup"><span data-stu-id="73fc7-133">In the next step, you use some Entity Framework Tools to reverse engineer the database.</span></span> <span data-ttu-id="73fc7-134">因此，也請安裝工具套件。</span><span class="sxs-lookup"><span data-stu-id="73fc7-134">So install the tools package as well.</span></span>

* <span data-ttu-id="73fc7-135">執行 `Install-Package Microsoft.EntityFrameworkCore.Tools`</span><span class="sxs-lookup"><span data-stu-id="73fc7-135">Run `Install-Package Microsoft.EntityFrameworkCore.Tools`</span></span>

## <a name="reverse-engineer-the-model"></a><span data-ttu-id="73fc7-136">對模型進行還原工程</span><span class="sxs-lookup"><span data-stu-id="73fc7-136">Reverse engineer the model</span></span>

<span data-ttu-id="73fc7-137">現在就可以根據您現有的資料庫來建立 EF 模型。</span><span class="sxs-lookup"><span data-stu-id="73fc7-137">Now it's time to create the EF model based on an existing database.</span></span>

* <span data-ttu-id="73fc7-138">[工具] –> [NuGet 套件管理員] –> [套件管理員主控台]</span><span class="sxs-lookup"><span data-stu-id="73fc7-138">**Tools –> NuGet Package Manager –> Package Manager Console**</span></span>

* <span data-ttu-id="73fc7-139">執行下列命令，以便從現有的資料庫來建立模型</span><span class="sxs-lookup"><span data-stu-id="73fc7-139">Run the following command to create a model from the existing database</span></span>

  ``` powershell
  Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
  ```

> [!TIP]  
> <span data-ttu-id="73fc7-140">您可以將 `-Tables` 引數新增至上述命令，以指定要為哪些資料表產生實體。</span><span class="sxs-lookup"><span data-stu-id="73fc7-140">You can specify the tables to generate entities for by adding the `-Tables` argument to the command above.</span></span> <span data-ttu-id="73fc7-141">例如，`-Tables Blog,Post`。</span><span class="sxs-lookup"><span data-stu-id="73fc7-141">For example, `-Tables Blog,Post`.</span></span>

<span data-ttu-id="73fc7-142">還原工程處理序會根據現有資料庫的結構描述，建立實體類別 (`Blog` 和 `Post`) 與衍生的內容 (`BloggingContext`)。</span><span class="sxs-lookup"><span data-stu-id="73fc7-142">The reverse engineer process created entity classes (`Blog` and `Post`) and a derived context (`BloggingContext`) based on the schema of the existing database.</span></span>

<span data-ttu-id="73fc7-143">實體類別是簡單的 C# 物件，代表您要查詢和儲存的資料。</span><span class="sxs-lookup"><span data-stu-id="73fc7-143">The entity classes are simple C# objects that represent the data you will be querying and saving.</span></span> <span data-ttu-id="73fc7-144">以下為 `Blog` 與 `Post` 實體類別：</span><span class="sxs-lookup"><span data-stu-id="73fc7-144">Here are the `Blog` and `Post` entity classes:</span></span>

 [!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/Blog.cs)]

[!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/Post.cs)]

> [!TIP]  
> <span data-ttu-id="73fc7-145">若要啟用延遲載入，可以設定瀏覽屬性 `virtual` (Blog.Post 與 Post.Blog)。</span><span class="sxs-lookup"><span data-stu-id="73fc7-145">To enable lazy loading, you can make navigation properties `virtual` (Blog.Post and Post.Blog).</span></span>

<span data-ttu-id="73fc7-146">該內容代表具備資料庫的工作階段。</span><span class="sxs-lookup"><span data-stu-id="73fc7-146">The context represents a session with the database.</span></span> <span data-ttu-id="73fc7-147">其備有多種您可用於查詢及儲存實體類別執行個體的方法。</span><span class="sxs-lookup"><span data-stu-id="73fc7-147">It has methods that you can use to query and save instances of the entity classes.</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/BloggingContext.cs)]

## <a name="use-the-model"></a><span data-ttu-id="73fc7-148">使用模型</span><span class="sxs-lookup"><span data-stu-id="73fc7-148">Use the model</span></span>

<span data-ttu-id="73fc7-149">您現已可使用模型來執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="73fc7-149">You can now use the model to perform data access.</span></span>

* <span data-ttu-id="73fc7-150">開啟 *Program.cs*</span><span class="sxs-lookup"><span data-stu-id="73fc7-150">Open *Program.cs*</span></span>

* <span data-ttu-id="73fc7-151">以下列程式碼來取代檔案的內容</span><span class="sxs-lookup"><span data-stu-id="73fc7-151">Replace the contents of the file with the following code</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/Program.cs)] 

* <span data-ttu-id="73fc7-152">偵錯 > 啟動但不偵錯</span><span class="sxs-lookup"><span data-stu-id="73fc7-152">Debug > Start Without Debugging</span></span>

  <span data-ttu-id="73fc7-153">您會看到有一個部落格儲存至資料庫，然後所有部落格的詳細資料皆會列印至主控台。</span><span class="sxs-lookup"><span data-stu-id="73fc7-153">You see that one blog is saved to the database and then the details of all blogs are printed to the console.</span></span>

  ![影像](_static/output-existing-db.png)

## <a name="next-steps"></a><span data-ttu-id="73fc7-155">後續步驟</span><span class="sxs-lookup"><span data-stu-id="73fc7-155">Next steps</span></span>

<span data-ttu-id="73fc7-156">如需如何建立內容和實體類別的詳細資訊，請參閱下列文章：</span><span class="sxs-lookup"><span data-stu-id="73fc7-156">For more information about how to scaffold a context and entity classes, see the following articles:</span></span>
* [<span data-ttu-id="73fc7-157">Entity Framework Core 工具參考 - .NET CLI</span><span class="sxs-lookup"><span data-stu-id="73fc7-157">Entity Framework Core tools reference - .NET CLI</span></span>](xref:core/miscellaneous/cli/dotnet#dotnet-ef-dbcontext-scaffold)
* [<span data-ttu-id="73fc7-158">Entity Framework Core 工具參考 - 套件管理員主控台</span><span class="sxs-lookup"><span data-stu-id="73fc7-158">Entity Framework Core tools reference - Package Manager Console</span></span>](xref:core/miscellaneous/cli/powershell#scaffold-dbcontext)