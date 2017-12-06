---
title: ".NET core CLI-EF 核心"
author: bricelam
ms.author: bricelam
ms.date: 11/6/2017
ms.technology: entity-framework-core
ms.openlocfilehash: 26b5fb326d20575ed2f3c6955c699e0c3757bf57
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
<a name="ef-core-net-command-line-tools"></a><span data-ttu-id="f86e4-102">EF 核心.NET 命令列工具</span><span class="sxs-lookup"><span data-stu-id="f86e4-102">EF Core .NET Command-line Tools</span></span>
===============================
<span data-ttu-id="f86e4-103">Entity Framework Core.NET 命令列工具是跨平台擴充**dotnet**命令，也就是組件的[.NET Core SDK][2]。</span><span class="sxs-lookup"><span data-stu-id="f86e4-103">The Entity Framework Core .NET Command-line Tools are an extension to the cross-platform **dotnet** command, which is part of the [.NET Core SDK][2].</span></span>

> [!TIP]
> <span data-ttu-id="f86e4-104">如果您使用 Visual Studio，我們建議您[PMC 工具][ 1]相反地，因為它們提供更加整合的經驗。</span><span class="sxs-lookup"><span data-stu-id="f86e4-104">If you're using Visual Studio, we recommend [the PMC Tools][1] instead since they provide a more integrated experience.</span></span>

<a name="installing-the-tools"></a><span data-ttu-id="f86e4-105">安裝工具</span><span class="sxs-lookup"><span data-stu-id="f86e4-105">Installing the tools</span></span>
--------------------
<span data-ttu-id="f86e4-106">安裝 EF 核心.NET 命令列工具使用下列步驟：</span><span class="sxs-lookup"><span data-stu-id="f86e4-106">Install the EF Core .NET Command-line Tools using these steps:</span></span>

1. <span data-ttu-id="f86e4-107">編輯專案檔，並新增 Microsoft.EntityFrameworkCore.Tools.DotNet 為 DotNetCliToolReference 項目 （請參閱下文）</span><span class="sxs-lookup"><span data-stu-id="f86e4-107">Edit the project file and add Microsoft.EntityFrameworkCore.Tools.DotNet as a DotNetCliToolReference item (See below)</span></span>
2. <span data-ttu-id="f86e4-108">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="f86e4-108">Run the following commands:</span></span>

       dotnet add package Microsoft.EntityFrameworkCore.Design
       dotnet restore


<span data-ttu-id="f86e4-109">產生的專案看起來應該像這樣：</span><span class="sxs-lookup"><span data-stu-id="f86e4-109">The resulting project should look something like this:</span></span>

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
> <span data-ttu-id="f86e4-110">封裝參考`PrivateAssets="All"`表示它不公開給專案參考此專案中，這是特別適用於通常只在開發期間使用的封裝。</span><span class="sxs-lookup"><span data-stu-id="f86e4-110">A package reference with `PrivateAssets="All"` means it isn't exposed to projects that reference this project, which is especially useful for packages that are typically only used during development.</span></span>

<span data-ttu-id="f86e4-111">如果您這樣做的所有項目權限，您應該能夠成功地在命令提示字元中執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="f86e4-111">If you did everything right, you should be able to successfully run the following command in a command prompt.</span></span>

``` Console
dotnet ef
```

<a name="using-the-tools"></a><span data-ttu-id="f86e4-112">使用的工具</span><span class="sxs-lookup"><span data-stu-id="f86e4-112">Using the tools</span></span>
---------------
<span data-ttu-id="f86e4-113">當您叫用命令時，有兩個專案相關：</span><span class="sxs-lookup"><span data-stu-id="f86e4-113">Whenever you invoke a command, there are two projects involved:</span></span>

<span data-ttu-id="f86e4-114">目標專案是其中的任何檔案會加入 （或在某些情況下移除）。</span><span class="sxs-lookup"><span data-stu-id="f86e4-114">The target project is where any files are added (or in some cases removed).</span></span> <span data-ttu-id="f86e4-115">目標專案預設值為目前的目錄中的專案，但可以使用變更<nobr> **-專案**</nobr>選項。</span><span class="sxs-lookup"><span data-stu-id="f86e4-115">The target project defaults to the project in the current directory, but can be changed using the <nobr>**--project**</nobr> option.</span></span>

<span data-ttu-id="f86e4-116">啟始專案是模擬的工具執行您的專案程式碼時。</span><span class="sxs-lookup"><span data-stu-id="f86e4-116">The startup project is the one emulated by the tools when executing your project's code.</span></span> <span data-ttu-id="f86e4-117">它也會預設為目前的目錄中的專案，但可以使用變更**-啟始專案**選項。</span><span class="sxs-lookup"><span data-stu-id="f86e4-117">It also defaults to the project in the current directory, but can be changed using the **--startup-project** option.</span></span>

<span data-ttu-id="f86e4-118">常用選項：</span><span class="sxs-lookup"><span data-stu-id="f86e4-118">Common options:</span></span>

|    |                                  |                             |
| -- | -------------------------------- | --------------------------- |
|    | <span data-ttu-id="f86e4-119">-json</span><span class="sxs-lookup"><span data-stu-id="f86e4-119">--json</span></span>                           | <span data-ttu-id="f86e4-120">顯示 JSON 輸出。</span><span class="sxs-lookup"><span data-stu-id="f86e4-120">Show JSON output.</span></span>           |
| <span data-ttu-id="f86e4-121">-c</span><span class="sxs-lookup"><span data-stu-id="f86e4-121">-c</span></span> | <span data-ttu-id="f86e4-122">-內容\<DBCONTEXT ></span><span class="sxs-lookup"><span data-stu-id="f86e4-122">--context \<DBCONTEXT></span></span>           | <span data-ttu-id="f86e4-123">若要使用 DbContext。</span><span class="sxs-lookup"><span data-stu-id="f86e4-123">The DbContext to use.</span></span>       |
| <span data-ttu-id="f86e4-124">-P</span><span class="sxs-lookup"><span data-stu-id="f86e4-124">-p</span></span> | <span data-ttu-id="f86e4-125">-專案\<專案 ></span><span class="sxs-lookup"><span data-stu-id="f86e4-125">--project \<PROJECT></span></span>             | <span data-ttu-id="f86e4-126">要使用的專案。</span><span class="sxs-lookup"><span data-stu-id="f86e4-126">The project to use.</span></span>         |
| <span data-ttu-id="f86e4-127">-s</span><span class="sxs-lookup"><span data-stu-id="f86e4-127">-s</span></span> | <span data-ttu-id="f86e4-128">-啟始專案\<專案 ></span><span class="sxs-lookup"><span data-stu-id="f86e4-128">--startup-project \<PROJECT></span></span>     | <span data-ttu-id="f86e4-129">若要使用啟始專案。</span><span class="sxs-lookup"><span data-stu-id="f86e4-129">The startup project to use.</span></span> |
|    | <span data-ttu-id="f86e4-130">-framework\<架構 ></span><span class="sxs-lookup"><span data-stu-id="f86e4-130">--framework \<FRAMEWORK></span></span>         | <span data-ttu-id="f86e4-131">目標架構。</span><span class="sxs-lookup"><span data-stu-id="f86e4-131">The target framework.</span></span>       |
|    | <span data-ttu-id="f86e4-132">-設定\<設定 ></span><span class="sxs-lookup"><span data-stu-id="f86e4-132">--configuration \<CONFIGURATION></span></span> | <span data-ttu-id="f86e4-133">要使用的組態。</span><span class="sxs-lookup"><span data-stu-id="f86e4-133">The configuration to use.</span></span>   |
|    | <span data-ttu-id="f86e4-134">-執行階段\<識別項 ></span><span class="sxs-lookup"><span data-stu-id="f86e4-134">--runtime \<IDENTIFIER></span></span>          | <span data-ttu-id="f86e4-135">在執行階段使用。</span><span class="sxs-lookup"><span data-stu-id="f86e4-135">The runtime to use.</span></span>         |
| <span data-ttu-id="f86e4-136">-h</span><span class="sxs-lookup"><span data-stu-id="f86e4-136">-h</span></span> | <span data-ttu-id="f86e4-137">-說明</span><span class="sxs-lookup"><span data-stu-id="f86e4-137">--help</span></span>                           | <span data-ttu-id="f86e4-138">顯示說明資訊。</span><span class="sxs-lookup"><span data-stu-id="f86e4-138">Show help information.</span></span>      |
| <span data-ttu-id="f86e4-139">-v</span><span class="sxs-lookup"><span data-stu-id="f86e4-139">-v</span></span> | <span data-ttu-id="f86e4-140">-verbose</span><span class="sxs-lookup"><span data-stu-id="f86e4-140">--verbose</span></span>                        | <span data-ttu-id="f86e4-141">顯示詳細資訊輸出。</span><span class="sxs-lookup"><span data-stu-id="f86e4-141">Show verbose output.</span></span>        |
|    | <span data-ttu-id="f86e4-142">-無色彩</span><span class="sxs-lookup"><span data-stu-id="f86e4-142">--no-color</span></span>                       | <span data-ttu-id="f86e4-143">不要以色彩標示輸出。</span><span class="sxs-lookup"><span data-stu-id="f86e4-143">Don't colorize output.</span></span>      |
|    | <span data-ttu-id="f86e4-144">-前置詞輸出</span><span class="sxs-lookup"><span data-stu-id="f86e4-144">--prefix-output</span></span>                  | <span data-ttu-id="f86e4-145">輸出層級的前置詞。</span><span class="sxs-lookup"><span data-stu-id="f86e4-145">Prefix output with level.</span></span>   |


> [!TIP]
> <span data-ttu-id="f86e4-146">指定 ASP.NET Core 環境，請設定**ASPNETCORE_ENVIRONMENT**之前執行的環境變數。</span><span class="sxs-lookup"><span data-stu-id="f86e4-146">To specify the ASP.NET Core environment, set the **ASPNETCORE_ENVIRONMENT** environment variable before running.</span></span>

<a name="commands"></a><span data-ttu-id="f86e4-147">命令</span><span class="sxs-lookup"><span data-stu-id="f86e4-147">Commands</span></span>
--------

### <a name="dotnet-ef-database-drop"></a><span data-ttu-id="f86e4-148">dotnet ef 資料庫卸除</span><span class="sxs-lookup"><span data-stu-id="f86e4-148">dotnet ef database drop</span></span>

<span data-ttu-id="f86e4-149">卸除資料庫。</span><span class="sxs-lookup"><span data-stu-id="f86e4-149">Drops the database.</span></span>

<span data-ttu-id="f86e4-150">選項:</span><span class="sxs-lookup"><span data-stu-id="f86e4-150">Options:</span></span>

|    |           |                                                          |
| -- | --------- | -------------------------------------------------------- |
| <span data-ttu-id="f86e4-151">-f</span><span class="sxs-lookup"><span data-stu-id="f86e4-151">-f</span></span> | <span data-ttu-id="f86e4-152">-強制</span><span class="sxs-lookup"><span data-stu-id="f86e4-152">--force</span></span>   | <span data-ttu-id="f86e4-153">未確認。</span><span class="sxs-lookup"><span data-stu-id="f86e4-153">Don't confirm.</span></span>                                           |
|    | <span data-ttu-id="f86e4-154">-乾執行</span><span class="sxs-lookup"><span data-stu-id="f86e4-154">--dry-run</span></span> | <span data-ttu-id="f86e4-155">顯示哪一個資料庫會卸除，但不卸除它。</span><span class="sxs-lookup"><span data-stu-id="f86e4-155">Show which database would be dropped, but don't drop it.</span></span> |

### <a name="dotnet-ef-database-update"></a><span data-ttu-id="f86e4-156">dotnet ef 資料庫更新</span><span class="sxs-lookup"><span data-stu-id="f86e4-156">dotnet ef database update</span></span>

<span data-ttu-id="f86e4-157">將資料庫更新為指定的移轉。</span><span class="sxs-lookup"><span data-stu-id="f86e4-157">Updates the database to a specified migration.</span></span>

<span data-ttu-id="f86e4-158">引數：</span><span class="sxs-lookup"><span data-stu-id="f86e4-158">Arguments:</span></span>

|              |                                                                                              |
| ------------ | ---------------------------------------------------------------------------------------------|
| <span data-ttu-id="f86e4-159">\<移轉 ></span><span class="sxs-lookup"><span data-stu-id="f86e4-159">\<MIGRATION></span></span> | <span data-ttu-id="f86e4-160">在目標移轉。</span><span class="sxs-lookup"><span data-stu-id="f86e4-160">The target migration.</span></span> <span data-ttu-id="f86e4-161">如果為 0，就會還原所有移轉。</span><span class="sxs-lookup"><span data-stu-id="f86e4-161">If 0, all migrations will be reverted.</span></span> <span data-ttu-id="f86e4-162">預設的最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="f86e4-162">Defaults to the last migration.</span></span> |

### <a name="dotnet-ef-dbcontext-info"></a><span data-ttu-id="f86e4-163">dotnet ef dbcontext 資訊</span><span class="sxs-lookup"><span data-stu-id="f86e4-163">dotnet ef dbcontext info</span></span>

<span data-ttu-id="f86e4-164">取得 DbContext 類型的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="f86e4-164">Gets information about a DbContext type.</span></span>

### <a name="dotnet-ef-dbcontext-list"></a><span data-ttu-id="f86e4-165">dotnet ef dbcontext 清單</span><span class="sxs-lookup"><span data-stu-id="f86e4-165">dotnet ef dbcontext list</span></span>

<span data-ttu-id="f86e4-166">列出可用的 DbContext 類型。</span><span class="sxs-lookup"><span data-stu-id="f86e4-166">Lists available DbContext types.</span></span>

### <a name="dotnet-ef-dbcontext-scaffold"></a><span data-ttu-id="f86e4-167">dotnet ef dbcontext scaffold</span><span class="sxs-lookup"><span data-stu-id="f86e4-167">dotnet ef dbcontext scaffold</span></span>

<span data-ttu-id="f86e4-168">Scaffold DbContext 類型及實體類型的資料庫。</span><span class="sxs-lookup"><span data-stu-id="f86e4-168">Scaffolds a DbContext and entity types for a database.</span></span>

<span data-ttu-id="f86e4-169">引數：</span><span class="sxs-lookup"><span data-stu-id="f86e4-169">Arguments:</span></span>

|               |                                                                     |
| ------------- | ------------------------------------------------------------------- |
| <span data-ttu-id="f86e4-170">\<連接 ></span><span class="sxs-lookup"><span data-stu-id="f86e4-170">\<CONNECTION></span></span> | <span data-ttu-id="f86e4-171">資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="f86e4-171">The connection string to the database.</span></span>                              |
| <span data-ttu-id="f86e4-172">\<提供者 ></span><span class="sxs-lookup"><span data-stu-id="f86e4-172">\<PROVIDER></span></span>   | <span data-ttu-id="f86e4-173">若要使用提供者。</span><span class="sxs-lookup"><span data-stu-id="f86e4-173">The provider to use.</span></span> <span data-ttu-id="f86e4-174">(例如，</span><span class="sxs-lookup"><span data-stu-id="f86e4-174">(E.g.</span></span> <span data-ttu-id="f86e4-175">Microsoft.EntityFrameworkCore.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="f86e4-175">Microsoft.EntityFrameworkCore.SqlServer)</span></span> |

<span data-ttu-id="f86e4-176">選項:</span><span class="sxs-lookup"><span data-stu-id="f86e4-176">Options:</span></span>

|                 |                                         |                                                          |
| --------------- | --------------------------------------- | -------------------------------------------------------- |
| <span data-ttu-id="f86e4-177"><nobr>-d</nobr></span><span class="sxs-lookup"><span data-stu-id="f86e4-177"><nobr>-d</nobr></span></span> |       <span data-ttu-id="f86e4-178">-資料註解</span><span class="sxs-lookup"><span data-stu-id="f86e4-178">--data-annotations</span></span>                | <span data-ttu-id="f86e4-179">使用屬性，將模型設定 （如果可能的話）。</span><span class="sxs-lookup"><span data-stu-id="f86e4-179">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="f86e4-180">如果省略，則會使用 fluent 應用程式開發的 API。</span><span class="sxs-lookup"><span data-stu-id="f86e4-180">If omitted, only the fluent API is used.</span></span> |
|       <span data-ttu-id="f86e4-181">-c</span><span class="sxs-lookup"><span data-stu-id="f86e4-181">-c</span></span>        |       <span data-ttu-id="f86e4-182">-內容\<名稱 ></span><span class="sxs-lookup"><span data-stu-id="f86e4-182">--context \<NAME></span></span>                 | <span data-ttu-id="f86e4-183">DbContext 名稱。</span><span class="sxs-lookup"><span data-stu-id="f86e4-183">The name of the DbContext.</span></span>                               |
|       <span data-ttu-id="f86e4-184">-f</span><span class="sxs-lookup"><span data-stu-id="f86e4-184">-f</span></span>        |       <span data-ttu-id="f86e4-185">-強制</span><span class="sxs-lookup"><span data-stu-id="f86e4-185">--force</span></span>                           | <span data-ttu-id="f86e4-186">覆寫現有檔案。</span><span class="sxs-lookup"><span data-stu-id="f86e4-186">Overwrite existing files.</span></span>                                |
|       <span data-ttu-id="f86e4-187">-o</span><span class="sxs-lookup"><span data-stu-id="f86e4-187">-o</span></span>        |       <span data-ttu-id="f86e4-188">-輸出 dir\<路徑 ></span><span class="sxs-lookup"><span data-stu-id="f86e4-188">--output-dir \<PATH></span></span>              | <span data-ttu-id="f86e4-189">將檔案放入目錄。</span><span class="sxs-lookup"><span data-stu-id="f86e4-189">The directory to put files in.</span></span> <span data-ttu-id="f86e4-190">路徑是相對於專案目錄。</span><span class="sxs-lookup"><span data-stu-id="f86e4-190">Paths are relative to the project directory.</span></span> |
|                 | <span data-ttu-id="f86e4-191"><nobr>-結構描述\<SCHEMA_NAME >...</nobr></span><span class="sxs-lookup"><span data-stu-id="f86e4-191"><nobr>--schema \<SCHEMA_NAME>...</nobr></span></span> | <span data-ttu-id="f86e4-192">要產生實體類型的資料表結構描述。</span><span class="sxs-lookup"><span data-stu-id="f86e4-192">The schemas of tables to generate entity types for.</span></span>      |
|       <span data-ttu-id="f86e4-193">-t</span><span class="sxs-lookup"><span data-stu-id="f86e4-193">-t</span></span>        |       <span data-ttu-id="f86e4-194">-資料表\<TABLE_NAME >...</span><span class="sxs-lookup"><span data-stu-id="f86e4-194">--table \<TABLE_NAME>...</span></span>          | <span data-ttu-id="f86e4-195">要產生實體類型的資料表。</span><span class="sxs-lookup"><span data-stu-id="f86e4-195">The tables to generate entity types for.</span></span>                 |
|                 |       <span data-ttu-id="f86e4-196">-使用資料庫名稱</span><span class="sxs-lookup"><span data-stu-id="f86e4-196">--use-database-names</span></span>              | <span data-ttu-id="f86e4-197">使用直接從資料庫資料表和資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="f86e4-197">Use table and column names directly from the database.</span></span>   |

### <a name="dotnet-ef-migrations-add"></a><span data-ttu-id="f86e4-198">dotnet ef 移轉新增</span><span class="sxs-lookup"><span data-stu-id="f86e4-198">dotnet ef migrations add</span></span>

<span data-ttu-id="f86e4-199">加入新的移轉。</span><span class="sxs-lookup"><span data-stu-id="f86e4-199">Adds a new migration.</span></span>

<span data-ttu-id="f86e4-200">引數：</span><span class="sxs-lookup"><span data-stu-id="f86e4-200">Arguments:</span></span>

|         |                            |
| ------- | -------------------------- |
| <span data-ttu-id="f86e4-201">\<名稱 ></span><span class="sxs-lookup"><span data-stu-id="f86e4-201">\<NAME></span></span> | <span data-ttu-id="f86e4-202">移轉程序的名稱。</span><span class="sxs-lookup"><span data-stu-id="f86e4-202">The name of the migration.</span></span> |

<span data-ttu-id="f86e4-203">選項:</span><span class="sxs-lookup"><span data-stu-id="f86e4-203">Options:</span></span>

|                 |                                   |                                                                |
| --------------- |---------------------------------- | -------------------------------------------------------------- |
| <span data-ttu-id="f86e4-204"><nobr>-o</nobr></span><span class="sxs-lookup"><span data-stu-id="f86e4-204"><nobr>-o</nobr></span></span> | <span data-ttu-id="f86e4-205"><nobr>-輸出 dir\<路徑 ></nobr></span><span class="sxs-lookup"><span data-stu-id="f86e4-205"><nobr>--output-dir \<PATH></nobr></span></span> | <span data-ttu-id="f86e4-206">目錄 （以及子命名空間） 使用。</span><span class="sxs-lookup"><span data-stu-id="f86e4-206">The directory (and sub-namespace) to use.</span></span> <span data-ttu-id="f86e4-207">路徑是相對於專案目錄。</span><span class="sxs-lookup"><span data-stu-id="f86e4-207">Paths are relative to the project directory.</span></span> <span data-ttu-id="f86e4-208">預設為 「 移轉 」。</span><span class="sxs-lookup"><span data-stu-id="f86e4-208">Defaults to "Migrations".</span></span> |

### <a name="dotnet-ef-migrations-list"></a><span data-ttu-id="f86e4-209">dotnet ef 移轉清單</span><span class="sxs-lookup"><span data-stu-id="f86e4-209">dotnet ef migrations list</span></span>

<span data-ttu-id="f86e4-210">列出可用的移轉。</span><span class="sxs-lookup"><span data-stu-id="f86e4-210">Lists available migrations.</span></span>

### <a name="dotnet-ef-migrations-remove"></a><span data-ttu-id="f86e4-211">dotnet ef 移轉移除</span><span class="sxs-lookup"><span data-stu-id="f86e4-211">dotnet ef migrations remove</span></span>

<span data-ttu-id="f86e4-212">移除最後的移轉。</span><span class="sxs-lookup"><span data-stu-id="f86e4-212">Removes the last migration.</span></span>

<span data-ttu-id="f86e4-213">選項:</span><span class="sxs-lookup"><span data-stu-id="f86e4-213">Options:</span></span>

|    |         |                                                                       |
| -- | ------- | --------------------------------------------------------------------- |
| <span data-ttu-id="f86e4-214">-f</span><span class="sxs-lookup"><span data-stu-id="f86e4-214">-f</span></span> | <span data-ttu-id="f86e4-215">-強制</span><span class="sxs-lookup"><span data-stu-id="f86e4-215">--force</span></span> | <span data-ttu-id="f86e4-216">不會檢查以查看是否移轉套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="f86e4-216">Don't check to see if the migration has been applied to the database.</span></span> |

### <a name="dotnet-ef-migrations-script"></a><span data-ttu-id="f86e4-217">dotnet ef 移轉指令碼</span><span class="sxs-lookup"><span data-stu-id="f86e4-217">dotnet ef migrations script</span></span>

<span data-ttu-id="f86e4-218">從移轉中產生 SQL 指令碼。</span><span class="sxs-lookup"><span data-stu-id="f86e4-218">Generates a SQL script from migrations.</span></span>

<span data-ttu-id="f86e4-219">引數：</span><span class="sxs-lookup"><span data-stu-id="f86e4-219">Arguments:</span></span>

|         |                                                               |
| ------- | ------------------------------------------------------------- |
| <span data-ttu-id="f86e4-220">\<從 ></span><span class="sxs-lookup"><span data-stu-id="f86e4-220">\<FROM></span></span> | <span data-ttu-id="f86e4-221">開始移轉。</span><span class="sxs-lookup"><span data-stu-id="f86e4-221">The starting migration.</span></span> <span data-ttu-id="f86e4-222">預設值為 0 （初始資料庫）。</span><span class="sxs-lookup"><span data-stu-id="f86e4-222">Defaults to 0 (the initial database).</span></span> |
| <span data-ttu-id="f86e4-223">\<若要 ></span><span class="sxs-lookup"><span data-stu-id="f86e4-223">\<TO></span></span>   | <span data-ttu-id="f86e4-224">結束的移轉。</span><span class="sxs-lookup"><span data-stu-id="f86e4-224">The ending migration.</span></span> <span data-ttu-id="f86e4-225">預設的最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="f86e4-225">Defaults to the last migration.</span></span>         |

<span data-ttu-id="f86e4-226">選項:</span><span class="sxs-lookup"><span data-stu-id="f86e4-226">Options:</span></span>

|    |                  |                                                                    |
| -- | ---------------- | ------------------------------------------------------------------ |
| <span data-ttu-id="f86e4-227">-o</span><span class="sxs-lookup"><span data-stu-id="f86e4-227">-o</span></span> | <span data-ttu-id="f86e4-228">-輸出\<檔案 ></span><span class="sxs-lookup"><span data-stu-id="f86e4-228">--output \<FILE></span></span> | <span data-ttu-id="f86e4-229">若要將結果寫入檔案。</span><span class="sxs-lookup"><span data-stu-id="f86e4-229">The file to write the result to.</span></span>                                   |
| <span data-ttu-id="f86e4-230">-i</span><span class="sxs-lookup"><span data-stu-id="f86e4-230">-i</span></span> | <span data-ttu-id="f86e4-231">-等冪</span><span class="sxs-lookup"><span data-stu-id="f86e4-231">--idempotent</span></span>     | <span data-ttu-id="f86e4-232">產生的指令碼，可用在任何移轉的資料庫。</span><span class="sxs-lookup"><span data-stu-id="f86e4-232">Generate a script that can be used on a database at any migration.</span></span> |


  [1]: powershell.md
  [2]: https://www.microsoft.com/net/core
