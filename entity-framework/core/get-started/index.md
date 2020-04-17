---
title: 使用者入門 - EF Core
author: rick-anderson
ms.date: 09/17/2019
ms.assetid: 3c88427c-20c6-42ec-a736-22d3eccd5071
uid: core/get-started/index
ms.openlocfilehash: 7ace80bf326395d3b68f3e745100cd45356d7973
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434093"
---
# <a name="getting-started-with-ef-core"></a><span data-ttu-id="46d7d-102">開始使用 EF Core</span><span class="sxs-lookup"><span data-stu-id="46d7d-102">Getting Started with EF Core</span></span>

<span data-ttu-id="46d7d-103">在此教學課程中，您會建置 .NET Core 主控台應用程式，它會使用 Entity Framework Core 對 SQLite 資料庫執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="46d7d-103">In this tutorial, you create a .NET Core console app that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="46d7d-104">若要遵循本教學課程，您可以在 Windows 上使用 Visual Studio 或在 Windows、macOS 或 Linux 上使用 .NET Core CLI。</span><span class="sxs-lookup"><span data-stu-id="46d7d-104">You can follow the tutorial by using Visual Studio on Windows, or by using the .NET Core CLI on Windows, macOS, or Linux.</span></span>

<span data-ttu-id="46d7d-105">[在 GitHub 上檢視此文章的範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted)。</span><span class="sxs-lookup"><span data-stu-id="46d7d-105">[View this article's sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="46d7d-106">必要條件</span><span class="sxs-lookup"><span data-stu-id="46d7d-106">Prerequisites</span></span>

<span data-ttu-id="46d7d-107">安裝下列軟體：</span><span class="sxs-lookup"><span data-stu-id="46d7d-107">Install the following software:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="46d7d-108">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="46d7d-108">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="46d7d-109">[.NET Core SDK](https://www.microsoft.com/net/download/core)。</span><span class="sxs-lookup"><span data-stu-id="46d7d-109">[.NET Core SDK](https://www.microsoft.com/net/download/core).</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="46d7d-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d7d-110">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="46d7d-111">[Visual Studio 2019 16.3 版或更新版本](https://www.visualstudio.com/downloads/)與此工作負載：</span><span class="sxs-lookup"><span data-stu-id="46d7d-111">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this  workload:</span></span>
  * <span data-ttu-id="46d7d-112">**.NET Core 跨平台開發** (位在 [其他工具組]  下)</span><span class="sxs-lookup"><span data-stu-id="46d7d-112">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>

---

## <a name="create-a-new-project"></a><span data-ttu-id="46d7d-113">建立新專案</span><span class="sxs-lookup"><span data-stu-id="46d7d-113">Create a new project</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="46d7d-114">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="46d7d-114">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new console -o EFGetStarted
cd EFGetStarted
```

### <a name="visual-studio"></a>[<span data-ttu-id="46d7d-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d7d-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="46d7d-116">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d7d-116">Open Visual Studio</span></span>
* <span data-ttu-id="46d7d-117">按一下 [建立新專案] </span><span class="sxs-lookup"><span data-stu-id="46d7d-117">Click **Create a new project**</span></span>
* <span data-ttu-id="46d7d-118">選取 [主控台應用程式 (.NET Core)]  以及 [C#]  標籤，並按一下 [下一步] </span><span class="sxs-lookup"><span data-stu-id="46d7d-118">Select **Console App (.NET Core)** with the **C#** tag and click **Next**</span></span>
* <span data-ttu-id="46d7d-119">輸入 **EFGetStarted** 作為名稱，然後按一下 [建立] </span><span class="sxs-lookup"><span data-stu-id="46d7d-119">Enter **EFGetStarted** for the name and click **Create**</span></span>

---

## <a name="install-entity-framework-core"></a><span data-ttu-id="46d7d-120">安裝 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="46d7d-120">Install Entity Framework Core</span></span>

<span data-ttu-id="46d7d-121">若要安裝 EF Core，請為希望作為目標的 EF Core 資料庫提供者，安裝此套件。</span><span class="sxs-lookup"><span data-stu-id="46d7d-121">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="46d7d-122">本教學課程會使用 SQLite，因為它可以在 .NET Core 支援的所有平台上執行。</span><span class="sxs-lookup"><span data-stu-id="46d7d-122">This tutorial uses SQLite because it runs on all platforms that .NET Core supports.</span></span> <span data-ttu-id="46d7d-123">如需可用的提供者清單，請參閱[資料庫提供者](../providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="46d7d-123">For a list of available providers, see [Database Providers](../providers/index.md).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="46d7d-124">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="46d7d-124">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="46d7d-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d7d-125">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="46d7d-126">[工具] > [NuGet 套件管理員] > [套件管理員主控台] </span><span class="sxs-lookup"><span data-stu-id="46d7d-126">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="46d7d-127">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="46d7d-127">Run the following commands:</span></span>

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

<span data-ttu-id="46d7d-128">提示：您也可以用右鍵按一下專案並選取 [管理 NuGet 套件]  來安裝套件</span><span class="sxs-lookup"><span data-stu-id="46d7d-128">Tip: You can also install packages by right-clicking on the project and selecting **Manage NuGet Packages**</span></span>

---

## <a name="create-the-model"></a><span data-ttu-id="46d7d-129">建立模型</span><span class="sxs-lookup"><span data-stu-id="46d7d-129">Create the model</span></span>

<span data-ttu-id="46d7d-130">定義組成模型的內容類別與實體類別。</span><span class="sxs-lookup"><span data-stu-id="46d7d-130">Define a context class and entity classes that make up the model.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="46d7d-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="46d7d-131">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="46d7d-132">在專案目錄中，使用下列程式碼建立 **Model.cs**</span><span class="sxs-lookup"><span data-stu-id="46d7d-132">In the project directory, create **Model.cs** with the following code</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="46d7d-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d7d-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="46d7d-134">以滑鼠右鍵按一下專案，然後選取 [新增] > [類別] </span><span class="sxs-lookup"><span data-stu-id="46d7d-134">Right-click on the project and select **Add > Class**</span></span>
* <span data-ttu-id="46d7d-135">輸入 **Model.cs** 作為名稱，然後按一下 [新增] </span><span class="sxs-lookup"><span data-stu-id="46d7d-135">Enter **Model.cs** as the name and click **Add**</span></span>
* <span data-ttu-id="46d7d-136">以下列程式碼來取代檔案的內容</span><span class="sxs-lookup"><span data-stu-id="46d7d-136">Replace the contents of the file with the following code</span></span>

---

[!code-csharp[Main](../../../samples/core/GetStarted/Model.cs)]

<span data-ttu-id="46d7d-137">EF Core 也可以對現有資料庫中的模型進行[反向工程](../managing-schemas/scaffolding.md)。</span><span class="sxs-lookup"><span data-stu-id="46d7d-137">EF Core can also [reverse engineer](../managing-schemas/scaffolding.md) a model from an existing database.</span></span>

<span data-ttu-id="46d7d-138">提示：為了清晰易懂，此應用程式刻意保持簡潔。</span><span class="sxs-lookup"><span data-stu-id="46d7d-138">Tip: This application intentionally keeps things simple for clarity.</span></span> <span data-ttu-id="46d7d-139">[連接字串](../miscellaneous/connection-strings.md)不應儲存在實際執行應用程式的程式碼中。</span><span class="sxs-lookup"><span data-stu-id="46d7d-139">[Connection strings](../miscellaneous/connection-strings.md) should not be stored in the code for production applications.</span></span> <span data-ttu-id="46d7d-140">也建議您將每個 C# 類別分割成個別的檔案。</span><span class="sxs-lookup"><span data-stu-id="46d7d-140">You may also want to split each C# class into it's own file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="46d7d-141">建立資料庫</span><span class="sxs-lookup"><span data-stu-id="46d7d-141">Create the database</span></span>

<span data-ttu-id="46d7d-142">下列步驟會使用[移轉](xref:core/managing-schemas/migrations/index)來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="46d7d-142">The following steps use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="46d7d-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="46d7d-143">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="46d7d-144">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="46d7d-144">Run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  <span data-ttu-id="46d7d-145">這會安裝 [dotnet ef](../miscellaneous/cli/dotnet.md) 以及在專案上執行命令所需的設計套件。</span><span class="sxs-lookup"><span data-stu-id="46d7d-145">This installs [dotnet ef](../miscellaneous/cli/dotnet.md) and the design package which is required to run the command on a project.</span></span> <span data-ttu-id="46d7d-146">`migrations` 命令會建立移轉的 Scaffolding，以針對模型建立一組初始資料表。</span><span class="sxs-lookup"><span data-stu-id="46d7d-146">The `migrations` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="46d7d-147">`database update` 命令會建立資料庫，並對資料庫套用新的移轉。</span><span class="sxs-lookup"><span data-stu-id="46d7d-147">The `database update` command creates the database and applies the new migration to it.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="46d7d-148">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d7d-148">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="46d7d-149">在**套件管理器主控台**中執行下列命令</span><span class="sxs-lookup"><span data-stu-id="46d7d-149">Run the following commands in **Package Manager Console**</span></span>

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  <span data-ttu-id="46d7d-150">這會安裝[適用於 EF Core 的 PMC 工具](../miscellaneous/cli/powershell.md)。</span><span class="sxs-lookup"><span data-stu-id="46d7d-150">This installs the [PMC tools for EF Core](../miscellaneous/cli/powershell.md).</span></span> <span data-ttu-id="46d7d-151">`Add-Migration` 命令會建立移轉的 Scaffolding，以針對模型建立一組初始資料表。</span><span class="sxs-lookup"><span data-stu-id="46d7d-151">The `Add-Migration` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="46d7d-152">`Update-Database` 命令會建立資料庫，並對資料庫套用新的移轉。</span><span class="sxs-lookup"><span data-stu-id="46d7d-152">The `Update-Database` command creates the database and applies the new migration to it.</span></span>

---

## <a name="create-read-update--delete"></a><span data-ttu-id="46d7d-153">建立、讀取、更新及刪除</span><span class="sxs-lookup"><span data-stu-id="46d7d-153">Create, read, update & delete</span></span>

* <span data-ttu-id="46d7d-154">開啟 *Program.cs* 並使用下列程式碼來取代內容：</span><span class="sxs-lookup"><span data-stu-id="46d7d-154">Open *Program.cs* and replace the contents with the following code:</span></span>

  [!code-csharp[Main](../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a><span data-ttu-id="46d7d-155">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="46d7d-155">Run the app</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="46d7d-156">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="46d7d-156">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet run
```

### <a name="visual-studio"></a>[<span data-ttu-id="46d7d-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d7d-157">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="46d7d-158">執行 .NET Core 主控台應用程式時，Visual Studio 會使用不一致的工作目錄。</span><span class="sxs-lookup"><span data-stu-id="46d7d-158">Visual Studio uses an inconsistent working directory when running .NET Core console apps.</span></span> <span data-ttu-id="46d7d-159">(請參閱 [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)) 這會導致擲回例外狀況：「沒有這個表格：  部落格」。</span><span class="sxs-lookup"><span data-stu-id="46d7d-159">(see [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)) This results in an exception being thrown: *no such table: Blogs*.</span></span> <span data-ttu-id="46d7d-160">更新工作目錄：</span><span class="sxs-lookup"><span data-stu-id="46d7d-160">To update the working directory:</span></span>

* <span data-ttu-id="46d7d-161">以滑鼠右鍵按一下專案，然後選取 [編輯專案檔] </span><span class="sxs-lookup"><span data-stu-id="46d7d-161">Right-click on the project and select **Edit Project File**</span></span>
* <span data-ttu-id="46d7d-162">在 *TargetFramework* 屬性下方新增下列內容：</span><span class="sxs-lookup"><span data-stu-id="46d7d-162">Just below the *TargetFramework* property, add the following:</span></span>

  ``` XML
  <StartWorkingDirectory>$(MSBuildProjectDirectory)</StartWorkingDirectory>
  ```

* <span data-ttu-id="46d7d-163">儲存檔案</span><span class="sxs-lookup"><span data-stu-id="46d7d-163">Save the file</span></span>

<span data-ttu-id="46d7d-164">現在您可以執行應用程式：</span><span class="sxs-lookup"><span data-stu-id="46d7d-164">Now you can run the app:</span></span>

* <span data-ttu-id="46d7d-165">[偵錯] > [啟動但不偵錯] </span><span class="sxs-lookup"><span data-stu-id="46d7d-165">**Debug > Start Without Debugging**</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="46d7d-166">後續步驟</span><span class="sxs-lookup"><span data-stu-id="46d7d-166">Next steps</span></span>

* <span data-ttu-id="46d7d-167">遵循 [ASP.NET Core 教學課程](/aspnet/core/data/ef-rp/intro)以在 Web 應用程式中使用 EF Core</span><span class="sxs-lookup"><span data-stu-id="46d7d-167">Follow the [ASP.NET Core Tutorial](/aspnet/core/data/ef-rp/intro) to use EF Core in a web app</span></span>
* <span data-ttu-id="46d7d-168">深入了解 [LINQ 查詢運算式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="46d7d-168">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="46d7d-169">[設定模型](xref:core/modeling/index)以指定如[必要](xref:core/modeling/entity-properties#required-and-optional-properties)和[最大長度](xref:core/modeling/entity-properties#maximum-length)之類的內容</span><span class="sxs-lookup"><span data-stu-id="46d7d-169">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
* <span data-ttu-id="46d7d-170">變更模型後，請使用[移轉](xref:core/managing-schemas/migrations/index)來更新資料庫結構描述</span><span class="sxs-lookup"><span data-stu-id="46d7d-170">Use [Migrations](xref:core/managing-schemas/migrations/index) to update the database schema after changing your model</span></span>
