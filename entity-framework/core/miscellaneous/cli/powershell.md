---
title: EF Core 工具參考 (Package Manager Console)-EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/18/2018
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: cb05e3fb66adf96f8a6778711a76520d0be24c71
ms.sourcegitcommit: 645785187ae23ddf7d7b0642c7a4da5ffb0c7f30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2019
ms.locfileid: "58419766"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a><span data-ttu-id="01c4b-102">Entity Framework Core 工具的參考-Visual Studio 中的套件管理員主控台</span><span class="sxs-lookup"><span data-stu-id="01c4b-102">Entity Framework Core tools reference - Package Manager Console in Visual Studio</span></span>

<span data-ttu-id="01c4b-103">Entity Framework Core 套件管理員主控台 (PMC) 工具會執行設計階段開發工作。</span><span class="sxs-lookup"><span data-stu-id="01c4b-103">The Package Manager Console (PMC) tools for Entity Framework Core perform design-time development tasks.</span></span> <span data-ttu-id="01c4b-104">例如，建立[移轉](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0#introduction-to-migrations)、 套用移轉，並產生程式碼在現有的資料庫為基礎的模型。</span><span class="sxs-lookup"><span data-stu-id="01c4b-104">For example, they create [migrations](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0#introduction-to-migrations), apply migrations, and generate code for a model based on an existing database.</span></span> <span data-ttu-id="01c4b-105">使用 Visual Studio 內執行的命令[Package Manager Console](/nuget/tools/package-manager-console)。</span><span class="sxs-lookup"><span data-stu-id="01c4b-105">The commands run inside of Visual Studio using the [Package Manager Console](/nuget/tools/package-manager-console).</span></span> <span data-ttu-id="01c4b-106">這些工具會使用 .NET Framework 和 .NET Core 專案。</span><span class="sxs-lookup"><span data-stu-id="01c4b-106">These tools work with both .NET Framework and .NET Core projects.</span></span>

<span data-ttu-id="01c4b-107">如果您未使用 Visual Studio，我們建議您[EF Core 命令列工具](dotnet.md)改。</span><span class="sxs-lookup"><span data-stu-id="01c4b-107">If you aren't using Visual Studio, we recommend the [EF Core Command-line Tools](dotnet.md) instead.</span></span> <span data-ttu-id="01c4b-108">CLI 工具是跨平台和在命令提示字元內執行。</span><span class="sxs-lookup"><span data-stu-id="01c4b-108">The CLI tools are cross-platform and run inside a command prompt.</span></span>

## <a name="installing-the-tools"></a><span data-ttu-id="01c4b-109">安裝工具</span><span class="sxs-lookup"><span data-stu-id="01c4b-109">Installing the tools</span></span>

<span data-ttu-id="01c4b-110">安裝和更新工具的程序是根據 ASP.NET Core 2.1 + 和更早版本或其他專案類型而不同。</span><span class="sxs-lookup"><span data-stu-id="01c4b-110">The procedures for installing and updating the tools differ between ASP.NET Core 2.1+ and earlier versions or other project types.</span></span>

### <a name="aspnet-core-version-21-and-later"></a><span data-ttu-id="01c4b-111">ASP.NET Core 2.1 和更新版本</span><span class="sxs-lookup"><span data-stu-id="01c4b-111">ASP.NET Core version 2.1 and later</span></span>

<span data-ttu-id="01c4b-112">工具會自動包含在 ASP.NET Core 2.1 + 專案因為`Microsoft.EntityFrameworkCore.Tools`套件包含在[Microsoft.AspNetCore.App 中繼套件](/aspnet/core/fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="01c4b-112">The tools are automatically included in an ASP.NET Core 2.1+ project because the `Microsoft.EntityFrameworkCore.Tools` package is included in the [Microsoft.AspNetCore.App metapackage](/aspnet/core/fundamentals/metapackage-app).</span></span>

<span data-ttu-id="01c4b-113">因此，您不必採取任何動作來安裝工具，但您需要：</span><span class="sxs-lookup"><span data-stu-id="01c4b-113">Therefore, you don't have to do anything to install the tools, but you do have to:</span></span>
* <span data-ttu-id="01c4b-114">新的專案中使用工具之前先還原套件。</span><span class="sxs-lookup"><span data-stu-id="01c4b-114">Restore packages before using the tools in a new project.</span></span>
* <span data-ttu-id="01c4b-115">將封裝安裝到更新為較新版本的工具。</span><span class="sxs-lookup"><span data-stu-id="01c4b-115">Install a package to update the tools to a newer version.</span></span>

<span data-ttu-id="01c4b-116">若要確定您取得最新版本的工具，我們建議，您也可以下列步驟：</span><span class="sxs-lookup"><span data-stu-id="01c4b-116">To make sure that you're getting the latest version of the tools, we recommend that you also do the following step:</span></span>

* <span data-ttu-id="01c4b-117">編輯您 *.csproj*檔案，並新增一行，指定最新版[Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/)封裝。</span><span class="sxs-lookup"><span data-stu-id="01c4b-117">Edit your *.csproj* file and add a line specifying the latest version of the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) package.</span></span> <span data-ttu-id="01c4b-118">例如， *.csproj*檔案可能包含`ItemGroup`看起來像這樣：</span><span class="sxs-lookup"><span data-stu-id="01c4b-118">For example, the *.csproj* file might include an `ItemGroup` that looks like this:</span></span>

  ```xml
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="2.1.3" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="2.1.1" />
  </ItemGroup>
  ```

<span data-ttu-id="01c4b-119">更新工具，當您收到訊息，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="01c4b-119">Update the tools when you get a message like the following example:</span></span>

> <span data-ttu-id="01c4b-120">EF Core 工具版本 '2.1.1-rtm-30846' 會超過執行階段 '2.1.3-rtm-32065'。</span><span class="sxs-lookup"><span data-stu-id="01c4b-120">The EF Core tools version '2.1.1-rtm-30846' is older than that of the runtime '2.1.3-rtm-32065'.</span></span> <span data-ttu-id="01c4b-121">更新適用於最新的功能和 bug 修正的工具。</span><span class="sxs-lookup"><span data-stu-id="01c4b-121">Update the tools for the latest features and bug fixes.</span></span>

<span data-ttu-id="01c4b-122">若要更新的工具：</span><span class="sxs-lookup"><span data-stu-id="01c4b-122">To update the tools:</span></span>
* <span data-ttu-id="01c4b-123">安裝最新的.NET Core SDK。</span><span class="sxs-lookup"><span data-stu-id="01c4b-123">Install the latest .NET Core SDK.</span></span>
* <span data-ttu-id="01c4b-124">Visual Studio 更新為最新版本。</span><span class="sxs-lookup"><span data-stu-id="01c4b-124">Update Visual Studio to the latest version.</span></span>
* <span data-ttu-id="01c4b-125">編輯 *.csproj*檔案使其包含最新的工具套件的套件參考，如先前所示。</span><span class="sxs-lookup"><span data-stu-id="01c4b-125">Edit the *.csproj* file so that it includes a package reference to the latest tools package, as shown earlier.</span></span>

### <a name="other-versions-and-project-types"></a><span data-ttu-id="01c4b-126">其他版本和專案類型</span><span class="sxs-lookup"><span data-stu-id="01c4b-126">Other versions and project types</span></span>

<span data-ttu-id="01c4b-127">在中執行下列命令安裝套件管理員主控台工具**Package Manager Console**:</span><span class="sxs-lookup"><span data-stu-id="01c4b-127">Install the Package Manager Console tools by running the following command in **Package Manager Console**:</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="01c4b-128">在中執行下列命令更新工具**Package Manager Console**。</span><span class="sxs-lookup"><span data-stu-id="01c4b-128">Update the tools by running the following command in **Package Manager Console**.</span></span>

``` powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a><span data-ttu-id="01c4b-129">確認安裝</span><span class="sxs-lookup"><span data-stu-id="01c4b-129">Verify the installation</span></span>

<span data-ttu-id="01c4b-130">確認安裝工具時，則執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="01c4b-130">Verify that the tools are installed by running this command:</span></span>

``` powershell
Get-Help about_EntityFrameworkCore
```

<span data-ttu-id="01c4b-131">輸出看起來像這樣 （它不會告訴您正在使用的工具版本）：</span><span class="sxs-lookup"><span data-stu-id="01c4b-131">The output looks like this (it doesn't tell you which version of the tools you're using):</span></span>

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

## <a name="using-the-tools"></a><span data-ttu-id="01c4b-132">使用的工具</span><span class="sxs-lookup"><span data-stu-id="01c4b-132">Using the tools</span></span>

<span data-ttu-id="01c4b-133">之前使用的工具：</span><span class="sxs-lookup"><span data-stu-id="01c4b-133">Before using the tools:</span></span>
* <span data-ttu-id="01c4b-134">了解目標和啟動專案之間的差異。</span><span class="sxs-lookup"><span data-stu-id="01c4b-134">Understand the difference between target and startup project.</span></span>
* <span data-ttu-id="01c4b-135">了解如何使用.NET Standard 類別庫的工具。</span><span class="sxs-lookup"><span data-stu-id="01c4b-135">Learn how to use the tools with .NET Standard class libraries.</span></span>
* <span data-ttu-id="01c4b-136">針對 ASP.NET Core 專案，設定環境。</span><span class="sxs-lookup"><span data-stu-id="01c4b-136">For ASP.NET Core projects, set the environment.</span></span>

### <a name="target-and-startup-project"></a><span data-ttu-id="01c4b-137">目標和啟動專案</span><span class="sxs-lookup"><span data-stu-id="01c4b-137">Target and startup project</span></span>

<span data-ttu-id="01c4b-138">命令是指*專案*並*啟始專案*。</span><span class="sxs-lookup"><span data-stu-id="01c4b-138">The commands refer to a *project* and a *startup project*.</span></span>

* <span data-ttu-id="01c4b-139">*專案*也稱為*目標專案*因為它是命令新增或移除檔案的位置。</span><span class="sxs-lookup"><span data-stu-id="01c4b-139">The *project* is also known as the *target project* because it's where the commands add or remove files.</span></span> <span data-ttu-id="01c4b-140">根據預設，**預設專案**中選取**Package Manager Console**是目標專案。</span><span class="sxs-lookup"><span data-stu-id="01c4b-140">By default, the **Default project** selected in **Package Manager Console** is the target project.</span></span> <span data-ttu-id="01c4b-141">您也可以使用為目標的專案指定不同的專案<nobr> `--project` </nobr>選項。</span><span class="sxs-lookup"><span data-stu-id="01c4b-141">You can specify a different project as target project by using the <nobr>`--project`</nobr> option.</span></span>

* <span data-ttu-id="01c4b-142">*啟始專案*是指的工具建置和執行。</span><span class="sxs-lookup"><span data-stu-id="01c4b-142">The *startup project* is the one that the tools build and run.</span></span> <span data-ttu-id="01c4b-143">工具必須執行的應用程式程式碼，在設計階段，以取得專案中，例如資料庫連接字串和設定模型的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="01c4b-143">The tools have to execute application code at design time to get information about the project, such as the database connection string and the configuration of the model.</span></span> <span data-ttu-id="01c4b-144">根據預設，**啟始專案**中**方案總管 中**是啟始專案。</span><span class="sxs-lookup"><span data-stu-id="01c4b-144">By default, the **Startup Project** in **Solution Explorer** is the startup project.</span></span> <span data-ttu-id="01c4b-145">您也可以使用為啟始專案指定不同的專案<nobr> `--startup-project` </nobr>選項。</span><span class="sxs-lookup"><span data-stu-id="01c4b-145">You can specify a different project as startup project by using the <nobr>`--startup-project`</nobr> option.</span></span>

<span data-ttu-id="01c4b-146">啟始專案和目標專案通常是相同的專案。</span><span class="sxs-lookup"><span data-stu-id="01c4b-146">The startup project and target project are often the same project.</span></span> <span data-ttu-id="01c4b-147">典型的案例，其中兩者就會不同的專案時：</span><span class="sxs-lookup"><span data-stu-id="01c4b-147">A typical scenario where they are separate projects is when:</span></span>

* <span data-ttu-id="01c4b-148">EF Core 內容和實體類別是.NET Core 類別庫中。</span><span class="sxs-lookup"><span data-stu-id="01c4b-148">The EF Core context and entity classes are in a .NET Core class library.</span></span>
* <span data-ttu-id="01c4b-149">.NET Core 主控台應用程式或 web 應用程式參考類別庫。</span><span class="sxs-lookup"><span data-stu-id="01c4b-149">A .NET Core console app or web app references the class library.</span></span>

<span data-ttu-id="01c4b-150">它也可[移轉程式碼置於不同於 EF Core 內容的類別庫](xref:core/managing-schemas/migrations/projects)。</span><span class="sxs-lookup"><span data-stu-id="01c4b-150">It's also possible to [put migrations code in a class library separate from the EF Core context](xref:core/managing-schemas/migrations/projects).</span></span>

### <a name="other-target-frameworks"></a><span data-ttu-id="01c4b-151">其他的目標架構</span><span class="sxs-lookup"><span data-stu-id="01c4b-151">Other target frameworks</span></span>

<span data-ttu-id="01c4b-152">套件管理員主控台工具會使用.NET Core 或.NET Framework 的專案。</span><span class="sxs-lookup"><span data-stu-id="01c4b-152">The Package Manager Console tools work with .NET Core or .NET Framework projects.</span></span> <span data-ttu-id="01c4b-153">.NET Standard 類別庫中有 EF Core 模型的應用程式可能沒有將.NET Core 或.NET Framework 專案。</span><span class="sxs-lookup"><span data-stu-id="01c4b-153">Apps that have the EF Core model in a .NET Standard class library might not have a .NET Core or .NET Framework project.</span></span> <span data-ttu-id="01c4b-154">比方說，這是 Xamarin 與通用 Windows 平台的應用程式，則為 true。</span><span class="sxs-lookup"><span data-stu-id="01c4b-154">For example, this is true of Xamarin and Universal Windows Platform apps.</span></span> <span data-ttu-id="01c4b-155">在此情況下，您可以建立.NET Core 或.NET Framework 主控台應用程式專案的唯一目的是要做為啟始專案的工具。</span><span class="sxs-lookup"><span data-stu-id="01c4b-155">In such cases, you can create a .NET Core or .NET Framework console app project whose only purpose is to act as startup project for the tools.</span></span> <span data-ttu-id="01c4b-156">專案可以是使用實際沒有程式碼的虛設專案&mdash;只需要用它來提供這項工具的目標。</span><span class="sxs-lookup"><span data-stu-id="01c4b-156">The project can be a dummy project with no real code &mdash; it is only needed to provide a target for the tooling.</span></span>

<span data-ttu-id="01c4b-157">為何需要一個 dummy 專案？</span><span class="sxs-lookup"><span data-stu-id="01c4b-157">Why is a dummy project required?</span></span> <span data-ttu-id="01c4b-158">如先前所述，這些工具必須在設計階段中執行應用程式程式碼。</span><span class="sxs-lookup"><span data-stu-id="01c4b-158">As mentioned earlier, the tools have to execute application code at design time.</span></span> <span data-ttu-id="01c4b-159">若要這樣做，請他們需要使用.NET Core 或.NET Framework 執行階段。</span><span class="sxs-lookup"><span data-stu-id="01c4b-159">To do that, they need to use the .NET Core or .NET Framework runtime.</span></span> <span data-ttu-id="01c4b-160">.NET Core 或.NET Framework 為目標的專案中的 EF Core 模型時，EF Core 工具會借用從專案的執行階段。</span><span class="sxs-lookup"><span data-stu-id="01c4b-160">When the EF Core model is in a project that targets .NET Core or .NET Framework, the EF Core tools borrow the runtime from the project.</span></span> <span data-ttu-id="01c4b-161">如果是.NET Standard 類別庫中的 EF Core 模型，它們不能這麼做。</span><span class="sxs-lookup"><span data-stu-id="01c4b-161">They can't do that if the EF Core model is in a .NET Standard class library.</span></span> <span data-ttu-id="01c4b-162">.NET Standard 並不是實際的.NET 實作;它是一組.NET 實作必須支援的 Api 的規格。</span><span class="sxs-lookup"><span data-stu-id="01c4b-162">The .NET Standard is not an actual .NET implementation; it's a specification of a set of APIs that .NET implementations must support.</span></span> <span data-ttu-id="01c4b-163">因此.NET Standard 是不夠的 EF Core 工具來執行應用程式程式碼。</span><span class="sxs-lookup"><span data-stu-id="01c4b-163">Therefore .NET Standard is not sufficient for the EF Core tools to execute application code.</span></span> <span data-ttu-id="01c4b-164">您建立來做為啟始專案的虛設專案提供具體的目標平台工具可以在其中載入.NET Standard 類別庫。</span><span class="sxs-lookup"><span data-stu-id="01c4b-164">The dummy project you create to use as startup project provides a concrete target platform into which the tools can load the .NET Standard class library.</span></span>

### <a name="aspnet-core-environment"></a><span data-ttu-id="01c4b-165">ASP.NET Core 環境</span><span class="sxs-lookup"><span data-stu-id="01c4b-165">ASP.NET Core environment</span></span>

<span data-ttu-id="01c4b-166">指定 ASP.NET Core 專案的環境，請設定**env:ASPNETCORE_ENVIRONMENT**之前執行命令。</span><span class="sxs-lookup"><span data-stu-id="01c4b-166">To specify the environment for ASP.NET Core projects, set **env:ASPNETCORE_ENVIRONMENT** before running commands.</span></span>

## <a name="common-parameters"></a><span data-ttu-id="01c4b-167">一般參數</span><span class="sxs-lookup"><span data-stu-id="01c4b-167">Common parameters</span></span>

<span data-ttu-id="01c4b-168">下表顯示通用於所有的 EF Core 命令的參數：</span><span class="sxs-lookup"><span data-stu-id="01c4b-168">The following table shows parameters that are common to all of the EF Core commands:</span></span>

| <span data-ttu-id="01c4b-169">參數</span><span class="sxs-lookup"><span data-stu-id="01c4b-169">Parameter</span></span>                 | <span data-ttu-id="01c4b-170">描述</span><span class="sxs-lookup"><span data-stu-id="01c4b-170">Description</span></span>                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="01c4b-171">-Context \<String></span><span class="sxs-lookup"><span data-stu-id="01c4b-171">-Context \<String></span></span>        | <span data-ttu-id="01c4b-172">要使用的 `DbContext` 類別。</span><span class="sxs-lookup"><span data-stu-id="01c4b-172">The `DbContext` class to use.</span></span> <span data-ttu-id="01c4b-173">只有或與命名空間完整限定的類別名稱。</span><span class="sxs-lookup"><span data-stu-id="01c4b-173">Class name only or fully qualified with namespaces.</span></span>  <span data-ttu-id="01c4b-174">如果省略這個參數時，EF Core 會尋找內容類別。</span><span class="sxs-lookup"><span data-stu-id="01c4b-174">If this parameter is omitted, EF Core finds the context class.</span></span> <span data-ttu-id="01c4b-175">如果有多個內容類別，這是必要參數。</span><span class="sxs-lookup"><span data-stu-id="01c4b-175">If there are multiple context classes, this parameter is required.</span></span> |
| <span data-ttu-id="01c4b-176">-Project \<String></span><span class="sxs-lookup"><span data-stu-id="01c4b-176">-Project \<String></span></span>        | <span data-ttu-id="01c4b-177">目標專案。</span><span class="sxs-lookup"><span data-stu-id="01c4b-177">The target project.</span></span> <span data-ttu-id="01c4b-178">如果省略這個參數，則**預設專案**for **Package Manager Console**做為目標的專案。</span><span class="sxs-lookup"><span data-stu-id="01c4b-178">If this parameter is omitted, the **Default project** for **Package Manager Console** is used as the target project.</span></span>                                                                             |
| <span data-ttu-id="01c4b-179">-StartupProject \<String></span><span class="sxs-lookup"><span data-stu-id="01c4b-179">-StartupProject \<String></span></span> | <span data-ttu-id="01c4b-180">啟始專案。</span><span class="sxs-lookup"><span data-stu-id="01c4b-180">The startup project.</span></span> <span data-ttu-id="01c4b-181">如果省略這個參數，則**啟始專案**中**方案屬性**做為目標的專案。</span><span class="sxs-lookup"><span data-stu-id="01c4b-181">If this parameter is omitted, the **Startup project** in **Solution properties** is used as the target project.</span></span>                                                                                 |
| <span data-ttu-id="01c4b-182">-Verbose</span><span class="sxs-lookup"><span data-stu-id="01c4b-182">-Verbose</span></span>                  | <span data-ttu-id="01c4b-183">顯示詳細資訊輸出。</span><span class="sxs-lookup"><span data-stu-id="01c4b-183">Show verbose output.</span></span>                                                                                                                                                                                                 |

<span data-ttu-id="01c4b-184">若要顯示命令的說明資訊，請使用 PowerShell 的`Get-Help`命令。</span><span class="sxs-lookup"><span data-stu-id="01c4b-184">To show help information about a command, use PowerShell's `Get-Help` command.</span></span>

> [!TIP]
> <span data-ttu-id="01c4b-185">內容、 專案和啟始專案參數支援 tab 鍵擴充。</span><span class="sxs-lookup"><span data-stu-id="01c4b-185">The Context, Project, and StartupProject parameters support tab-expansion.</span></span>

## <a name="add-migration"></a><span data-ttu-id="01c4b-186">新增移轉</span><span class="sxs-lookup"><span data-stu-id="01c4b-186">Add-Migration</span></span>

<span data-ttu-id="01c4b-187">加入新的移轉。</span><span class="sxs-lookup"><span data-stu-id="01c4b-187">Adds a new migration.</span></span>

<span data-ttu-id="01c4b-188">參數：</span><span class="sxs-lookup"><span data-stu-id="01c4b-188">Parameters:</span></span>

| <span data-ttu-id="01c4b-189">參數</span><span class="sxs-lookup"><span data-stu-id="01c4b-189">Parameter</span></span>                         | <span data-ttu-id="01c4b-190">描述</span><span class="sxs-lookup"><span data-stu-id="01c4b-190">Description</span></span>                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="01c4b-191"><nobr>-Name \<String><nobr></span><span class="sxs-lookup"><span data-stu-id="01c4b-191"><nobr>-Name \<String><nobr></span></span>       | <span data-ttu-id="01c4b-192">移轉名稱。</span><span class="sxs-lookup"><span data-stu-id="01c4b-192">The name of the migration.</span></span> <span data-ttu-id="01c4b-193">這是位置參數，而且為必要。</span><span class="sxs-lookup"><span data-stu-id="01c4b-193">This is a positional parameter and is required.</span></span>                                              |
| <span data-ttu-id="01c4b-194"><nobr>-OutputDir \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="01c4b-194"><nobr>-OutputDir \<String></nobr></span></span> | <span data-ttu-id="01c4b-195">目錄 （及子命名空間） 使用。</span><span class="sxs-lookup"><span data-stu-id="01c4b-195">The directory (and sub-namespace) to use.</span></span> <span data-ttu-id="01c4b-196">路徑是相對於目標的專案目錄。</span><span class="sxs-lookup"><span data-stu-id="01c4b-196">Paths are relative to the target project directory.</span></span> <span data-ttu-id="01c4b-197">預設為 「 移轉 」。</span><span class="sxs-lookup"><span data-stu-id="01c4b-197">Defaults to "Migrations".</span></span> |

## <a name="drop-database"></a><span data-ttu-id="01c4b-198">Drop-Database</span><span class="sxs-lookup"><span data-stu-id="01c4b-198">Drop-Database</span></span>

<span data-ttu-id="01c4b-199">卸除資料庫。</span><span class="sxs-lookup"><span data-stu-id="01c4b-199">Drops the database.</span></span>

<span data-ttu-id="01c4b-200">參數：</span><span class="sxs-lookup"><span data-stu-id="01c4b-200">Parameters:</span></span>

| <span data-ttu-id="01c4b-201">參數</span><span class="sxs-lookup"><span data-stu-id="01c4b-201">Parameter</span></span> | <span data-ttu-id="01c4b-202">描述</span><span class="sxs-lookup"><span data-stu-id="01c4b-202">Description</span></span>                                              |
|:----------|:---------------------------------------------------------|
| <span data-ttu-id="01c4b-203">-WhatIf</span><span class="sxs-lookup"><span data-stu-id="01c4b-203">-WhatIf</span></span>   | <span data-ttu-id="01c4b-204">顯示哪個資料庫會卸除，但不會卸除它。</span><span class="sxs-lookup"><span data-stu-id="01c4b-204">Show which database would be dropped, but don't drop it.</span></span> |

## <a name="get-dbcontext"></a><span data-ttu-id="01c4b-205">Get-DbContext</span><span class="sxs-lookup"><span data-stu-id="01c4b-205">Get-DbContext</span></span>

<span data-ttu-id="01c4b-206">取得有關的資訊`DbContext`型別。</span><span class="sxs-lookup"><span data-stu-id="01c4b-206">Gets information about a `DbContext` type.</span></span>

## <a name="remove-migration"></a><span data-ttu-id="01c4b-207">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="01c4b-207">Remove-Migration</span></span>

<span data-ttu-id="01c4b-208">移除最後一個移轉 （回復移轉已完成的程式碼變更）。</span><span class="sxs-lookup"><span data-stu-id="01c4b-208">Removes the last migration (rolls back the code changes that were done for the migration).</span></span>

<span data-ttu-id="01c4b-209">參數：</span><span class="sxs-lookup"><span data-stu-id="01c4b-209">Parameters:</span></span>

| <span data-ttu-id="01c4b-210">參數</span><span class="sxs-lookup"><span data-stu-id="01c4b-210">Parameter</span></span> | <span data-ttu-id="01c4b-211">描述</span><span class="sxs-lookup"><span data-stu-id="01c4b-211">Description</span></span>                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| <span data-ttu-id="01c4b-212">-Force</span><span class="sxs-lookup"><span data-stu-id="01c4b-212">-Force</span></span>    | <span data-ttu-id="01c4b-213">還原移轉 （回復已套用至資料庫的變更）。</span><span class="sxs-lookup"><span data-stu-id="01c4b-213">Revert the migration (roll back the changes that were applied to the database).</span></span> |

## <a name="scaffold-dbcontext"></a><span data-ttu-id="01c4b-214">Scaffold-DbContext</span><span class="sxs-lookup"><span data-stu-id="01c4b-214">Scaffold-DbContext</span></span>

<span data-ttu-id="01c4b-215">產生的程式碼`DbContext`和資料庫的實體類型。</span><span class="sxs-lookup"><span data-stu-id="01c4b-215">Generates code for a `DbContext` and entity types for a database.</span></span> <span data-ttu-id="01c4b-216">為了讓`Scaffold-DbContext`來產生實體類型、 資料庫資料表必須有主索引鍵。</span><span class="sxs-lookup"><span data-stu-id="01c4b-216">In order for `Scaffold-DbContext` to generate an entity type, the database table must have a primary key.</span></span>

<span data-ttu-id="01c4b-217">參數：</span><span class="sxs-lookup"><span data-stu-id="01c4b-217">Parameters:</span></span>

| <span data-ttu-id="01c4b-218">參數</span><span class="sxs-lookup"><span data-stu-id="01c4b-218">Parameter</span></span>                          | <span data-ttu-id="01c4b-219">描述</span><span class="sxs-lookup"><span data-stu-id="01c4b-219">Description</span></span>                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="01c4b-220"><nobr>Test-connection\<String></nobr></span><span class="sxs-lookup"><span data-stu-id="01c4b-220"><nobr>-Connection \<String></nobr></span></span> | <span data-ttu-id="01c4b-221">資料庫連接字串。</span><span class="sxs-lookup"><span data-stu-id="01c4b-221">The connection string to the database.</span></span> <span data-ttu-id="01c4b-222">針對 ASP.NET Core 2.x 專案，此值可以是*名稱 =\<的連接字串名稱 >* 。</span><span class="sxs-lookup"><span data-stu-id="01c4b-222">For ASP.NET Core 2.x projects, the value can be *name=\<name of connection string>*.</span></span> <span data-ttu-id="01c4b-223">在此情況下，名稱來自於 專案設定的組態來源。</span><span class="sxs-lookup"><span data-stu-id="01c4b-223">In that case the name comes from the configuration sources that are set up for the project.</span></span> <span data-ttu-id="01c4b-224">這是位置參數，而且為必要。</span><span class="sxs-lookup"><span data-stu-id="01c4b-224">This is a positional parameter and is required.</span></span> |
| <span data-ttu-id="01c4b-225"><nobr>-Provider \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="01c4b-225"><nobr>-Provider \<String></nobr></span></span>   | <span data-ttu-id="01c4b-226">要使用提供者</span><span class="sxs-lookup"><span data-stu-id="01c4b-226">The provider to use.</span></span> <span data-ttu-id="01c4b-227">通常這是 NuGet 套件的名稱，例如： `Microsoft.EntityFrameworkCore.SqlServer`。</span><span class="sxs-lookup"><span data-stu-id="01c4b-227">Typically this is the name of the NuGet package, for example: `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="01c4b-228">這是位置參數，而且為必要。</span><span class="sxs-lookup"><span data-stu-id="01c4b-228">This is a positional parameter and is required.</span></span>                                                                                           |
| <span data-ttu-id="01c4b-229">-OutputDir \<String></span><span class="sxs-lookup"><span data-stu-id="01c4b-229">-OutputDir \<String></span></span>               | <span data-ttu-id="01c4b-230">將檔案放入目錄。</span><span class="sxs-lookup"><span data-stu-id="01c4b-230">The directory to put files in.</span></span> <span data-ttu-id="01c4b-231">路徑相對於專案目錄。</span><span class="sxs-lookup"><span data-stu-id="01c4b-231">Paths are relative to the project directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="01c4b-232">-ContextDir \<String></span><span class="sxs-lookup"><span data-stu-id="01c4b-232">-ContextDir \<String></span></span>              | <span data-ttu-id="01c4b-233">要在其中放入 `DbContext`檔案的目錄。</span><span class="sxs-lookup"><span data-stu-id="01c4b-233">The directory to put the `DbContext` file in.</span></span> <span data-ttu-id="01c4b-234">路徑相對於專案目錄。</span><span class="sxs-lookup"><span data-stu-id="01c4b-234">Paths are relative to the project directory.</span></span>                                                                                                                                                                              |
| <span data-ttu-id="01c4b-235">-Context \<String></span><span class="sxs-lookup"><span data-stu-id="01c4b-235">-Context \<String></span></span>                 | <span data-ttu-id="01c4b-236">名稱`DbContext`類別來產生。</span><span class="sxs-lookup"><span data-stu-id="01c4b-236">The name of the `DbContext` class to generate.</span></span>                                                                                                                                                                                                                          |
| <span data-ttu-id="01c4b-237">-Schemas \<String[]></span><span class="sxs-lookup"><span data-stu-id="01c4b-237">-Schemas \<String[]></span></span>               | <span data-ttu-id="01c4b-238">要產生的實體類型的資料表結構描述。</span><span class="sxs-lookup"><span data-stu-id="01c4b-238">The schemas of tables to generate entity types for.</span></span> <span data-ttu-id="01c4b-239">如果省略這個參數，則會包含 所有結構描述。</span><span class="sxs-lookup"><span data-stu-id="01c4b-239">If this parameter is omitted, all schemas are included.</span></span>                                                                                                                                                             |
| <span data-ttu-id="01c4b-240">-Tables\<String []></span><span class="sxs-lookup"><span data-stu-id="01c4b-240">-Tables \<String[]></span></span>                | <span data-ttu-id="01c4b-241">要產生的實體類型的資料表。</span><span class="sxs-lookup"><span data-stu-id="01c4b-241">The tables to generate entity types for.</span></span> <span data-ttu-id="01c4b-242">如果省略這個參數，則所有資料表都都包含在內。</span><span class="sxs-lookup"><span data-stu-id="01c4b-242">If this parameter is omitted, all tables are included.</span></span>                                                                                                                                                                         |
| <span data-ttu-id="01c4b-243">-DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="01c4b-243">-DataAnnotations</span></span>                   | <span data-ttu-id="01c4b-244">使用屬性來設定模型 (如果可能的話)。</span><span class="sxs-lookup"><span data-stu-id="01c4b-244">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="01c4b-245">如果省略這個參數，則會使用 fluent API。</span><span class="sxs-lookup"><span data-stu-id="01c4b-245">If this parameter is omitted, only the fluent API is used.</span></span>                                                                                                                                                      |
| <span data-ttu-id="01c4b-246">-UseDatabaseNames</span><span class="sxs-lookup"><span data-stu-id="01c4b-246">-UseDatabaseNames</span></span>                  | <span data-ttu-id="01c4b-247">完全依照其出現在資料庫中，請使用資料表和資料行的名稱。</span><span class="sxs-lookup"><span data-stu-id="01c4b-247">Use table and column names exactly as they appear in the database.</span></span> <span data-ttu-id="01c4b-248">如果省略這個參數，則資料庫名稱會變更為更密切符合 C# 命名樣式慣例。</span><span class="sxs-lookup"><span data-stu-id="01c4b-248">If this parameter is omitted, database names are changed to more closely conform to C# name style conventions.</span></span>                                                                                       |
| <span data-ttu-id="01c4b-249">-Force</span><span class="sxs-lookup"><span data-stu-id="01c4b-249">-Force</span></span>                             | <span data-ttu-id="01c4b-250">覆寫現有的檔案。</span><span class="sxs-lookup"><span data-stu-id="01c4b-250">Overwrite existing files.</span></span>                                                                                                                                                                                                                                               |

<span data-ttu-id="01c4b-251">範例：</span><span class="sxs-lookup"><span data-stu-id="01c4b-251">Example:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

<span data-ttu-id="01c4b-252">範例中，則只有選取的資料表，並建立具有指定名稱的個別資料夾的內容：</span><span class="sxs-lookup"><span data-stu-id="01c4b-252">Example that scaffolds only selected tables and creates the context in a separate folder with a specified name:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext
```

## <a name="script-migration"></a><span data-ttu-id="01c4b-253">Script-Migration</span><span class="sxs-lookup"><span data-stu-id="01c4b-253">Script-Migration</span></span>

<span data-ttu-id="01c4b-254">產生 SQL 指令碼從一個所選移轉至另一個所選的移轉套用的所有變更。</span><span class="sxs-lookup"><span data-stu-id="01c4b-254">Generates a SQL script that applies all of the changes from one selected migration to another selected migration.</span></span>

<span data-ttu-id="01c4b-255">參數：</span><span class="sxs-lookup"><span data-stu-id="01c4b-255">Parameters:</span></span>

| <span data-ttu-id="01c4b-256">參數</span><span class="sxs-lookup"><span data-stu-id="01c4b-256">Parameter</span></span>                | <span data-ttu-id="01c4b-257">描述</span><span class="sxs-lookup"><span data-stu-id="01c4b-257">Description</span></span>                                                                                                                                                                                                                |
|:-------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="01c4b-258">*-From* \<String></span><span class="sxs-lookup"><span data-stu-id="01c4b-258">*-From* \<String></span></span>        | <span data-ttu-id="01c4b-259">在開始移轉。</span><span class="sxs-lookup"><span data-stu-id="01c4b-259">The starting migration.</span></span> <span data-ttu-id="01c4b-260">移轉可能會識別，依名稱或識別碼。</span><span class="sxs-lookup"><span data-stu-id="01c4b-260">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="01c4b-261">數字 0 是特殊案例，這表示*第一個移轉之前，先*。</span><span class="sxs-lookup"><span data-stu-id="01c4b-261">The number 0 is a special case that means *before the first migration*.</span></span> <span data-ttu-id="01c4b-262">預設值為 0。</span><span class="sxs-lookup"><span data-stu-id="01c4b-262">Defaults to 0.</span></span>                                                              |
| <span data-ttu-id="01c4b-263">*-To* \<String></span><span class="sxs-lookup"><span data-stu-id="01c4b-263">*-To* \<String></span></span>          | <span data-ttu-id="01c4b-264">結束的移轉。</span><span class="sxs-lookup"><span data-stu-id="01c4b-264">The ending migration.</span></span> <span data-ttu-id="01c4b-265">預設為最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="01c4b-265">Defaults to the last migration.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="01c4b-266"><nobr>-Idempotent</nobr></span><span class="sxs-lookup"><span data-stu-id="01c4b-266"><nobr>-Idempotent</nobr></span></span> | <span data-ttu-id="01c4b-267">產生可在任何移轉隨時用在資料庫的指令碼。</span><span class="sxs-lookup"><span data-stu-id="01c4b-267">Generate a script that can be used on a database at any migration.</span></span>                                                                                                                                                         |
| <span data-ttu-id="01c4b-268">-輸出\<字串 ></span><span class="sxs-lookup"><span data-stu-id="01c4b-268">-Output \<String></span></span>        | <span data-ttu-id="01c4b-269">要將結果寫入其中的檔案。</span><span class="sxs-lookup"><span data-stu-id="01c4b-269">The file to write the result to.</span></span> <span data-ttu-id="01c4b-270">應用程式的執行階段檔案建立時，比方說，如果省略這個參數，則要檔案建立產生的名稱相同的資料夾中： */obj/Debug/netcoreapp2.1/ghbkztfz.sql/* 。</span><span class="sxs-lookup"><span data-stu-id="01c4b-270">IF this parameter is omitted, the file is created with a generated name in the same folder as the app's runtime files are created, for example: */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*.</span></span> |

> [!TIP]
> <span data-ttu-id="01c4b-271">收件者，，和輸出參數支援 tab 鍵擴充。</span><span class="sxs-lookup"><span data-stu-id="01c4b-271">The To, From, and Output parameters support tab-expansion.</span></span>

<span data-ttu-id="01c4b-272">下列範例會建立指令碼為 InitialCreate 移轉時，使用移轉名稱。</span><span class="sxs-lookup"><span data-stu-id="01c4b-272">The following example creates a script for the InitialCreate migration, using the migration name.</span></span>

```powershell
Script-Migration -To InitialCreate
```

<span data-ttu-id="01c4b-273">下列範例會建立所有移轉的指令碼為 InitialCreate 移轉之後，使用 移轉識別碼。</span><span class="sxs-lookup"><span data-stu-id="01c4b-273">The following example creates a script for all migrations after the InitialCreate migration, using the migration ID.</span></span>

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a><span data-ttu-id="01c4b-274">Update-Database</span><span class="sxs-lookup"><span data-stu-id="01c4b-274">Update-Database</span></span>

<span data-ttu-id="01c4b-275">最後一個移轉，或指定的移轉，請更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="01c4b-275">Updates the database to the last migration or to a specified migration.</span></span>

| <span data-ttu-id="01c4b-276">參數</span><span class="sxs-lookup"><span data-stu-id="01c4b-276">Parameter</span></span>                           | <span data-ttu-id="01c4b-277">描述</span><span class="sxs-lookup"><span data-stu-id="01c4b-277">Description</span></span>                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="01c4b-278"><nobr> *-Migration* \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="01c4b-278"><nobr>*-Migration* \<String></nobr></span></span> | <span data-ttu-id="01c4b-279">目標移轉。</span><span class="sxs-lookup"><span data-stu-id="01c4b-279">The target migration.</span></span> <span data-ttu-id="01c4b-280">移轉可能會識別，依名稱或識別碼。</span><span class="sxs-lookup"><span data-stu-id="01c4b-280">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="01c4b-281">數字 0 是特殊案例，這表示*第一個移轉之前，先*並造成所有移轉到還原作業。</span><span class="sxs-lookup"><span data-stu-id="01c4b-281">The number 0 is a special case that means *before the first migration* and causes all migrations to be reverted.</span></span> <span data-ttu-id="01c4b-282">如果未不指定任何移轉，則此命令預設的最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="01c4b-282">If no migration is specified, the command defaults to the last migration.</span></span> |

> [!TIP]
> <span data-ttu-id="01c4b-283">移轉參數支援 tab 鍵擴充。</span><span class="sxs-lookup"><span data-stu-id="01c4b-283">The Migration parameter supports tab-expansion.</span></span>

<span data-ttu-id="01c4b-284">下列範例會還原所有移轉作業。</span><span class="sxs-lookup"><span data-stu-id="01c4b-284">The following example reverts all migrations.</span></span>

```powershell
Update-Database -Migration 0
```

<span data-ttu-id="01c4b-285">下列範例會更新資料庫，以指定的移轉。</span><span class="sxs-lookup"><span data-stu-id="01c4b-285">The following examples update the database to a specified migration.</span></span> <span data-ttu-id="01c4b-286">第一個是使用移轉名稱和第二個會使用移轉識別碼：</span><span class="sxs-lookup"><span data-stu-id="01c4b-286">The first uses the migration name and the second uses the migration ID:</span></span>

```powershell
Update-Database -Migration InitialCreate
Update-Database -Migration 20180904195021_InitialCreate
```

## <a name="additional-resources"></a><span data-ttu-id="01c4b-287">其他資源</span><span class="sxs-lookup"><span data-stu-id="01c4b-287">Additional resources</span></span>

* [<span data-ttu-id="01c4b-288">移轉</span><span class="sxs-lookup"><span data-stu-id="01c4b-288">Migrations</span></span>](xref:core/managing-schemas/migrations/index)
* [<span data-ttu-id="01c4b-289">反向工程</span><span class="sxs-lookup"><span data-stu-id="01c4b-289">Reverse Engineering</span></span>](xref:core/managing-schemas/scaffolding)
