---
title: 使用者入門 - EF Core
description: Entity Framework Core 的使用者入門教學課程
author: rick-anderson
ms.date: 09/17/2019
uid: core/get-started/overview/first-app
ms.openlocfilehash: 560a6f293e32f4e1f75c13367291c4e31e83b157
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024338"
---
# <a name="getting-started-with-ef-core"></a><span data-ttu-id="0fc93-103">開始使用 EF Core</span><span class="sxs-lookup"><span data-stu-id="0fc93-103">Getting Started with EF Core</span></span>

<span data-ttu-id="0fc93-104">在此教學課程中，您會建置 .NET Core 主控台應用程式，它會使用 Entity Framework Core 對 SQLite 資料庫執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="0fc93-104">In this tutorial, you create a .NET Core console app that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="0fc93-105">若要遵循本教學課程，您可以在 Windows 上使用 Visual Studio 或在 Windows、macOS 或 Linux 上使用 .NET Core CLI。</span><span class="sxs-lookup"><span data-stu-id="0fc93-105">You can follow the tutorial by using Visual Studio on Windows, or by using the .NET Core CLI on Windows, macOS, or Linux.</span></span>

<span data-ttu-id="0fc93-106">[在 GitHub 上檢視此文章的範例](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/GetStarted)。</span><span class="sxs-lookup"><span data-stu-id="0fc93-106">[View this article's sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/GetStarted).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0fc93-107">必要條件</span><span class="sxs-lookup"><span data-stu-id="0fc93-107">Prerequisites</span></span>

<span data-ttu-id="0fc93-108">安裝下列軟體：</span><span class="sxs-lookup"><span data-stu-id="0fc93-108">Install the following software:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="0fc93-109">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0fc93-109">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="0fc93-110">[.NET Core SDK](https://www.microsoft.com/net/download/core)。</span><span class="sxs-lookup"><span data-stu-id="0fc93-110">[.NET Core SDK](https://www.microsoft.com/net/download/core).</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="0fc93-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0fc93-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0fc93-112">[Visual Studio 2019 16.3 版或更新版本](https://www.visualstudio.com/downloads/)與此工作負載：</span><span class="sxs-lookup"><span data-stu-id="0fc93-112">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this  workload:</span></span>
  * <span data-ttu-id="0fc93-113">**.NET Core 跨平台開發** (位在 [其他工具組]  下)</span><span class="sxs-lookup"><span data-stu-id="0fc93-113">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>

---

## <a name="create-a-new-project"></a><span data-ttu-id="0fc93-114">建立新專案</span><span class="sxs-lookup"><span data-stu-id="0fc93-114">Create a new project</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="0fc93-115">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0fc93-115">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new console -o EFGetStarted
cd EFGetStarted
```

### <a name="visual-studio"></a>[<span data-ttu-id="0fc93-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0fc93-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0fc93-117">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0fc93-117">Open Visual Studio</span></span>
* <span data-ttu-id="0fc93-118">按一下 [建立新專案] </span><span class="sxs-lookup"><span data-stu-id="0fc93-118">Click **Create a new project**</span></span>
* <span data-ttu-id="0fc93-119">選取 [主控台應用程式 (.NET Core)]  以及 [C#]  標籤，並按一下 [下一步] </span><span class="sxs-lookup"><span data-stu-id="0fc93-119">Select **Console App (.NET Core)** with the **C#** tag and click **Next**</span></span>
* <span data-ttu-id="0fc93-120">輸入 **EFGetStarted** 作為名稱，然後按一下 [建立] </span><span class="sxs-lookup"><span data-stu-id="0fc93-120">Enter **EFGetStarted** for the name and click **Create**</span></span>

---

## <a name="install-entity-framework-core"></a><span data-ttu-id="0fc93-121">安裝 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="0fc93-121">Install Entity Framework Core</span></span>

<span data-ttu-id="0fc93-122">若要安裝 EF Core，請為希望作為目標的 EF Core 資料庫提供者，安裝此套件。</span><span class="sxs-lookup"><span data-stu-id="0fc93-122">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="0fc93-123">本教學課程會使用 SQLite，因為它可以在 .NET Core 支援的所有平台上執行。</span><span class="sxs-lookup"><span data-stu-id="0fc93-123">This tutorial uses SQLite because it runs on all platforms that .NET Core supports.</span></span> <span data-ttu-id="0fc93-124">如需可用的提供者清單，請參閱[資料庫提供者](xref:core/providers/index)。</span><span class="sxs-lookup"><span data-stu-id="0fc93-124">For a list of available providers, see [Database Providers](xref:core/providers/index).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="0fc93-125">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0fc93-125">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="0fc93-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0fc93-126">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0fc93-127">[工具] > [NuGet 套件管理員] > [套件管理員主控台] </span><span class="sxs-lookup"><span data-stu-id="0fc93-127">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="0fc93-128">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="0fc93-128">Run the following commands:</span></span>

  ```powershell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

<span data-ttu-id="0fc93-129">提示：您也可以用右鍵按一下專案並選取 [管理 NuGet 套件]  來安裝套件</span><span class="sxs-lookup"><span data-stu-id="0fc93-129">Tip: You can also install packages by right-clicking on the project and selecting **Manage NuGet Packages**</span></span>

---

## <a name="create-the-model"></a><span data-ttu-id="0fc93-130">建立模型</span><span class="sxs-lookup"><span data-stu-id="0fc93-130">Create the model</span></span>

<span data-ttu-id="0fc93-131">定義組成模型的內容類別與實體類別。</span><span class="sxs-lookup"><span data-stu-id="0fc93-131">Define a context class and entity classes that make up the model.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="0fc93-132">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0fc93-132">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="0fc93-133">在專案目錄中，使用下列程式碼建立 **Model.cs**</span><span class="sxs-lookup"><span data-stu-id="0fc93-133">In the project directory, create **Model.cs** with the following code</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="0fc93-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0fc93-134">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0fc93-135">以滑鼠右鍵按一下專案，然後選取 [新增] > [類別] </span><span class="sxs-lookup"><span data-stu-id="0fc93-135">Right-click on the project and select **Add > Class**</span></span>
* <span data-ttu-id="0fc93-136">輸入 **Model.cs** 作為名稱，然後按一下 [新增] </span><span class="sxs-lookup"><span data-stu-id="0fc93-136">Enter **Model.cs** as the name and click **Add**</span></span>
* <span data-ttu-id="0fc93-137">以下列程式碼來取代檔案的內容</span><span class="sxs-lookup"><span data-stu-id="0fc93-137">Replace the contents of the file with the following code</span></span>

---

[!code-csharp[Main](../../../../samples/core/GetStarted/Model.cs)]

<span data-ttu-id="0fc93-138">EF Core 也可以對現有資料庫中的模型進行[反向工程](xref:core/managing-schemas/scaffolding)。</span><span class="sxs-lookup"><span data-stu-id="0fc93-138">EF Core can also [reverse engineer](xref:core/managing-schemas/scaffolding) a model from an existing database.</span></span>

<span data-ttu-id="0fc93-139">提示：為了清晰易懂，此應用程式刻意保持簡潔。</span><span class="sxs-lookup"><span data-stu-id="0fc93-139">Tip: This application intentionally keeps things simple for clarity.</span></span> <span data-ttu-id="0fc93-140">[連接字串](xref:core/miscellaneous/connection-strings)不應儲存在實際執行應用程式的程式碼中。</span><span class="sxs-lookup"><span data-stu-id="0fc93-140">[Connection strings](xref:core/miscellaneous/connection-strings) should not be stored in the code for production applications.</span></span> <span data-ttu-id="0fc93-141">您也可以將每個 C# 類別分割到其自有檔案。</span><span class="sxs-lookup"><span data-stu-id="0fc93-141">You may also want to split each C# class into its own file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="0fc93-142">建立資料庫</span><span class="sxs-lookup"><span data-stu-id="0fc93-142">Create the database</span></span>

<span data-ttu-id="0fc93-143">下列步驟會使用[移轉](xref:core/managing-schemas/migrations/index)來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="0fc93-143">The following steps use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="0fc93-144">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0fc93-144">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="0fc93-145">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="0fc93-145">Run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  <span data-ttu-id="0fc93-146">這會安裝 [dotnet ef](xref:core/cli/dotnet) 以及在專案上執行命令所需的設計套件。</span><span class="sxs-lookup"><span data-stu-id="0fc93-146">This installs [dotnet ef](xref:core/cli/dotnet) and the design package which is required to run the command on a project.</span></span> <span data-ttu-id="0fc93-147">`migrations` 命令會建立移轉的 Scaffolding，以針對模型建立一組初始資料表。</span><span class="sxs-lookup"><span data-stu-id="0fc93-147">The `migrations` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="0fc93-148">`database update` 命令會建立資料庫，並對資料庫套用新的移轉。</span><span class="sxs-lookup"><span data-stu-id="0fc93-148">The `database update` command creates the database and applies the new migration to it.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="0fc93-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0fc93-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0fc93-150">在 **套件管理員主控台 (PMC)** 中執行下列命令</span><span class="sxs-lookup"><span data-stu-id="0fc93-150">Run the following commands in **Package Manager Console (PMC)**</span></span>

  ```powershell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  <span data-ttu-id="0fc93-151">這會安裝[適用於 EF Core 的 PMC 工具](xref:core/cli/powershell)。</span><span class="sxs-lookup"><span data-stu-id="0fc93-151">This installs the [PMC tools for EF Core](xref:core/cli/powershell).</span></span> <span data-ttu-id="0fc93-152">`Add-Migration` 命令會建立移轉的 Scaffolding，以針對模型建立一組初始資料表。</span><span class="sxs-lookup"><span data-stu-id="0fc93-152">The `Add-Migration` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="0fc93-153">`Update-Database` 命令會建立資料庫，並對資料庫套用新的移轉。</span><span class="sxs-lookup"><span data-stu-id="0fc93-153">The `Update-Database` command creates the database and applies the new migration to it.</span></span>

---

## <a name="create-read-update--delete"></a><span data-ttu-id="0fc93-154">建立、讀取、更新及刪除</span><span class="sxs-lookup"><span data-stu-id="0fc93-154">Create, read, update & delete</span></span>

* <span data-ttu-id="0fc93-155">開啟 *Program.cs* 並使用下列程式碼來取代內容：</span><span class="sxs-lookup"><span data-stu-id="0fc93-155">Open *Program.cs* and replace the contents with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a><span data-ttu-id="0fc93-156">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="0fc93-156">Run the app</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="0fc93-157">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0fc93-157">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet run
```

### <a name="visual-studio"></a>[<span data-ttu-id="0fc93-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0fc93-158">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0fc93-159">[偵錯] > [啟動但不偵錯] </span><span class="sxs-lookup"><span data-stu-id="0fc93-159">**Debug > Start Without Debugging**</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="0fc93-160">後續步驟</span><span class="sxs-lookup"><span data-stu-id="0fc93-160">Next steps</span></span>

* <span data-ttu-id="0fc93-161">遵循 [ASP.NET Core 教學課程](/aspnet/core/data/ef-rp/intro)以在 Web 應用程式中使用 EF Core</span><span class="sxs-lookup"><span data-stu-id="0fc93-161">Follow the [ASP.NET Core Tutorial](/aspnet/core/data/ef-rp/intro) to use EF Core in a web app</span></span>
* <span data-ttu-id="0fc93-162">深入了解 [LINQ 查詢運算式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="0fc93-162">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="0fc93-163">[設定模型](xref:core/modeling/index)以指定如[必要](xref:core/modeling/entity-properties#required-and-optional-properties)和[最大長度](xref:core/modeling/entity-properties#maximum-length)之類的內容</span><span class="sxs-lookup"><span data-stu-id="0fc93-163">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
* <span data-ttu-id="0fc93-164">變更模型後，請使用[移轉](xref:core/managing-schemas/migrations/index)來更新資料庫結構描述</span><span class="sxs-lookup"><span data-stu-id="0fc93-164">Use [Migrations](xref:core/managing-schemas/migrations/index) to update the database schema after changing your model</span></span>
