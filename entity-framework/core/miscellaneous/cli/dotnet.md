---
title: .NET core CLI-EF 核心
author: bricelam
ms.author: bricelam
ms.date: 11/6/2017
ms.technology: entity-framework-core
ms.openlocfilehash: 396d31c9d0c0f47d299f49e82e557ed29b8420e7
ms.sourcegitcommit: 4997314356118d0d97b04ad82e433e49bb9420a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
<a name="ef-core-net-command-line-tools"></a><span data-ttu-id="a9013-102">EF 核心.NET 命令列工具</span><span class="sxs-lookup"><span data-stu-id="a9013-102">EF Core .NET Command-line Tools</span></span>
===============================
<span data-ttu-id="a9013-103">Entity Framework Core.NET 命令列工具是跨平台擴充**dotnet**命令，也就是組件的[.NET Core SDK][2]。</span><span class="sxs-lookup"><span data-stu-id="a9013-103">The Entity Framework Core .NET Command-line Tools are an extension to the cross-platform **dotnet** command, which is part of the [.NET Core SDK][2].</span></span>

> [!TIP]
> <span data-ttu-id="a9013-104">如果您使用 Visual Studio，我們建議您[PMC 工具][ 1]相反地，因為它們提供更加整合的經驗。</span><span class="sxs-lookup"><span data-stu-id="a9013-104">If you're using Visual Studio, we recommend [the PMC Tools][1] instead since they provide a more integrated experience.</span></span>

<a name="installing-the-tools"></a><span data-ttu-id="a9013-105">安裝工具</span><span class="sxs-lookup"><span data-stu-id="a9013-105">Installing the tools</span></span>
--------------------
<span data-ttu-id="a9013-106">安裝 EF 核心.NET 命令列工具使用下列步驟：</span><span class="sxs-lookup"><span data-stu-id="a9013-106">Install the EF Core .NET Command-line Tools using these steps:</span></span>

1. <span data-ttu-id="a9013-107">編輯專案檔，並新增 Microsoft.EntityFrameworkCore.Tools.DotNet 為 DotNetCliToolReference 項目 （請參閱下文）</span><span class="sxs-lookup"><span data-stu-id="a9013-107">Edit the project file and add Microsoft.EntityFrameworkCore.Tools.DotNet as a DotNetCliToolReference item (See below)</span></span>
2. <span data-ttu-id="a9013-108">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="a9013-108">Run the following commands:</span></span>

       dotnet add package Microsoft.EntityFrameworkCore.Design
       dotnet restore


<span data-ttu-id="a9013-109">產生的專案看起來應該像這樣：</span><span class="sxs-lookup"><span data-stu-id="a9013-109">The resulting project should look something like this:</span></span>

``` xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>netcoreapp2.0</TargetFramework>
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.EntityFrameworkCore.Design"
                      Version="2.0.0"
                      PrivateAssets="All" />
  </ItemGroup>
  <ItemGroup>
    <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
                            Version="2.0.0" />
  </ItemGroup>
</Project>
```

> [!NOTE]
> <span data-ttu-id="a9013-110">封裝參考`PrivateAssets="All"`表示它不公開給專案參考此專案中，這是特別適用於通常只在開發期間使用的封裝。</span><span class="sxs-lookup"><span data-stu-id="a9013-110">A package reference with `PrivateAssets="All"` means it isn't exposed to projects that reference this project, which is especially useful for packages that are typically only used during development.</span></span>

<span data-ttu-id="a9013-111">如果您這樣做的所有項目權限，您應該能夠成功地在命令提示字元中執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="a9013-111">If you did everything right, you should be able to successfully run the following command in a command prompt.</span></span>

``` Console
dotnet ef
```

<a name="using-the-tools"></a><span data-ttu-id="a9013-112">使用的工具</span><span class="sxs-lookup"><span data-stu-id="a9013-112">Using the tools</span></span>
---------------
<span data-ttu-id="a9013-113">當您叫用命令時，有兩個專案相關：</span><span class="sxs-lookup"><span data-stu-id="a9013-113">Whenever you invoke a command, there are two projects involved:</span></span>

<span data-ttu-id="a9013-114">目標專案就是新增 (在某些情況下會移除) 任何檔案的位置。</span><span class="sxs-lookup"><span data-stu-id="a9013-114">The target project is where any files are added (or in some cases removed).</span></span> <span data-ttu-id="a9013-115">目標專案預設值為目前的目錄中的專案，但可以使用變更<nobr> **-專案**</nobr>選項。</span><span class="sxs-lookup"><span data-stu-id="a9013-115">The target project defaults to the project in the current directory, but can be changed using the <nobr>**--project**</nobr> option.</span></span>

<span data-ttu-id="a9013-116">啟始專案是工具在執行您的專案程式碼時所模擬的專案。</span><span class="sxs-lookup"><span data-stu-id="a9013-116">The startup project is the one emulated by the tools when executing your project's code.</span></span> <span data-ttu-id="a9013-117">它也會預設為目前的目錄中的專案，但可以使用變更**-啟始專案**選項。</span><span class="sxs-lookup"><span data-stu-id="a9013-117">It also defaults to the project in the current directory, but can be changed using the **--startup-project** option.</span></span>

> [!NOTE]
> <span data-ttu-id="a9013-118">比方說，更新 web 應用程式的 EF 核心安裝在不同的專案中的資料庫看起來會像這樣： `dotnet ef database update --project {project-path}` （從您的 web 應用程式目錄）</span><span class="sxs-lookup"><span data-stu-id="a9013-118">For instance, updating the database of your web application that has EF Core installed in a different project would look like this: `dotnet ef database update --project {project-path}` (from your web app directory)</span></span>

<span data-ttu-id="a9013-119">常用選項：</span><span class="sxs-lookup"><span data-stu-id="a9013-119">Common options:</span></span>

|    |                                  |                             |
|:---|:---------------------------------|:----------------------------|
|    | <span data-ttu-id="a9013-120">--json</span><span class="sxs-lookup"><span data-stu-id="a9013-120">--json</span></span>                           | <span data-ttu-id="a9013-121">顯示 JSON 輸出。</span><span class="sxs-lookup"><span data-stu-id="a9013-121">Show JSON output.</span></span>           |
| <span data-ttu-id="a9013-122">-c</span><span class="sxs-lookup"><span data-stu-id="a9013-122">-c</span></span> | <span data-ttu-id="a9013-123">-內容\<DBCONTEXT ></span><span class="sxs-lookup"><span data-stu-id="a9013-123">--context \<DBCONTEXT></span></span>           | <span data-ttu-id="a9013-124">若要使用 DbContext。</span><span class="sxs-lookup"><span data-stu-id="a9013-124">The DbContext to use.</span></span>       |
| <span data-ttu-id="a9013-125">-p</span><span class="sxs-lookup"><span data-stu-id="a9013-125">-p</span></span> | <span data-ttu-id="a9013-126">-專案\<專案 ></span><span class="sxs-lookup"><span data-stu-id="a9013-126">--project \<PROJECT></span></span>             | <span data-ttu-id="a9013-127">要使用的專案。</span><span class="sxs-lookup"><span data-stu-id="a9013-127">The project to use.</span></span>         |
| <span data-ttu-id="a9013-128">-s</span><span class="sxs-lookup"><span data-stu-id="a9013-128">-s</span></span> | <span data-ttu-id="a9013-129">-啟始專案\<專案 ></span><span class="sxs-lookup"><span data-stu-id="a9013-129">--startup-project \<PROJECT></span></span>     | <span data-ttu-id="a9013-130">若要使用啟始專案。</span><span class="sxs-lookup"><span data-stu-id="a9013-130">The startup project to use.</span></span> |
|    | <span data-ttu-id="a9013-131">-framework\<架構 ></span><span class="sxs-lookup"><span data-stu-id="a9013-131">--framework \<FRAMEWORK></span></span>         | <span data-ttu-id="a9013-132">目標 framework。</span><span class="sxs-lookup"><span data-stu-id="a9013-132">The target framework.</span></span>       |
|    | <span data-ttu-id="a9013-133">-設定\<設定 ></span><span class="sxs-lookup"><span data-stu-id="a9013-133">--configuration \<CONFIGURATION></span></span> | <span data-ttu-id="a9013-134">要使用的組態。</span><span class="sxs-lookup"><span data-stu-id="a9013-134">The configuration to use.</span></span>   |
|    | <span data-ttu-id="a9013-135">-執行階段\<識別項 ></span><span class="sxs-lookup"><span data-stu-id="a9013-135">--runtime \<IDENTIFIER></span></span>          | <span data-ttu-id="a9013-136">在執行階段使用。</span><span class="sxs-lookup"><span data-stu-id="a9013-136">The runtime to use.</span></span>         |
| <span data-ttu-id="a9013-137">-h</span><span class="sxs-lookup"><span data-stu-id="a9013-137">-h</span></span> | <span data-ttu-id="a9013-138">-說明</span><span class="sxs-lookup"><span data-stu-id="a9013-138">--help</span></span>                           | <span data-ttu-id="a9013-139">顯示說明資訊。</span><span class="sxs-lookup"><span data-stu-id="a9013-139">Show help information.</span></span>      |
| <span data-ttu-id="a9013-140">-v</span><span class="sxs-lookup"><span data-stu-id="a9013-140">-v</span></span> | <span data-ttu-id="a9013-141">-verbose</span><span class="sxs-lookup"><span data-stu-id="a9013-141">--verbose</span></span>                        | <span data-ttu-id="a9013-142">顯示詳細資訊輸出。</span><span class="sxs-lookup"><span data-stu-id="a9013-142">Show verbose output.</span></span>        |
|    | <span data-ttu-id="a9013-143">--no-color</span><span class="sxs-lookup"><span data-stu-id="a9013-143">--no-color</span></span>                       | <span data-ttu-id="a9013-144">不要以色彩標示輸出。</span><span class="sxs-lookup"><span data-stu-id="a9013-144">Don't colorize output.</span></span>      |
|    | <span data-ttu-id="a9013-145">--prefix-output</span><span class="sxs-lookup"><span data-stu-id="a9013-145">--prefix-output</span></span>                  | <span data-ttu-id="a9013-146">輸出層級的前置詞。</span><span class="sxs-lookup"><span data-stu-id="a9013-146">Prefix output with level.</span></span>   |


> [!TIP]
> <span data-ttu-id="a9013-147">指定 ASP.NET Core 環境，請設定**ASPNETCORE_ENVIRONMENT**之前執行的環境變數。</span><span class="sxs-lookup"><span data-stu-id="a9013-147">To specify the ASP.NET Core environment, set the **ASPNETCORE_ENVIRONMENT** environment variable before running.</span></span>

<a name="commands"></a><span data-ttu-id="a9013-148">命令</span><span class="sxs-lookup"><span data-stu-id="a9013-148">Commands</span></span>
--------

### <a name="dotnet-ef-database-drop"></a><span data-ttu-id="a9013-149">dotnet ef 資料庫卸除</span><span class="sxs-lookup"><span data-stu-id="a9013-149">dotnet ef database drop</span></span>

<span data-ttu-id="a9013-150">卸除資料庫。</span><span class="sxs-lookup"><span data-stu-id="a9013-150">Drops the database.</span></span>

<span data-ttu-id="a9013-151">選項:</span><span class="sxs-lookup"><span data-stu-id="a9013-151">Options:</span></span>

|    |           |                                                          |
|:---|:----------|:---------------------------------------------------------|
| <span data-ttu-id="a9013-152">-f</span><span class="sxs-lookup"><span data-stu-id="a9013-152">-f</span></span> | <span data-ttu-id="a9013-153">--force</span><span class="sxs-lookup"><span data-stu-id="a9013-153">--force</span></span>   | <span data-ttu-id="a9013-154">未確認。</span><span class="sxs-lookup"><span data-stu-id="a9013-154">Don't confirm.</span></span>                                           |
|    | <span data-ttu-id="a9013-155">-乾執行</span><span class="sxs-lookup"><span data-stu-id="a9013-155">--dry-run</span></span> | <span data-ttu-id="a9013-156">顯示哪一個資料庫會卸除，但不卸除它。</span><span class="sxs-lookup"><span data-stu-id="a9013-156">Show which database would be dropped, but don't drop it.</span></span> |

### <a name="dotnet-ef-database-update"></a><span data-ttu-id="a9013-157">dotnet ef 資料庫更新</span><span class="sxs-lookup"><span data-stu-id="a9013-157">dotnet ef database update</span></span>

<span data-ttu-id="a9013-158">將資料庫更新為指定的移轉。</span><span class="sxs-lookup"><span data-stu-id="a9013-158">Updates the database to a specified migration.</span></span>

<span data-ttu-id="a9013-159">引數：</span><span class="sxs-lookup"><span data-stu-id="a9013-159">Arguments:</span></span>

|              |                                                                                              |
|:-------------|:---------------------------------------------------------------------------------------------|
| <span data-ttu-id="a9013-160">\<移轉 &GT;</span><span class="sxs-lookup"><span data-stu-id="a9013-160">\<MIGRATION></span></span> | <span data-ttu-id="a9013-161">在目標移轉。</span><span class="sxs-lookup"><span data-stu-id="a9013-161">The target migration.</span></span> <span data-ttu-id="a9013-162">如果為 0，就會還原所有移轉。</span><span class="sxs-lookup"><span data-stu-id="a9013-162">If 0, all migrations will be reverted.</span></span> <span data-ttu-id="a9013-163">預設的最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="a9013-163">Defaults to the last migration.</span></span> |

### <a name="dotnet-ef-dbcontext-info"></a><span data-ttu-id="a9013-164">dotnet ef dbcontext 資訊</span><span class="sxs-lookup"><span data-stu-id="a9013-164">dotnet ef dbcontext info</span></span>

<span data-ttu-id="a9013-165">取得 DbContext 類型的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="a9013-165">Gets information about a DbContext type.</span></span>

### <a name="dotnet-ef-dbcontext-list"></a><span data-ttu-id="a9013-166">dotnet ef dbcontext 清單</span><span class="sxs-lookup"><span data-stu-id="a9013-166">dotnet ef dbcontext list</span></span>

<span data-ttu-id="a9013-167">列出可用的 DbContext 類型。</span><span class="sxs-lookup"><span data-stu-id="a9013-167">Lists available DbContext types.</span></span>

### <a name="dotnet-ef-dbcontext-scaffold"></a><span data-ttu-id="a9013-168">dotnet ef dbcontext scaffold</span><span class="sxs-lookup"><span data-stu-id="a9013-168">dotnet ef dbcontext scaffold</span></span>

<span data-ttu-id="a9013-169">Scaffold DbContext 類型及實體類型的資料庫。</span><span class="sxs-lookup"><span data-stu-id="a9013-169">Scaffolds a DbContext and entity types for a database.</span></span>

<span data-ttu-id="a9013-170">引數：</span><span class="sxs-lookup"><span data-stu-id="a9013-170">Arguments:</span></span>

|               |                                                                     |
|:--------------|:--------------------------------------------------------------------|
| <span data-ttu-id="a9013-171">\<連接 &GT;</span><span class="sxs-lookup"><span data-stu-id="a9013-171">\<CONNECTION></span></span> | <span data-ttu-id="a9013-172">資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="a9013-172">The connection string to the database.</span></span>                              |
| <span data-ttu-id="a9013-173">\<提供者 &GT;</span><span class="sxs-lookup"><span data-stu-id="a9013-173">\<PROVIDER></span></span>   | <span data-ttu-id="a9013-174">若要使用提供者。</span><span class="sxs-lookup"><span data-stu-id="a9013-174">The provider to use.</span></span> <span data-ttu-id="a9013-175">(例如，</span><span class="sxs-lookup"><span data-stu-id="a9013-175">(E.g.</span></span> <span data-ttu-id="a9013-176">Microsoft.EntityFrameworkCore.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="a9013-176">Microsoft.EntityFrameworkCore.SqlServer)</span></span> |

<span data-ttu-id="a9013-177">選項:</span><span class="sxs-lookup"><span data-stu-id="a9013-177">Options:</span></span>

|                 |                                         |                                                                                                  |
|:----------------|:----------------------------------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a9013-178"><nobr>-d</nobr></span><span class="sxs-lookup"><span data-stu-id="a9013-178"><nobr>-d</nobr></span></span> | <span data-ttu-id="a9013-179">-資料註解</span><span class="sxs-lookup"><span data-stu-id="a9013-179">--data-annotations</span></span>                      | <span data-ttu-id="a9013-180">使用屬性，將模型設定 （如果可能的話）。</span><span class="sxs-lookup"><span data-stu-id="a9013-180">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="a9013-181">如果省略，則會使用 fluent 應用程式開發的 API。</span><span class="sxs-lookup"><span data-stu-id="a9013-181">If omitted, only the fluent API is used.</span></span> |
| <span data-ttu-id="a9013-182">-c</span><span class="sxs-lookup"><span data-stu-id="a9013-182">-c</span></span>              | <span data-ttu-id="a9013-183">-內容\<名稱 ></span><span class="sxs-lookup"><span data-stu-id="a9013-183">--context \<NAME></span></span>                       | <span data-ttu-id="a9013-184">DbContext 名稱。</span><span class="sxs-lookup"><span data-stu-id="a9013-184">The name of the DbContext.</span></span>                                                                       |
|                 | <span data-ttu-id="a9013-185">-內容-dir\<路徑 ></span><span class="sxs-lookup"><span data-stu-id="a9013-185">--context-dir \<PATH></span></span>                   | <span data-ttu-id="a9013-186">將 DbContext 檔案放入目錄。</span><span class="sxs-lookup"><span data-stu-id="a9013-186">The directory to put DbContext file in.</span></span> <span data-ttu-id="a9013-187">路徑是相對於專案目錄。</span><span class="sxs-lookup"><span data-stu-id="a9013-187">Paths are relative to the project directory.</span></span>             |
| <span data-ttu-id="a9013-188">-f</span><span class="sxs-lookup"><span data-stu-id="a9013-188">-f</span></span>              | <span data-ttu-id="a9013-189">--force</span><span class="sxs-lookup"><span data-stu-id="a9013-189">--force</span></span>                                 | <span data-ttu-id="a9013-190">覆寫現有檔案。</span><span class="sxs-lookup"><span data-stu-id="a9013-190">Overwrite existing files.</span></span>                                                                        |
| <span data-ttu-id="a9013-191">-o</span><span class="sxs-lookup"><span data-stu-id="a9013-191">-o</span></span>              | <span data-ttu-id="a9013-192">-輸出 dir\<路徑 ></span><span class="sxs-lookup"><span data-stu-id="a9013-192">--output-dir \<PATH></span></span>                    | <span data-ttu-id="a9013-193">將檔案放入目錄。</span><span class="sxs-lookup"><span data-stu-id="a9013-193">The directory to put files in.</span></span> <span data-ttu-id="a9013-194">路徑是相對於專案目錄。</span><span class="sxs-lookup"><span data-stu-id="a9013-194">Paths are relative to the project directory.</span></span>                      |
|                 | <span data-ttu-id="a9013-195"><nobr>-結構描述\<SCHEMA_NAME >...</nobr></span><span class="sxs-lookup"><span data-stu-id="a9013-195"><nobr>--schema \<SCHEMA_NAME>...</nobr></span></span> | <span data-ttu-id="a9013-196">要產生實體類型的資料表結構描述。</span><span class="sxs-lookup"><span data-stu-id="a9013-196">The schemas of tables to generate entity types for.</span></span>                                              |
| <span data-ttu-id="a9013-197">-t</span><span class="sxs-lookup"><span data-stu-id="a9013-197">-t</span></span>              | <span data-ttu-id="a9013-198">-資料表\<TABLE_NAME >...</span><span class="sxs-lookup"><span data-stu-id="a9013-198">--table \<TABLE_NAME>...</span></span>                | <span data-ttu-id="a9013-199">要產生實體類型的資料表。</span><span class="sxs-lookup"><span data-stu-id="a9013-199">The tables to generate entity types for.</span></span>                                                         |
|                 | <span data-ttu-id="a9013-200">-使用資料庫名稱</span><span class="sxs-lookup"><span data-stu-id="a9013-200">--use-database-names</span></span>                    | <span data-ttu-id="a9013-201">使用直接從資料庫資料表和資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="a9013-201">Use table and column names directly from the database.</span></span>                                           |

### <a name="dotnet-ef-migrations-add"></a><span data-ttu-id="a9013-202">dotnet ef 移轉新增</span><span class="sxs-lookup"><span data-stu-id="a9013-202">dotnet ef migrations add</span></span>

<span data-ttu-id="a9013-203">加入新的移轉。</span><span class="sxs-lookup"><span data-stu-id="a9013-203">Adds a new migration.</span></span>

<span data-ttu-id="a9013-204">引數：</span><span class="sxs-lookup"><span data-stu-id="a9013-204">Arguments:</span></span>

|         |                            |
|:--------|:---------------------------|
| <span data-ttu-id="a9013-205">\<名稱 &GT;</span><span class="sxs-lookup"><span data-stu-id="a9013-205">\<NAME></span></span> | <span data-ttu-id="a9013-206">移轉程序的名稱。</span><span class="sxs-lookup"><span data-stu-id="a9013-206">The name of the migration.</span></span> |

<span data-ttu-id="a9013-207">選項:</span><span class="sxs-lookup"><span data-stu-id="a9013-207">Options:</span></span>

|                 |                                   |                                                                                                                  |
|:----------------|:----------------------------------|:-----------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a9013-208"><nobr>-o</nobr></span><span class="sxs-lookup"><span data-stu-id="a9013-208"><nobr>-o</nobr></span></span> | <span data-ttu-id="a9013-209"><nobr>-輸出 dir\<路徑 ></nobr></span><span class="sxs-lookup"><span data-stu-id="a9013-209"><nobr>--output-dir \<PATH></nobr></span></span> | <span data-ttu-id="a9013-210">目錄 （以及子命名空間） 使用。</span><span class="sxs-lookup"><span data-stu-id="a9013-210">The directory (and sub-namespace) to use.</span></span> <span data-ttu-id="a9013-211">路徑是相對於專案目錄。</span><span class="sxs-lookup"><span data-stu-id="a9013-211">Paths are relative to the project directory.</span></span> <span data-ttu-id="a9013-212">預設為 「 移轉 」。</span><span class="sxs-lookup"><span data-stu-id="a9013-212">Defaults to "Migrations".</span></span> |

### <a name="dotnet-ef-migrations-list"></a><span data-ttu-id="a9013-213">dotnet ef 移轉清單</span><span class="sxs-lookup"><span data-stu-id="a9013-213">dotnet ef migrations list</span></span>

<span data-ttu-id="a9013-214">列出可用的移轉。</span><span class="sxs-lookup"><span data-stu-id="a9013-214">Lists available migrations.</span></span>

### <a name="dotnet-ef-migrations-remove"></a><span data-ttu-id="a9013-215">dotnet ef 移轉移除</span><span class="sxs-lookup"><span data-stu-id="a9013-215">dotnet ef migrations remove</span></span>

<span data-ttu-id="a9013-216">移除最後的移轉。</span><span class="sxs-lookup"><span data-stu-id="a9013-216">Removes the last migration.</span></span>

<span data-ttu-id="a9013-217">選項:</span><span class="sxs-lookup"><span data-stu-id="a9013-217">Options:</span></span>

|    |         |                                                                       |
|:---|:--------|:----------------------------------------------------------------------|
| <span data-ttu-id="a9013-218">-f</span><span class="sxs-lookup"><span data-stu-id="a9013-218">-f</span></span> | <span data-ttu-id="a9013-219">--force</span><span class="sxs-lookup"><span data-stu-id="a9013-219">--force</span></span> | <span data-ttu-id="a9013-220">如果套用至資料庫，請還原移轉。</span><span class="sxs-lookup"><span data-stu-id="a9013-220">Revert the migration if it has been applied to the database.</span></span> |

### <a name="dotnet-ef-migrations-script"></a><span data-ttu-id="a9013-221">dotnet ef 移轉指令碼</span><span class="sxs-lookup"><span data-stu-id="a9013-221">dotnet ef migrations script</span></span>

<span data-ttu-id="a9013-222">從移轉中產生 SQL 指令碼。</span><span class="sxs-lookup"><span data-stu-id="a9013-222">Generates a SQL script from migrations.</span></span>

<span data-ttu-id="a9013-223">引數：</span><span class="sxs-lookup"><span data-stu-id="a9013-223">Arguments:</span></span>

|         |                                                               |
|:--------|:--------------------------------------------------------------|
| <span data-ttu-id="a9013-224">\<FROM></span><span class="sxs-lookup"><span data-stu-id="a9013-224">\<FROM></span></span> | <span data-ttu-id="a9013-225">開始移轉。</span><span class="sxs-lookup"><span data-stu-id="a9013-225">The starting migration.</span></span> <span data-ttu-id="a9013-226">預設值為 0 （初始資料庫）。</span><span class="sxs-lookup"><span data-stu-id="a9013-226">Defaults to 0 (the initial database).</span></span> |
| <span data-ttu-id="a9013-227">\<若要 &GT;</span><span class="sxs-lookup"><span data-stu-id="a9013-227">\<TO></span></span>   | <span data-ttu-id="a9013-228">結束的移轉。</span><span class="sxs-lookup"><span data-stu-id="a9013-228">The ending migration.</span></span> <span data-ttu-id="a9013-229">預設的最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="a9013-229">Defaults to the last migration.</span></span>         |

<span data-ttu-id="a9013-230">選項:</span><span class="sxs-lookup"><span data-stu-id="a9013-230">Options:</span></span>

|    |                  |                                                                    |
|:---|:-----------------|:-------------------------------------------------------------------|
| <span data-ttu-id="a9013-231">-o</span><span class="sxs-lookup"><span data-stu-id="a9013-231">-o</span></span> | <span data-ttu-id="a9013-232">-輸出\<檔案 ></span><span class="sxs-lookup"><span data-stu-id="a9013-232">--output \<FILE></span></span> | <span data-ttu-id="a9013-233">若要將結果寫入檔案。</span><span class="sxs-lookup"><span data-stu-id="a9013-233">The file to write the result to.</span></span>                                   |
| <span data-ttu-id="a9013-234">-i</span><span class="sxs-lookup"><span data-stu-id="a9013-234">-i</span></span> | <span data-ttu-id="a9013-235">-等冪</span><span class="sxs-lookup"><span data-stu-id="a9013-235">--idempotent</span></span>     | <span data-ttu-id="a9013-236">產生的指令碼，可用在任何移轉的資料庫。</span><span class="sxs-lookup"><span data-stu-id="a9013-236">Generate a script that can be used on a database at any migration.</span></span> |


  [1]: powershell.md
  [2]: https://www.microsoft.com/net/core
