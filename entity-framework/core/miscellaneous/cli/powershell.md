---
title: Package Manager Console (Visual Studio)-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/6/2017
ms.openlocfilehash: 6a3594a3535f8de30ec1898fd21cfcbe272f216b
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997930"
---
<a name="ef-core-package-manager-console-tools"></a><span data-ttu-id="f1cee-102">EF Core 封裝管理員主控台工具</span><span class="sxs-lookup"><span data-stu-id="f1cee-102">EF Core Package Manager Console Tools</span></span>
=====================================
<span data-ttu-id="f1cee-103">EF Core 封裝管理員主控台 (PMC) 工具，請執行 Visual Studio 內使用 NuGet 的[Package Manager Console][2]。</span><span class="sxs-lookup"><span data-stu-id="f1cee-103">The EF Core Package Manager Console (PMC) Tools run inside of Visual Studio using NuGet's [Package Manager Console][2].</span></span>
<span data-ttu-id="f1cee-104">這些工具會使用 .NET Framework 和 .NET Core 專案。</span><span class="sxs-lookup"><span data-stu-id="f1cee-104">These tools work with both .NET Framework and .NET Core projects.</span></span>

> [!TIP]
> <span data-ttu-id="f1cee-105">不使用 Visual Studio 嗎？</span><span class="sxs-lookup"><span data-stu-id="f1cee-105">Not using Visual Studio?</span></span> <span data-ttu-id="f1cee-106">[EF Core 命令列工具][ 1]是跨平台和在命令提示字元內執行。</span><span class="sxs-lookup"><span data-stu-id="f1cee-106">The [EF Core Command-line Tools][1] are cross-platform and run inside a command prompt.</span></span>

<a name="installing-the-tools"></a><span data-ttu-id="f1cee-107">安裝工具</span><span class="sxs-lookup"><span data-stu-id="f1cee-107">Installing the tools</span></span>
--------------------
<span data-ttu-id="f1cee-108">安裝 EF Core 封裝管理員主控台工具安裝 Microsoft.EntityFrameworkCore.Tools NuGet 封裝。</span><span class="sxs-lookup"><span data-stu-id="f1cee-108">Install the EF Core Package Manager Console Tools by installing the Microsoft.EntityFrameworkCore.Tools NuGet package.</span></span>
<span data-ttu-id="f1cee-109">您可以藉由執行下列命令安裝它[Package Manager Console][2]。</span><span class="sxs-lookup"><span data-stu-id="f1cee-109">You can install it by executing the following command inside [Package Manager Console][2].</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="f1cee-110">如果一切正常運作，您應該能夠執行此命令：</span><span class="sxs-lookup"><span data-stu-id="f1cee-110">If everything worked correctly, you should be able to run this command:</span></span>

``` powershell
Get-Help about_EntityFrameworkCore
```
> [!TIP]
> <span data-ttu-id="f1cee-111">如果您的啟始專案目標設為.NET Standard[跨目標的受支援的 framework] [ 3]之前使用的工具。</span><span class="sxs-lookup"><span data-stu-id="f1cee-111">If your startup project targets .NET Standard, [cross-target a supported framework][3] before using the tools.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f1cee-112">如果您使用**通用 Windows**或是**Xamarin**，將您的 EF 程式碼移至.NET Standard 類別庫和[跨目標的受支援的 framework] [ 3]之前使用的工具。</span><span class="sxs-lookup"><span data-stu-id="f1cee-112">If you're using **Universal Windows** or **Xamarin**, move your EF code to a .NET Standard class library and [cross-target a supported framework][3] before using the tools.</span></span> <span data-ttu-id="f1cee-113">指定類別庫做為您的啟始專案。</span><span class="sxs-lookup"><span data-stu-id="f1cee-113">Specify the class library as your startup project.</span></span>

<a name="using-the-tools"></a><span data-ttu-id="f1cee-114">使用的工具</span><span class="sxs-lookup"><span data-stu-id="f1cee-114">Using the tools</span></span>
---------------
<span data-ttu-id="f1cee-115">每當您叫用命令時，有兩個專案包含：</span><span class="sxs-lookup"><span data-stu-id="f1cee-115">Whenever you invoke a command, there are two projects involved:</span></span>

<span data-ttu-id="f1cee-116">目標專案就是新增 (在某些情況下會移除) 任何檔案的位置。</span><span class="sxs-lookup"><span data-stu-id="f1cee-116">The target project is where any files are added (or in some cases removed).</span></span> <span data-ttu-id="f1cee-117">目標專案的預設值為**預設專案**在 Package Manager Console 中選取，但也可以指定使用-專案參數。</span><span class="sxs-lookup"><span data-stu-id="f1cee-117">The target project defaults to the **Default project** selected in Package Manager Console, but can also be specified using the -Project parameter.</span></span>

<span data-ttu-id="f1cee-118">啟始專案是工具在執行您的專案程式碼時所模擬的專案。</span><span class="sxs-lookup"><span data-stu-id="f1cee-118">The startup project is the one emulated by the tools when executing your project's code.</span></span> <span data-ttu-id="f1cee-119">它會預設為其中一個**設定為啟始專案**方案總管 中。</span><span class="sxs-lookup"><span data-stu-id="f1cee-119">It defaults to one **Set as StartUp Project** in Solution Explorer.</span></span> <span data-ttu-id="f1cee-120">它也可以指定使用-啟始專案參數。</span><span class="sxs-lookup"><span data-stu-id="f1cee-120">It can also be specified using the -StartupProject parameter.</span></span>

<span data-ttu-id="f1cee-121">一般參數：</span><span class="sxs-lookup"><span data-stu-id="f1cee-121">Common parameters:</span></span>

|                           |                             |
|:--------------------------|:----------------------------|
| <span data-ttu-id="f1cee-122">內容\<字串 ></span><span class="sxs-lookup"><span data-stu-id="f1cee-122">-Context \<String></span></span>        | <span data-ttu-id="f1cee-123">要使用的 DbContext。</span><span class="sxs-lookup"><span data-stu-id="f1cee-123">The DbContext to use.</span></span>       |
| <span data-ttu-id="f1cee-124">-專案\<字串 ></span><span class="sxs-lookup"><span data-stu-id="f1cee-124">-Project \<String></span></span>        | <span data-ttu-id="f1cee-125">要使用的專案。</span><span class="sxs-lookup"><span data-stu-id="f1cee-125">The project to use.</span></span>         |
| <span data-ttu-id="f1cee-126">-啟始專案\<字串 ></span><span class="sxs-lookup"><span data-stu-id="f1cee-126">-StartupProject \<String></span></span> | <span data-ttu-id="f1cee-127">要使用的啟始專案。</span><span class="sxs-lookup"><span data-stu-id="f1cee-127">The startup project to use.</span></span> |
| <span data-ttu-id="f1cee-128">-Verbose</span><span class="sxs-lookup"><span data-stu-id="f1cee-128">-Verbose</span></span>                  | <span data-ttu-id="f1cee-129">顯示詳細資訊輸出。</span><span class="sxs-lookup"><span data-stu-id="f1cee-129">Show verbose output.</span></span>        |

<span data-ttu-id="f1cee-130">若要顯示命令的說明資訊，請使用 PowerShell 的`Get-Help`命令。</span><span class="sxs-lookup"><span data-stu-id="f1cee-130">To show help information about a command, use PowerShell's `Get-Help` command.</span></span>

> [!TIP]
> <span data-ttu-id="f1cee-131">內容、 專案和啟始專案參數支援 tab 鍵擴充。</span><span class="sxs-lookup"><span data-stu-id="f1cee-131">The Context, Project, and StartupProject parameters support tab-expansion.</span></span>

> [!TIP]
> <span data-ttu-id="f1cee-132">設定**env:ASPNETCORE_ENVIRONMENT**之前指定 ASP.NET Core 環境執行。</span><span class="sxs-lookup"><span data-stu-id="f1cee-132">Set **env:ASPNETCORE_ENVIRONMENT** before running to specify the ASP.NET Core environment.</span></span>

<a name="commands"></a><span data-ttu-id="f1cee-133">命令</span><span class="sxs-lookup"><span data-stu-id="f1cee-133">Commands</span></span>
--------

### <a name="add-migration"></a><span data-ttu-id="f1cee-134">新增移轉</span><span class="sxs-lookup"><span data-stu-id="f1cee-134">Add-Migration</span></span>

<span data-ttu-id="f1cee-135">加入新的移轉。</span><span class="sxs-lookup"><span data-stu-id="f1cee-135">Adds a new migration.</span></span>

<span data-ttu-id="f1cee-136">參數：</span><span class="sxs-lookup"><span data-stu-id="f1cee-136">Parameters:</span></span>

|                                   |                                                                                                                  |
|:----------------------------------|:-----------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f1cee-137">***-Name*** \<字串 ></span><span class="sxs-lookup"><span data-stu-id="f1cee-137">***-Name*** \<String></span></span>             | <span data-ttu-id="f1cee-138">移轉名稱。</span><span class="sxs-lookup"><span data-stu-id="f1cee-138">The name of the migration.</span></span>                                                                                       |
| <span data-ttu-id="f1cee-139"><nobr>-OutputDir\<字串 ></nobr></span><span class="sxs-lookup"><span data-stu-id="f1cee-139"><nobr>-OutputDir \<String></nobr></span></span> | <span data-ttu-id="f1cee-140">目錄 （及子命名空間） 使用。</span><span class="sxs-lookup"><span data-stu-id="f1cee-140">The directory (and sub-namespace) to use.</span></span> <span data-ttu-id="f1cee-141">路徑是相對於專案目錄。</span><span class="sxs-lookup"><span data-stu-id="f1cee-141">Paths are relative to the project directory.</span></span> <span data-ttu-id="f1cee-142">預設為 「 移轉 」。</span><span class="sxs-lookup"><span data-stu-id="f1cee-142">Defaults to "Migrations".</span></span> |

> [!NOTE]
> <span data-ttu-id="f1cee-143">中的參數**粗體**是必要的並於*斜體*是位置。</span><span class="sxs-lookup"><span data-stu-id="f1cee-143">Parameters in **bold** are required, and ones in *italics* are positional.</span></span>

### <a name="drop-database"></a><span data-ttu-id="f1cee-144">卸除資料庫</span><span class="sxs-lookup"><span data-stu-id="f1cee-144">Drop-Database</span></span>

<span data-ttu-id="f1cee-145">卸除資料庫。</span><span class="sxs-lookup"><span data-stu-id="f1cee-145">Drops the database.</span></span>

<span data-ttu-id="f1cee-146">參數：</span><span class="sxs-lookup"><span data-stu-id="f1cee-146">Parameters:</span></span>

|         |                                                          |
|:--------|:---------------------------------------------------------|
| <span data-ttu-id="f1cee-147">-WhatIf</span><span class="sxs-lookup"><span data-stu-id="f1cee-147">-WhatIf</span></span> | <span data-ttu-id="f1cee-148">顯示哪個資料庫會卸除，但不會卸除它。</span><span class="sxs-lookup"><span data-stu-id="f1cee-148">Show which database would be dropped, but don't drop it.</span></span> |

### <a name="get-dbcontext"></a><span data-ttu-id="f1cee-149">Get-DbContext</span><span class="sxs-lookup"><span data-stu-id="f1cee-149">Get-DbContext</span></span>

<span data-ttu-id="f1cee-150">取得 DbContext 類型的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="f1cee-150">Gets information about a DbContext type.</span></span>

### <a name="remove-migration"></a><span data-ttu-id="f1cee-151">移除移轉</span><span class="sxs-lookup"><span data-stu-id="f1cee-151">Remove-Migration</span></span>

<span data-ttu-id="f1cee-152">移除最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="f1cee-152">Removes the last migration.</span></span>

<span data-ttu-id="f1cee-153">參數：</span><span class="sxs-lookup"><span data-stu-id="f1cee-153">Parameters:</span></span>

|        |                                                              |
|:-------|:-------------------------------------------------------------|
| <span data-ttu-id="f1cee-154">-Force</span><span class="sxs-lookup"><span data-stu-id="f1cee-154">-Force</span></span> | <span data-ttu-id="f1cee-155">如果套用至資料庫，請還原移轉。</span><span class="sxs-lookup"><span data-stu-id="f1cee-155">Revert the migration if it has been applied to the database.</span></span> |

### <a name="scaffold-dbcontext"></a><span data-ttu-id="f1cee-156">Scaffold DbContext</span><span class="sxs-lookup"><span data-stu-id="f1cee-156">Scaffold-DbContext</span></span>

<span data-ttu-id="f1cee-157">建立資料庫的 DbContext 和實體類型。</span><span class="sxs-lookup"><span data-stu-id="f1cee-157">Scaffolds a DbContext and entity types for a database.</span></span>

<span data-ttu-id="f1cee-158">參數：</span><span class="sxs-lookup"><span data-stu-id="f1cee-158">Parameters:</span></span>

|                                          |                                                                                                  |
|:-----------------------------------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f1cee-159"><nobr>***Test-connection*** \<字串 ></nobr></span><span class="sxs-lookup"><span data-stu-id="f1cee-159"><nobr>***-Connection*** \<String></nobr></span></span> | <span data-ttu-id="f1cee-160">資料庫連接字串。</span><span class="sxs-lookup"><span data-stu-id="f1cee-160">The connection string to the database.</span></span>                                                           |
| <span data-ttu-id="f1cee-161">***-Provider*** \<String></span><span class="sxs-lookup"><span data-stu-id="f1cee-161">***-Provider*** \<String></span></span>                | <span data-ttu-id="f1cee-162">要使用提供者</span><span class="sxs-lookup"><span data-stu-id="f1cee-162">The provider to use.</span></span> <span data-ttu-id="f1cee-163">(例如 Microsoft.EntityFrameworkCore.SqlServer)。</span><span class="sxs-lookup"><span data-stu-id="f1cee-163">(for example, Microsoft.EntityFrameworkCore.SqlServer)</span></span>                      |
| <span data-ttu-id="f1cee-164">-OutputDir\<字串 ></span><span class="sxs-lookup"><span data-stu-id="f1cee-164">-OutputDir \<String></span></span>                     | <span data-ttu-id="f1cee-165">將檔案放入目錄。</span><span class="sxs-lookup"><span data-stu-id="f1cee-165">The directory to put files in.</span></span> <span data-ttu-id="f1cee-166">路徑是相對於專案目錄。</span><span class="sxs-lookup"><span data-stu-id="f1cee-166">Paths are relative to the project directory.</span></span>                      |
| <span data-ttu-id="f1cee-167">-ContextDir\<字串 ></span><span class="sxs-lookup"><span data-stu-id="f1cee-167">-ContextDir \<String></span></span>                    | <span data-ttu-id="f1cee-168">若要將 DbContext 檔案放在目錄。</span><span class="sxs-lookup"><span data-stu-id="f1cee-168">The directory to put DbContext file in.</span></span> <span data-ttu-id="f1cee-169">路徑是相對於專案目錄。</span><span class="sxs-lookup"><span data-stu-id="f1cee-169">Paths are relative to the project directory.</span></span>             |
| <span data-ttu-id="f1cee-170">內容\<字串 ></span><span class="sxs-lookup"><span data-stu-id="f1cee-170">-Context \<String></span></span>                       | <span data-ttu-id="f1cee-171">若要產生 DbContext 的名稱。</span><span class="sxs-lookup"><span data-stu-id="f1cee-171">The name of the DbContext to generate.</span></span>                                                           |
| <span data-ttu-id="f1cee-172">結構描述\<String [] ></span><span class="sxs-lookup"><span data-stu-id="f1cee-172">-Schemas \<String[]></span></span>                     | <span data-ttu-id="f1cee-173">要產生的實體類型的資料表結構描述。</span><span class="sxs-lookup"><span data-stu-id="f1cee-173">The schemas of tables to generate entity types for.</span></span>                                              |
| <span data-ttu-id="f1cee-174">-資料表\<String [] ></span><span class="sxs-lookup"><span data-stu-id="f1cee-174">-Tables \<String[]></span></span>                      | <span data-ttu-id="f1cee-175">要產生的實體類型的資料表。</span><span class="sxs-lookup"><span data-stu-id="f1cee-175">The tables to generate entity types for.</span></span>                                                         |
| <span data-ttu-id="f1cee-176">-DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="f1cee-176">-DataAnnotations</span></span>                         | <span data-ttu-id="f1cee-177">使用屬性來設定模型 (如果可能的話)。</span><span class="sxs-lookup"><span data-stu-id="f1cee-177">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="f1cee-178">如果省略，則只會使用 fluent API。</span><span class="sxs-lookup"><span data-stu-id="f1cee-178">If omitted, only the fluent API is used.</span></span> |
| <span data-ttu-id="f1cee-179">-UseDatabaseNames</span><span class="sxs-lookup"><span data-stu-id="f1cee-179">-UseDatabaseNames</span></span>                        | <span data-ttu-id="f1cee-180">直接使用資料庫的資料表與資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="f1cee-180">Use table and column names directly from the database.</span></span>                                           |
| <span data-ttu-id="f1cee-181">-Force</span><span class="sxs-lookup"><span data-stu-id="f1cee-181">-Force</span></span>                                   | <span data-ttu-id="f1cee-182">覆寫現有的檔案。</span><span class="sxs-lookup"><span data-stu-id="f1cee-182">Overwrite existing files.</span></span>                                                                        |

### <a name="script-migration"></a><span data-ttu-id="f1cee-183">Script-Migration</span><span class="sxs-lookup"><span data-stu-id="f1cee-183">Script-Migration</span></span>

<span data-ttu-id="f1cee-184">產生 SQL 指令碼從移轉。</span><span class="sxs-lookup"><span data-stu-id="f1cee-184">Generates a SQL script from migrations.</span></span>

<span data-ttu-id="f1cee-185">參數：</span><span class="sxs-lookup"><span data-stu-id="f1cee-185">Parameters:</span></span>

|                   |                                                                    |
|:------------------|:-------------------------------------------------------------------|
| <span data-ttu-id="f1cee-186">*-從*\<字串 ></span><span class="sxs-lookup"><span data-stu-id="f1cee-186">*-From* \<String></span></span> | <span data-ttu-id="f1cee-187">在開始移轉。</span><span class="sxs-lookup"><span data-stu-id="f1cee-187">The starting migration.</span></span> <span data-ttu-id="f1cee-188">預設值為 0 （初始資料庫）。</span><span class="sxs-lookup"><span data-stu-id="f1cee-188">Defaults to 0 (the initial database).</span></span>      |
| <span data-ttu-id="f1cee-189">*-至*\<字串 ></span><span class="sxs-lookup"><span data-stu-id="f1cee-189">*-To* \<String></span></span>   | <span data-ttu-id="f1cee-190">結束的移轉。</span><span class="sxs-lookup"><span data-stu-id="f1cee-190">The ending migration.</span></span> <span data-ttu-id="f1cee-191">預設為最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="f1cee-191">Defaults to the last migration.</span></span>              |
| <span data-ttu-id="f1cee-192">-具有等冪性</span><span class="sxs-lookup"><span data-stu-id="f1cee-192">-Idempotent</span></span>       | <span data-ttu-id="f1cee-193">產生可在任何移轉隨時用在資料庫的指令碼。</span><span class="sxs-lookup"><span data-stu-id="f1cee-193">Generate a script that can be used on a database at any migration.</span></span> |
| <span data-ttu-id="f1cee-194">-輸出\<字串 ></span><span class="sxs-lookup"><span data-stu-id="f1cee-194">-Output \<String></span></span> | <span data-ttu-id="f1cee-195">要將結果寫入其中的檔案。</span><span class="sxs-lookup"><span data-stu-id="f1cee-195">The file to write the result to.</span></span>                                   |

> [!TIP]
> <span data-ttu-id="f1cee-196">收件者，，和輸出參數支援 tab 鍵擴充。</span><span class="sxs-lookup"><span data-stu-id="f1cee-196">The To, From, and Output parameters support tab-expansion.</span></span>

### <a name="update-database"></a><span data-ttu-id="f1cee-197">更新資料庫</span><span class="sxs-lookup"><span data-stu-id="f1cee-197">Update-Database</span></span>

|                                     |                                                                                                |
|:------------------------------------|:-----------------------------------------------------------------------------------------------|
| <span data-ttu-id="f1cee-198"><nobr>*-移轉*\<字串 ></nobr></span><span class="sxs-lookup"><span data-stu-id="f1cee-198"><nobr>*-Migration* \<String></nobr></span></span> | <span data-ttu-id="f1cee-199">目標移轉。</span><span class="sxs-lookup"><span data-stu-id="f1cee-199">The target migration.</span></span> <span data-ttu-id="f1cee-200">如果為 '0'，就會還原所有移轉作業。</span><span class="sxs-lookup"><span data-stu-id="f1cee-200">If '0', all migrations will be reverted.</span></span> <span data-ttu-id="f1cee-201">預設為最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="f1cee-201">Defaults to the last migration.</span></span> |

> [!TIP]
> <span data-ttu-id="f1cee-202">移轉參數支援 tab 鍵擴充。</span><span class="sxs-lookup"><span data-stu-id="f1cee-202">The Migration parameter supports tab-expansion.</span></span>


  [1]: dotnet.md
  [2]: https://docs.microsoft.com/nuget/tools/package-manager-console
  [3]: index.md#frameworks
