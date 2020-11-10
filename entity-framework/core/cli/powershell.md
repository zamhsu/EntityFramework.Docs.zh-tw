---
title: EF Core 工具參考 (封裝管理員主控台) -EF Core
description: Entity Framework Core Visual Studio 封裝管理員主控台的參考指南
author: bricelam
ms.date: 10/27/2020
uid: core/cli/powershell
ms.openlocfilehash: 4a1ab889fc1117b67252ace51fd3df4797b6c8d3
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431472"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a><span data-ttu-id="e089a-103">Visual Studio 中的 Entity Framework Core 工具參考-封裝管理員主控台</span><span class="sxs-lookup"><span data-stu-id="e089a-103">Entity Framework Core tools reference - Package Manager Console in Visual Studio</span></span>

<span data-ttu-id="e089a-104">封裝管理員主控台 (PMC) 工具，以 Entity Framework Core 執行設計階段開發工作。</span><span class="sxs-lookup"><span data-stu-id="e089a-104">The Package Manager Console (PMC) tools for Entity Framework Core perform design-time development tasks.</span></span> <span data-ttu-id="e089a-105">例如，他們會建立 [遷移](/aspnet/core/data/ef-mvc/migrations)、套用遷移，並根據現有的資料庫產生模型的程式碼。</span><span class="sxs-lookup"><span data-stu-id="e089a-105">For example, they create [migrations](/aspnet/core/data/ef-mvc/migrations), apply migrations, and generate code for a model based on an existing database.</span></span> <span data-ttu-id="e089a-106">這些命令會使用 [封裝管理員主控台](/nuget/tools/package-manager-console)在 Visual Studio 內部執行。</span><span class="sxs-lookup"><span data-stu-id="e089a-106">The commands run inside of Visual Studio using the [Package Manager Console](/nuget/tools/package-manager-console).</span></span> <span data-ttu-id="e089a-107">這些工具會使用 .NET Framework 和 .NET Core 專案。</span><span class="sxs-lookup"><span data-stu-id="e089a-107">These tools work with both .NET Framework and .NET Core projects.</span></span>

<span data-ttu-id="e089a-108">如果您不是使用 Visual Studio，建議您改用 [EF Core 命令列工具](xref:core/cli/dotnet) 。</span><span class="sxs-lookup"><span data-stu-id="e089a-108">If you aren't using Visual Studio, we recommend the [EF Core Command-line Tools](xref:core/cli/dotnet) instead.</span></span> <span data-ttu-id="e089a-109">.NET Core CLI 工具是跨平臺，並在命令提示字元內執行。</span><span class="sxs-lookup"><span data-stu-id="e089a-109">The .NET Core CLI tools are cross-platform and run inside a command prompt.</span></span>

## <a name="installing-the-tools"></a><span data-ttu-id="e089a-110">安裝工具</span><span class="sxs-lookup"><span data-stu-id="e089a-110">Installing the tools</span></span>

<span data-ttu-id="e089a-111">在 **封裝管理員主控台** 中執行下列命令，以安裝封裝管理員主控台工具：</span><span class="sxs-lookup"><span data-stu-id="e089a-111">Install the Package Manager Console tools by running the following command in **Package Manager Console** :</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="e089a-112">在 **封裝管理員主控台** 中執行下列命令，以更新工具。</span><span class="sxs-lookup"><span data-stu-id="e089a-112">Update the tools by running the following command in **Package Manager Console**.</span></span>

```powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a><span data-ttu-id="e089a-113">驗證安裝</span><span class="sxs-lookup"><span data-stu-id="e089a-113">Verify the installation</span></span>

<span data-ttu-id="e089a-114">執行下列命令，確認是否已安裝這些工具：</span><span class="sxs-lookup"><span data-stu-id="e089a-114">Verify that the tools are installed by running this command:</span></span>

```powershell
Get-Help about_EntityFrameworkCore
```

<span data-ttu-id="e089a-115">輸出看起來會像這樣 (不會告訴您所使用的工具是哪個版本) ：</span><span class="sxs-lookup"><span data-stu-id="e089a-115">The output looks like this (it doesn't tell you which version of the tools you're using):</span></span>

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

## <a name="using-the-tools"></a><span data-ttu-id="e089a-116">使用工具</span><span class="sxs-lookup"><span data-stu-id="e089a-116">Using the tools</span></span>

<span data-ttu-id="e089a-117">使用工具之前：</span><span class="sxs-lookup"><span data-stu-id="e089a-117">Before using the tools:</span></span>

* <span data-ttu-id="e089a-118">瞭解目標和啟始專案之間的差異。</span><span class="sxs-lookup"><span data-stu-id="e089a-118">Understand the difference between target and startup project.</span></span>
* <span data-ttu-id="e089a-119">瞭解如何使用 .NET Standard 類別庫的工具。</span><span class="sxs-lookup"><span data-stu-id="e089a-119">Learn how to use the tools with .NET Standard class libraries.</span></span>
* <span data-ttu-id="e089a-120">針對 ASP.NET Core 專案，請設定環境。</span><span class="sxs-lookup"><span data-stu-id="e089a-120">For ASP.NET Core projects, set the environment.</span></span>

### <a name="target-and-startup-project"></a><span data-ttu-id="e089a-121">目標和啟始專案</span><span class="sxs-lookup"><span data-stu-id="e089a-121">Target and startup project</span></span>

<span data-ttu-id="e089a-122">這些命令會參考 *專案* 和 *啟始專案* 。</span><span class="sxs-lookup"><span data-stu-id="e089a-122">The commands refer to a *project* and a *startup project*.</span></span>

* <span data-ttu-id="e089a-123">*專案* 也稱為 *目標專案* ，因為這是命令新增或移除檔案的位置。</span><span class="sxs-lookup"><span data-stu-id="e089a-123">The *project* is also known as the *target project* because it's where the commands add or remove files.</span></span> <span data-ttu-id="e089a-124">依預設， **封裝管理員主控台** 中選取的 **預設專案** 是目標專案。</span><span class="sxs-lookup"><span data-stu-id="e089a-124">By default, the **Default project** selected in **Package Manager Console** is the target project.</span></span> <span data-ttu-id="e089a-125">您可以使用選項，將不同的專案指定為目標專案 <nobr>`--project`</nobr> 。</span><span class="sxs-lookup"><span data-stu-id="e089a-125">You can specify a different project as target project by using the <nobr>`--project`</nobr> option.</span></span>

* <span data-ttu-id="e089a-126">*啟始專案* 是工具建立和執行的專案。</span><span class="sxs-lookup"><span data-stu-id="e089a-126">The *startup project* is the one that the tools build and run.</span></span> <span data-ttu-id="e089a-127">這些工具必須在設計階段執行應用程式程式碼，以取得專案的相關資訊，例如資料庫連接字串和模型的設定。</span><span class="sxs-lookup"><span data-stu-id="e089a-127">The tools have to execute application code at design time to get information about the project, such as the database connection string and the configuration of the model.</span></span> <span data-ttu-id="e089a-128">根據預設， **方案總管** 中的 **啟始專案** 是啟始專案。</span><span class="sxs-lookup"><span data-stu-id="e089a-128">By default, the **Startup Project** in **Solution Explorer** is the startup project.</span></span> <span data-ttu-id="e089a-129">您可以使用選項，將不同的專案指定為啟始專案 <nobr>`--startup-project`</nobr> 。</span><span class="sxs-lookup"><span data-stu-id="e089a-129">You can specify a different project as startup project by using the <nobr>`--startup-project`</nobr> option.</span></span>

<span data-ttu-id="e089a-130">啟始專案和目標專案通常是相同的專案。</span><span class="sxs-lookup"><span data-stu-id="e089a-130">The startup project and target project are often the same project.</span></span> <span data-ttu-id="e089a-131">一般情況下，它們是不同的專案：</span><span class="sxs-lookup"><span data-stu-id="e089a-131">A typical scenario where they are separate projects is when:</span></span>

* <span data-ttu-id="e089a-132">EF Core 內容和實體類別位於 .NET Core 類別庫中。</span><span class="sxs-lookup"><span data-stu-id="e089a-132">The EF Core context and entity classes are in a .NET Core class library.</span></span>
* <span data-ttu-id="e089a-133">.NET Core 主控台應用程式或 web 應用程式會參考類別庫。</span><span class="sxs-lookup"><span data-stu-id="e089a-133">A .NET Core console app or web app references the class library.</span></span>

<span data-ttu-id="e089a-134">您也可以將與 [EF Core 內容分開的類別庫中的遷移程式碼](xref:core/managing-schemas/migrations/projects)。</span><span class="sxs-lookup"><span data-stu-id="e089a-134">It's also possible to [put migrations code in a class library separate from the EF Core context](xref:core/managing-schemas/migrations/projects).</span></span>

### <a name="other-target-frameworks"></a><span data-ttu-id="e089a-135">其他目標 framework</span><span class="sxs-lookup"><span data-stu-id="e089a-135">Other target frameworks</span></span>

<span data-ttu-id="e089a-136">封裝管理員主控台工具適用于 .NET Core 或 .NET Framework 專案。</span><span class="sxs-lookup"><span data-stu-id="e089a-136">The Package Manager Console tools work with .NET Core or .NET Framework projects.</span></span> <span data-ttu-id="e089a-137">在 .NET Standard 類別庫中具有 EF Core 模型的應用程式可能沒有 .NET Core 或 .NET Framework 專案。</span><span class="sxs-lookup"><span data-stu-id="e089a-137">Apps that have the EF Core model in a .NET Standard class library might not have a .NET Core or .NET Framework project.</span></span> <span data-ttu-id="e089a-138">例如，這適用于 Xamarin 和通用 Windows 平臺應用程式。</span><span class="sxs-lookup"><span data-stu-id="e089a-138">For example, this is true of Xamarin and Universal Windows Platform apps.</span></span> <span data-ttu-id="e089a-139">在這種情況下，您可以建立 .NET Core 或 .NET Framework 主控台應用程式專案，其唯一目的是作為工具的啟始專案。</span><span class="sxs-lookup"><span data-stu-id="e089a-139">In such cases, you can create a .NET Core or .NET Framework console app project whose only purpose is to act as startup project for the tools.</span></span> <span data-ttu-id="e089a-140">專案可以是沒有真正程式碼的虛擬專案， &mdash; 只需要提供工具的目標。</span><span class="sxs-lookup"><span data-stu-id="e089a-140">The project can be a dummy project with no real code &mdash; it is only needed to provide a target for the tooling.</span></span>

<span data-ttu-id="e089a-141">為什麼需要虛擬專案？</span><span class="sxs-lookup"><span data-stu-id="e089a-141">Why is a dummy project required?</span></span> <span data-ttu-id="e089a-142">如先前所述，這些工具必須在設計階段執行應用程式程式碼。</span><span class="sxs-lookup"><span data-stu-id="e089a-142">As mentioned earlier, the tools have to execute application code at design time.</span></span> <span data-ttu-id="e089a-143">若要這樣做，他們必須使用 .NET Core 或 .NET Framework 執行時間。</span><span class="sxs-lookup"><span data-stu-id="e089a-143">To do that, they need to use the .NET Core or .NET Framework runtime.</span></span> <span data-ttu-id="e089a-144">當 EF Core 模型位於以 .NET Core 或 .NET Framework 為目標的專案中時，EF Core 工具會從專案借用執行時間。</span><span class="sxs-lookup"><span data-stu-id="e089a-144">When the EF Core model is in a project that targets .NET Core or .NET Framework, the EF Core tools borrow the runtime from the project.</span></span> <span data-ttu-id="e089a-145">如果 EF Core 模型位於 .NET Standard 類別庫中，則無法這麼做。</span><span class="sxs-lookup"><span data-stu-id="e089a-145">They can't do that if the EF Core model is in a .NET Standard class library.</span></span> <span data-ttu-id="e089a-146">.NET Standard 不是實際的 .NET 執行;它是一組 .NET 開發人員必須支援的 Api 的規格。</span><span class="sxs-lookup"><span data-stu-id="e089a-146">The .NET Standard is not an actual .NET implementation; it's a specification of a set of APIs that .NET implementations must support.</span></span> <span data-ttu-id="e089a-147">因此 .NET Standard 不足以執行應用程式程式碼 EF Core 工具。</span><span class="sxs-lookup"><span data-stu-id="e089a-147">Therefore .NET Standard is not sufficient for the EF Core tools to execute application code.</span></span> <span data-ttu-id="e089a-148">您建立用來做為啟始專案的虛擬專案提供可讓工具載入 .NET Standard 類別庫的具體目標平臺。</span><span class="sxs-lookup"><span data-stu-id="e089a-148">The dummy project you create to use as startup project provides a concrete target platform into which the tools can load the .NET Standard class library.</span></span>

### <a name="aspnet-core-environment"></a><span data-ttu-id="e089a-149">ASP.NET Core 環境</span><span class="sxs-lookup"><span data-stu-id="e089a-149">ASP.NET Core environment</span></span>

<span data-ttu-id="e089a-150">若要指定 ASP.NET Core 專案 [的環境](/aspnet/core/fundamentals/environments) ，請先設定 **env： ASPNETCORE_ENVIRONMENT** ，再執行命令。</span><span class="sxs-lookup"><span data-stu-id="e089a-150">To specify [the environment](/aspnet/core/fundamentals/environments) for ASP.NET Core projects, set **env:ASPNETCORE_ENVIRONMENT** before running commands.</span></span>

<span data-ttu-id="e089a-151">從 EF Core 5.0 開始，還可以將額外的引數傳遞至 CreateHostBuilder，讓您在命令列上指定環境：</span><span class="sxs-lookup"><span data-stu-id="e089a-151">Starting in EF Core 5.0, additional arguments can also be passed into Program.CreateHostBuilder allowing you to specify the environment on the command-line:</span></span>

```powershell
Update-Database -Args '--environment Production'
```

## <a name="common-parameters"></a><span data-ttu-id="e089a-152">一般參數</span><span class="sxs-lookup"><span data-stu-id="e089a-152">Common parameters</span></span>

<span data-ttu-id="e089a-153">下表顯示所有 EF Core 命令通用的參數：</span><span class="sxs-lookup"><span data-stu-id="e089a-153">The following table shows parameters that are common to all of the EF Core commands:</span></span>

| <span data-ttu-id="e089a-154">參數</span><span class="sxs-lookup"><span data-stu-id="e089a-154">Parameter</span></span>                 | <span data-ttu-id="e089a-155">說明</span><span class="sxs-lookup"><span data-stu-id="e089a-155">Description</span></span>                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e089a-156">-CoNtext \<String></span><span class="sxs-lookup"><span data-stu-id="e089a-156">-Context \<String></span></span>        | <span data-ttu-id="e089a-157">要使用的 `DbContext` 類別。</span><span class="sxs-lookup"><span data-stu-id="e089a-157">The `DbContext` class to use.</span></span> <span data-ttu-id="e089a-158">僅限類別名稱或完整限定命名空間。</span><span class="sxs-lookup"><span data-stu-id="e089a-158">Class name only or fully qualified with namespaces.</span></span>  <span data-ttu-id="e089a-159">如果省略此參數，EF Core 會尋找內容類別。</span><span class="sxs-lookup"><span data-stu-id="e089a-159">If this parameter is omitted, EF Core finds the context class.</span></span> <span data-ttu-id="e089a-160">如果有多個內容類別，則需要此參數。</span><span class="sxs-lookup"><span data-stu-id="e089a-160">If there are multiple context classes, this parameter is required.</span></span> |
| <span data-ttu-id="e089a-161">-Project \<String></span><span class="sxs-lookup"><span data-stu-id="e089a-161">-Project \<String></span></span>        | <span data-ttu-id="e089a-162">目標專案。</span><span class="sxs-lookup"><span data-stu-id="e089a-162">The target project.</span></span> <span data-ttu-id="e089a-163">如果省略此參數，則會使用 **封裝管理員主控台** 的 **預設專案** 做為目標專案。</span><span class="sxs-lookup"><span data-stu-id="e089a-163">If this parameter is omitted, the **Default project** for **Package Manager Console** is used as the target project.</span></span>                                                                             |
| <span data-ttu-id="e089a-164"><nobr>-啟始專案</nobr>\<String></span><span class="sxs-lookup"><span data-stu-id="e089a-164"><nobr>-StartupProject</nobr> \<String></span></span> | <span data-ttu-id="e089a-165">啟始專案。</span><span class="sxs-lookup"><span data-stu-id="e089a-165">The startup project.</span></span> <span data-ttu-id="e089a-166">如果省略此參數，則會使用 **方案屬性** 中的 **啟始專案** 做為目標專案。</span><span class="sxs-lookup"><span data-stu-id="e089a-166">If this parameter is omitted, the **Startup project** in **Solution properties** is used as the target project.</span></span>                                                                                 |
| <span data-ttu-id="e089a-167">-Args \<String></span><span class="sxs-lookup"><span data-stu-id="e089a-167">-Args \<String></span></span>           | <span data-ttu-id="e089a-168">傳遞至應用程式的引數。</span><span class="sxs-lookup"><span data-stu-id="e089a-168">Arguments passed to the application.</span></span> <span data-ttu-id="e089a-169">在 EF Core 5.0 中新增。</span><span class="sxs-lookup"><span data-stu-id="e089a-169">Added in EF Core 5.0.</span></span>                                                                                                                                                           |
| <span data-ttu-id="e089a-170">-Verbose</span><span class="sxs-lookup"><span data-stu-id="e089a-170">-Verbose</span></span>                  | <span data-ttu-id="e089a-171">顯示詳細資訊輸出。</span><span class="sxs-lookup"><span data-stu-id="e089a-171">Show verbose output.</span></span>                                                                                                                                                                                                 |

<span data-ttu-id="e089a-172">若要顯示命令的說明資訊，請使用 PowerShell 的 `Get-Help` 命令。</span><span class="sxs-lookup"><span data-stu-id="e089a-172">To show help information about a command, use PowerShell's `Get-Help` command.</span></span>

> [!TIP]
> <span data-ttu-id="e089a-173">CoNtext、Project 和啟始專案參數支援 tab 鍵展開。</span><span class="sxs-lookup"><span data-stu-id="e089a-173">The Context, Project, and StartupProject parameters support tab-expansion.</span></span>

## <a name="add-migration"></a><span data-ttu-id="e089a-174">Add-Migration</span><span class="sxs-lookup"><span data-stu-id="e089a-174">Add-Migration</span></span>

<span data-ttu-id="e089a-175">加入新的遷移。</span><span class="sxs-lookup"><span data-stu-id="e089a-175">Adds a new migration.</span></span>

<span data-ttu-id="e089a-176">參數：</span><span class="sxs-lookup"><span data-stu-id="e089a-176">Parameters:</span></span>

| <span data-ttu-id="e089a-177">參數</span><span class="sxs-lookup"><span data-stu-id="e089a-177">Parameter</span></span>                         | <span data-ttu-id="e089a-178">說明</span><span class="sxs-lookup"><span data-stu-id="e089a-178">Description</span></span>                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e089a-179"><nobr>-Name \<String><nobr></span><span class="sxs-lookup"><span data-stu-id="e089a-179"><nobr>-Name \<String><nobr></span></span>       | <span data-ttu-id="e089a-180">遷移的名稱。</span><span class="sxs-lookup"><span data-stu-id="e089a-180">The name of the migration.</span></span> <span data-ttu-id="e089a-181">這是位置參數，而且是必要的。</span><span class="sxs-lookup"><span data-stu-id="e089a-181">This is a positional parameter and is required.</span></span>                                              |
| <span data-ttu-id="e089a-182"><nobr>-OutputDir \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="e089a-182"><nobr>-OutputDir \<String></nobr></span></span> | <span data-ttu-id="e089a-183">用來輸出檔案的目錄。</span><span class="sxs-lookup"><span data-stu-id="e089a-183">The directory use to output the files.</span></span> <span data-ttu-id="e089a-184">路徑是相對於目標專案目錄。</span><span class="sxs-lookup"><span data-stu-id="e089a-184">Paths are relative to the target project directory.</span></span> <span data-ttu-id="e089a-185">預設為「遷移」。</span><span class="sxs-lookup"><span data-stu-id="e089a-185">Defaults to "Migrations".</span></span> |
| <span data-ttu-id="e089a-186"><nobr>-命名空間 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="e089a-186"><nobr>-Namespace \<String></nobr></span></span> | <span data-ttu-id="e089a-187">要用於產生之類別的命名空間。</span><span class="sxs-lookup"><span data-stu-id="e089a-187">The namespace to use for the generated classes.</span></span> <span data-ttu-id="e089a-188">預設為從輸出目錄產生。</span><span class="sxs-lookup"><span data-stu-id="e089a-188">Defaults to generated from the output directory.</span></span> <span data-ttu-id="e089a-189">在 EF Core 5.0 中新增。</span><span class="sxs-lookup"><span data-stu-id="e089a-189">Added in EF Core 5.0.</span></span>  |

<span data-ttu-id="e089a-190">上面列出 [一般參數](#common-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="e089a-190">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="drop-database"></a><span data-ttu-id="e089a-191">Drop-Database</span><span class="sxs-lookup"><span data-stu-id="e089a-191">Drop-Database</span></span>

<span data-ttu-id="e089a-192">卸除資料庫。</span><span class="sxs-lookup"><span data-stu-id="e089a-192">Drops the database.</span></span>

<span data-ttu-id="e089a-193">參數：</span><span class="sxs-lookup"><span data-stu-id="e089a-193">Parameters:</span></span>

| <span data-ttu-id="e089a-194">參數</span><span class="sxs-lookup"><span data-stu-id="e089a-194">Parameter</span></span> | <span data-ttu-id="e089a-195">說明</span><span class="sxs-lookup"><span data-stu-id="e089a-195">Description</span></span>                                              |
|:----------|:---------------------------------------------------------|
| <span data-ttu-id="e089a-196">-WhatIf</span><span class="sxs-lookup"><span data-stu-id="e089a-196">-WhatIf</span></span>   | <span data-ttu-id="e089a-197">顯示要卸載的資料庫，但不要卸載它。</span><span class="sxs-lookup"><span data-stu-id="e089a-197">Show which database would be dropped, but don't drop it.</span></span> |

<span data-ttu-id="e089a-198">上面列出 [一般參數](#common-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="e089a-198">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="get-dbcontext"></a><span data-ttu-id="e089a-199">Get-DbContext</span><span class="sxs-lookup"><span data-stu-id="e089a-199">Get-DbContext</span></span>

<span data-ttu-id="e089a-200">列出並取得可用 `DbContext` 類型的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="e089a-200">Lists and gets information about available `DbContext` types.</span></span>

<span data-ttu-id="e089a-201">上面列出 [一般參數](#common-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="e089a-201">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="get-migration"></a><span data-ttu-id="e089a-202">Get-Migration</span><span class="sxs-lookup"><span data-stu-id="e089a-202">Get-Migration</span></span>

<span data-ttu-id="e089a-203">列出可用的遷移。</span><span class="sxs-lookup"><span data-stu-id="e089a-203">Lists available migrations.</span></span> <span data-ttu-id="e089a-204">在 EF Core 5.0 中新增。</span><span class="sxs-lookup"><span data-stu-id="e089a-204">Added in EF Core 5.0.</span></span>

<span data-ttu-id="e089a-205">參數：</span><span class="sxs-lookup"><span data-stu-id="e089a-205">Parameters:</span></span>

| <span data-ttu-id="e089a-206">參數</span><span class="sxs-lookup"><span data-stu-id="e089a-206">Parameter</span></span>                          | <span data-ttu-id="e089a-207">說明</span><span class="sxs-lookup"><span data-stu-id="e089a-207">Description</span></span>                                                                                            |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------ |
| <span data-ttu-id="e089a-208"><nobr>-連接 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="e089a-208"><nobr>-Connection \<String></nobr></span></span> | <span data-ttu-id="e089a-209">資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="e089a-209">The connection string to the database.</span></span> <span data-ttu-id="e089a-210">預設為 AddDbCoNtext 或 OnConfiguring 中指定的值。</span><span class="sxs-lookup"><span data-stu-id="e089a-210">Defaults to the one specified in AddDbContext or OnConfiguring.</span></span> |
| <span data-ttu-id="e089a-211">-NoConnect</span><span class="sxs-lookup"><span data-stu-id="e089a-211">-NoConnect</span></span>                         | <span data-ttu-id="e089a-212">請勿連接至資料庫。</span><span class="sxs-lookup"><span data-stu-id="e089a-212">Don't connect to the database.</span></span>                                                                         |

<span data-ttu-id="e089a-213">上面列出 [一般參數](#common-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="e089a-213">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="remove-migration"></a><span data-ttu-id="e089a-214">移除移轉</span><span class="sxs-lookup"><span data-stu-id="e089a-214">Remove-Migration</span></span>

<span data-ttu-id="e089a-215">移除最後一個遷移 (復原針對遷移) 所做的程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="e089a-215">Removes the last migration (rolls back the code changes that were done for the migration).</span></span>

<span data-ttu-id="e089a-216">參數：</span><span class="sxs-lookup"><span data-stu-id="e089a-216">Parameters:</span></span>

| <span data-ttu-id="e089a-217">參數</span><span class="sxs-lookup"><span data-stu-id="e089a-217">Parameter</span></span> | <span data-ttu-id="e089a-218">說明</span><span class="sxs-lookup"><span data-stu-id="e089a-218">Description</span></span>                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| <span data-ttu-id="e089a-219">-Force</span><span class="sxs-lookup"><span data-stu-id="e089a-219">-Force</span></span>    | <span data-ttu-id="e089a-220">還原遷移 (復原已套用至資料庫) 的變更。</span><span class="sxs-lookup"><span data-stu-id="e089a-220">Revert the migration (roll back the changes that were applied to the database).</span></span> |

<span data-ttu-id="e089a-221">上面列出 [一般參數](#common-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="e089a-221">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="scaffold-dbcontext"></a><span data-ttu-id="e089a-222">Scaffold-DbContext</span><span class="sxs-lookup"><span data-stu-id="e089a-222">Scaffold-DbContext</span></span>

<span data-ttu-id="e089a-223">`DbContext`針對資料庫的和實體類型產生程式碼。</span><span class="sxs-lookup"><span data-stu-id="e089a-223">Generates code for a `DbContext` and entity types for a database.</span></span> <span data-ttu-id="e089a-224">為了 `Scaffold-DbContext` 產生實體型別，資料庫資料表必須有主鍵。</span><span class="sxs-lookup"><span data-stu-id="e089a-224">In order for `Scaffold-DbContext` to generate an entity type, the database table must have a primary key.</span></span>

<span data-ttu-id="e089a-225">參數：</span><span class="sxs-lookup"><span data-stu-id="e089a-225">Parameters:</span></span>

| <span data-ttu-id="e089a-226">參數</span><span class="sxs-lookup"><span data-stu-id="e089a-226">Parameter</span></span>                          | <span data-ttu-id="e089a-227">說明</span><span class="sxs-lookup"><span data-stu-id="e089a-227">Description</span></span>                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e089a-228"><nobr>-連接 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="e089a-228"><nobr>-Connection \<String></nobr></span></span> | <span data-ttu-id="e089a-229">資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="e089a-229">The connection string to the database.</span></span> <span data-ttu-id="e089a-230">針對 ASP.NET Core 2.x 專案，值可以是 *名稱 = \<name of connection string>* 。</span><span class="sxs-lookup"><span data-stu-id="e089a-230">For ASP.NET Core 2.x projects, the value can be *name=\<name of connection string>*.</span></span> <span data-ttu-id="e089a-231">在該情況下，名稱來自為專案設定的設定來源。</span><span class="sxs-lookup"><span data-stu-id="e089a-231">In that case the name comes from the configuration sources that are set up for the project.</span></span> <span data-ttu-id="e089a-232">這是位置參數，而且是必要的。</span><span class="sxs-lookup"><span data-stu-id="e089a-232">This is a positional parameter and is required.</span></span> |
| <span data-ttu-id="e089a-233"><nobr>-提供者 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="e089a-233"><nobr>-Provider \<String></nobr></span></span>   | <span data-ttu-id="e089a-234">要使用的提供者。</span><span class="sxs-lookup"><span data-stu-id="e089a-234">The provider to use.</span></span> <span data-ttu-id="e089a-235">這通常是 NuGet 套件的名稱，例如： `Microsoft.EntityFrameworkCore.SqlServer` 。</span><span class="sxs-lookup"><span data-stu-id="e089a-235">Typically this is the name of the NuGet package, for example: `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="e089a-236">這是位置參數，而且是必要的。</span><span class="sxs-lookup"><span data-stu-id="e089a-236">This is a positional parameter and is required.</span></span>                                                                                           |
| <span data-ttu-id="e089a-237">-OutputDir \<String></span><span class="sxs-lookup"><span data-stu-id="e089a-237">-OutputDir \<String></span></span>               | <span data-ttu-id="e089a-238">要放置檔案的目錄。</span><span class="sxs-lookup"><span data-stu-id="e089a-238">The directory to put files in.</span></span> <span data-ttu-id="e089a-239">路徑是相對於專案目錄。</span><span class="sxs-lookup"><span data-stu-id="e089a-239">Paths are relative to the project directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="e089a-240">-CoNtextDir \<String></span><span class="sxs-lookup"><span data-stu-id="e089a-240">-ContextDir \<String></span></span>              | <span data-ttu-id="e089a-241">要放置檔案的目錄 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="e089a-241">The directory to put the `DbContext` file in.</span></span> <span data-ttu-id="e089a-242">路徑是相對於專案目錄。</span><span class="sxs-lookup"><span data-stu-id="e089a-242">Paths are relative to the project directory.</span></span>                                                                                                                                                               |
| <span data-ttu-id="e089a-243">-命名空間 \<String></span><span class="sxs-lookup"><span data-stu-id="e089a-243">-Namespace \<String></span></span>               | <span data-ttu-id="e089a-244">要用於所有產生之類別的命名空間。</span><span class="sxs-lookup"><span data-stu-id="e089a-244">The namespace to use for all generated classes.</span></span> <span data-ttu-id="e089a-245">預設為從根命名空間和輸出目錄產生。</span><span class="sxs-lookup"><span data-stu-id="e089a-245">Defaults to generated from the root namespace and the output directory.</span></span> <span data-ttu-id="e089a-246">在 EF Core 5.0 中新增。</span><span class="sxs-lookup"><span data-stu-id="e089a-246">Added in EF Core 5.0.</span></span>                                                                                                                           |
| <span data-ttu-id="e089a-247">-CoNtextNamespace \<String></span><span class="sxs-lookup"><span data-stu-id="e089a-247">-ContextNamespace \<String></span></span>        | <span data-ttu-id="e089a-248">要用於產生之類別的命名空間 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="e089a-248">The namespace to use for the generated `DbContext` class.</span></span> <span data-ttu-id="e089a-249">注意：覆寫 `-Namespace` 。</span><span class="sxs-lookup"><span data-stu-id="e089a-249">Note: overrides `-Namespace`.</span></span> <span data-ttu-id="e089a-250">在 EF Core 5.0 中新增。</span><span class="sxs-lookup"><span data-stu-id="e089a-250">Added in EF Core 5.0.</span></span>                                                                                                                                                           |
| <span data-ttu-id="e089a-251">-CoNtext \<String></span><span class="sxs-lookup"><span data-stu-id="e089a-251">-Context \<String></span></span>                 | <span data-ttu-id="e089a-252">`DbContext`要產生的類別名稱。</span><span class="sxs-lookup"><span data-stu-id="e089a-252">The name of the `DbContext` class to generate.</span></span>                                                                                                                                                                                                                          |
| <span data-ttu-id="e089a-253">-架構 \<String[]></span><span class="sxs-lookup"><span data-stu-id="e089a-253">-Schemas \<String[]></span></span>               | <span data-ttu-id="e089a-254">要產生之實體類型的資料表架構。</span><span class="sxs-lookup"><span data-stu-id="e089a-254">The schemas of tables to generate entity types for.</span></span> <span data-ttu-id="e089a-255">如果省略此參數，則會包含所有架構。</span><span class="sxs-lookup"><span data-stu-id="e089a-255">If this parameter is omitted, all schemas are included.</span></span>                                                                                                                                                             |
| <span data-ttu-id="e089a-256">-資料表 \<String[]></span><span class="sxs-lookup"><span data-stu-id="e089a-256">-Tables \<String[]></span></span>                | <span data-ttu-id="e089a-257">要產生之實體類型的資料表。</span><span class="sxs-lookup"><span data-stu-id="e089a-257">The tables to generate entity types for.</span></span> <span data-ttu-id="e089a-258">如果省略此參數，則會包含所有資料表。</span><span class="sxs-lookup"><span data-stu-id="e089a-258">If this parameter is omitted, all tables are included.</span></span>                                                                                                                                                                         |
| <span data-ttu-id="e089a-259">-DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="e089a-259">-DataAnnotations</span></span>                   | <span data-ttu-id="e089a-260">您可以使用屬性來設定模型 (可能的) 。</span><span class="sxs-lookup"><span data-stu-id="e089a-260">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="e089a-261">如果省略此參數，則只會使用流暢的 API。</span><span class="sxs-lookup"><span data-stu-id="e089a-261">If this parameter is omitted, only the fluent API is used.</span></span>                                                                                                                                                      |
| <span data-ttu-id="e089a-262">-UseDatabaseNames</span><span class="sxs-lookup"><span data-stu-id="e089a-262">-UseDatabaseNames</span></span>                  | <span data-ttu-id="e089a-263">使用資料表和資料行名稱，如同它們出現在資料庫中一樣。</span><span class="sxs-lookup"><span data-stu-id="e089a-263">Use table and column names exactly as they appear in the database.</span></span> <span data-ttu-id="e089a-264">如果省略此參數，則會變更資料庫名稱，以更緊密符合 c # 名稱樣式慣例。</span><span class="sxs-lookup"><span data-stu-id="e089a-264">If this parameter is omitted, database names are changed to more closely conform to C# name style conventions.</span></span>                                                                                       |
| <span data-ttu-id="e089a-265">-Force</span><span class="sxs-lookup"><span data-stu-id="e089a-265">-Force</span></span>                             | <span data-ttu-id="e089a-266">覆寫現有檔案。</span><span class="sxs-lookup"><span data-stu-id="e089a-266">Overwrite existing files.</span></span>                                                                                                                                                                                                                                               |
| <span data-ttu-id="e089a-267">-NoOnConfiguring</span><span class="sxs-lookup"><span data-stu-id="e089a-267">-NoOnConfiguring</span></span>                   | <span data-ttu-id="e089a-268">不要產生 `DbContext.OnConfiguring` 。</span><span class="sxs-lookup"><span data-stu-id="e089a-268">Don't generate `DbContext.OnConfiguring`.</span></span> <span data-ttu-id="e089a-269">在 EF Core 5.0 中新增。</span><span class="sxs-lookup"><span data-stu-id="e089a-269">Added in EF Core 5.0.</span></span>                                                                                                                                                                                                         |
| <span data-ttu-id="e089a-270">-NoPluralize</span><span class="sxs-lookup"><span data-stu-id="e089a-270">-NoPluralize</span></span>                       | <span data-ttu-id="e089a-271">請勿使用 pluralizer。</span><span class="sxs-lookup"><span data-stu-id="e089a-271">Don't use the pluralizer.</span></span> <span data-ttu-id="e089a-272">在 EF Core 5.0 中新增。</span><span class="sxs-lookup"><span data-stu-id="e089a-272">Added in EF Core 5.0.</span></span>                                                                                                                                                                                                                         |

<span data-ttu-id="e089a-273">上面列出 [一般參數](#common-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="e089a-273">The [common parameters](#common-parameters) are listed above.</span></span>

<span data-ttu-id="e089a-274">範例：</span><span class="sxs-lookup"><span data-stu-id="e089a-274">Example:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

<span data-ttu-id="e089a-275">僅 scaffold 選取的資料表，並在具有指定之名稱和命名空間的個別資料夾中建立內容的範例：</span><span class="sxs-lookup"><span data-stu-id="e089a-275">Example that scaffolds only selected tables and creates the context in a separate folder with a specified name and namespace:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext -ContextNamespace New.Namespace
```

<span data-ttu-id="e089a-276">下列範例會從專案的設定讀取連接字串，可能會使用「 [秘密管理員」工具](/aspnet/core/security/app-secrets#secret-manager)來設定。</span><span class="sxs-lookup"><span data-stu-id="e089a-276">The following example reads the connection string from the project's configuration possibly set using the [Secret Manager tool](/aspnet/core/security/app-secrets#secret-manager).</span></span>

```powershell
Scaffold-DbContext "Name=ConnectionStrings.Blogging" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="script-dbcontext"></a><span data-ttu-id="e089a-277">Script-DbContext</span><span class="sxs-lookup"><span data-stu-id="e089a-277">Script-DbContext</span></span>

<span data-ttu-id="e089a-278">從 DbCoNtext 產生 SQL 腳本。</span><span class="sxs-lookup"><span data-stu-id="e089a-278">Generates a SQL script from the DbContext.</span></span> <span data-ttu-id="e089a-279">略過任何遷移。</span><span class="sxs-lookup"><span data-stu-id="e089a-279">Bypasses any migrations.</span></span> <span data-ttu-id="e089a-280">在 EF Core 3.0 中新增。</span><span class="sxs-lookup"><span data-stu-id="e089a-280">Added in EF Core 3.0.</span></span>

<span data-ttu-id="e089a-281">參數：</span><span class="sxs-lookup"><span data-stu-id="e089a-281">Parameters:</span></span>

| <span data-ttu-id="e089a-282">參數</span><span class="sxs-lookup"><span data-stu-id="e089a-282">Parameter</span></span>                      | <span data-ttu-id="e089a-283">說明</span><span class="sxs-lookup"><span data-stu-id="e089a-283">Description</span></span>                      |
| ------------------------------ | -------------------------------- |
| <span data-ttu-id="e089a-284"><nobr>-Output \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="e089a-284"><nobr>-Output \<String></nobr></span></span> | <span data-ttu-id="e089a-285">要寫入結果的檔案。</span><span class="sxs-lookup"><span data-stu-id="e089a-285">The file to write the result to.</span></span> |

<span data-ttu-id="e089a-286">上面列出 [一般參數](#common-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="e089a-286">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="script-migration"></a><span data-ttu-id="e089a-287">Script-Migration</span><span class="sxs-lookup"><span data-stu-id="e089a-287">Script-Migration</span></span>

<span data-ttu-id="e089a-288">產生 SQL 腳本，將所選遷移的所有變更套用到另一個選取的遷移。</span><span class="sxs-lookup"><span data-stu-id="e089a-288">Generates a SQL script that applies all of the changes from one selected migration to another selected migration.</span></span>

<span data-ttu-id="e089a-289">參數：</span><span class="sxs-lookup"><span data-stu-id="e089a-289">Parameters:</span></span>

| <span data-ttu-id="e089a-290">參數</span><span class="sxs-lookup"><span data-stu-id="e089a-290">Parameter</span></span>                    | <span data-ttu-id="e089a-291">說明</span><span class="sxs-lookup"><span data-stu-id="e089a-291">Description</span></span>                                                                                                                                                                                                                |
|:---------------------------- |:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e089a-292">*-從*\<String></span><span class="sxs-lookup"><span data-stu-id="e089a-292">*-From* \<String></span></span>            | <span data-ttu-id="e089a-293">開始遷移。</span><span class="sxs-lookup"><span data-stu-id="e089a-293">The starting migration.</span></span> <span data-ttu-id="e089a-294">遷移可依名稱或識別碼來識別。</span><span class="sxs-lookup"><span data-stu-id="e089a-294">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="e089a-295">數位0是特殊案例，這表示在 *第一次遷移之前* 。</span><span class="sxs-lookup"><span data-stu-id="e089a-295">The number 0 is a special case that means *before the first migration*.</span></span> <span data-ttu-id="e089a-296">預設為 0。</span><span class="sxs-lookup"><span data-stu-id="e089a-296">Defaults to 0.</span></span>                                                              |
| <span data-ttu-id="e089a-297">*-至*\<String></span><span class="sxs-lookup"><span data-stu-id="e089a-297">*-To* \<String></span></span>              | <span data-ttu-id="e089a-298">結束的遷移。</span><span class="sxs-lookup"><span data-stu-id="e089a-298">The ending migration.</span></span> <span data-ttu-id="e089a-299">預設為上次的遷移。</span><span class="sxs-lookup"><span data-stu-id="e089a-299">Defaults to the last migration.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="e089a-300">-等冪</span><span class="sxs-lookup"><span data-stu-id="e089a-300">-Idempotent</span></span>                  | <span data-ttu-id="e089a-301">產生可在任何遷移時用於資料庫的腳本。</span><span class="sxs-lookup"><span data-stu-id="e089a-301">Generate a script that can be used on a database at any migration.</span></span>                                                                                                                                                         |
| <span data-ttu-id="e089a-302"><nobr>-NoTransactions</nobr></span><span class="sxs-lookup"><span data-stu-id="e089a-302"><nobr>-NoTransactions</nobr></span></span> | <span data-ttu-id="e089a-303">不要產生 SQL 交易語句。</span><span class="sxs-lookup"><span data-stu-id="e089a-303">Don't generate SQL transaction statements.</span></span> <span data-ttu-id="e089a-304">在 EF Core 5.0 中新增。</span><span class="sxs-lookup"><span data-stu-id="e089a-304">Added in EF Core 5.0.</span></span>                                                                                                                                                           |
| <span data-ttu-id="e089a-305">-Output \<String></span><span class="sxs-lookup"><span data-stu-id="e089a-305">-Output \<String></span></span>            | <span data-ttu-id="e089a-306">要寫入結果的檔案。</span><span class="sxs-lookup"><span data-stu-id="e089a-306">The file to write the result to.</span></span> <span data-ttu-id="e089a-307">如果省略此參數，則會在建立應用程式的執行時間檔案時，在相同的資料夾中建立檔案，例如： */obj/Debug/netcoreapp2.1/ghbkztfz.sql/* 。</span><span class="sxs-lookup"><span data-stu-id="e089a-307">IF this parameter is omitted, the file is created with a generated name in the same folder as the app's runtime files are created, for example: */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*.</span></span> |

<span data-ttu-id="e089a-308">上面列出 [一般參數](#common-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="e089a-308">The [common parameters](#common-parameters) are listed above.</span></span>

> [!TIP]
> <span data-ttu-id="e089a-309">To、From 和 Output 參數支援 tab 鍵展開。</span><span class="sxs-lookup"><span data-stu-id="e089a-309">The To, From, and Output parameters support tab-expansion.</span></span>

<span data-ttu-id="e089a-310">下列範例會使用遷移名稱，從資料庫建立 InitialCreate 遷移 (的腳本，而不需任何遷移) 。</span><span class="sxs-lookup"><span data-stu-id="e089a-310">The following example creates a script for the InitialCreate migration (from a database without any migrations), using the migration name.</span></span>

```powershell
Script-Migration 0 InitialCreate
```

<span data-ttu-id="e089a-311">下列範例會使用遷移識別碼，在 InitialCreate 遷移之後建立所有遷移的腳本。</span><span class="sxs-lookup"><span data-stu-id="e089a-311">The following example creates a script for all migrations after the InitialCreate migration, using the migration ID.</span></span>

```powershell
Script-Migration 20180904195021_InitialCreate
```

## <a name="update-database"></a><span data-ttu-id="e089a-312">Update-Database</span><span class="sxs-lookup"><span data-stu-id="e089a-312">Update-Database</span></span>

<span data-ttu-id="e089a-313">將資料庫更新為上次遷移或指定的遷移。</span><span class="sxs-lookup"><span data-stu-id="e089a-313">Updates the database to the last migration or to a specified migration.</span></span>

| <span data-ttu-id="e089a-314">參數</span><span class="sxs-lookup"><span data-stu-id="e089a-314">Parameter</span></span>                           | <span data-ttu-id="e089a-315">說明</span><span class="sxs-lookup"><span data-stu-id="e089a-315">Description</span></span>                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e089a-316"><nobr>*-遷移*\<String></nobr></span><span class="sxs-lookup"><span data-stu-id="e089a-316"><nobr>*-Migration* \<String></nobr></span></span> | <span data-ttu-id="e089a-317">目標遷移。</span><span class="sxs-lookup"><span data-stu-id="e089a-317">The target migration.</span></span> <span data-ttu-id="e089a-318">遷移可依名稱或識別碼來識別。</span><span class="sxs-lookup"><span data-stu-id="e089a-318">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="e089a-319">數位0是特殊案例，這表示在 *第一次遷移之前* ，會將所有遷移還原。</span><span class="sxs-lookup"><span data-stu-id="e089a-319">The number 0 is a special case that means *before the first migration* and causes all migrations to be reverted.</span></span> <span data-ttu-id="e089a-320">如果未指定任何遷移，則命令會預設為上次的遷移。</span><span class="sxs-lookup"><span data-stu-id="e089a-320">If no migration is specified, the command defaults to the last migration.</span></span> |
| <span data-ttu-id="e089a-321"><nobr>-連接 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="e089a-321"><nobr>-Connection \<String></nobr></span></span>  | <span data-ttu-id="e089a-322">資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="e089a-322">The connection string to the database.</span></span> <span data-ttu-id="e089a-323">預設為或中所指定 `AddDbContext` 的 `OnConfiguring` 。</span><span class="sxs-lookup"><span data-stu-id="e089a-323">Defaults to the one specified in `AddDbContext` or `OnConfiguring`.</span></span> <span data-ttu-id="e089a-324">在 EF Core 5.0 中新增。</span><span class="sxs-lookup"><span data-stu-id="e089a-324">Added in EF Core 5.0.</span></span>                                                                                                                                |

<span data-ttu-id="e089a-325">上面列出 [一般參數](#common-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="e089a-325">The [common parameters](#common-parameters) are listed above.</span></span>

> [!TIP]
> <span data-ttu-id="e089a-326">遷移參數支援 tab 鍵展開。</span><span class="sxs-lookup"><span data-stu-id="e089a-326">The Migration parameter supports tab-expansion.</span></span>

<span data-ttu-id="e089a-327">下列範例會還原所有的遷移。</span><span class="sxs-lookup"><span data-stu-id="e089a-327">The following example reverts all migrations.</span></span>

```powershell
Update-Database 0
```

<span data-ttu-id="e089a-328">下列範例會將資料庫更新為指定的遷移。</span><span class="sxs-lookup"><span data-stu-id="e089a-328">The following examples update the database to a specified migration.</span></span> <span data-ttu-id="e089a-329">第一個使用遷移名稱，第二個使用遷移識別碼和指定的連接：</span><span class="sxs-lookup"><span data-stu-id="e089a-329">The first uses the migration name and the second uses the migration ID and a specified connection:</span></span>

```powershell
Update-Database InitialCreate
Update-Database 20180904195021_InitialCreate -Connection your_connection_string
```

## <a name="additional-resources"></a><span data-ttu-id="e089a-330">其他資源</span><span class="sxs-lookup"><span data-stu-id="e089a-330">Additional resources</span></span>

* [<span data-ttu-id="e089a-331">移轉</span><span class="sxs-lookup"><span data-stu-id="e089a-331">Migrations</span></span>](xref:core/managing-schemas/migrations/index)
* [<span data-ttu-id="e089a-332">反向工程</span><span class="sxs-lookup"><span data-stu-id="e089a-332">Reverse Engineering</span></span>](xref:core/managing-schemas/scaffolding)
