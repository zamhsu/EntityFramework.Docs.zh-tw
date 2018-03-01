---
title: "Package Manager Console (Visual Studio)-EF 核心"
author: bricelam
ms.author: bricelam
ms.date: 11/6/2017
ms.technology: entity-framework-core
ms.openlocfilehash: aacf8c8564a3966db6202c9ff1c1c02a19a10814
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
<a name="ef-core-package-manager-console-tools"></a><span data-ttu-id="96fa2-102">EF 核心封裝管理員主控台工具</span><span class="sxs-lookup"><span data-stu-id="96fa2-102">EF Core Package Manager Console Tools</span></span>
=====================================
<span data-ttu-id="96fa2-103">EF 核心封裝管理員主控台 (PMC) 工具，請執行 Visual Studio 內使用 NuGet 的[Package Manager Console][2]。</span><span class="sxs-lookup"><span data-stu-id="96fa2-103">The EF Core Package Manager Console (PMC) Tools run inside of Visual Studio using NuGet's [Package Manager Console][2].</span></span>
<span data-ttu-id="96fa2-104">這些工具會使用 .NET Framework 和 .NET Core 專案。</span><span class="sxs-lookup"><span data-stu-id="96fa2-104">These tools work with both .NET Framework and .NET Core projects.</span></span>

> [!TIP]
> <span data-ttu-id="96fa2-105">不使用 Visual Studio 嗎？</span><span class="sxs-lookup"><span data-stu-id="96fa2-105">Not using Visual Studio?</span></span> <span data-ttu-id="96fa2-106">[EF 核心命令列工具][ 1]是跨平台和在命令提示字元內執行。</span><span class="sxs-lookup"><span data-stu-id="96fa2-106">The [EF Core Command-line Tools][1] are cross-platform and run inside a command prompt.</span></span>

<a name="installing-the-tools"></a><span data-ttu-id="96fa2-107">安裝工具</span><span class="sxs-lookup"><span data-stu-id="96fa2-107">Installing the tools</span></span>
--------------------
<span data-ttu-id="96fa2-108">安裝 EF 核心封裝管理員主控台工具安裝 Microsoft.EntityFrameworkCore.Tools NuGet 封裝。</span><span class="sxs-lookup"><span data-stu-id="96fa2-108">Install the EF Core Package Manager Console Tools by installing the Microsoft.EntityFrameworkCore.Tools NuGet package.</span></span>
<span data-ttu-id="96fa2-109">您可以執行內的下列命令來安裝[Package Manager Console][2]。</span><span class="sxs-lookup"><span data-stu-id="96fa2-109">You can install it by executing the following command inside [Package Manager Console][2].</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="96fa2-110">如果一切正常運作，您應該能夠執行這個命令：</span><span class="sxs-lookup"><span data-stu-id="96fa2-110">If everything worked correctly, you should be able to run this command:</span></span>

``` powershell
Get-Help about_EntityFrameworkCore
```
> [!TIP]
> <span data-ttu-id="96fa2-111">如果您的啟始專案的目標.NET 標準[跨目標受支援的 framework] [ 3]之前使用的工具。</span><span class="sxs-lookup"><span data-stu-id="96fa2-111">If your startup project targets .NET Standard, [cross-target a supported framework][3] before using the tools.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="96fa2-112">如果您使用**通用 Windows**或**Xamarin**，將您的 EF 程式碼移至.NET 標準的類別庫和[跨目標受支援的 framework] [ 3]之前使用的工具。</span><span class="sxs-lookup"><span data-stu-id="96fa2-112">If you're using **Universal Windows** or **Xamarin**, move your EF code to a .NET Standard class library and [cross-target a supported framework][3] before using the tools.</span></span> <span data-ttu-id="96fa2-113">指定的類別庫做為您的啟始專案。</span><span class="sxs-lookup"><span data-stu-id="96fa2-113">Specify the class library as your startup project.</span></span>

<a name="using-the-tools"></a><span data-ttu-id="96fa2-114">使用的工具</span><span class="sxs-lookup"><span data-stu-id="96fa2-114">Using the tools</span></span>
---------------
<span data-ttu-id="96fa2-115">當您叫用命令時，有兩個專案相關：</span><span class="sxs-lookup"><span data-stu-id="96fa2-115">Whenever you invoke a command, there are two projects involved:</span></span>

<span data-ttu-id="96fa2-116">目標專案就是新增 (在某些情況下會移除) 任何檔案的位置。</span><span class="sxs-lookup"><span data-stu-id="96fa2-116">The target project is where any files are added (or in some cases removed).</span></span> <span data-ttu-id="96fa2-117">目標專案預設值為**預設專案**Package Manager Console 中選取，但您也可以指定使用-專案參數。</span><span class="sxs-lookup"><span data-stu-id="96fa2-117">The target project defaults to the **Default project** selected in Package Manager Console, but can also be specified using the -Project parameter.</span></span>

<span data-ttu-id="96fa2-118">啟始專案是工具在執行您的專案程式碼時所模擬的專案。</span><span class="sxs-lookup"><span data-stu-id="96fa2-118">The startup project is the one emulated by the tools when executing your project's code.</span></span> <span data-ttu-id="96fa2-119">預設為其中一個**設定為啟始專案**方案總管 中。</span><span class="sxs-lookup"><span data-stu-id="96fa2-119">It defaults to one **Set as StartUp Project** in Solution Explorer.</span></span> <span data-ttu-id="96fa2-120">它也可以指定使用-啟始專案參數。</span><span class="sxs-lookup"><span data-stu-id="96fa2-120">It can also be specified using the -StartupProject parameter.</span></span>

<span data-ttu-id="96fa2-121">一般參數：</span><span class="sxs-lookup"><span data-stu-id="96fa2-121">Common parameters:</span></span>

|                           |                             |
|:--------------------------|:----------------------------|
| <span data-ttu-id="96fa2-122">內容\<字串 ></span><span class="sxs-lookup"><span data-stu-id="96fa2-122">-Context \<String></span></span>        | <span data-ttu-id="96fa2-123">若要使用 DbContext。</span><span class="sxs-lookup"><span data-stu-id="96fa2-123">The DbContext to use.</span></span>       |
| <span data-ttu-id="96fa2-124">-Project \<String></span><span class="sxs-lookup"><span data-stu-id="96fa2-124">-Project \<String></span></span>        | <span data-ttu-id="96fa2-125">要使用的專案。</span><span class="sxs-lookup"><span data-stu-id="96fa2-125">The project to use.</span></span>         |
| <span data-ttu-id="96fa2-126">-StartupProject \<String></span><span class="sxs-lookup"><span data-stu-id="96fa2-126">-StartupProject \<String></span></span> | <span data-ttu-id="96fa2-127">若要使用啟始專案。</span><span class="sxs-lookup"><span data-stu-id="96fa2-127">The startup project to use.</span></span> |
| <span data-ttu-id="96fa2-128">-Verbose</span><span class="sxs-lookup"><span data-stu-id="96fa2-128">-Verbose</span></span>                  | <span data-ttu-id="96fa2-129">顯示詳細資訊輸出。</span><span class="sxs-lookup"><span data-stu-id="96fa2-129">Show verbose output.</span></span>        |

<span data-ttu-id="96fa2-130">若要顯示命令的說明資訊，請使用 PowerShell 的`Get-Help`命令。</span><span class="sxs-lookup"><span data-stu-id="96fa2-130">To show help information about a command, use PowerShell's `Get-Help` command.</span></span>

> [!TIP]
> <span data-ttu-id="96fa2-131">內容、 專案和啟始專案參數支援 tab 鍵擴充。</span><span class="sxs-lookup"><span data-stu-id="96fa2-131">The Context, Project, and StartupProject parameters support tab-expansion.</span></span>

> [!TIP]
> <span data-ttu-id="96fa2-132">設定**env:ASPNETCORE_ENVIRONMENT**之前指定 ASP.NET Core 環境執行。</span><span class="sxs-lookup"><span data-stu-id="96fa2-132">Set **env:ASPNETCORE_ENVIRONMENT** before running to specify the ASP.NET Core environment.</span></span>

<a name="commands"></a><span data-ttu-id="96fa2-133">命令</span><span class="sxs-lookup"><span data-stu-id="96fa2-133">Commands</span></span>
--------

### <a name="add-migration"></a><span data-ttu-id="96fa2-134">新增移轉</span><span class="sxs-lookup"><span data-stu-id="96fa2-134">Add-Migration</span></span>

<span data-ttu-id="96fa2-135">加入新的移轉。</span><span class="sxs-lookup"><span data-stu-id="96fa2-135">Adds a new migration.</span></span>

<span data-ttu-id="96fa2-136">參數：</span><span class="sxs-lookup"><span data-stu-id="96fa2-136">Parameters:</span></span>

|                                   |                                                                                                                  |
|:----------------------------------|:-----------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="96fa2-137">***-Name*** \<String></span><span class="sxs-lookup"><span data-stu-id="96fa2-137">***-Name*** \<String></span></span>             | <span data-ttu-id="96fa2-138">移轉程序的名稱。</span><span class="sxs-lookup"><span data-stu-id="96fa2-138">The name of the migration.</span></span>                                                                                       |
| <span data-ttu-id="96fa2-139"><nobr>-OutputDir\<字串 ></nobr></span><span class="sxs-lookup"><span data-stu-id="96fa2-139"><nobr>-OutputDir \<String></nobr></span></span> | <span data-ttu-id="96fa2-140">目錄 （以及子命名空間） 使用。</span><span class="sxs-lookup"><span data-stu-id="96fa2-140">The directory (and sub-namespace) to use.</span></span> <span data-ttu-id="96fa2-141">路徑是相對於專案目錄。</span><span class="sxs-lookup"><span data-stu-id="96fa2-141">Paths are relative to the project directory.</span></span> <span data-ttu-id="96fa2-142">預設為 「 移轉 」。</span><span class="sxs-lookup"><span data-stu-id="96fa2-142">Defaults to "Migrations".</span></span> |

> [!NOTE]
> <span data-ttu-id="96fa2-143">中的參數**粗體**是必要的以及在*斜體*是位置。</span><span class="sxs-lookup"><span data-stu-id="96fa2-143">Parameters in **bold** are required, and ones in *italics* are positional.</span></span>

### <a name="drop-database"></a><span data-ttu-id="96fa2-144">Drop-Database</span><span class="sxs-lookup"><span data-stu-id="96fa2-144">Drop-Database</span></span>

<span data-ttu-id="96fa2-145">卸除資料庫。</span><span class="sxs-lookup"><span data-stu-id="96fa2-145">Drops the database.</span></span>

<span data-ttu-id="96fa2-146">參數：</span><span class="sxs-lookup"><span data-stu-id="96fa2-146">Parameters:</span></span>

|         |                                                          |
|:--------|:---------------------------------------------------------|
| <span data-ttu-id="96fa2-147">-WhatIf</span><span class="sxs-lookup"><span data-stu-id="96fa2-147">-WhatIf</span></span> | <span data-ttu-id="96fa2-148">顯示哪一個資料庫會卸除，但不卸除它。</span><span class="sxs-lookup"><span data-stu-id="96fa2-148">Show which database would be dropped, but don't drop it.</span></span> |

### <a name="get-dbcontext"></a><span data-ttu-id="96fa2-149">Get-DbContext</span><span class="sxs-lookup"><span data-stu-id="96fa2-149">Get-DbContext</span></span>

<span data-ttu-id="96fa2-150">取得 DbContext 類型的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="96fa2-150">Gets information about a DbContext type.</span></span>

### <a name="remove-migration"></a><span data-ttu-id="96fa2-151">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="96fa2-151">Remove-Migration</span></span>

<span data-ttu-id="96fa2-152">移除最後的移轉。</span><span class="sxs-lookup"><span data-stu-id="96fa2-152">Removes the last migration.</span></span>

<span data-ttu-id="96fa2-153">參數：</span><span class="sxs-lookup"><span data-stu-id="96fa2-153">Parameters:</span></span>

|        |                                                                       |
|:-------|:----------------------------------------------------------------------|
| <span data-ttu-id="96fa2-154">-Force</span><span class="sxs-lookup"><span data-stu-id="96fa2-154">-Force</span></span> | <span data-ttu-id="96fa2-155">不會檢查以查看是否移轉套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="96fa2-155">Don't check to see if the migration has been applied to the database.</span></span> |

### <a name="scaffold-dbcontext"></a><span data-ttu-id="96fa2-156">Scaffold-DbContext</span><span class="sxs-lookup"><span data-stu-id="96fa2-156">Scaffold-DbContext</span></span>

<span data-ttu-id="96fa2-157">Scaffold DbContext 類型及實體類型的資料庫。</span><span class="sxs-lookup"><span data-stu-id="96fa2-157">Scaffolds a DbContext and entity types for a database.</span></span>

<span data-ttu-id="96fa2-158">參數：</span><span class="sxs-lookup"><span data-stu-id="96fa2-158">Parameters:</span></span>

|                                          |                                                                                                  |
|:-----------------------------------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="96fa2-159"><nobr>***-Connection*** \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="96fa2-159"><nobr>***-Connection*** \<String></nobr></span></span> | <span data-ttu-id="96fa2-160">資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="96fa2-160">The connection string to the database.</span></span>                                                           |
| <span data-ttu-id="96fa2-161">***-Provider*** \<String></span><span class="sxs-lookup"><span data-stu-id="96fa2-161">***-Provider*** \<String></span></span>                | <span data-ttu-id="96fa2-162">若要使用提供者。</span><span class="sxs-lookup"><span data-stu-id="96fa2-162">The provider to use.</span></span> <span data-ttu-id="96fa2-163">（例如：</span><span class="sxs-lookup"><span data-stu-id="96fa2-163">(E.g.</span></span> <span data-ttu-id="96fa2-164">Microsoft.EntityFrameworkCore.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="96fa2-164">Microsoft.EntityFrameworkCore.SqlServer)</span></span>                              |
| <span data-ttu-id="96fa2-165">-OutputDir\<字串 ></span><span class="sxs-lookup"><span data-stu-id="96fa2-165">-OutputDir \<String></span></span>                     | <span data-ttu-id="96fa2-166">將檔案放入目錄。</span><span class="sxs-lookup"><span data-stu-id="96fa2-166">The directory to put files in.</span></span> <span data-ttu-id="96fa2-167">路徑是相對於專案目錄。</span><span class="sxs-lookup"><span data-stu-id="96fa2-167">Paths are relative to the project directory.</span></span>                      |
| <span data-ttu-id="96fa2-168">內容\<字串 ></span><span class="sxs-lookup"><span data-stu-id="96fa2-168">-Context \<String></span></span>                       | <span data-ttu-id="96fa2-169">若要產生 DbContext 名稱。</span><span class="sxs-lookup"><span data-stu-id="96fa2-169">The name of the DbContext to generate.</span></span>                                                           |
| <span data-ttu-id="96fa2-170">-Schemas \<String[]></span><span class="sxs-lookup"><span data-stu-id="96fa2-170">-Schemas \<String[]></span></span>                     | <span data-ttu-id="96fa2-171">要產生實體類型的資料表結構描述。</span><span class="sxs-lookup"><span data-stu-id="96fa2-171">The schemas of tables to generate entity types for.</span></span>                                              |
| <span data-ttu-id="96fa2-172">-Tables \<String[]></span><span class="sxs-lookup"><span data-stu-id="96fa2-172">-Tables \<String[]></span></span>                      | <span data-ttu-id="96fa2-173">要產生實體類型的資料表。</span><span class="sxs-lookup"><span data-stu-id="96fa2-173">The tables to generate entity types for.</span></span>                                                         |
| <span data-ttu-id="96fa2-174">-DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="96fa2-174">-DataAnnotations</span></span>                         | <span data-ttu-id="96fa2-175">使用屬性，將模型設定 （如果可能的話）。</span><span class="sxs-lookup"><span data-stu-id="96fa2-175">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="96fa2-176">如果省略，則會使用 fluent 應用程式開發的 API。</span><span class="sxs-lookup"><span data-stu-id="96fa2-176">If omitted, only the fluent API is used.</span></span> |
| <span data-ttu-id="96fa2-177">-UseDatabaseNames</span><span class="sxs-lookup"><span data-stu-id="96fa2-177">-UseDatabaseNames</span></span>                        | <span data-ttu-id="96fa2-178">使用直接從資料庫資料表和資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="96fa2-178">Use table and column names directly from the database.</span></span>                                           |
| <span data-ttu-id="96fa2-179">-Force</span><span class="sxs-lookup"><span data-stu-id="96fa2-179">-Force</span></span>                                   | <span data-ttu-id="96fa2-180">覆寫現有檔案。</span><span class="sxs-lookup"><span data-stu-id="96fa2-180">Overwrite existing files.</span></span>                                                                        |

### <a name="script-migration"></a><span data-ttu-id="96fa2-181">Script-Migration</span><span class="sxs-lookup"><span data-stu-id="96fa2-181">Script-Migration</span></span>

<span data-ttu-id="96fa2-182">從移轉中產生 SQL 指令碼。</span><span class="sxs-lookup"><span data-stu-id="96fa2-182">Generates a SQL script from migrations.</span></span>

<span data-ttu-id="96fa2-183">參數：</span><span class="sxs-lookup"><span data-stu-id="96fa2-183">Parameters:</span></span>

|                   |                                                                    |
|:------------------|:-------------------------------------------------------------------|
| <span data-ttu-id="96fa2-184">*-From* \<String></span><span class="sxs-lookup"><span data-stu-id="96fa2-184">*-From* \<String></span></span> | <span data-ttu-id="96fa2-185">開始移轉。</span><span class="sxs-lookup"><span data-stu-id="96fa2-185">The starting migration.</span></span> <span data-ttu-id="96fa2-186">預設值為 0 （初始資料庫）。</span><span class="sxs-lookup"><span data-stu-id="96fa2-186">Defaults to 0 (the initial database).</span></span>      |
| <span data-ttu-id="96fa2-187">*-To* \<String></span><span class="sxs-lookup"><span data-stu-id="96fa2-187">*-To* \<String></span></span>   | <span data-ttu-id="96fa2-188">結束的移轉。</span><span class="sxs-lookup"><span data-stu-id="96fa2-188">The ending migration.</span></span> <span data-ttu-id="96fa2-189">預設的最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="96fa2-189">Defaults to the last migration.</span></span>              |
| <span data-ttu-id="96fa2-190">為等冪</span><span class="sxs-lookup"><span data-stu-id="96fa2-190">-Idempotent</span></span>       | <span data-ttu-id="96fa2-191">產生的指令碼，可用在任何移轉的資料庫。</span><span class="sxs-lookup"><span data-stu-id="96fa2-191">Generate a script that can be used on a database at any migration.</span></span> |
| <span data-ttu-id="96fa2-192">-輸出\<字串 ></span><span class="sxs-lookup"><span data-stu-id="96fa2-192">-Output \<String></span></span> | <span data-ttu-id="96fa2-193">若要將結果寫入檔案。</span><span class="sxs-lookup"><span data-stu-id="96fa2-193">The file to write the result to.</span></span>                                   |

> [!TIP]
> <span data-ttu-id="96fa2-194">To、 From、 和輸出參數支援 tab 鍵擴充。</span><span class="sxs-lookup"><span data-stu-id="96fa2-194">The To, From, and Output parameters support tab-expansion.</span></span>

### <a name="update-database"></a><span data-ttu-id="96fa2-195">Update-Database</span><span class="sxs-lookup"><span data-stu-id="96fa2-195">Update-Database</span></span>

|                                     |                                                                                                |
|:------------------------------------|:-----------------------------------------------------------------------------------------------|
| <span data-ttu-id="96fa2-196"><nobr>*-Migration* \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="96fa2-196"><nobr>*-Migration* \<String></nobr></span></span> | <span data-ttu-id="96fa2-197">在目標移轉。</span><span class="sxs-lookup"><span data-stu-id="96fa2-197">The target migration.</span></span> <span data-ttu-id="96fa2-198">如果為 '0'，將會還原所有移轉。</span><span class="sxs-lookup"><span data-stu-id="96fa2-198">If '0', all migrations will be reverted.</span></span> <span data-ttu-id="96fa2-199">預設的最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="96fa2-199">Defaults to the last migration.</span></span> |

> [!TIP]
> <span data-ttu-id="96fa2-200">移轉參數支援 tab 鍵擴充。</span><span class="sxs-lookup"><span data-stu-id="96fa2-200">The Migration parameter supports tab-expansion.</span></span>


  [1]: dotnet.md
  [2]: https://docs.microsoft.com/nuget/tools/package-manager-console
  [3]: index.md#frameworks
