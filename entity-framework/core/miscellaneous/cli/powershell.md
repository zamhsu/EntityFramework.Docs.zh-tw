---
title: EF Core 工具參考 (封裝管理員主控台) -EF Core
description: Entity Framework Core Visual Studio 封裝管理員主控台的參考指南
author: bricelam
ms.date: 10/13/2020
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: 3a9599288d74013bf4da910c64bc858539c0c32c
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062382"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a><span data-ttu-id="b3d1a-103">Visual Studio 中的 Entity Framework Core 工具參考-封裝管理員主控台</span><span class="sxs-lookup"><span data-stu-id="b3d1a-103">Entity Framework Core tools reference - Package Manager Console in Visual Studio</span></span>

<span data-ttu-id="b3d1a-104">封裝管理員主控台 (PMC) 工具，以 Entity Framework Core 執行設計階段開發工作。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-104">The Package Manager Console (PMC) tools for Entity Framework Core perform design-time development tasks.</span></span> <span data-ttu-id="b3d1a-105">例如，他們會建立 [遷移](/aspnet/core/data/ef-mvc/migrations)、套用遷移，並根據現有的資料庫產生模型的程式碼。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-105">For example, they create [migrations](/aspnet/core/data/ef-mvc/migrations), apply migrations, and generate code for a model based on an existing database.</span></span> <span data-ttu-id="b3d1a-106">這些命令會使用 [封裝管理員主控台](/nuget/tools/package-manager-console)在 Visual Studio 內部執行。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-106">The commands run inside of Visual Studio using the [Package Manager Console](/nuget/tools/package-manager-console).</span></span> <span data-ttu-id="b3d1a-107">這些工具會使用 .NET Framework 和 .NET Core 專案。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-107">These tools work with both .NET Framework and .NET Core projects.</span></span>

<span data-ttu-id="b3d1a-108">如果您不是使用 Visual Studio，建議您改用 [EF Core 命令列工具](xref:core/miscellaneous/cli/dotnet) 。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-108">If you aren't using Visual Studio, we recommend the [EF Core Command-line Tools](xref:core/miscellaneous/cli/dotnet) instead.</span></span> <span data-ttu-id="b3d1a-109">.NET Core CLI 工具是跨平臺，並在命令提示字元內執行。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-109">The .NET Core CLI tools are cross-platform and run inside a command prompt.</span></span>

## <a name="installing-the-tools"></a><span data-ttu-id="b3d1a-110">安裝工具</span><span class="sxs-lookup"><span data-stu-id="b3d1a-110">Installing the tools</span></span>

<span data-ttu-id="b3d1a-111">在 **封裝管理員主控台**中執行下列命令，以安裝封裝管理員主控台工具：</span><span class="sxs-lookup"><span data-stu-id="b3d1a-111">Install the Package Manager Console tools by running the following command in **Package Manager Console**:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="b3d1a-112">在 **封裝管理員主控台**中執行下列命令，以更新工具。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-112">Update the tools by running the following command in **Package Manager Console**.</span></span>

```powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a><span data-ttu-id="b3d1a-113">驗證安裝</span><span class="sxs-lookup"><span data-stu-id="b3d1a-113">Verify the installation</span></span>

<span data-ttu-id="b3d1a-114">執行下列命令，確認是否已安裝這些工具：</span><span class="sxs-lookup"><span data-stu-id="b3d1a-114">Verify that the tools are installed by running this command:</span></span>

```powershell
Get-Help about_EntityFrameworkCore
```

<span data-ttu-id="b3d1a-115">輸出看起來會像這樣 (不會告訴您所使用的工具是哪個版本) ：</span><span class="sxs-lookup"><span data-stu-id="b3d1a-115">The output looks like this (it doesn't tell you which version of the tools you're using):</span></span>

```output

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

TOPIC
    about_EntityFrameworkCore

SHORT DESCRIPTION
    Provides information about the Entity Framework Core Package Manager Console Tools.

<A list of available commands follows, omitted here.>
```

## <a name="using-the-tools"></a><span data-ttu-id="b3d1a-116">使用工具</span><span class="sxs-lookup"><span data-stu-id="b3d1a-116">Using the tools</span></span>

<span data-ttu-id="b3d1a-117">使用工具之前：</span><span class="sxs-lookup"><span data-stu-id="b3d1a-117">Before using the tools:</span></span>

* <span data-ttu-id="b3d1a-118">瞭解目標和啟始專案之間的差異。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-118">Understand the difference between target and startup project.</span></span>
* <span data-ttu-id="b3d1a-119">瞭解如何使用 .NET Standard 類別庫的工具。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-119">Learn how to use the tools with .NET Standard class libraries.</span></span>
* <span data-ttu-id="b3d1a-120">針對 ASP.NET Core 專案，請設定環境。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-120">For ASP.NET Core projects, set the environment.</span></span>

### <a name="target-and-startup-project"></a><span data-ttu-id="b3d1a-121">目標和啟始專案</span><span class="sxs-lookup"><span data-stu-id="b3d1a-121">Target and startup project</span></span>

<span data-ttu-id="b3d1a-122">這些命令會參考 *專案* 和 *啟始專案*。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-122">The commands refer to a *project* and a *startup project*.</span></span>

* <span data-ttu-id="b3d1a-123">*專案*也稱為*目標專案*，因為這是命令新增或移除檔案的位置。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-123">The *project* is also known as the *target project* because it's where the commands add or remove files.</span></span> <span data-ttu-id="b3d1a-124">依預設，**封裝管理員主控台**中選取的**預設專案**是目標專案。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-124">By default, the **Default project** selected in **Package Manager Console** is the target project.</span></span> <span data-ttu-id="b3d1a-125">您可以使用選項，將不同的專案指定為目標專案 <nobr>`--project`</nobr> 。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-125">You can specify a different project as target project by using the <nobr>`--project`</nobr> option.</span></span>

* <span data-ttu-id="b3d1a-126">*啟始專案*是工具建立和執行的專案。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-126">The *startup project* is the one that the tools build and run.</span></span> <span data-ttu-id="b3d1a-127">這些工具必須在設計階段執行應用程式程式碼，以取得專案的相關資訊，例如資料庫連接字串和模型的設定。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-127">The tools have to execute application code at design time to get information about the project, such as the database connection string and the configuration of the model.</span></span> <span data-ttu-id="b3d1a-128">根據預設，**方案總管**中的**啟始專案**是啟始專案。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-128">By default, the **Startup Project** in **Solution Explorer** is the startup project.</span></span> <span data-ttu-id="b3d1a-129">您可以使用選項，將不同的專案指定為啟始專案 <nobr>`--startup-project`</nobr> 。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-129">You can specify a different project as startup project by using the <nobr>`--startup-project`</nobr> option.</span></span>

<span data-ttu-id="b3d1a-130">啟始專案和目標專案通常是相同的專案。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-130">The startup project and target project are often the same project.</span></span> <span data-ttu-id="b3d1a-131">一般情況下，它們是不同的專案：</span><span class="sxs-lookup"><span data-stu-id="b3d1a-131">A typical scenario where they are separate projects is when:</span></span>

* <span data-ttu-id="b3d1a-132">EF Core 內容和實體類別位於 .NET Core 類別庫中。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-132">The EF Core context and entity classes are in a .NET Core class library.</span></span>
* <span data-ttu-id="b3d1a-133">.NET Core 主控台應用程式或 web 應用程式會參考類別庫。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-133">A .NET Core console app or web app references the class library.</span></span>

<span data-ttu-id="b3d1a-134">您也可以將與 [EF Core 內容分開的類別庫中的遷移程式碼](xref:core/managing-schemas/migrations/projects)。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-134">It's also possible to [put migrations code in a class library separate from the EF Core context](xref:core/managing-schemas/migrations/projects).</span></span>

### <a name="other-target-frameworks"></a><span data-ttu-id="b3d1a-135">其他目標 framework</span><span class="sxs-lookup"><span data-stu-id="b3d1a-135">Other target frameworks</span></span>

<span data-ttu-id="b3d1a-136">封裝管理員主控台工具適用于 .NET Core 或 .NET Framework 專案。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-136">The Package Manager Console tools work with .NET Core or .NET Framework projects.</span></span> <span data-ttu-id="b3d1a-137">在 .NET Standard 類別庫中具有 EF Core 模型的應用程式可能沒有 .NET Core 或 .NET Framework 專案。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-137">Apps that have the EF Core model in a .NET Standard class library might not have a .NET Core or .NET Framework project.</span></span> <span data-ttu-id="b3d1a-138">例如，這適用于 Xamarin 和通用 Windows 平臺應用程式。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-138">For example, this is true of Xamarin and Universal Windows Platform apps.</span></span> <span data-ttu-id="b3d1a-139">在這種情況下，您可以建立 .NET Core 或 .NET Framework 主控台應用程式專案，其唯一目的是作為工具的啟始專案。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-139">In such cases, you can create a .NET Core or .NET Framework console app project whose only purpose is to act as startup project for the tools.</span></span> <span data-ttu-id="b3d1a-140">專案可以是沒有真正程式碼的虛擬專案， &mdash; 只需要提供工具的目標。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-140">The project can be a dummy project with no real code &mdash; it is only needed to provide a target for the tooling.</span></span>

<span data-ttu-id="b3d1a-141">為什麼需要虛擬專案？</span><span class="sxs-lookup"><span data-stu-id="b3d1a-141">Why is a dummy project required?</span></span> <span data-ttu-id="b3d1a-142">如先前所述，這些工具必須在設計階段執行應用程式程式碼。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-142">As mentioned earlier, the tools have to execute application code at design time.</span></span> <span data-ttu-id="b3d1a-143">若要這樣做，他們必須使用 .NET Core 或 .NET Framework 執行時間。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-143">To do that, they need to use the .NET Core or .NET Framework runtime.</span></span> <span data-ttu-id="b3d1a-144">當 EF Core 模型位於以 .NET Core 或 .NET Framework 為目標的專案中時，EF Core 工具會從專案借用執行時間。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-144">When the EF Core model is in a project that targets .NET Core or .NET Framework, the EF Core tools borrow the runtime from the project.</span></span> <span data-ttu-id="b3d1a-145">如果 EF Core 模型位於 .NET Standard 類別庫中，則無法這麼做。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-145">They can't do that if the EF Core model is in a .NET Standard class library.</span></span> <span data-ttu-id="b3d1a-146">.NET Standard 不是實際的 .NET 執行;它是一組 .NET 開發人員必須支援的 Api 的規格。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-146">The .NET Standard is not an actual .NET implementation; it's a specification of a set of APIs that .NET implementations must support.</span></span> <span data-ttu-id="b3d1a-147">因此 .NET Standard 不足以執行應用程式程式碼 EF Core 工具。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-147">Therefore .NET Standard is not sufficient for the EF Core tools to execute application code.</span></span> <span data-ttu-id="b3d1a-148">您建立用來做為啟始專案的虛擬專案提供可讓工具載入 .NET Standard 類別庫的具體目標平臺。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-148">The dummy project you create to use as startup project provides a concrete target platform into which the tools can load the .NET Standard class library.</span></span>

### <a name="aspnet-core-environment"></a><span data-ttu-id="b3d1a-149">ASP.NET Core 環境</span><span class="sxs-lookup"><span data-stu-id="b3d1a-149">ASP.NET Core environment</span></span>

<span data-ttu-id="b3d1a-150">若要指定 ASP.NET Core 專案的環境，請先設定 **env： ASPNETCORE_ENVIRONMENT** ，再執行命令。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-150">To specify the environment for ASP.NET Core projects, set **env:ASPNETCORE_ENVIRONMENT** before running commands.</span></span>

## <a name="common-parameters"></a><span data-ttu-id="b3d1a-151">一般參數</span><span class="sxs-lookup"><span data-stu-id="b3d1a-151">Common parameters</span></span>

<span data-ttu-id="b3d1a-152">下表顯示所有 EF Core 命令通用的參數：</span><span class="sxs-lookup"><span data-stu-id="b3d1a-152">The following table shows parameters that are common to all of the EF Core commands:</span></span>

| <span data-ttu-id="b3d1a-153">參數</span><span class="sxs-lookup"><span data-stu-id="b3d1a-153">Parameter</span></span>                 | <span data-ttu-id="b3d1a-154">描述</span><span class="sxs-lookup"><span data-stu-id="b3d1a-154">Description</span></span>                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b3d1a-155">-CoNtext \<String></span><span class="sxs-lookup"><span data-stu-id="b3d1a-155">-Context \<String></span></span>        | <span data-ttu-id="b3d1a-156">要使用的 `DbContext` 類別。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-156">The `DbContext` class to use.</span></span> <span data-ttu-id="b3d1a-157">僅限類別名稱或完整限定命名空間。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-157">Class name only or fully qualified with namespaces.</span></span>  <span data-ttu-id="b3d1a-158">如果省略此參數，EF Core 會尋找內容類別。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-158">If this parameter is omitted, EF Core finds the context class.</span></span> <span data-ttu-id="b3d1a-159">如果有多個內容類別，則需要此參數。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-159">If there are multiple context classes, this parameter is required.</span></span> |
| <span data-ttu-id="b3d1a-160">-Project \<String></span><span class="sxs-lookup"><span data-stu-id="b3d1a-160">-Project \<String></span></span>        | <span data-ttu-id="b3d1a-161">目標專案。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-161">The target project.</span></span> <span data-ttu-id="b3d1a-162">如果省略此參數，則會使用**封裝管理員主控台**的**預設專案**做為目標專案。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-162">If this parameter is omitted, the **Default project** for **Package Manager Console** is used as the target project.</span></span>                                                                             |
| <span data-ttu-id="b3d1a-163"><nobr>-啟始專案</nobr>\<String></span><span class="sxs-lookup"><span data-stu-id="b3d1a-163"><nobr>-StartupProject</nobr> \<String></span></span> | <span data-ttu-id="b3d1a-164">啟始專案。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-164">The startup project.</span></span> <span data-ttu-id="b3d1a-165">如果省略此參數，則會使用**方案屬性**中的**啟始專案**做為目標專案。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-165">If this parameter is omitted, the **Startup project** in **Solution properties** is used as the target project.</span></span>                                                                                 |
| <span data-ttu-id="b3d1a-166">-Args \<String></span><span class="sxs-lookup"><span data-stu-id="b3d1a-166">-Args \<String></span></span>           | <span data-ttu-id="b3d1a-167">傳遞至應用程式的引數。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-167">Arguments passed to the application.</span></span> <span data-ttu-id="b3d1a-168">在 EF Core 5.0 中新增。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-168">Added in EF Core 5.0.</span></span>                                                                                                                                                           |
| <span data-ttu-id="b3d1a-169">-Verbose</span><span class="sxs-lookup"><span data-stu-id="b3d1a-169">-Verbose</span></span>                  | <span data-ttu-id="b3d1a-170">顯示詳細資訊輸出。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-170">Show verbose output.</span></span>                                                                                                                                                                                                 |

<span data-ttu-id="b3d1a-171">若要顯示命令的說明資訊，請使用 PowerShell 的 `Get-Help` 命令。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-171">To show help information about a command, use PowerShell's `Get-Help` command.</span></span>

> [!TIP]
> <span data-ttu-id="b3d1a-172">CoNtext、Project 和啟始專案參數支援 tab 鍵展開。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-172">The Context, Project, and StartupProject parameters support tab-expansion.</span></span>

## <a name="add-migration"></a><span data-ttu-id="b3d1a-173">Add-Migration</span><span class="sxs-lookup"><span data-stu-id="b3d1a-173">Add-Migration</span></span>

<span data-ttu-id="b3d1a-174">加入新的遷移。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-174">Adds a new migration.</span></span>

<span data-ttu-id="b3d1a-175">參數：</span><span class="sxs-lookup"><span data-stu-id="b3d1a-175">Parameters:</span></span>

| <span data-ttu-id="b3d1a-176">參數</span><span class="sxs-lookup"><span data-stu-id="b3d1a-176">Parameter</span></span>                         | <span data-ttu-id="b3d1a-177">描述</span><span class="sxs-lookup"><span data-stu-id="b3d1a-177">Description</span></span>                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b3d1a-178"><nobr>-Name \<String><nobr></span><span class="sxs-lookup"><span data-stu-id="b3d1a-178"><nobr>-Name \<String><nobr></span></span>       | <span data-ttu-id="b3d1a-179">遷移的名稱。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-179">The name of the migration.</span></span> <span data-ttu-id="b3d1a-180">這是位置參數，而且是必要的。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-180">This is a positional parameter and is required.</span></span>                                              |
| <span data-ttu-id="b3d1a-181"><nobr>-OutputDir \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="b3d1a-181"><nobr>-OutputDir \<String></nobr></span></span> | <span data-ttu-id="b3d1a-182">用來輸出檔案的目錄。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-182">The directory use to output the files.</span></span> <span data-ttu-id="b3d1a-183">路徑是相對於目標專案目錄。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-183">Paths are relative to the target project directory.</span></span> <span data-ttu-id="b3d1a-184">預設為「遷移」。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-184">Defaults to "Migrations".</span></span> |
| <span data-ttu-id="b3d1a-185"><nobr>-命名空間 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="b3d1a-185"><nobr>-Namespace \<String></nobr></span></span> | <span data-ttu-id="b3d1a-186">要用於產生之類別的命名空間。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-186">The namespace to use for the generated classes.</span></span> <span data-ttu-id="b3d1a-187">預設為從輸出目錄產生。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-187">Defaults to generated from the output directory.</span></span> <span data-ttu-id="b3d1a-188">在 EF Core 5.0 中新增。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-188">Added in EF Core 5.0.</span></span>  |

<span data-ttu-id="b3d1a-189">上面列出 [一般參數](#common-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-189">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="drop-database"></a><span data-ttu-id="b3d1a-190">Drop-Database</span><span class="sxs-lookup"><span data-stu-id="b3d1a-190">Drop-Database</span></span>

<span data-ttu-id="b3d1a-191">卸除資料庫。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-191">Drops the database.</span></span>

<span data-ttu-id="b3d1a-192">參數：</span><span class="sxs-lookup"><span data-stu-id="b3d1a-192">Parameters:</span></span>

| <span data-ttu-id="b3d1a-193">參數</span><span class="sxs-lookup"><span data-stu-id="b3d1a-193">Parameter</span></span> | <span data-ttu-id="b3d1a-194">描述</span><span class="sxs-lookup"><span data-stu-id="b3d1a-194">Description</span></span>                                              |
|:----------|:---------------------------------------------------------|
| <span data-ttu-id="b3d1a-195">-WhatIf</span><span class="sxs-lookup"><span data-stu-id="b3d1a-195">-WhatIf</span></span>   | <span data-ttu-id="b3d1a-196">顯示要卸載的資料庫，但不要卸載它。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-196">Show which database would be dropped, but don't drop it.</span></span> |

<span data-ttu-id="b3d1a-197">上面列出 [一般參數](#common-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-197">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="get-dbcontext"></a><span data-ttu-id="b3d1a-198">Get-DbContext</span><span class="sxs-lookup"><span data-stu-id="b3d1a-198">Get-DbContext</span></span>

<span data-ttu-id="b3d1a-199">列出並取得可用 `DbContext` 類型的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-199">Lists and gets information about available `DbContext` types.</span></span>

<span data-ttu-id="b3d1a-200">上面列出 [一般參數](#common-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-200">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="get-migration"></a><span data-ttu-id="b3d1a-201">Get-Migration</span><span class="sxs-lookup"><span data-stu-id="b3d1a-201">Get-Migration</span></span>

<span data-ttu-id="b3d1a-202">列出可用的遷移。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-202">Lists available migrations.</span></span> <span data-ttu-id="b3d1a-203">在 EF Core 5.0 中新增。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-203">Added in EF Core 5.0.</span></span>

<span data-ttu-id="b3d1a-204">參數：</span><span class="sxs-lookup"><span data-stu-id="b3d1a-204">Parameters:</span></span>

| <span data-ttu-id="b3d1a-205">參數</span><span class="sxs-lookup"><span data-stu-id="b3d1a-205">Parameter</span></span>                          | <span data-ttu-id="b3d1a-206">描述</span><span class="sxs-lookup"><span data-stu-id="b3d1a-206">Description</span></span>                                                                                            |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------ |
| <span data-ttu-id="b3d1a-207"><nobr>-連接 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="b3d1a-207"><nobr>-Connection \<String></nobr></span></span> | <span data-ttu-id="b3d1a-208">資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-208">The connection string to the database.</span></span> <span data-ttu-id="b3d1a-209">預設為 AddDbCoNtext 或 OnConfiguring 中指定的值。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-209">Defaults to the one specified in AddDbContext or OnConfiguring.</span></span> |
| <span data-ttu-id="b3d1a-210">-NoConnect</span><span class="sxs-lookup"><span data-stu-id="b3d1a-210">-NoConnect</span></span>                         | <span data-ttu-id="b3d1a-211">請勿連接至資料庫。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-211">Don't connect to the database.</span></span>                                                                         |

<span data-ttu-id="b3d1a-212">上面列出 [一般參數](#common-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-212">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="remove-migration"></a><span data-ttu-id="b3d1a-213">移除移轉</span><span class="sxs-lookup"><span data-stu-id="b3d1a-213">Remove-Migration</span></span>

<span data-ttu-id="b3d1a-214">移除最後一個遷移 (復原針對遷移) 所做的程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-214">Removes the last migration (rolls back the code changes that were done for the migration).</span></span>

<span data-ttu-id="b3d1a-215">參數：</span><span class="sxs-lookup"><span data-stu-id="b3d1a-215">Parameters:</span></span>

| <span data-ttu-id="b3d1a-216">參數</span><span class="sxs-lookup"><span data-stu-id="b3d1a-216">Parameter</span></span> | <span data-ttu-id="b3d1a-217">描述</span><span class="sxs-lookup"><span data-stu-id="b3d1a-217">Description</span></span>                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| <span data-ttu-id="b3d1a-218">-Force</span><span class="sxs-lookup"><span data-stu-id="b3d1a-218">-Force</span></span>    | <span data-ttu-id="b3d1a-219">還原遷移 (復原已套用至資料庫) 的變更。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-219">Revert the migration (roll back the changes that were applied to the database).</span></span> |

<span data-ttu-id="b3d1a-220">上面列出 [一般參數](#common-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-220">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="scaffold-dbcontext"></a><span data-ttu-id="b3d1a-221">Scaffold-DbContext</span><span class="sxs-lookup"><span data-stu-id="b3d1a-221">Scaffold-DbContext</span></span>

<span data-ttu-id="b3d1a-222">`DbContext`針對資料庫的和實體類型產生程式碼。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-222">Generates code for a `DbContext` and entity types for a database.</span></span> <span data-ttu-id="b3d1a-223">為了 `Scaffold-DbContext` 產生實體型別，資料庫資料表必須有主鍵。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-223">In order for `Scaffold-DbContext` to generate an entity type, the database table must have a primary key.</span></span>

<span data-ttu-id="b3d1a-224">參數：</span><span class="sxs-lookup"><span data-stu-id="b3d1a-224">Parameters:</span></span>

| <span data-ttu-id="b3d1a-225">參數</span><span class="sxs-lookup"><span data-stu-id="b3d1a-225">Parameter</span></span>                          | <span data-ttu-id="b3d1a-226">描述</span><span class="sxs-lookup"><span data-stu-id="b3d1a-226">Description</span></span>                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b3d1a-227"><nobr>-連接 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="b3d1a-227"><nobr>-Connection \<String></nobr></span></span> | <span data-ttu-id="b3d1a-228">資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-228">The connection string to the database.</span></span> <span data-ttu-id="b3d1a-229">針對 ASP.NET Core 2.x 專案，值可以是\*名稱 = \<name of connection string> \*。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-229">For ASP.NET Core 2.x projects, the value can be *name=\<name of connection string>*.</span></span> <span data-ttu-id="b3d1a-230">在該情況下，名稱來自為專案設定的設定來源。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-230">In that case the name comes from the configuration sources that are set up for the project.</span></span> <span data-ttu-id="b3d1a-231">這是位置參數，而且是必要的。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-231">This is a positional parameter and is required.</span></span> |
| <span data-ttu-id="b3d1a-232"><nobr>-提供者 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="b3d1a-232"><nobr>-Provider \<String></nobr></span></span>   | <span data-ttu-id="b3d1a-233">要使用的提供者。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-233">The provider to use.</span></span> <span data-ttu-id="b3d1a-234">這通常是 NuGet 套件的名稱，例如： `Microsoft.EntityFrameworkCore.SqlServer` 。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-234">Typically this is the name of the NuGet package, for example: `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="b3d1a-235">這是位置參數，而且是必要的。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-235">This is a positional parameter and is required.</span></span>                                                                                           |
| <span data-ttu-id="b3d1a-236">-OutputDir \<String></span><span class="sxs-lookup"><span data-stu-id="b3d1a-236">-OutputDir \<String></span></span>               | <span data-ttu-id="b3d1a-237">要放置檔案的目錄。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-237">The directory to put files in.</span></span> <span data-ttu-id="b3d1a-238">路徑是相對於專案目錄。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-238">Paths are relative to the project directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="b3d1a-239">-CoNtextDir \<String></span><span class="sxs-lookup"><span data-stu-id="b3d1a-239">-ContextDir \<String></span></span>              | <span data-ttu-id="b3d1a-240">要放置檔案的目錄 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-240">The directory to put the `DbContext` file in.</span></span> <span data-ttu-id="b3d1a-241">路徑是相對於專案目錄。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-241">Paths are relative to the project directory.</span></span>                                                                                                                                                               |
| <span data-ttu-id="b3d1a-242">-命名空間 \<String></span><span class="sxs-lookup"><span data-stu-id="b3d1a-242">-Namespace \<String></span></span>               | <span data-ttu-id="b3d1a-243">要用於所有產生之類別的命名空間。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-243">The namespace to use for all generated classes.</span></span> <span data-ttu-id="b3d1a-244">預設為從根命名空間和輸出目錄產生。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-244">Defaults to generated from the root namespace and the output directory.</span></span> <span data-ttu-id="b3d1a-245">在 EF Core 5.0 中新增。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-245">Added in EF Core 5.0.</span></span>                                                                                                                           |
| <span data-ttu-id="b3d1a-246">-CoNtextNamespace \<String></span><span class="sxs-lookup"><span data-stu-id="b3d1a-246">-ContextNamespace \<String></span></span>        | <span data-ttu-id="b3d1a-247">要用於產生之類別的命名空間 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-247">The namespace to use for the generated `DbContext` class.</span></span> <span data-ttu-id="b3d1a-248">注意：覆寫 `-Namespace` 。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-248">Note: overrides `-Namespace`.</span></span> <span data-ttu-id="b3d1a-249">在 EF Core 5.0 中新增。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-249">Added in EF Core 5.0.</span></span>                                                                                                                                                           |
| <span data-ttu-id="b3d1a-250">-CoNtext \<String></span><span class="sxs-lookup"><span data-stu-id="b3d1a-250">-Context \<String></span></span>                 | <span data-ttu-id="b3d1a-251">`DbContext`要產生的類別名稱。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-251">The name of the `DbContext` class to generate.</span></span>                                                                                                                                                                                                                          |
| <span data-ttu-id="b3d1a-252">-架構 \<String[]></span><span class="sxs-lookup"><span data-stu-id="b3d1a-252">-Schemas \<String[]></span></span>               | <span data-ttu-id="b3d1a-253">要產生之實體類型的資料表架構。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-253">The schemas of tables to generate entity types for.</span></span> <span data-ttu-id="b3d1a-254">如果省略此參數，則會包含所有架構。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-254">If this parameter is omitted, all schemas are included.</span></span>                                                                                                                                                             |
| <span data-ttu-id="b3d1a-255">-資料表 \<String[]></span><span class="sxs-lookup"><span data-stu-id="b3d1a-255">-Tables \<String[]></span></span>                | <span data-ttu-id="b3d1a-256">要產生之實體類型的資料表。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-256">The tables to generate entity types for.</span></span> <span data-ttu-id="b3d1a-257">如果省略此參數，則會包含所有資料表。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-257">If this parameter is omitted, all tables are included.</span></span>                                                                                                                                                                         |
| <span data-ttu-id="b3d1a-258">-DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="b3d1a-258">-DataAnnotations</span></span>                   | <span data-ttu-id="b3d1a-259">您可以使用屬性來設定模型 (可能的) 。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-259">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="b3d1a-260">如果省略此參數，則只會使用流暢的 API。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-260">If this parameter is omitted, only the fluent API is used.</span></span>                                                                                                                                                      |
| <span data-ttu-id="b3d1a-261">-UseDatabaseNames</span><span class="sxs-lookup"><span data-stu-id="b3d1a-261">-UseDatabaseNames</span></span>                  | <span data-ttu-id="b3d1a-262">使用資料表和資料行名稱，如同它們出現在資料庫中一樣。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-262">Use table and column names exactly as they appear in the database.</span></span> <span data-ttu-id="b3d1a-263">如果省略此參數，則會變更資料庫名稱，以更緊密符合 c # 名稱樣式慣例。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-263">If this parameter is omitted, database names are changed to more closely conform to C# name style conventions.</span></span>                                                                                       |
| <span data-ttu-id="b3d1a-264">-Force</span><span class="sxs-lookup"><span data-stu-id="b3d1a-264">-Force</span></span>                             | <span data-ttu-id="b3d1a-265">覆寫現有檔案。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-265">Overwrite existing files.</span></span>                                                                                                                                                                                                                                               |
| <span data-ttu-id="b3d1a-266">-NoOnConfiguring</span><span class="sxs-lookup"><span data-stu-id="b3d1a-266">-NoOnConfiguring</span></span>                   | <span data-ttu-id="b3d1a-267">不要產生 `DbContext.OnConfiguring` 。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-267">Don't generate `DbContext.OnConfiguring`.</span></span> <span data-ttu-id="b3d1a-268">在 EF Core 5.0 中新增。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-268">Added in EF Core 5.0.</span></span>                                                                                                                                                                                                         |
| <span data-ttu-id="b3d1a-269">-NoPluralize</span><span class="sxs-lookup"><span data-stu-id="b3d1a-269">-NoPluralize</span></span>                       | <span data-ttu-id="b3d1a-270">請勿使用 pluralizer。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-270">Don't use the pluralizer.</span></span> <span data-ttu-id="b3d1a-271">在 EF Core 5.0 中新增。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-271">Added in EF Core 5.0.</span></span>                                                                                                                                                                                                                         |

<span data-ttu-id="b3d1a-272">上面列出 [一般參數](#common-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-272">The [common parameters](#common-parameters) are listed above.</span></span>

<span data-ttu-id="b3d1a-273">範例：</span><span class="sxs-lookup"><span data-stu-id="b3d1a-273">Example:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

<span data-ttu-id="b3d1a-274">僅 scaffold 選取的資料表，並在具有指定之名稱和命名空間的個別資料夾中建立內容的範例：</span><span class="sxs-lookup"><span data-stu-id="b3d1a-274">Example that scaffolds only selected tables and creates the context in a separate folder with a specified name and namespace:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext -ContextNamespace New.Namespace
```

## <a name="script-dbcontext"></a><span data-ttu-id="b3d1a-275">Script-DbContext</span><span class="sxs-lookup"><span data-stu-id="b3d1a-275">Script-DbContext</span></span>

<span data-ttu-id="b3d1a-276">從 DbCoNtext 產生 SQL 腳本。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-276">Generates a SQL script from the DbContext.</span></span> <span data-ttu-id="b3d1a-277">略過任何遷移。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-277">Bypasses any migrations.</span></span> <span data-ttu-id="b3d1a-278">在 EF Core 3.0 中新增。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-278">Added in EF Core 3.0.</span></span>

<span data-ttu-id="b3d1a-279">參數：</span><span class="sxs-lookup"><span data-stu-id="b3d1a-279">Parameters:</span></span>

| <span data-ttu-id="b3d1a-280">參數</span><span class="sxs-lookup"><span data-stu-id="b3d1a-280">Parameter</span></span>                      | <span data-ttu-id="b3d1a-281">描述</span><span class="sxs-lookup"><span data-stu-id="b3d1a-281">Description</span></span>                      |
| ------------------------------ | -------------------------------- |
| <span data-ttu-id="b3d1a-282"><nobr>-Output \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="b3d1a-282"><nobr>-Output \<String></nobr></span></span> | <span data-ttu-id="b3d1a-283">要寫入結果的檔案。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-283">The file to write the result to.</span></span> |

<span data-ttu-id="b3d1a-284">上面列出 [一般參數](#common-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-284">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="script-migration"></a><span data-ttu-id="b3d1a-285">Script-Migration</span><span class="sxs-lookup"><span data-stu-id="b3d1a-285">Script-Migration</span></span>

<span data-ttu-id="b3d1a-286">產生 SQL 腳本，將所選遷移的所有變更套用到另一個選取的遷移。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-286">Generates a SQL script that applies all of the changes from one selected migration to another selected migration.</span></span>

<span data-ttu-id="b3d1a-287">參數：</span><span class="sxs-lookup"><span data-stu-id="b3d1a-287">Parameters:</span></span>

| <span data-ttu-id="b3d1a-288">參數</span><span class="sxs-lookup"><span data-stu-id="b3d1a-288">Parameter</span></span>                    | <span data-ttu-id="b3d1a-289">描述</span><span class="sxs-lookup"><span data-stu-id="b3d1a-289">Description</span></span>                                                                                                                                                                                                                |
|:---------------------------- |:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b3d1a-290">*-從*\<String></span><span class="sxs-lookup"><span data-stu-id="b3d1a-290">*-From* \<String></span></span>            | <span data-ttu-id="b3d1a-291">開始遷移。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-291">The starting migration.</span></span> <span data-ttu-id="b3d1a-292">遷移可依名稱或識別碼來識別。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-292">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="b3d1a-293">數位0是特殊案例，這表示在 *第一次遷移之前*。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-293">The number 0 is a special case that means *before the first migration*.</span></span> <span data-ttu-id="b3d1a-294">預設為 0。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-294">Defaults to 0.</span></span>                                                              |
| <span data-ttu-id="b3d1a-295">*-至*\<String></span><span class="sxs-lookup"><span data-stu-id="b3d1a-295">*-To* \<String></span></span>              | <span data-ttu-id="b3d1a-296">結束的遷移。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-296">The ending migration.</span></span> <span data-ttu-id="b3d1a-297">預設為上次的遷移。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-297">Defaults to the last migration.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="b3d1a-298">-等冪</span><span class="sxs-lookup"><span data-stu-id="b3d1a-298">-Idempotent</span></span>                  | <span data-ttu-id="b3d1a-299">產生可在任何遷移時用於資料庫的腳本。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-299">Generate a script that can be used on a database at any migration.</span></span>                                                                                                                                                         |
| <span data-ttu-id="b3d1a-300"><nobr>-NoTransactions</nobr></span><span class="sxs-lookup"><span data-stu-id="b3d1a-300"><nobr>-NoTransactions</nobr></span></span> | <span data-ttu-id="b3d1a-301">不要產生 SQL 交易語句。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-301">Don't generate SQL transaction statements.</span></span> <span data-ttu-id="b3d1a-302">在 EF Core 5.0 中新增。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-302">Added in EF Core 5.0.</span></span>                                                                                                                                                           |
| <span data-ttu-id="b3d1a-303">-Output \<String></span><span class="sxs-lookup"><span data-stu-id="b3d1a-303">-Output \<String></span></span>            | <span data-ttu-id="b3d1a-304">要寫入結果的檔案。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-304">The file to write the result to.</span></span> <span data-ttu-id="b3d1a-305">如果省略此參數，則會在建立應用程式的執行時間檔案時，在相同的資料夾中建立檔案，例如： */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-305">IF this parameter is omitted, the file is created with a generated name in the same folder as the app's runtime files are created, for example: */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*.</span></span> |

<span data-ttu-id="b3d1a-306">上面列出 [一般參數](#common-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-306">The [common parameters](#common-parameters) are listed above.</span></span>

> [!TIP]
> <span data-ttu-id="b3d1a-307">To、From 和 Output 參數支援 tab 鍵展開。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-307">The To, From, and Output parameters support tab-expansion.</span></span>

<span data-ttu-id="b3d1a-308">下列範例會使用遷移名稱，建立 InitialCreate 遷移的腳本。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-308">The following example creates a script for the InitialCreate migration, using the migration name.</span></span>

```powershell
Script-Migration -To InitialCreate
```

<span data-ttu-id="b3d1a-309">下列範例會使用遷移識別碼，在 InitialCreate 遷移之後建立所有遷移的腳本。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-309">The following example creates a script for all migrations after the InitialCreate migration, using the migration ID.</span></span>

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a><span data-ttu-id="b3d1a-310">Update-Database</span><span class="sxs-lookup"><span data-stu-id="b3d1a-310">Update-Database</span></span>

<span data-ttu-id="b3d1a-311">將資料庫更新為上次遷移或指定的遷移。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-311">Updates the database to the last migration or to a specified migration.</span></span>

| <span data-ttu-id="b3d1a-312">參數</span><span class="sxs-lookup"><span data-stu-id="b3d1a-312">Parameter</span></span>                           | <span data-ttu-id="b3d1a-313">描述</span><span class="sxs-lookup"><span data-stu-id="b3d1a-313">Description</span></span>                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b3d1a-314"><nobr>*-遷移*\<String></nobr></span><span class="sxs-lookup"><span data-stu-id="b3d1a-314"><nobr>*-Migration* \<String></nobr></span></span> | <span data-ttu-id="b3d1a-315">目標遷移。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-315">The target migration.</span></span> <span data-ttu-id="b3d1a-316">遷移可依名稱或識別碼來識別。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-316">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="b3d1a-317">數位0是特殊案例，這表示在 *第一次遷移之前* ，會將所有遷移還原。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-317">The number 0 is a special case that means *before the first migration* and causes all migrations to be reverted.</span></span> <span data-ttu-id="b3d1a-318">如果未指定任何遷移，則命令會預設為上次的遷移。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-318">If no migration is specified, the command defaults to the last migration.</span></span> |
| <span data-ttu-id="b3d1a-319"><nobr>-連接 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="b3d1a-319"><nobr>-Connection \<String></nobr></span></span>  | <span data-ttu-id="b3d1a-320">資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-320">The connection string to the database.</span></span> <span data-ttu-id="b3d1a-321">預設為或中所指定 `AddDbContext` 的 `OnConfiguring` 。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-321">Defaults to the one specified in `AddDbContext` or `OnConfiguring`.</span></span> <span data-ttu-id="b3d1a-322">在 EF Core 5.0 中新增。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-322">Added in EF Core 5.0.</span></span>                                                                                                                                |

<span data-ttu-id="b3d1a-323">上面列出 [一般參數](#common-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-323">The [common parameters](#common-parameters) are listed above.</span></span>

> [!TIP]
> <span data-ttu-id="b3d1a-324">遷移參數支援 tab 鍵展開。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-324">The Migration parameter supports tab-expansion.</span></span>

<span data-ttu-id="b3d1a-325">下列範例會還原所有的遷移。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-325">The following example reverts all migrations.</span></span>

```powershell
Update-Database -Migration 0
```

<span data-ttu-id="b3d1a-326">下列範例會將資料庫更新為指定的遷移。</span><span class="sxs-lookup"><span data-stu-id="b3d1a-326">The following examples update the database to a specified migration.</span></span> <span data-ttu-id="b3d1a-327">第一個使用遷移名稱，第二個使用遷移識別碼和指定的連接：</span><span class="sxs-lookup"><span data-stu-id="b3d1a-327">The first uses the migration name and the second uses the migration ID and a specified connection:</span></span>

```powershell
Update-Database -Migration InitialCreate
Update-Database -Migration 20180904195021_InitialCreate -Connection your_connection_string
```

## <a name="additional-resources"></a><span data-ttu-id="b3d1a-328">其他資源</span><span class="sxs-lookup"><span data-stu-id="b3d1a-328">Additional resources</span></span>

* [<span data-ttu-id="b3d1a-329">移轉</span><span class="sxs-lookup"><span data-stu-id="b3d1a-329">Migrations</span></span>](xref:core/managing-schemas/migrations/index)
* [<span data-ttu-id="b3d1a-330">反向工程</span><span class="sxs-lookup"><span data-stu-id="b3d1a-330">Reverse Engineering</span></span>](xref:core/managing-schemas/scaffolding)
