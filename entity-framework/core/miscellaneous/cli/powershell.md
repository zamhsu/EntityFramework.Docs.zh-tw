---
title: EF Core 工具參考（套件管理員主控台）-EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/18/2018
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: 3893d561ccb7d97f3d9c25d9ea66509ad0f3da75
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370590"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a><span data-ttu-id="5d165-102">Entity Framework Core 工具參考-Visual Studio 中的套件管理員主控台</span><span class="sxs-lookup"><span data-stu-id="5d165-102">Entity Framework Core tools reference - Package Manager Console in Visual Studio</span></span>

<span data-ttu-id="5d165-103">適用于 Entity Framework Core 的套件管理員主控台（PMC）工具可執行設計階段開發工作。</span><span class="sxs-lookup"><span data-stu-id="5d165-103">The Package Manager Console (PMC) tools for Entity Framework Core perform design-time development tasks.</span></span> <span data-ttu-id="5d165-104">例如，他們會針對以現有資料庫為基礎的模型建立[遷移](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0)、套用遷移，以及產生程式碼。</span><span class="sxs-lookup"><span data-stu-id="5d165-104">For example, they create [migrations](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0), apply migrations, and generate code for a model based on an existing database.</span></span> <span data-ttu-id="5d165-105">命令會使用[套件管理員主控台](/nuget/tools/package-manager-console)在 Visual Studio 內執行。</span><span class="sxs-lookup"><span data-stu-id="5d165-105">The commands run inside of Visual Studio using the [Package Manager Console](/nuget/tools/package-manager-console).</span></span> <span data-ttu-id="5d165-106">這些工具會使用 .NET Framework 和 .NET Core 專案。</span><span class="sxs-lookup"><span data-stu-id="5d165-106">These tools work with both .NET Framework and .NET Core projects.</span></span>

<span data-ttu-id="5d165-107">如果您不是使用 Visual Studio，建議改用[EF Core 命令列工具](dotnet.md)。</span><span class="sxs-lookup"><span data-stu-id="5d165-107">If you aren't using Visual Studio, we recommend the [EF Core Command-line Tools](dotnet.md) instead.</span></span> <span data-ttu-id="5d165-108">.NET Core CLI 工具是跨平臺的，而且會在命令提示字元中執行。</span><span class="sxs-lookup"><span data-stu-id="5d165-108">The .NET Core CLI tools are cross-platform and run inside a command prompt.</span></span>

## <a name="installing-the-tools"></a><span data-ttu-id="5d165-109">安裝工具</span><span class="sxs-lookup"><span data-stu-id="5d165-109">Installing the tools</span></span>

<span data-ttu-id="5d165-110">安裝和更新工具的程式在 ASP.NET Core 2.1 + 和更早版本或其他專案類型之間有所不同。</span><span class="sxs-lookup"><span data-stu-id="5d165-110">The procedures for installing and updating the tools differ between ASP.NET Core 2.1+ and earlier versions or other project types.</span></span>

### <a name="aspnet-core-version-21-and-later"></a><span data-ttu-id="5d165-111">ASP.NET Core 2.1 版和更新版本</span><span class="sxs-lookup"><span data-stu-id="5d165-111">ASP.NET Core version 2.1 and later</span></span>

<span data-ttu-id="5d165-112">這些工具會自動包含在 ASP.NET Core 2.1 + 專案中，因為此 `Microsoft.EntityFrameworkCore.Tools` 套件包含在[AspNetCore 應用程式中繼套件](/aspnet/core/fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="5d165-112">The tools are automatically included in an ASP.NET Core 2.1+ project because the `Microsoft.EntityFrameworkCore.Tools` package is included in the [Microsoft.AspNetCore.App metapackage](/aspnet/core/fundamentals/metapackage-app).</span></span>

<span data-ttu-id="5d165-113">因此，您不需要執行任何動作來安裝工具，但是您必須：</span><span class="sxs-lookup"><span data-stu-id="5d165-113">Therefore, you don't have to do anything to install the tools, but you do have to:</span></span>

* <span data-ttu-id="5d165-114">在新專案中使用工具之前，請先還原套件。</span><span class="sxs-lookup"><span data-stu-id="5d165-114">Restore packages before using the tools in a new project.</span></span>
* <span data-ttu-id="5d165-115">安裝套件，將工具更新為較新的版本。</span><span class="sxs-lookup"><span data-stu-id="5d165-115">Install a package to update the tools to a newer version.</span></span>

<span data-ttu-id="5d165-116">若要確定您是取得最新版本的工具，建議您同時執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="5d165-116">To make sure that you're getting the latest version of the tools, we recommend that you also do the following step:</span></span>

* <span data-ttu-id="5d165-117">編輯您的 *.csproj*檔案，並加入一行，指定最新版本的[microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/)套件。</span><span class="sxs-lookup"><span data-stu-id="5d165-117">Edit your *.csproj* file and add a line specifying the latest version of the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) package.</span></span> <span data-ttu-id="5d165-118">例如， *.csproj*檔案可能包含如下所示的 `ItemGroup` ：</span><span class="sxs-lookup"><span data-stu-id="5d165-118">For example, the *.csproj* file might include an `ItemGroup` that looks like this:</span></span>

  ```xml
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="3.1.3" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="3.1.2" />
  </ItemGroup>
  ```

<span data-ttu-id="5d165-119">當您收到類似下列範例的訊息時，請更新工具：</span><span class="sxs-lookup"><span data-stu-id="5d165-119">Update the tools when you get a message like the following example:</span></span>

> <span data-ttu-id="5d165-120">EF Core 工具版本 ' 2.1.1-30846 ' 早于執行時間 ' 2.1.3-rtm-32065 '。</span><span class="sxs-lookup"><span data-stu-id="5d165-120">The EF Core tools version '2.1.1-rtm-30846' is older than that of the runtime '2.1.3-rtm-32065'.</span></span> <span data-ttu-id="5d165-121">更新工具以取得最新功能和錯誤修正。</span><span class="sxs-lookup"><span data-stu-id="5d165-121">Update the tools for the latest features and bug fixes.</span></span>

<span data-ttu-id="5d165-122">若要更新工具：</span><span class="sxs-lookup"><span data-stu-id="5d165-122">To update the tools:</span></span>

* <span data-ttu-id="5d165-123">安裝最新的 .NET Core SDK。</span><span class="sxs-lookup"><span data-stu-id="5d165-123">Install the latest .NET Core SDK.</span></span>
* <span data-ttu-id="5d165-124">將 Visual Studio 更新為最新版本。</span><span class="sxs-lookup"><span data-stu-id="5d165-124">Update Visual Studio to the latest version.</span></span>
* <span data-ttu-id="5d165-125">編輯 *.csproj*檔案，使其包含最新工具套件的套件參考，如先前所示。</span><span class="sxs-lookup"><span data-stu-id="5d165-125">Edit the *.csproj* file so that it includes a package reference to the latest tools package, as shown earlier.</span></span>

### <a name="other-versions-and-project-types"></a><span data-ttu-id="5d165-126">其他版本和專案類型</span><span class="sxs-lookup"><span data-stu-id="5d165-126">Other versions and project types</span></span>

<span data-ttu-id="5d165-127">在 [**套件管理員主控台**] 中執行下列命令，以安裝套件管理員主控台工具：</span><span class="sxs-lookup"><span data-stu-id="5d165-127">Install the Package Manager Console tools by running the following command in **Package Manager Console**:</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="5d165-128">在 [**套件管理員主控台**] 中執行下列命令，以更新工具。</span><span class="sxs-lookup"><span data-stu-id="5d165-128">Update the tools by running the following command in **Package Manager Console**.</span></span>

``` powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a><span data-ttu-id="5d165-129">驗證安裝</span><span class="sxs-lookup"><span data-stu-id="5d165-129">Verify the installation</span></span>

<span data-ttu-id="5d165-130">執行下列命令來確認是否已安裝工具：</span><span class="sxs-lookup"><span data-stu-id="5d165-130">Verify that the tools are installed by running this command:</span></span>

``` powershell
Get-Help about_EntityFrameworkCore
```

<span data-ttu-id="5d165-131">輸出看起來像這樣（它並不會告訴您所使用的工具版本）：</span><span class="sxs-lookup"><span data-stu-id="5d165-131">The output looks like this (it doesn't tell you which version of the tools you're using):</span></span>

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

## <a name="using-the-tools"></a><span data-ttu-id="5d165-132">使用工具</span><span class="sxs-lookup"><span data-stu-id="5d165-132">Using the tools</span></span>

<span data-ttu-id="5d165-133">使用工具之前：</span><span class="sxs-lookup"><span data-stu-id="5d165-133">Before using the tools:</span></span>

* <span data-ttu-id="5d165-134">瞭解目標和啟始專案之間的差異。</span><span class="sxs-lookup"><span data-stu-id="5d165-134">Understand the difference between target and startup project.</span></span>
* <span data-ttu-id="5d165-135">瞭解如何搭配使用工具與 .NET Standard 類別庫。</span><span class="sxs-lookup"><span data-stu-id="5d165-135">Learn how to use the tools with .NET Standard class libraries.</span></span>
* <span data-ttu-id="5d165-136">針對 ASP.NET Core 專案，設定環境。</span><span class="sxs-lookup"><span data-stu-id="5d165-136">For ASP.NET Core projects, set the environment.</span></span>

### <a name="target-and-startup-project"></a><span data-ttu-id="5d165-137">目標和啟始專案</span><span class="sxs-lookup"><span data-stu-id="5d165-137">Target and startup project</span></span>

<span data-ttu-id="5d165-138">這些命令會參考*專案*和*啟始專案*。</span><span class="sxs-lookup"><span data-stu-id="5d165-138">The commands refer to a *project* and a *startup project*.</span></span>

* <span data-ttu-id="5d165-139">*專案*也稱為「*目標專案*」，因為它是命令新增或移除檔案的位置。</span><span class="sxs-lookup"><span data-stu-id="5d165-139">The *project* is also known as the *target project* because it's where the commands add or remove files.</span></span> <span data-ttu-id="5d165-140">根據預設，在 [**套件管理員主控台**] 中選取的**預設專案**是目標專案。</span><span class="sxs-lookup"><span data-stu-id="5d165-140">By default, the **Default project** selected in **Package Manager Console** is the target project.</span></span> <span data-ttu-id="5d165-141">您可以使用選項，將不同的專案指定為目標專案 <nobr>`--project`</nobr> 。</span><span class="sxs-lookup"><span data-stu-id="5d165-141">You can specify a different project as target project by using the <nobr>`--project`</nobr> option.</span></span>

* <span data-ttu-id="5d165-142">*啟始專案*是工具所建立和執行的專案。</span><span class="sxs-lookup"><span data-stu-id="5d165-142">The *startup project* is the one that the tools build and run.</span></span> <span data-ttu-id="5d165-143">這些工具必須在設計階段執行應用程式程式碼，以取得專案的相關資訊，例如資料庫連接字串和模型的設定。</span><span class="sxs-lookup"><span data-stu-id="5d165-143">The tools have to execute application code at design time to get information about the project, such as the database connection string and the configuration of the model.</span></span> <span data-ttu-id="5d165-144">根據預設，**方案總管**中的**啟始專案**是啟始專案。</span><span class="sxs-lookup"><span data-stu-id="5d165-144">By default, the **Startup Project** in **Solution Explorer** is the startup project.</span></span> <span data-ttu-id="5d165-145">您可以使用選項，將不同的專案指定為啟始專案 <nobr>`--startup-project`</nobr> 。</span><span class="sxs-lookup"><span data-stu-id="5d165-145">You can specify a different project as startup project by using the <nobr>`--startup-project`</nobr> option.</span></span>

<span data-ttu-id="5d165-146">啟始專案和目標專案通常是相同的專案。</span><span class="sxs-lookup"><span data-stu-id="5d165-146">The startup project and target project are often the same project.</span></span> <span data-ttu-id="5d165-147">一般情況下，它們是個別的專案，如下所示：</span><span class="sxs-lookup"><span data-stu-id="5d165-147">A typical scenario where they are separate projects is when:</span></span>

* <span data-ttu-id="5d165-148">EF Core 內容和實體類別位於 .NET Core 類別庫中。</span><span class="sxs-lookup"><span data-stu-id="5d165-148">The EF Core context and entity classes are in a .NET Core class library.</span></span>
* <span data-ttu-id="5d165-149">.NET Core 主控台應用程式或 web 應用程式會參考類別庫。</span><span class="sxs-lookup"><span data-stu-id="5d165-149">A .NET Core console app or web app references the class library.</span></span>

<span data-ttu-id="5d165-150">您也可以[將遷移程式碼放在與 EF Core 內容不同的類別庫中](xref:core/managing-schemas/migrations/projects)。</span><span class="sxs-lookup"><span data-stu-id="5d165-150">It's also possible to [put migrations code in a class library separate from the EF Core context](xref:core/managing-schemas/migrations/projects).</span></span>

### <a name="other-target-frameworks"></a><span data-ttu-id="5d165-151">其他目標 framework</span><span class="sxs-lookup"><span data-stu-id="5d165-151">Other target frameworks</span></span>

<span data-ttu-id="5d165-152">套件管理員主控台工具適用于 .NET Core 或 .NET Framework 專案。</span><span class="sxs-lookup"><span data-stu-id="5d165-152">The Package Manager Console tools work with .NET Core or .NET Framework projects.</span></span> <span data-ttu-id="5d165-153">在 .NET Standard 類別庫中具有 EF Core 模型的應用程式，可能沒有 .NET Core 或 .NET Framework 專案。</span><span class="sxs-lookup"><span data-stu-id="5d165-153">Apps that have the EF Core model in a .NET Standard class library might not have a .NET Core or .NET Framework project.</span></span> <span data-ttu-id="5d165-154">例如，這適用于 Xamarin 和通用 Windows 平臺應用程式。</span><span class="sxs-lookup"><span data-stu-id="5d165-154">For example, this is true of Xamarin and Universal Windows Platform apps.</span></span> <span data-ttu-id="5d165-155">在這種情況下，您可以建立 .NET Core 或 .NET Framework 主控台應用程式專案，其目的只是要作為工具的啟始專案。</span><span class="sxs-lookup"><span data-stu-id="5d165-155">In such cases, you can create a .NET Core or .NET Framework console app project whose only purpose is to act as startup project for the tools.</span></span> <span data-ttu-id="5d165-156">專案可以是沒有實際程式碼的虛擬專案， &mdash; 只需要提供工具的目標。</span><span class="sxs-lookup"><span data-stu-id="5d165-156">The project can be a dummy project with no real code &mdash; it is only needed to provide a target for the tooling.</span></span>

<span data-ttu-id="5d165-157">為什麼需要虛擬專案？</span><span class="sxs-lookup"><span data-stu-id="5d165-157">Why is a dummy project required?</span></span> <span data-ttu-id="5d165-158">如先前所述，工具必須在設計階段執行應用程式程式碼。</span><span class="sxs-lookup"><span data-stu-id="5d165-158">As mentioned earlier, the tools have to execute application code at design time.</span></span> <span data-ttu-id="5d165-159">若要這麼做，他們必須使用 .NET Core 或 .NET Framework 執行時間。</span><span class="sxs-lookup"><span data-stu-id="5d165-159">To do that, they need to use the .NET Core or .NET Framework runtime.</span></span> <span data-ttu-id="5d165-160">當 EF Core 模型位於以 .NET Core 或 .NET Framework 為目標的專案中時，EF Core 工具會從專案借用執行時間。</span><span class="sxs-lookup"><span data-stu-id="5d165-160">When the EF Core model is in a project that targets .NET Core or .NET Framework, the EF Core tools borrow the runtime from the project.</span></span> <span data-ttu-id="5d165-161">如果 EF Core 模型位於 .NET Standard 類別庫中，他們就無法這麼做。</span><span class="sxs-lookup"><span data-stu-id="5d165-161">They can't do that if the EF Core model is in a .NET Standard class library.</span></span> <span data-ttu-id="5d165-162">.NET Standard 不是實際的 .NET 執行;這是一組 .NET 部署必須支援之 Api 的規格。</span><span class="sxs-lookup"><span data-stu-id="5d165-162">The .NET Standard is not an actual .NET implementation; it's a specification of a set of APIs that .NET implementations must support.</span></span> <span data-ttu-id="5d165-163">因此 .NET Standard 並不足以讓 EF Core 工具執行應用程式程式碼。</span><span class="sxs-lookup"><span data-stu-id="5d165-163">Therefore .NET Standard is not sufficient for the EF Core tools to execute application code.</span></span> <span data-ttu-id="5d165-164">您建立用來做為啟始專案的虛設專案，會提供可讓工具載入 .NET Standard 類別庫的具體目標平臺。</span><span class="sxs-lookup"><span data-stu-id="5d165-164">The dummy project you create to use as startup project provides a concrete target platform into which the tools can load the .NET Standard class library.</span></span>

### <a name="aspnet-core-environment"></a><span data-ttu-id="5d165-165">ASP.NET Core 環境</span><span class="sxs-lookup"><span data-stu-id="5d165-165">ASP.NET Core environment</span></span>

<span data-ttu-id="5d165-166">若要指定 ASP.NET Core 專案的環境，請在執行命令之前，先設定**env： ASPNETCORE_ENVIRONMENT** 。</span><span class="sxs-lookup"><span data-stu-id="5d165-166">To specify the environment for ASP.NET Core projects, set **env:ASPNETCORE_ENVIRONMENT** before running commands.</span></span>

## <a name="common-parameters"></a><span data-ttu-id="5d165-167">一般參數</span><span class="sxs-lookup"><span data-stu-id="5d165-167">Common parameters</span></span>

<span data-ttu-id="5d165-168">下表顯示所有 EF Core 命令通用的參數：</span><span class="sxs-lookup"><span data-stu-id="5d165-168">The following table shows parameters that are common to all of the EF Core commands:</span></span>

| <span data-ttu-id="5d165-169">參數</span><span class="sxs-lookup"><span data-stu-id="5d165-169">Parameter</span></span>                 | <span data-ttu-id="5d165-170">描述</span><span class="sxs-lookup"><span data-stu-id="5d165-170">Description</span></span>                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="5d165-171">-內容\<String></span><span class="sxs-lookup"><span data-stu-id="5d165-171">-Context \<String></span></span>        | <span data-ttu-id="5d165-172">要使用的 `DbContext` 類別。</span><span class="sxs-lookup"><span data-stu-id="5d165-172">The `DbContext` class to use.</span></span> <span data-ttu-id="5d165-173">僅限類別名稱，或使用命名空間完整限定。</span><span class="sxs-lookup"><span data-stu-id="5d165-173">Class name only or fully qualified with namespaces.</span></span>  <span data-ttu-id="5d165-174">如果省略此參數，EF Core 會尋找內容類別。</span><span class="sxs-lookup"><span data-stu-id="5d165-174">If this parameter is omitted, EF Core finds the context class.</span></span> <span data-ttu-id="5d165-175">如果有多個內容類別，則需要此參數。</span><span class="sxs-lookup"><span data-stu-id="5d165-175">If there are multiple context classes, this parameter is required.</span></span> |
| <span data-ttu-id="5d165-176">-Project\<String></span><span class="sxs-lookup"><span data-stu-id="5d165-176">-Project \<String></span></span>        | <span data-ttu-id="5d165-177">目標專案。</span><span class="sxs-lookup"><span data-stu-id="5d165-177">The target project.</span></span> <span data-ttu-id="5d165-178">如果省略此參數，則會使用 [**封裝管理員主控台**] 的**預設專案**做為目標專案。</span><span class="sxs-lookup"><span data-stu-id="5d165-178">If this parameter is omitted, the **Default project** for **Package Manager Console** is used as the target project.</span></span>                                                                             |
| <span data-ttu-id="5d165-179"><nobr>-啟始專案</nobr>\<String></span><span class="sxs-lookup"><span data-stu-id="5d165-179"><nobr>-StartupProject</nobr> \<String></span></span> | <span data-ttu-id="5d165-180">啟始專案。</span><span class="sxs-lookup"><span data-stu-id="5d165-180">The startup project.</span></span> <span data-ttu-id="5d165-181">如果省略此參數，則會使用 [**方案屬性**] 中的**啟始專案**做為目標專案。</span><span class="sxs-lookup"><span data-stu-id="5d165-181">If this parameter is omitted, the **Startup project** in **Solution properties** is used as the target project.</span></span>                                                                                 |
| <span data-ttu-id="5d165-182">-Verbose</span><span class="sxs-lookup"><span data-stu-id="5d165-182">-Verbose</span></span>                  | <span data-ttu-id="5d165-183">顯示詳細資訊輸出。</span><span class="sxs-lookup"><span data-stu-id="5d165-183">Show verbose output.</span></span>                                                                                                                                                                                                 |

<span data-ttu-id="5d165-184">若要顯示命令的說明資訊，請使用 PowerShell 的 `Get-Help` 命令。</span><span class="sxs-lookup"><span data-stu-id="5d165-184">To show help information about a command, use PowerShell's `Get-Help` command.</span></span>

> [!TIP]
> <span data-ttu-id="5d165-185">CoNtext、Project 和啟始專案參數支援 tab 鍵展開。</span><span class="sxs-lookup"><span data-stu-id="5d165-185">The Context, Project, and StartupProject parameters support tab-expansion.</span></span>

## <a name="add-migration"></a><span data-ttu-id="5d165-186">新增-遷移</span><span class="sxs-lookup"><span data-stu-id="5d165-186">Add-Migration</span></span>

<span data-ttu-id="5d165-187">加入新的遷移。</span><span class="sxs-lookup"><span data-stu-id="5d165-187">Adds a new migration.</span></span>

<span data-ttu-id="5d165-188">參數：</span><span class="sxs-lookup"><span data-stu-id="5d165-188">Parameters:</span></span>

| <span data-ttu-id="5d165-189">參數</span><span class="sxs-lookup"><span data-stu-id="5d165-189">Parameter</span></span>                         | <span data-ttu-id="5d165-190">描述</span><span class="sxs-lookup"><span data-stu-id="5d165-190">Description</span></span>                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="5d165-191"><nobr>-Name\<String><nobr></span><span class="sxs-lookup"><span data-stu-id="5d165-191"><nobr>-Name \<String><nobr></span></span>       | <span data-ttu-id="5d165-192">遷移的名稱。</span><span class="sxs-lookup"><span data-stu-id="5d165-192">The name of the migration.</span></span> <span data-ttu-id="5d165-193">這是位置參數，而且是必要的。</span><span class="sxs-lookup"><span data-stu-id="5d165-193">This is a positional parameter and is required.</span></span>                                              |
| <span data-ttu-id="5d165-194"><nobr>-OutputDir\<String></nobr></span><span class="sxs-lookup"><span data-stu-id="5d165-194"><nobr>-OutputDir \<String></nobr></span></span> | <span data-ttu-id="5d165-195">目錄用來輸出檔案。</span><span class="sxs-lookup"><span data-stu-id="5d165-195">The directory use to output the files.</span></span> <span data-ttu-id="5d165-196">路徑相對於目標專案目錄。</span><span class="sxs-lookup"><span data-stu-id="5d165-196">Paths are relative to the target project directory.</span></span> <span data-ttu-id="5d165-197">預設為「遷移」。</span><span class="sxs-lookup"><span data-stu-id="5d165-197">Defaults to "Migrations".</span></span> |
| <span data-ttu-id="5d165-198"><nobr>-Namespace\<String></nobr></span><span class="sxs-lookup"><span data-stu-id="5d165-198"><nobr>-Namespace \<String></nobr></span></span> | <span data-ttu-id="5d165-199">要用於產生之類別的命名空間。</span><span class="sxs-lookup"><span data-stu-id="5d165-199">The namespace to use for the generated classes.</span></span> <span data-ttu-id="5d165-200">預設為從輸出目錄產生。</span><span class="sxs-lookup"><span data-stu-id="5d165-200">Defaults to generated from the output directory.</span></span> <span data-ttu-id="5d165-201">（可從 EFCore 5.0.0 開始取得）。</span><span class="sxs-lookup"><span data-stu-id="5d165-201">(Available from EFCore 5.0.0 onwards.)</span></span> |

## <a name="drop-database"></a><span data-ttu-id="5d165-202">放置資料庫</span><span class="sxs-lookup"><span data-stu-id="5d165-202">Drop-Database</span></span>

<span data-ttu-id="5d165-203">卸除資料庫。</span><span class="sxs-lookup"><span data-stu-id="5d165-203">Drops the database.</span></span>

<span data-ttu-id="5d165-204">參數：</span><span class="sxs-lookup"><span data-stu-id="5d165-204">Parameters:</span></span>

| <span data-ttu-id="5d165-205">參數</span><span class="sxs-lookup"><span data-stu-id="5d165-205">Parameter</span></span> | <span data-ttu-id="5d165-206">描述</span><span class="sxs-lookup"><span data-stu-id="5d165-206">Description</span></span>                                              |
|:----------|:---------------------------------------------------------|
| <span data-ttu-id="5d165-207">-WhatIf</span><span class="sxs-lookup"><span data-stu-id="5d165-207">-WhatIf</span></span>   | <span data-ttu-id="5d165-208">顯示要捨棄的資料庫，但不要卸載它。</span><span class="sxs-lookup"><span data-stu-id="5d165-208">Show which database would be dropped, but don't drop it.</span></span> |

## <a name="get-dbcontext"></a><span data-ttu-id="5d165-209">DbCoNtext</span><span class="sxs-lookup"><span data-stu-id="5d165-209">Get-DbContext</span></span>

<span data-ttu-id="5d165-210">取得類型的相關資訊 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="5d165-210">Gets information about a `DbContext` type.</span></span>

## <a name="remove-migration"></a><span data-ttu-id="5d165-211">移除移轉</span><span class="sxs-lookup"><span data-stu-id="5d165-211">Remove-Migration</span></span>

<span data-ttu-id="5d165-212">移除上一次的遷移作業（復原已針對遷移完成的程式碼變更）。</span><span class="sxs-lookup"><span data-stu-id="5d165-212">Removes the last migration (rolls back the code changes that were done for the migration).</span></span>

<span data-ttu-id="5d165-213">參數：</span><span class="sxs-lookup"><span data-stu-id="5d165-213">Parameters:</span></span>

| <span data-ttu-id="5d165-214">參數</span><span class="sxs-lookup"><span data-stu-id="5d165-214">Parameter</span></span> | <span data-ttu-id="5d165-215">描述</span><span class="sxs-lookup"><span data-stu-id="5d165-215">Description</span></span>                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| <span data-ttu-id="5d165-216">-Force</span><span class="sxs-lookup"><span data-stu-id="5d165-216">-Force</span></span>    | <span data-ttu-id="5d165-217">還原遷移（復原已套用至資料庫的變更）。</span><span class="sxs-lookup"><span data-stu-id="5d165-217">Revert the migration (roll back the changes that were applied to the database).</span></span> |

## <a name="scaffold-dbcontext"></a><span data-ttu-id="5d165-218">Scaffold-DbCoNtext</span><span class="sxs-lookup"><span data-stu-id="5d165-218">Scaffold-DbContext</span></span>

<span data-ttu-id="5d165-219">為 `DbContext` 資料庫的和實體類型產生程式碼。</span><span class="sxs-lookup"><span data-stu-id="5d165-219">Generates code for a `DbContext` and entity types for a database.</span></span> <span data-ttu-id="5d165-220">為了讓 `Scaffold-DbContext` 產生實體類型，資料庫資料表必須有主鍵。</span><span class="sxs-lookup"><span data-stu-id="5d165-220">In order for `Scaffold-DbContext` to generate an entity type, the database table must have a primary key.</span></span>

<span data-ttu-id="5d165-221">參數：</span><span class="sxs-lookup"><span data-stu-id="5d165-221">Parameters:</span></span>

| <span data-ttu-id="5d165-222">參數</span><span class="sxs-lookup"><span data-stu-id="5d165-222">Parameter</span></span>                          | <span data-ttu-id="5d165-223">描述</span><span class="sxs-lookup"><span data-stu-id="5d165-223">Description</span></span>                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="5d165-224"><nobr>-連接\<String></nobr></span><span class="sxs-lookup"><span data-stu-id="5d165-224"><nobr>-Connection \<String></nobr></span></span> | <span data-ttu-id="5d165-225">資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="5d165-225">The connection string to the database.</span></span> <span data-ttu-id="5d165-226">針對 ASP.NET Core 2.x 專案，此值可以是\*name = \<name of connection string> \*。</span><span class="sxs-lookup"><span data-stu-id="5d165-226">For ASP.NET Core 2.x projects, the value can be *name=\<name of connection string>*.</span></span> <span data-ttu-id="5d165-227">在此情況下，此名稱來自針對專案所設定的設定來源。</span><span class="sxs-lookup"><span data-stu-id="5d165-227">In that case the name comes from the configuration sources that are set up for the project.</span></span> <span data-ttu-id="5d165-228">這是位置參數，而且是必要的。</span><span class="sxs-lookup"><span data-stu-id="5d165-228">This is a positional parameter and is required.</span></span> |
| <span data-ttu-id="5d165-229"><nobr>-提供者\<String></nobr></span><span class="sxs-lookup"><span data-stu-id="5d165-229"><nobr>-Provider \<String></nobr></span></span>   | <span data-ttu-id="5d165-230">要使用的提供者。</span><span class="sxs-lookup"><span data-stu-id="5d165-230">The provider to use.</span></span> <span data-ttu-id="5d165-231">這通常是 NuGet 套件的名稱，例如： `Microsoft.EntityFrameworkCore.SqlServer` 。</span><span class="sxs-lookup"><span data-stu-id="5d165-231">Typically this is the name of the NuGet package, for example: `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="5d165-232">這是位置參數，而且是必要的。</span><span class="sxs-lookup"><span data-stu-id="5d165-232">This is a positional parameter and is required.</span></span>                                                                                           |
| <span data-ttu-id="5d165-233">-OutputDir\<String></span><span class="sxs-lookup"><span data-stu-id="5d165-233">-OutputDir \<String></span></span>               | <span data-ttu-id="5d165-234">要用來放置檔案的目錄。</span><span class="sxs-lookup"><span data-stu-id="5d165-234">The directory to put files in.</span></span> <span data-ttu-id="5d165-235">路徑相對於專案目錄。</span><span class="sxs-lookup"><span data-stu-id="5d165-235">Paths are relative to the project directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="5d165-236">-CoNtextDir\<String></span><span class="sxs-lookup"><span data-stu-id="5d165-236">-ContextDir \<String></span></span>              | <span data-ttu-id="5d165-237">要放置檔案的目錄 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="5d165-237">The directory to put the `DbContext` file in.</span></span> <span data-ttu-id="5d165-238">路徑相對於專案目錄。</span><span class="sxs-lookup"><span data-stu-id="5d165-238">Paths are relative to the project directory.</span></span>                                                                                                                                                               |
| <span data-ttu-id="5d165-239">-Namespace\<String></span><span class="sxs-lookup"><span data-stu-id="5d165-239">-Namespace \<String></span></span>               | <span data-ttu-id="5d165-240">要用於所有產生之類別的命名空間。</span><span class="sxs-lookup"><span data-stu-id="5d165-240">The namespace to use for all generated classes.</span></span> <span data-ttu-id="5d165-241">預設為從根命名空間和輸出目錄產生。</span><span class="sxs-lookup"><span data-stu-id="5d165-241">Defaults to generated from the root namespace and the output directory.</span></span> <span data-ttu-id="5d165-242">（可從 EFCore 5.0.0 開始取得）。</span><span class="sxs-lookup"><span data-stu-id="5d165-242">(Available from EFCore 5.0.0 onwards.)</span></span> |
| <span data-ttu-id="5d165-243">-CoNtextNamespace\<String></span><span class="sxs-lookup"><span data-stu-id="5d165-243">-ContextNamespace \<String></span></span>        | <span data-ttu-id="5d165-244">要用於所產生類別的命名空間 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="5d165-244">The namespace to use for the generated `DbContext` class.</span></span> <span data-ttu-id="5d165-245">注意：覆寫 `-Namespace` 。</span><span class="sxs-lookup"><span data-stu-id="5d165-245">Note: overrides `-Namespace`.</span></span> <span data-ttu-id="5d165-246">（可從 EFCore 5.0.0 開始取得）。</span><span class="sxs-lookup"><span data-stu-id="5d165-246">(Available from EFCore 5.0.0 onwards.)</span></span> |
| <span data-ttu-id="5d165-247">-內容\<String></span><span class="sxs-lookup"><span data-stu-id="5d165-247">-Context \<String></span></span>                 | <span data-ttu-id="5d165-248">`DbContext`要產生的類別名稱。</span><span class="sxs-lookup"><span data-stu-id="5d165-248">The name of the `DbContext` class to generate.</span></span>                                                                                                                                                                                                                          |
| <span data-ttu-id="5d165-249">-架構\<String[]></span><span class="sxs-lookup"><span data-stu-id="5d165-249">-Schemas \<String[]></span></span>               | <span data-ttu-id="5d165-250">要為其產生實體類型的資料表架構。</span><span class="sxs-lookup"><span data-stu-id="5d165-250">The schemas of tables to generate entity types for.</span></span> <span data-ttu-id="5d165-251">如果省略此參數，則會包含所有架構。</span><span class="sxs-lookup"><span data-stu-id="5d165-251">If this parameter is omitted, all schemas are included.</span></span>                                                                                                                                                             |
| <span data-ttu-id="5d165-252">-資料表\<String[]></span><span class="sxs-lookup"><span data-stu-id="5d165-252">-Tables \<String[]></span></span>                | <span data-ttu-id="5d165-253">要為其產生實體類型的資料表。</span><span class="sxs-lookup"><span data-stu-id="5d165-253">The tables to generate entity types for.</span></span> <span data-ttu-id="5d165-254">如果省略此參數，則會包含所有資料表。</span><span class="sxs-lookup"><span data-stu-id="5d165-254">If this parameter is omitted, all tables are included.</span></span>                                                                                                                                                                         |
| <span data-ttu-id="5d165-255">-DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="5d165-255">-DataAnnotations</span></span>                   | <span data-ttu-id="5d165-256">使用屬性來設定模型（可能的話）。</span><span class="sxs-lookup"><span data-stu-id="5d165-256">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="5d165-257">如果省略此參數，則只會使用 Fluent API。</span><span class="sxs-lookup"><span data-stu-id="5d165-257">If this parameter is omitted, only the fluent API is used.</span></span>                                                                                                                                                      |
| <span data-ttu-id="5d165-258">-UseDatabaseNames</span><span class="sxs-lookup"><span data-stu-id="5d165-258">-UseDatabaseNames</span></span>                  | <span data-ttu-id="5d165-259">使用資料表和資料行名稱，就像在資料庫中所出現的一樣。</span><span class="sxs-lookup"><span data-stu-id="5d165-259">Use table and column names exactly as they appear in the database.</span></span> <span data-ttu-id="5d165-260">如果省略這個參數，資料庫名稱就會變更，以更符合 c # 名稱樣式慣例。</span><span class="sxs-lookup"><span data-stu-id="5d165-260">If this parameter is omitted, database names are changed to more closely conform to C# name style conventions.</span></span>                                                                                       |
| <span data-ttu-id="5d165-261">-Force</span><span class="sxs-lookup"><span data-stu-id="5d165-261">-Force</span></span>                             | <span data-ttu-id="5d165-262">覆寫現有檔案。</span><span class="sxs-lookup"><span data-stu-id="5d165-262">Overwrite existing files.</span></span>                                                                                                                                                                                                                                               |
| <span data-ttu-id="5d165-263">-NoOnConfiguring</span><span class="sxs-lookup"><span data-stu-id="5d165-263">-NoOnConfiguring</span></span>                   | <span data-ttu-id="5d165-264">隱藏 `OnConfiguring` 產生的類別中的方法產生 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="5d165-264">Suppresses generation of the `OnConfiguring` method in the generated `DbContext` class.</span></span> <span data-ttu-id="5d165-265">（可從 EFCore 5.0.0 開始取得）。</span><span class="sxs-lookup"><span data-stu-id="5d165-265">(Available from EFCore 5.0.0 onwards.)</span></span> |

<span data-ttu-id="5d165-266">範例：</span><span class="sxs-lookup"><span data-stu-id="5d165-266">Example:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

<span data-ttu-id="5d165-267">僅 scaffold 選取的資料表，並在具有指定之名稱和命名空間的個別資料夾中建立內容的範例：</span><span class="sxs-lookup"><span data-stu-id="5d165-267">Example that scaffolds only selected tables and creates the context in a separate folder with a specified name and namespace:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext -ContextNamespace New.Namespace
```

## <a name="script-migration"></a><span data-ttu-id="5d165-268">腳本-遷移</span><span class="sxs-lookup"><span data-stu-id="5d165-268">Script-Migration</span></span>

<span data-ttu-id="5d165-269">產生 SQL 腳本，將所選遷移的所有變更套用到另一個選取的遷移。</span><span class="sxs-lookup"><span data-stu-id="5d165-269">Generates a SQL script that applies all of the changes from one selected migration to another selected migration.</span></span>

<span data-ttu-id="5d165-270">參數：</span><span class="sxs-lookup"><span data-stu-id="5d165-270">Parameters:</span></span>

| <span data-ttu-id="5d165-271">參數</span><span class="sxs-lookup"><span data-stu-id="5d165-271">Parameter</span></span>                | <span data-ttu-id="5d165-272">描述</span><span class="sxs-lookup"><span data-stu-id="5d165-272">Description</span></span>                                                                                                                                                                                                                |
|:-------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="5d165-273">*-來源*\<String></span><span class="sxs-lookup"><span data-stu-id="5d165-273">*-From* \<String></span></span>        | <span data-ttu-id="5d165-274">開始的遷移。</span><span class="sxs-lookup"><span data-stu-id="5d165-274">The starting migration.</span></span> <span data-ttu-id="5d165-275">可以依名稱或識別碼來識別遷移。</span><span class="sxs-lookup"><span data-stu-id="5d165-275">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="5d165-276">數位0是特殊案例，表示*第一次遷移之前*。</span><span class="sxs-lookup"><span data-stu-id="5d165-276">The number 0 is a special case that means *before the first migration*.</span></span> <span data-ttu-id="5d165-277">預設為 0。</span><span class="sxs-lookup"><span data-stu-id="5d165-277">Defaults to 0.</span></span>                                                              |
| <span data-ttu-id="5d165-278">*-至*\<String></span><span class="sxs-lookup"><span data-stu-id="5d165-278">*-To* \<String></span></span>          | <span data-ttu-id="5d165-279">結束遷移。</span><span class="sxs-lookup"><span data-stu-id="5d165-279">The ending migration.</span></span> <span data-ttu-id="5d165-280">預設為上次遷移。</span><span class="sxs-lookup"><span data-stu-id="5d165-280">Defaults to the last migration.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="5d165-281"><nobr>-等冪</nobr></span><span class="sxs-lookup"><span data-stu-id="5d165-281"><nobr>-Idempotent</nobr></span></span> | <span data-ttu-id="5d165-282">產生可在任何遷移的資料庫上使用的腳本。</span><span class="sxs-lookup"><span data-stu-id="5d165-282">Generate a script that can be used on a database at any migration.</span></span>                                                                                                                                                         |
| <span data-ttu-id="5d165-283">-輸出\<String></span><span class="sxs-lookup"><span data-stu-id="5d165-283">-Output \<String></span></span>        | <span data-ttu-id="5d165-284">要寫入結果的檔案。</span><span class="sxs-lookup"><span data-stu-id="5d165-284">The file to write the result to.</span></span> <span data-ttu-id="5d165-285">如果省略此參數，則會在建立應用程式的執行時間檔案所在的相同資料夾中建立具有所產生名稱的檔案，例如： */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*。</span><span class="sxs-lookup"><span data-stu-id="5d165-285">IF this parameter is omitted, the file is created with a generated name in the same folder as the app's runtime files are created, for example: */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*.</span></span> |

> [!TIP]
> <span data-ttu-id="5d165-286">To、From 和 Output 參數支援 tab 鍵展開。</span><span class="sxs-lookup"><span data-stu-id="5d165-286">The To, From, and Output parameters support tab-expansion.</span></span>

<span data-ttu-id="5d165-287">下列範例會使用遷移名稱來建立 InitialCreate 遷移的腳本。</span><span class="sxs-lookup"><span data-stu-id="5d165-287">The following example creates a script for the InitialCreate migration, using the migration name.</span></span>

```powershell
Script-Migration -To InitialCreate
```

<span data-ttu-id="5d165-288">下列範例會使用遷移識別碼，針對 InitialCreate 遷移之後的所有遷移建立腳本。</span><span class="sxs-lookup"><span data-stu-id="5d165-288">The following example creates a script for all migrations after the InitialCreate migration, using the migration ID.</span></span>

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a><span data-ttu-id="5d165-289">更新-資料庫</span><span class="sxs-lookup"><span data-stu-id="5d165-289">Update-Database</span></span>

<span data-ttu-id="5d165-290">將資料庫更新為上次遷移或指定的遷移。</span><span class="sxs-lookup"><span data-stu-id="5d165-290">Updates the database to the last migration or to a specified migration.</span></span>

| <span data-ttu-id="5d165-291">參數</span><span class="sxs-lookup"><span data-stu-id="5d165-291">Parameter</span></span>                           | <span data-ttu-id="5d165-292">描述</span><span class="sxs-lookup"><span data-stu-id="5d165-292">Description</span></span>                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="5d165-293"><nobr>*-遷移*\<String></nobr></span><span class="sxs-lookup"><span data-stu-id="5d165-293"><nobr>*-Migration* \<String></nobr></span></span> | <span data-ttu-id="5d165-294">目標遷移。</span><span class="sxs-lookup"><span data-stu-id="5d165-294">The target migration.</span></span> <span data-ttu-id="5d165-295">可以依名稱或識別碼來識別遷移。</span><span class="sxs-lookup"><span data-stu-id="5d165-295">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="5d165-296">數位0是特殊案例，表示*第一次遷移之前*，並會還原所有遷移。</span><span class="sxs-lookup"><span data-stu-id="5d165-296">The number 0 is a special case that means *before the first migration* and causes all migrations to be reverted.</span></span> <span data-ttu-id="5d165-297">如果未指定任何遷移，此命令會預設為上次遷移。</span><span class="sxs-lookup"><span data-stu-id="5d165-297">If no migration is specified, the command defaults to the last migration.</span></span> |
| <span data-ttu-id="5d165-298"><nobr>-連接\<String></nobr></span><span class="sxs-lookup"><span data-stu-id="5d165-298"><nobr>-Connection \<String></nobr></span></span>  | <span data-ttu-id="5d165-299">資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="5d165-299">The connection string to the database.</span></span> <span data-ttu-id="5d165-300">預設為或中指定的 `AddDbContext` 值 `OnConfiguring` 。</span><span class="sxs-lookup"><span data-stu-id="5d165-300">Defaults to the one specified in `AddDbContext` or `OnConfiguring`.</span></span> |

> [!TIP]
> <span data-ttu-id="5d165-301">[遷移] 參數支援 tab 鍵展開。</span><span class="sxs-lookup"><span data-stu-id="5d165-301">The Migration parameter supports tab-expansion.</span></span>

<span data-ttu-id="5d165-302">下列範例會還原所有的遷移。</span><span class="sxs-lookup"><span data-stu-id="5d165-302">The following example reverts all migrations.</span></span>

```powershell
Update-Database -Migration 0
```

<span data-ttu-id="5d165-303">下列範例會將資料庫更新為指定的遷移。</span><span class="sxs-lookup"><span data-stu-id="5d165-303">The following examples update the database to a specified migration.</span></span> <span data-ttu-id="5d165-304">第一個使用遷移名稱，第二個使用遷移識別碼和指定的連接：</span><span class="sxs-lookup"><span data-stu-id="5d165-304">The first uses the migration name and the second uses the migration ID and a specified connection:</span></span>

```powershell
Update-Database -Migration InitialCreate
Update-Database -Migration 20180904195021_InitialCreate -Connection your_connection_string
```

## <a name="additional-resources"></a><span data-ttu-id="5d165-305">其他資源</span><span class="sxs-lookup"><span data-stu-id="5d165-305">Additional resources</span></span>

* [<span data-ttu-id="5d165-306">移轉</span><span class="sxs-lookup"><span data-stu-id="5d165-306">Migrations</span></span>](xref:core/managing-schemas/migrations/index)
* [<span data-ttu-id="5d165-307">反向工程</span><span class="sxs-lookup"><span data-stu-id="5d165-307">Reverse Engineering</span></span>](xref:core/managing-schemas/scaffolding)
