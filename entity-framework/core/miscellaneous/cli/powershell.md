---
title: EF Core 工具參考 (封裝管理員主控台) -EF Core
description: Entity Framework Core Visual Studio 封裝管理員主控台的參考指南
author: bricelam
ms.author: bricelam
ms.date: 09/18/2018
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: 84ca41dc08f7bc813ee9491b66fc91b2854c7632
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617866"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a><span data-ttu-id="5591b-103">Visual Studio 中的 Entity Framework Core 工具參考-封裝管理員主控台</span><span class="sxs-lookup"><span data-stu-id="5591b-103">Entity Framework Core tools reference - Package Manager Console in Visual Studio</span></span>

<span data-ttu-id="5591b-104">封裝管理員主控台 (PMC) 工具，以 Entity Framework Core 執行設計階段開發工作。</span><span class="sxs-lookup"><span data-stu-id="5591b-104">The Package Manager Console (PMC) tools for Entity Framework Core perform design-time development tasks.</span></span> <span data-ttu-id="5591b-105">例如，他們會建立 [遷移](/aspnet/core/data/ef-mvc/migrations)、套用遷移，並根據現有的資料庫產生模型的程式碼。</span><span class="sxs-lookup"><span data-stu-id="5591b-105">For example, they create [migrations](/aspnet/core/data/ef-mvc/migrations), apply migrations, and generate code for a model based on an existing database.</span></span> <span data-ttu-id="5591b-106">這些命令會使用 [封裝管理員主控台](/nuget/tools/package-manager-console)在 Visual Studio 內部執行。</span><span class="sxs-lookup"><span data-stu-id="5591b-106">The commands run inside of Visual Studio using the [Package Manager Console](/nuget/tools/package-manager-console).</span></span> <span data-ttu-id="5591b-107">這些工具會使用 .NET Framework 和 .NET Core 專案。</span><span class="sxs-lookup"><span data-stu-id="5591b-107">These tools work with both .NET Framework and .NET Core projects.</span></span>

<span data-ttu-id="5591b-108">如果您不是使用 Visual Studio，建議您改用 [EF Core 命令列工具](xref:core/miscellaneous/cli/dotnet) 。</span><span class="sxs-lookup"><span data-stu-id="5591b-108">If you aren't using Visual Studio, we recommend the [EF Core Command-line Tools](xref:core/miscellaneous/cli/dotnet) instead.</span></span> <span data-ttu-id="5591b-109">.NET Core CLI 工具是跨平臺，並在命令提示字元內執行。</span><span class="sxs-lookup"><span data-stu-id="5591b-109">The .NET Core CLI tools are cross-platform and run inside a command prompt.</span></span>

## <a name="installing-the-tools"></a><span data-ttu-id="5591b-110">安裝工具</span><span class="sxs-lookup"><span data-stu-id="5591b-110">Installing the tools</span></span>

<span data-ttu-id="5591b-111">安裝和更新工具的程式會因 ASP.NET Core 2.1 + 和舊版或其他專案類型而有所不同。</span><span class="sxs-lookup"><span data-stu-id="5591b-111">The procedures for installing and updating the tools differ between ASP.NET Core 2.1+ and earlier versions or other project types.</span></span>

### <a name="aspnet-core-version-21-and-later"></a><span data-ttu-id="5591b-112">ASP.NET Core 2.1 版和更新版本</span><span class="sxs-lookup"><span data-stu-id="5591b-112">ASP.NET Core version 2.1 and later</span></span>

<span data-ttu-id="5591b-113">這些工具會自動包含在 ASP.NET Core 2.1 + 專案中，因為該 `Microsoft.EntityFrameworkCore.Tools` 套件包含在 [AspNetCore 中繼套件](/aspnet/core/fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="5591b-113">The tools are automatically included in an ASP.NET Core 2.1+ project because the `Microsoft.EntityFrameworkCore.Tools` package is included in the [Microsoft.AspNetCore.App metapackage](/aspnet/core/fundamentals/metapackage-app).</span></span>

<span data-ttu-id="5591b-114">因此，您不需要執行任何動作來安裝工具，但是您必須：</span><span class="sxs-lookup"><span data-stu-id="5591b-114">Therefore, you don't have to do anything to install the tools, but you do have to:</span></span>

* <span data-ttu-id="5591b-115">使用新專案中的工具之前，請先還原套件。</span><span class="sxs-lookup"><span data-stu-id="5591b-115">Restore packages before using the tools in a new project.</span></span>
* <span data-ttu-id="5591b-116">安裝套件以將工具更新至較新的版本。</span><span class="sxs-lookup"><span data-stu-id="5591b-116">Install a package to update the tools to a newer version.</span></span>

<span data-ttu-id="5591b-117">若要確定您是取得最新版本的工具，建議您也執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="5591b-117">To make sure that you're getting the latest version of the tools, we recommend that you also do the following step:</span></span>

* <span data-ttu-id="5591b-118">編輯您的 *.csproj* 檔案，並新增指定最新版 [microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) 的行。</span><span class="sxs-lookup"><span data-stu-id="5591b-118">Edit your *.csproj* file and add a line specifying the latest version of the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) package.</span></span> <span data-ttu-id="5591b-119">例如， *.csproj* 檔案可能包含如下所示的 `ItemGroup` ：</span><span class="sxs-lookup"><span data-stu-id="5591b-119">For example, the *.csproj* file might include an `ItemGroup` that looks like this:</span></span>

  ```xml
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="3.1.3" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="3.1.2" />
  </ItemGroup>
  ```

<span data-ttu-id="5591b-120">當您收到類似下列範例的訊息時，請更新工具：</span><span class="sxs-lookup"><span data-stu-id="5591b-120">Update the tools when you get a message like the following example:</span></span>

> <span data-ttu-id="5591b-121">EF Core 工具版本 ' 2.1.1-rtm-30846 ' 早于執行時間 ' 2.1.3-rtm-32065 '。</span><span class="sxs-lookup"><span data-stu-id="5591b-121">The EF Core tools version '2.1.1-rtm-30846' is older than that of the runtime '2.1.3-rtm-32065'.</span></span> <span data-ttu-id="5591b-122">更新工具以取得最新功能和 bug 修正。</span><span class="sxs-lookup"><span data-stu-id="5591b-122">Update the tools for the latest features and bug fixes.</span></span>

<span data-ttu-id="5591b-123">若要更新工具：</span><span class="sxs-lookup"><span data-stu-id="5591b-123">To update the tools:</span></span>

* <span data-ttu-id="5591b-124">安裝最新的 .NET Core SDK。</span><span class="sxs-lookup"><span data-stu-id="5591b-124">Install the latest .NET Core SDK.</span></span>
* <span data-ttu-id="5591b-125">將 Visual Studio 更新為最新版本。</span><span class="sxs-lookup"><span data-stu-id="5591b-125">Update Visual Studio to the latest version.</span></span>
* <span data-ttu-id="5591b-126">編輯 *.csproj* 檔案，使其包含最新工具套件的套件參考，如先前所示。</span><span class="sxs-lookup"><span data-stu-id="5591b-126">Edit the *.csproj* file so that it includes a package reference to the latest tools package, as shown earlier.</span></span>

### <a name="other-versions-and-project-types"></a><span data-ttu-id="5591b-127">其他版本和專案類型</span><span class="sxs-lookup"><span data-stu-id="5591b-127">Other versions and project types</span></span>

<span data-ttu-id="5591b-128">在 **封裝管理員主控台**中執行下列命令，以安裝封裝管理員主控台工具：</span><span class="sxs-lookup"><span data-stu-id="5591b-128">Install the Package Manager Console tools by running the following command in **Package Manager Console**:</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="5591b-129">在 **封裝管理員主控台**中執行下列命令，以更新工具。</span><span class="sxs-lookup"><span data-stu-id="5591b-129">Update the tools by running the following command in **Package Manager Console**.</span></span>

``` powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a><span data-ttu-id="5591b-130">驗證安裝</span><span class="sxs-lookup"><span data-stu-id="5591b-130">Verify the installation</span></span>

<span data-ttu-id="5591b-131">執行下列命令，確認是否已安裝這些工具：</span><span class="sxs-lookup"><span data-stu-id="5591b-131">Verify that the tools are installed by running this command:</span></span>

``` powershell
Get-Help about_EntityFrameworkCore
```

<span data-ttu-id="5591b-132">輸出看起來會像這樣 (不會告訴您所使用的工具是哪個版本) ：</span><span class="sxs-lookup"><span data-stu-id="5591b-132">The output looks like this (it doesn't tell you which version of the tools you're using):</span></span>

```console

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

## <a name="using-the-tools"></a><span data-ttu-id="5591b-133">使用工具</span><span class="sxs-lookup"><span data-stu-id="5591b-133">Using the tools</span></span>

<span data-ttu-id="5591b-134">使用工具之前：</span><span class="sxs-lookup"><span data-stu-id="5591b-134">Before using the tools:</span></span>

* <span data-ttu-id="5591b-135">瞭解目標和啟始專案之間的差異。</span><span class="sxs-lookup"><span data-stu-id="5591b-135">Understand the difference between target and startup project.</span></span>
* <span data-ttu-id="5591b-136">瞭解如何使用 .NET Standard 類別庫的工具。</span><span class="sxs-lookup"><span data-stu-id="5591b-136">Learn how to use the tools with .NET Standard class libraries.</span></span>
* <span data-ttu-id="5591b-137">針對 ASP.NET Core 專案，請設定環境。</span><span class="sxs-lookup"><span data-stu-id="5591b-137">For ASP.NET Core projects, set the environment.</span></span>

### <a name="target-and-startup-project"></a><span data-ttu-id="5591b-138">目標和啟始專案</span><span class="sxs-lookup"><span data-stu-id="5591b-138">Target and startup project</span></span>

<span data-ttu-id="5591b-139">這些命令會參考 *專案* 和 *啟始專案*。</span><span class="sxs-lookup"><span data-stu-id="5591b-139">The commands refer to a *project* and a *startup project*.</span></span>

* <span data-ttu-id="5591b-140">*專案*也稱為*目標專案*，因為這是命令新增或移除檔案的位置。</span><span class="sxs-lookup"><span data-stu-id="5591b-140">The *project* is also known as the *target project* because it's where the commands add or remove files.</span></span> <span data-ttu-id="5591b-141">依預設，**封裝管理員主控台**中選取的**預設專案**是目標專案。</span><span class="sxs-lookup"><span data-stu-id="5591b-141">By default, the **Default project** selected in **Package Manager Console** is the target project.</span></span> <span data-ttu-id="5591b-142">您可以使用選項，將不同的專案指定為目標專案 <nobr>`--project`</nobr> 。</span><span class="sxs-lookup"><span data-stu-id="5591b-142">You can specify a different project as target project by using the <nobr>`--project`</nobr> option.</span></span>

* <span data-ttu-id="5591b-143">*啟始專案*是工具建立和執行的專案。</span><span class="sxs-lookup"><span data-stu-id="5591b-143">The *startup project* is the one that the tools build and run.</span></span> <span data-ttu-id="5591b-144">這些工具必須在設計階段執行應用程式程式碼，以取得專案的相關資訊，例如資料庫連接字串和模型的設定。</span><span class="sxs-lookup"><span data-stu-id="5591b-144">The tools have to execute application code at design time to get information about the project, such as the database connection string and the configuration of the model.</span></span> <span data-ttu-id="5591b-145">根據預設，**方案總管**中的**啟始專案**是啟始專案。</span><span class="sxs-lookup"><span data-stu-id="5591b-145">By default, the **Startup Project** in **Solution Explorer** is the startup project.</span></span> <span data-ttu-id="5591b-146">您可以使用選項，將不同的專案指定為啟始專案 <nobr>`--startup-project`</nobr> 。</span><span class="sxs-lookup"><span data-stu-id="5591b-146">You can specify a different project as startup project by using the <nobr>`--startup-project`</nobr> option.</span></span>

<span data-ttu-id="5591b-147">啟始專案和目標專案通常是相同的專案。</span><span class="sxs-lookup"><span data-stu-id="5591b-147">The startup project and target project are often the same project.</span></span> <span data-ttu-id="5591b-148">一般情況下，它們是不同的專案：</span><span class="sxs-lookup"><span data-stu-id="5591b-148">A typical scenario where they are separate projects is when:</span></span>

* <span data-ttu-id="5591b-149">EF Core 內容和實體類別位於 .NET Core 類別庫中。</span><span class="sxs-lookup"><span data-stu-id="5591b-149">The EF Core context and entity classes are in a .NET Core class library.</span></span>
* <span data-ttu-id="5591b-150">.NET Core 主控台應用程式或 web 應用程式會參考類別庫。</span><span class="sxs-lookup"><span data-stu-id="5591b-150">A .NET Core console app or web app references the class library.</span></span>

<span data-ttu-id="5591b-151">您也可以將與 [EF Core 內容分開的類別庫中的遷移程式碼](xref:core/managing-schemas/migrations/projects)。</span><span class="sxs-lookup"><span data-stu-id="5591b-151">It's also possible to [put migrations code in a class library separate from the EF Core context](xref:core/managing-schemas/migrations/projects).</span></span>

### <a name="other-target-frameworks"></a><span data-ttu-id="5591b-152">其他目標 framework</span><span class="sxs-lookup"><span data-stu-id="5591b-152">Other target frameworks</span></span>

<span data-ttu-id="5591b-153">封裝管理員主控台工具適用于 .NET Core 或 .NET Framework 專案。</span><span class="sxs-lookup"><span data-stu-id="5591b-153">The Package Manager Console tools work with .NET Core or .NET Framework projects.</span></span> <span data-ttu-id="5591b-154">在 .NET Standard 類別庫中具有 EF Core 模型的應用程式可能沒有 .NET Core 或 .NET Framework 專案。</span><span class="sxs-lookup"><span data-stu-id="5591b-154">Apps that have the EF Core model in a .NET Standard class library might not have a .NET Core or .NET Framework project.</span></span> <span data-ttu-id="5591b-155">例如，這適用于 Xamarin 和通用 Windows 平臺應用程式。</span><span class="sxs-lookup"><span data-stu-id="5591b-155">For example, this is true of Xamarin and Universal Windows Platform apps.</span></span> <span data-ttu-id="5591b-156">在這種情況下，您可以建立 .NET Core 或 .NET Framework 主控台應用程式專案，其唯一目的是作為工具的啟始專案。</span><span class="sxs-lookup"><span data-stu-id="5591b-156">In such cases, you can create a .NET Core or .NET Framework console app project whose only purpose is to act as startup project for the tools.</span></span> <span data-ttu-id="5591b-157">專案可以是沒有真正程式碼的虛擬專案， &mdash; 只需要提供工具的目標。</span><span class="sxs-lookup"><span data-stu-id="5591b-157">The project can be a dummy project with no real code &mdash; it is only needed to provide a target for the tooling.</span></span>

<span data-ttu-id="5591b-158">為什麼需要虛擬專案？</span><span class="sxs-lookup"><span data-stu-id="5591b-158">Why is a dummy project required?</span></span> <span data-ttu-id="5591b-159">如先前所述，這些工具必須在設計階段執行應用程式程式碼。</span><span class="sxs-lookup"><span data-stu-id="5591b-159">As mentioned earlier, the tools have to execute application code at design time.</span></span> <span data-ttu-id="5591b-160">若要這樣做，他們必須使用 .NET Core 或 .NET Framework 執行時間。</span><span class="sxs-lookup"><span data-stu-id="5591b-160">To do that, they need to use the .NET Core or .NET Framework runtime.</span></span> <span data-ttu-id="5591b-161">當 EF Core 模型位於以 .NET Core 或 .NET Framework 為目標的專案中時，EF Core 工具會從專案借用執行時間。</span><span class="sxs-lookup"><span data-stu-id="5591b-161">When the EF Core model is in a project that targets .NET Core or .NET Framework, the EF Core tools borrow the runtime from the project.</span></span> <span data-ttu-id="5591b-162">如果 EF Core 模型位於 .NET Standard 類別庫中，則無法這麼做。</span><span class="sxs-lookup"><span data-stu-id="5591b-162">They can't do that if the EF Core model is in a .NET Standard class library.</span></span> <span data-ttu-id="5591b-163">.NET Standard 不是實際的 .NET 執行;它是一組 .NET 開發人員必須支援的 Api 的規格。</span><span class="sxs-lookup"><span data-stu-id="5591b-163">The .NET Standard is not an actual .NET implementation; it's a specification of a set of APIs that .NET implementations must support.</span></span> <span data-ttu-id="5591b-164">因此 .NET Standard 不足以執行應用程式程式碼 EF Core 工具。</span><span class="sxs-lookup"><span data-stu-id="5591b-164">Therefore .NET Standard is not sufficient for the EF Core tools to execute application code.</span></span> <span data-ttu-id="5591b-165">您建立用來做為啟始專案的虛擬專案提供可讓工具載入 .NET Standard 類別庫的具體目標平臺。</span><span class="sxs-lookup"><span data-stu-id="5591b-165">The dummy project you create to use as startup project provides a concrete target platform into which the tools can load the .NET Standard class library.</span></span>

### <a name="aspnet-core-environment"></a><span data-ttu-id="5591b-166">ASP.NET Core 環境</span><span class="sxs-lookup"><span data-stu-id="5591b-166">ASP.NET Core environment</span></span>

<span data-ttu-id="5591b-167">若要指定 ASP.NET Core 專案的環境，請先設定 **env： ASPNETCORE_ENVIRONMENT** ，再執行命令。</span><span class="sxs-lookup"><span data-stu-id="5591b-167">To specify the environment for ASP.NET Core projects, set **env:ASPNETCORE_ENVIRONMENT** before running commands.</span></span>

## <a name="common-parameters"></a><span data-ttu-id="5591b-168">一般參數</span><span class="sxs-lookup"><span data-stu-id="5591b-168">Common parameters</span></span>

<span data-ttu-id="5591b-169">下表顯示所有 EF Core 命令通用的參數：</span><span class="sxs-lookup"><span data-stu-id="5591b-169">The following table shows parameters that are common to all of the EF Core commands:</span></span>

| <span data-ttu-id="5591b-170">參數</span><span class="sxs-lookup"><span data-stu-id="5591b-170">Parameter</span></span>                 | <span data-ttu-id="5591b-171">描述</span><span class="sxs-lookup"><span data-stu-id="5591b-171">Description</span></span>                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="5591b-172">-CoNtext \<String></span><span class="sxs-lookup"><span data-stu-id="5591b-172">-Context \<String></span></span>        | <span data-ttu-id="5591b-173">要使用的 `DbContext` 類別。</span><span class="sxs-lookup"><span data-stu-id="5591b-173">The `DbContext` class to use.</span></span> <span data-ttu-id="5591b-174">僅限類別名稱或完整限定命名空間。</span><span class="sxs-lookup"><span data-stu-id="5591b-174">Class name only or fully qualified with namespaces.</span></span>  <span data-ttu-id="5591b-175">如果省略此參數，EF Core 會尋找內容類別。</span><span class="sxs-lookup"><span data-stu-id="5591b-175">If this parameter is omitted, EF Core finds the context class.</span></span> <span data-ttu-id="5591b-176">如果有多個內容類別，則需要此參數。</span><span class="sxs-lookup"><span data-stu-id="5591b-176">If there are multiple context classes, this parameter is required.</span></span> |
| <span data-ttu-id="5591b-177">-Project \<String></span><span class="sxs-lookup"><span data-stu-id="5591b-177">-Project \<String></span></span>        | <span data-ttu-id="5591b-178">目標專案。</span><span class="sxs-lookup"><span data-stu-id="5591b-178">The target project.</span></span> <span data-ttu-id="5591b-179">如果省略此參數，則會使用**封裝管理員主控台**的**預設專案**做為目標專案。</span><span class="sxs-lookup"><span data-stu-id="5591b-179">If this parameter is omitted, the **Default project** for **Package Manager Console** is used as the target project.</span></span>                                                                             |
| <span data-ttu-id="5591b-180"><nobr>-啟始專案</nobr>\<String></span><span class="sxs-lookup"><span data-stu-id="5591b-180"><nobr>-StartupProject</nobr> \<String></span></span> | <span data-ttu-id="5591b-181">啟始專案。</span><span class="sxs-lookup"><span data-stu-id="5591b-181">The startup project.</span></span> <span data-ttu-id="5591b-182">如果省略此參數，則會使用**方案屬性**中的**啟始專案**做為目標專案。</span><span class="sxs-lookup"><span data-stu-id="5591b-182">If this parameter is omitted, the **Startup project** in **Solution properties** is used as the target project.</span></span>                                                                                 |
| <span data-ttu-id="5591b-183">-Verbose</span><span class="sxs-lookup"><span data-stu-id="5591b-183">-Verbose</span></span>                  | <span data-ttu-id="5591b-184">顯示詳細資訊輸出。</span><span class="sxs-lookup"><span data-stu-id="5591b-184">Show verbose output.</span></span>                                                                                                                                                                                                 |

<span data-ttu-id="5591b-185">若要顯示命令的說明資訊，請使用 PowerShell 的 `Get-Help` 命令。</span><span class="sxs-lookup"><span data-stu-id="5591b-185">To show help information about a command, use PowerShell's `Get-Help` command.</span></span>

> [!TIP]
> <span data-ttu-id="5591b-186">CoNtext、Project 和啟始專案參數支援 tab 鍵展開。</span><span class="sxs-lookup"><span data-stu-id="5591b-186">The Context, Project, and StartupProject parameters support tab-expansion.</span></span>

## <a name="add-migration"></a><span data-ttu-id="5591b-187">新增-遷移</span><span class="sxs-lookup"><span data-stu-id="5591b-187">Add-Migration</span></span>

<span data-ttu-id="5591b-188">加入新的遷移。</span><span class="sxs-lookup"><span data-stu-id="5591b-188">Adds a new migration.</span></span>

<span data-ttu-id="5591b-189">參數：</span><span class="sxs-lookup"><span data-stu-id="5591b-189">Parameters:</span></span>

| <span data-ttu-id="5591b-190">參數</span><span class="sxs-lookup"><span data-stu-id="5591b-190">Parameter</span></span>                         | <span data-ttu-id="5591b-191">描述</span><span class="sxs-lookup"><span data-stu-id="5591b-191">Description</span></span>                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="5591b-192"><nobr>-Name \<String><nobr></span><span class="sxs-lookup"><span data-stu-id="5591b-192"><nobr>-Name \<String><nobr></span></span>       | <span data-ttu-id="5591b-193">遷移的名稱。</span><span class="sxs-lookup"><span data-stu-id="5591b-193">The name of the migration.</span></span> <span data-ttu-id="5591b-194">這是位置參數，而且是必要的。</span><span class="sxs-lookup"><span data-stu-id="5591b-194">This is a positional parameter and is required.</span></span>                                              |
| <span data-ttu-id="5591b-195"><nobr>-OutputDir \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="5591b-195"><nobr>-OutputDir \<String></nobr></span></span> | <span data-ttu-id="5591b-196">用來輸出檔案的目錄。</span><span class="sxs-lookup"><span data-stu-id="5591b-196">The directory use to output the files.</span></span> <span data-ttu-id="5591b-197">路徑是相對於目標專案目錄。</span><span class="sxs-lookup"><span data-stu-id="5591b-197">Paths are relative to the target project directory.</span></span> <span data-ttu-id="5591b-198">預設為「遷移」。</span><span class="sxs-lookup"><span data-stu-id="5591b-198">Defaults to "Migrations".</span></span> |
| <span data-ttu-id="5591b-199"><nobr>-命名空間 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="5591b-199"><nobr>-Namespace \<String></nobr></span></span> | <span data-ttu-id="5591b-200">要用於產生之類別的命名空間。</span><span class="sxs-lookup"><span data-stu-id="5591b-200">The namespace to use for the generated classes.</span></span> <span data-ttu-id="5591b-201">預設為從輸出目錄產生。</span><span class="sxs-lookup"><span data-stu-id="5591b-201">Defaults to generated from the output directory.</span></span> <span data-ttu-id="5591b-202"> (可從 EFCore 5.0.0 開始取得。 ) </span><span class="sxs-lookup"><span data-stu-id="5591b-202">(Available from EFCore 5.0.0 onwards.)</span></span> |

## <a name="drop-database"></a><span data-ttu-id="5591b-203">捨棄資料庫</span><span class="sxs-lookup"><span data-stu-id="5591b-203">Drop-Database</span></span>

<span data-ttu-id="5591b-204">卸除資料庫。</span><span class="sxs-lookup"><span data-stu-id="5591b-204">Drops the database.</span></span>

<span data-ttu-id="5591b-205">參數：</span><span class="sxs-lookup"><span data-stu-id="5591b-205">Parameters:</span></span>

| <span data-ttu-id="5591b-206">參數</span><span class="sxs-lookup"><span data-stu-id="5591b-206">Parameter</span></span> | <span data-ttu-id="5591b-207">描述</span><span class="sxs-lookup"><span data-stu-id="5591b-207">Description</span></span>                                              |
|:----------|:---------------------------------------------------------|
| <span data-ttu-id="5591b-208">-WhatIf</span><span class="sxs-lookup"><span data-stu-id="5591b-208">-WhatIf</span></span>   | <span data-ttu-id="5591b-209">顯示要卸載的資料庫，但不要卸載它。</span><span class="sxs-lookup"><span data-stu-id="5591b-209">Show which database would be dropped, but don't drop it.</span></span> |

## <a name="get-dbcontext"></a><span data-ttu-id="5591b-210">DbCoNtext</span><span class="sxs-lookup"><span data-stu-id="5591b-210">Get-DbContext</span></span>

<span data-ttu-id="5591b-211">取得型別的相關資訊 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="5591b-211">Gets information about a `DbContext` type.</span></span>

## <a name="remove-migration"></a><span data-ttu-id="5591b-212">移除移轉</span><span class="sxs-lookup"><span data-stu-id="5591b-212">Remove-Migration</span></span>

<span data-ttu-id="5591b-213">移除最後一個遷移 (復原針對遷移) 所做的程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="5591b-213">Removes the last migration (rolls back the code changes that were done for the migration).</span></span>

<span data-ttu-id="5591b-214">參數：</span><span class="sxs-lookup"><span data-stu-id="5591b-214">Parameters:</span></span>

| <span data-ttu-id="5591b-215">參數</span><span class="sxs-lookup"><span data-stu-id="5591b-215">Parameter</span></span> | <span data-ttu-id="5591b-216">描述</span><span class="sxs-lookup"><span data-stu-id="5591b-216">Description</span></span>                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| <span data-ttu-id="5591b-217">-Force</span><span class="sxs-lookup"><span data-stu-id="5591b-217">-Force</span></span>    | <span data-ttu-id="5591b-218">還原遷移 (復原已套用至資料庫) 的變更。</span><span class="sxs-lookup"><span data-stu-id="5591b-218">Revert the migration (roll back the changes that were applied to the database).</span></span> |

## <a name="scaffold-dbcontext"></a><span data-ttu-id="5591b-219">Scaffold-DbCoNtext</span><span class="sxs-lookup"><span data-stu-id="5591b-219">Scaffold-DbContext</span></span>

<span data-ttu-id="5591b-220">`DbContext`針對資料庫的和實體類型產生程式碼。</span><span class="sxs-lookup"><span data-stu-id="5591b-220">Generates code for a `DbContext` and entity types for a database.</span></span> <span data-ttu-id="5591b-221">為了 `Scaffold-DbContext` 產生實體型別，資料庫資料表必須有主鍵。</span><span class="sxs-lookup"><span data-stu-id="5591b-221">In order for `Scaffold-DbContext` to generate an entity type, the database table must have a primary key.</span></span>

<span data-ttu-id="5591b-222">參數：</span><span class="sxs-lookup"><span data-stu-id="5591b-222">Parameters:</span></span>

| <span data-ttu-id="5591b-223">參數</span><span class="sxs-lookup"><span data-stu-id="5591b-223">Parameter</span></span>                          | <span data-ttu-id="5591b-224">描述</span><span class="sxs-lookup"><span data-stu-id="5591b-224">Description</span></span>                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="5591b-225"><nobr>-連接 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="5591b-225"><nobr>-Connection \<String></nobr></span></span> | <span data-ttu-id="5591b-226">資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="5591b-226">The connection string to the database.</span></span> <span data-ttu-id="5591b-227">針對 ASP.NET Core 2.x 專案，值可以是\*名稱 = \<name of connection string> \*。</span><span class="sxs-lookup"><span data-stu-id="5591b-227">For ASP.NET Core 2.x projects, the value can be *name=\<name of connection string>*.</span></span> <span data-ttu-id="5591b-228">在該情況下，名稱來自為專案設定的設定來源。</span><span class="sxs-lookup"><span data-stu-id="5591b-228">In that case the name comes from the configuration sources that are set up for the project.</span></span> <span data-ttu-id="5591b-229">這是位置參數，而且是必要的。</span><span class="sxs-lookup"><span data-stu-id="5591b-229">This is a positional parameter and is required.</span></span> |
| <span data-ttu-id="5591b-230"><nobr>-提供者 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="5591b-230"><nobr>-Provider \<String></nobr></span></span>   | <span data-ttu-id="5591b-231">要使用的提供者。</span><span class="sxs-lookup"><span data-stu-id="5591b-231">The provider to use.</span></span> <span data-ttu-id="5591b-232">這通常是 NuGet 套件的名稱，例如： `Microsoft.EntityFrameworkCore.SqlServer` 。</span><span class="sxs-lookup"><span data-stu-id="5591b-232">Typically this is the name of the NuGet package, for example: `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="5591b-233">這是位置參數，而且是必要的。</span><span class="sxs-lookup"><span data-stu-id="5591b-233">This is a positional parameter and is required.</span></span>                                                                                           |
| <span data-ttu-id="5591b-234">-OutputDir \<String></span><span class="sxs-lookup"><span data-stu-id="5591b-234">-OutputDir \<String></span></span>               | <span data-ttu-id="5591b-235">要放置檔案的目錄。</span><span class="sxs-lookup"><span data-stu-id="5591b-235">The directory to put files in.</span></span> <span data-ttu-id="5591b-236">路徑是相對於專案目錄。</span><span class="sxs-lookup"><span data-stu-id="5591b-236">Paths are relative to the project directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="5591b-237">-CoNtextDir \<String></span><span class="sxs-lookup"><span data-stu-id="5591b-237">-ContextDir \<String></span></span>              | <span data-ttu-id="5591b-238">要放置檔案的目錄 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="5591b-238">The directory to put the `DbContext` file in.</span></span> <span data-ttu-id="5591b-239">路徑是相對於專案目錄。</span><span class="sxs-lookup"><span data-stu-id="5591b-239">Paths are relative to the project directory.</span></span>                                                                                                                                                               |
| <span data-ttu-id="5591b-240">-命名空間 \<String></span><span class="sxs-lookup"><span data-stu-id="5591b-240">-Namespace \<String></span></span>               | <span data-ttu-id="5591b-241">要用於所有產生之類別的命名空間。</span><span class="sxs-lookup"><span data-stu-id="5591b-241">The namespace to use for all generated classes.</span></span> <span data-ttu-id="5591b-242">預設為從根命名空間和輸出目錄產生。</span><span class="sxs-lookup"><span data-stu-id="5591b-242">Defaults to generated from the root namespace and the output directory.</span></span> <span data-ttu-id="5591b-243"> (可從 EFCore 5.0.0 開始取得。 ) </span><span class="sxs-lookup"><span data-stu-id="5591b-243">(Available from EFCore 5.0.0 onwards.)</span></span> |
| <span data-ttu-id="5591b-244">-CoNtextNamespace \<String></span><span class="sxs-lookup"><span data-stu-id="5591b-244">-ContextNamespace \<String></span></span>        | <span data-ttu-id="5591b-245">要用於產生之類別的命名空間 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="5591b-245">The namespace to use for the generated `DbContext` class.</span></span> <span data-ttu-id="5591b-246">注意：覆寫 `-Namespace` 。</span><span class="sxs-lookup"><span data-stu-id="5591b-246">Note: overrides `-Namespace`.</span></span> <span data-ttu-id="5591b-247"> (可從 EFCore 5.0.0 開始取得。 ) </span><span class="sxs-lookup"><span data-stu-id="5591b-247">(Available from EFCore 5.0.0 onwards.)</span></span> |
| <span data-ttu-id="5591b-248">-CoNtext \<String></span><span class="sxs-lookup"><span data-stu-id="5591b-248">-Context \<String></span></span>                 | <span data-ttu-id="5591b-249">`DbContext`要產生的類別名稱。</span><span class="sxs-lookup"><span data-stu-id="5591b-249">The name of the `DbContext` class to generate.</span></span>                                                                                                                                                                                                                          |
| <span data-ttu-id="5591b-250">-架構 \<String[]></span><span class="sxs-lookup"><span data-stu-id="5591b-250">-Schemas \<String[]></span></span>               | <span data-ttu-id="5591b-251">要產生之實體類型的資料表架構。</span><span class="sxs-lookup"><span data-stu-id="5591b-251">The schemas of tables to generate entity types for.</span></span> <span data-ttu-id="5591b-252">如果省略此參數，則會包含所有架構。</span><span class="sxs-lookup"><span data-stu-id="5591b-252">If this parameter is omitted, all schemas are included.</span></span>                                                                                                                                                             |
| <span data-ttu-id="5591b-253">-資料表 \<String[]></span><span class="sxs-lookup"><span data-stu-id="5591b-253">-Tables \<String[]></span></span>                | <span data-ttu-id="5591b-254">要產生之實體類型的資料表。</span><span class="sxs-lookup"><span data-stu-id="5591b-254">The tables to generate entity types for.</span></span> <span data-ttu-id="5591b-255">如果省略此參數，則會包含所有資料表。</span><span class="sxs-lookup"><span data-stu-id="5591b-255">If this parameter is omitted, all tables are included.</span></span>                                                                                                                                                                         |
| <span data-ttu-id="5591b-256">-DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="5591b-256">-DataAnnotations</span></span>                   | <span data-ttu-id="5591b-257">您可以使用屬性來設定模型 (可能的) 。</span><span class="sxs-lookup"><span data-stu-id="5591b-257">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="5591b-258">如果省略此參數，則只會使用流暢的 API。</span><span class="sxs-lookup"><span data-stu-id="5591b-258">If this parameter is omitted, only the fluent API is used.</span></span>                                                                                                                                                      |
| <span data-ttu-id="5591b-259">-UseDatabaseNames</span><span class="sxs-lookup"><span data-stu-id="5591b-259">-UseDatabaseNames</span></span>                  | <span data-ttu-id="5591b-260">使用資料表和資料行名稱，如同它們出現在資料庫中一樣。</span><span class="sxs-lookup"><span data-stu-id="5591b-260">Use table and column names exactly as they appear in the database.</span></span> <span data-ttu-id="5591b-261">如果省略此參數，則會變更資料庫名稱，以更緊密符合 c # 名稱樣式慣例。</span><span class="sxs-lookup"><span data-stu-id="5591b-261">If this parameter is omitted, database names are changed to more closely conform to C# name style conventions.</span></span>                                                                                       |
| <span data-ttu-id="5591b-262">-Force</span><span class="sxs-lookup"><span data-stu-id="5591b-262">-Force</span></span>                             | <span data-ttu-id="5591b-263">覆寫現有檔案。</span><span class="sxs-lookup"><span data-stu-id="5591b-263">Overwrite existing files.</span></span>                                                                                                                                                                                                                                               |
| <span data-ttu-id="5591b-264">-NoOnConfiguring</span><span class="sxs-lookup"><span data-stu-id="5591b-264">-NoOnConfiguring</span></span>                   | <span data-ttu-id="5591b-265">隱藏 `OnConfiguring` 產生的類別中的方法產生 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="5591b-265">Suppresses generation of the `OnConfiguring` method in the generated `DbContext` class.</span></span> <span data-ttu-id="5591b-266"> (可從 EFCore 5.0.0 開始取得。 ) </span><span class="sxs-lookup"><span data-stu-id="5591b-266">(Available from EFCore 5.0.0 onwards.)</span></span> |

<span data-ttu-id="5591b-267">範例：</span><span class="sxs-lookup"><span data-stu-id="5591b-267">Example:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

<span data-ttu-id="5591b-268">僅 scaffold 選取的資料表，並在具有指定之名稱和命名空間的個別資料夾中建立內容的範例：</span><span class="sxs-lookup"><span data-stu-id="5591b-268">Example that scaffolds only selected tables and creates the context in a separate folder with a specified name and namespace:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext -ContextNamespace New.Namespace
```

## <a name="script-migration"></a><span data-ttu-id="5591b-269">腳本-遷移</span><span class="sxs-lookup"><span data-stu-id="5591b-269">Script-Migration</span></span>

<span data-ttu-id="5591b-270">產生 SQL 腳本，將所選遷移的所有變更套用到另一個選取的遷移。</span><span class="sxs-lookup"><span data-stu-id="5591b-270">Generates a SQL script that applies all of the changes from one selected migration to another selected migration.</span></span>

<span data-ttu-id="5591b-271">參數：</span><span class="sxs-lookup"><span data-stu-id="5591b-271">Parameters:</span></span>

| <span data-ttu-id="5591b-272">參數</span><span class="sxs-lookup"><span data-stu-id="5591b-272">Parameter</span></span>                | <span data-ttu-id="5591b-273">描述</span><span class="sxs-lookup"><span data-stu-id="5591b-273">Description</span></span>                                                                                                                                                                                                                |
|:-------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="5591b-274">*-從*\<String></span><span class="sxs-lookup"><span data-stu-id="5591b-274">*-From* \<String></span></span>        | <span data-ttu-id="5591b-275">開始遷移。</span><span class="sxs-lookup"><span data-stu-id="5591b-275">The starting migration.</span></span> <span data-ttu-id="5591b-276">遷移可依名稱或識別碼來識別。</span><span class="sxs-lookup"><span data-stu-id="5591b-276">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="5591b-277">數位0是特殊案例，這表示在 *第一次遷移之前*。</span><span class="sxs-lookup"><span data-stu-id="5591b-277">The number 0 is a special case that means *before the first migration*.</span></span> <span data-ttu-id="5591b-278">預設為 0。</span><span class="sxs-lookup"><span data-stu-id="5591b-278">Defaults to 0.</span></span>                                                              |
| <span data-ttu-id="5591b-279">*-至*\<String></span><span class="sxs-lookup"><span data-stu-id="5591b-279">*-To* \<String></span></span>          | <span data-ttu-id="5591b-280">結束的遷移。</span><span class="sxs-lookup"><span data-stu-id="5591b-280">The ending migration.</span></span> <span data-ttu-id="5591b-281">預設為上次的遷移。</span><span class="sxs-lookup"><span data-stu-id="5591b-281">Defaults to the last migration.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="5591b-282"><nobr>-等冪</nobr></span><span class="sxs-lookup"><span data-stu-id="5591b-282"><nobr>-Idempotent</nobr></span></span> | <span data-ttu-id="5591b-283">產生可在任何遷移時用於資料庫的腳本。</span><span class="sxs-lookup"><span data-stu-id="5591b-283">Generate a script that can be used on a database at any migration.</span></span>                                                                                                                                                         |
| <span data-ttu-id="5591b-284">-Output \<String></span><span class="sxs-lookup"><span data-stu-id="5591b-284">-Output \<String></span></span>        | <span data-ttu-id="5591b-285">要寫入結果的檔案。</span><span class="sxs-lookup"><span data-stu-id="5591b-285">The file to write the result to.</span></span> <span data-ttu-id="5591b-286">如果省略此參數，則會在建立應用程式的執行時間檔案時，在相同的資料夾中建立檔案，例如： */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*。</span><span class="sxs-lookup"><span data-stu-id="5591b-286">IF this parameter is omitted, the file is created with a generated name in the same folder as the app's runtime files are created, for example: */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*.</span></span> |

> [!TIP]
> <span data-ttu-id="5591b-287">To、From 和 Output 參數支援 tab 鍵展開。</span><span class="sxs-lookup"><span data-stu-id="5591b-287">The To, From, and Output parameters support tab-expansion.</span></span>

<span data-ttu-id="5591b-288">下列範例會使用遷移名稱，建立 InitialCreate 遷移的腳本。</span><span class="sxs-lookup"><span data-stu-id="5591b-288">The following example creates a script for the InitialCreate migration, using the migration name.</span></span>

```powershell
Script-Migration -To InitialCreate
```

<span data-ttu-id="5591b-289">下列範例會使用遷移識別碼，在 InitialCreate 遷移之後建立所有遷移的腳本。</span><span class="sxs-lookup"><span data-stu-id="5591b-289">The following example creates a script for all migrations after the InitialCreate migration, using the migration ID.</span></span>

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a><span data-ttu-id="5591b-290">更新-資料庫</span><span class="sxs-lookup"><span data-stu-id="5591b-290">Update-Database</span></span>

<span data-ttu-id="5591b-291">將資料庫更新為上次遷移或指定的遷移。</span><span class="sxs-lookup"><span data-stu-id="5591b-291">Updates the database to the last migration or to a specified migration.</span></span>

| <span data-ttu-id="5591b-292">參數</span><span class="sxs-lookup"><span data-stu-id="5591b-292">Parameter</span></span>                           | <span data-ttu-id="5591b-293">描述</span><span class="sxs-lookup"><span data-stu-id="5591b-293">Description</span></span>                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="5591b-294"><nobr>*-遷移*\<String></nobr></span><span class="sxs-lookup"><span data-stu-id="5591b-294"><nobr>*-Migration* \<String></nobr></span></span> | <span data-ttu-id="5591b-295">目標遷移。</span><span class="sxs-lookup"><span data-stu-id="5591b-295">The target migration.</span></span> <span data-ttu-id="5591b-296">遷移可依名稱或識別碼來識別。</span><span class="sxs-lookup"><span data-stu-id="5591b-296">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="5591b-297">數位0是特殊案例，這表示在 *第一次遷移之前* ，會將所有遷移還原。</span><span class="sxs-lookup"><span data-stu-id="5591b-297">The number 0 is a special case that means *before the first migration* and causes all migrations to be reverted.</span></span> <span data-ttu-id="5591b-298">如果未指定任何遷移，則命令會預設為上次的遷移。</span><span class="sxs-lookup"><span data-stu-id="5591b-298">If no migration is specified, the command defaults to the last migration.</span></span> |
| <span data-ttu-id="5591b-299"><nobr>-連接 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="5591b-299"><nobr>-Connection \<String></nobr></span></span>  | <span data-ttu-id="5591b-300">資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="5591b-300">The connection string to the database.</span></span> <span data-ttu-id="5591b-301">預設為或中所指定 `AddDbContext` 的 `OnConfiguring` 。</span><span class="sxs-lookup"><span data-stu-id="5591b-301">Defaults to the one specified in `AddDbContext` or `OnConfiguring`.</span></span> |

> [!TIP]
> <span data-ttu-id="5591b-302">遷移參數支援 tab 鍵展開。</span><span class="sxs-lookup"><span data-stu-id="5591b-302">The Migration parameter supports tab-expansion.</span></span>

<span data-ttu-id="5591b-303">下列範例會還原所有的遷移。</span><span class="sxs-lookup"><span data-stu-id="5591b-303">The following example reverts all migrations.</span></span>

```powershell
Update-Database -Migration 0
```

<span data-ttu-id="5591b-304">下列範例會將資料庫更新為指定的遷移。</span><span class="sxs-lookup"><span data-stu-id="5591b-304">The following examples update the database to a specified migration.</span></span> <span data-ttu-id="5591b-305">第一個使用遷移名稱，第二個使用遷移識別碼和指定的連接：</span><span class="sxs-lookup"><span data-stu-id="5591b-305">The first uses the migration name and the second uses the migration ID and a specified connection:</span></span>

```powershell
Update-Database -Migration InitialCreate
Update-Database -Migration 20180904195021_InitialCreate -Connection your_connection_string
```

## <a name="additional-resources"></a><span data-ttu-id="5591b-306">其他資源</span><span class="sxs-lookup"><span data-stu-id="5591b-306">Additional resources</span></span>

* [<span data-ttu-id="5591b-307">移轉</span><span class="sxs-lookup"><span data-stu-id="5591b-307">Migrations</span></span>](xref:core/managing-schemas/migrations/index)
* [<span data-ttu-id="5591b-308">反向工程</span><span class="sxs-lookup"><span data-stu-id="5591b-308">Reverse Engineering</span></span>](xref:core/managing-schemas/scaffolding)
