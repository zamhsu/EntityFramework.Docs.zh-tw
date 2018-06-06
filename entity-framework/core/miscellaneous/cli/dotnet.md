---
title: .NET core CLI-EF 核心
author: bricelam
ms.author: bricelam
ms.date: 11/6/2017
ms.technology: entity-framework-core
uid: core/miscellaneous/cli/dotnet
ms.openlocfilehash: 721235b07e695efd8df43294e1f4e90c28ae83d7
ms.sourcegitcommit: 72e59e6af86b568653e1b29727529dfd7f65d312
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2018
ms.locfileid: "34754492"
---
<a name="ef-core-net-command-line-tools"></a><span data-ttu-id="f1ec8-102">EF 核心.NET 命令列工具</span><span class="sxs-lookup"><span data-stu-id="f1ec8-102">EF Core .NET Command-line Tools</span></span>
===============================
<span data-ttu-id="f1ec8-103">Entity Framework Core.NET 命令列工具是跨平台擴充**dotnet**命令，也就是組件的[.NET Core SDK][2]。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-103">The Entity Framework Core .NET Command-line Tools are an extension to the cross-platform **dotnet** command, which is part of the [.NET Core SDK][2].</span></span>

> [!TIP]
> <span data-ttu-id="f1ec8-104">如果您使用 Visual Studio，我們建議您[PMC 工具][ 1]相反地，因為它們提供更加整合的經驗。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-104">If you're using Visual Studio, we recommend [the PMC Tools][1] instead since they provide a more integrated experience.</span></span>

<a name="installing-the-tools"></a><span data-ttu-id="f1ec8-105">安裝工具</span><span class="sxs-lookup"><span data-stu-id="f1ec8-105">Installing the tools</span></span>
--------------------
> [!NOTE]
> <span data-ttu-id="f1ec8-106">.NET Core SDK 版本 2.1.300 和更新版本包含**dotnet ef**與 EF 核心 2.0 和更新版本相容的命令。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-106">The .NET Core SDK version 2.1.300 and newer includes **dotnet ef** commands that are compatible with EF Core 2.0 and later versions.</span></span> <span data-ttu-id="f1ec8-107">因此如果您使用最新版.NET Core SDK 和 EF 核心執行階段，不需安裝作業，而且您可以忽略此章節的其餘部分。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-107">Therefore if you are using recent versions of the .NET Core SDK and the EF Core runtime, no installation is required and you can ignore the rest of this section.</span></span>
>
> <span data-ttu-id="f1ec8-108">相反地， **dotnet ef**工具包含在.NET Core SDK 版本 2.1.300 及更新版本不相容的 EF 核心版本 1.0 和 1.1 版。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-108">On the other hand, the **dotnet ef** tool contained in .NET Core SDK version 2.1.300 and newer is not compatible with EF Core version 1.0 and 1.1.</span></span> <span data-ttu-id="f1ec8-109">您可以使用.NET Core sdk 2.1.300 的電腦使用的 EF 核心這些舊版本的專案或較新的安裝之前，您也必須安裝 2.1.200 版本或更舊版本的 sdk 和設定應用程式使用該較舊的版本修改其 [global.json](https://docs.microsoft.com/en-us/dotnet/core/tools/global-json)檔案。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-109">Before you can work with a project that uses these earlier versions of EF Core on a computer that has .NET Core SDK 2.1.300 or newer installed, you must also install version 2.1.200 or older of the SDK and configure the application to use that older version by modifying its [global.json](https://docs.microsoft.com/en-us/dotnet/core/tools/global-json) file.</span></span> <span data-ttu-id="f1ec8-110">這個檔案通常包含 （一個以上專案） 的方案目錄中。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-110">This file is normally included in the solution directory (one above the project).</span></span> <span data-ttu-id="f1ec8-111">然後您可以繼續以下列的 installlation 指令。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-111">Then you can proceed with the installlation instruction below.</span></span>

<span data-ttu-id="f1ec8-112">舊版的.NET Core SDK，您可以安裝 EF 核心.NET 命令列工具使用下列步驟：</span><span class="sxs-lookup"><span data-stu-id="f1ec8-112">For previous versions of the .NET Core SDK, you can install the EF Core .NET Command-line Tools using these steps:</span></span>

1. <span data-ttu-id="f1ec8-113">編輯專案檔，並新增 Microsoft.EntityFrameworkCore.Tools.DotNet 為 DotNetCliToolReference 項目 （請參閱下文）</span><span class="sxs-lookup"><span data-stu-id="f1ec8-113">Edit the project file and add Microsoft.EntityFrameworkCore.Tools.DotNet as a DotNetCliToolReference item (See below)</span></span>
2. <span data-ttu-id="f1ec8-114">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="f1ec8-114">Run the following commands:</span></span>

       dotnet add package Microsoft.EntityFrameworkCore.Design
       dotnet restore

<span data-ttu-id="f1ec8-115">產生的專案看起來應該像這樣：</span><span class="sxs-lookup"><span data-stu-id="f1ec8-115">The resulting project should look something like this:</span></span>

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
> <span data-ttu-id="f1ec8-116">封裝參考`PrivateAssets="All"`表示它不公開給專案參考此專案中，這是特別適用於通常只在開發期間使用的封裝。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-116">A package reference with `PrivateAssets="All"` means it isn't exposed to projects that reference this project, which is especially useful for packages that are typically only used during development.</span></span>

<span data-ttu-id="f1ec8-117">如果您這樣做的所有項目權限，您應該能夠成功地在命令提示字元中執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-117">If you did everything right, you should be able to successfully run the following command in a command prompt.</span></span>

``` Console
dotnet ef
```

<a name="using-the-tools"></a><span data-ttu-id="f1ec8-118">使用的工具</span><span class="sxs-lookup"><span data-stu-id="f1ec8-118">Using the tools</span></span>
---------------
<span data-ttu-id="f1ec8-119">當您叫用命令時，有兩個專案相關：</span><span class="sxs-lookup"><span data-stu-id="f1ec8-119">Whenever you invoke a command, there are two projects involved:</span></span>

<span data-ttu-id="f1ec8-120">目標專案就是新增 (在某些情況下會移除) 任何檔案的位置。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-120">The target project is where any files are added (or in some cases removed).</span></span> <span data-ttu-id="f1ec8-121">目標專案預設值為目前的目錄中的專案，但可以使用變更<nobr> **-專案**</nobr>選項。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-121">The target project defaults to the project in the current directory, but can be changed using the <nobr>**--project**</nobr> option.</span></span>

<span data-ttu-id="f1ec8-122">啟始專案是工具在執行您的專案程式碼時所模擬的專案。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-122">The startup project is the one emulated by the tools when executing your project's code.</span></span> <span data-ttu-id="f1ec8-123">它也會預設為目前的目錄中的專案，但可以使用變更 **-啟始專案**選項。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-123">It also defaults to the project in the current directory, but can be changed using the **--startup-project** option.</span></span>

> [!NOTE]
> <span data-ttu-id="f1ec8-124">比方說，更新 web 應用程式的 EF 核心安裝在不同的專案中的資料庫看起來會像這樣： `dotnet ef database update --project {project-path}` （從您的 web 應用程式目錄）</span><span class="sxs-lookup"><span data-stu-id="f1ec8-124">For instance, updating the database of your web application that has EF Core installed in a different project would look like this: `dotnet ef database update --project {project-path}` (from your web app directory)</span></span>

<span data-ttu-id="f1ec8-125">常用選項：</span><span class="sxs-lookup"><span data-stu-id="f1ec8-125">Common options:</span></span>

|    |                                  |                             |
|:---|:---------------------------------|:----------------------------|
|    | <span data-ttu-id="f1ec8-126">--json</span><span class="sxs-lookup"><span data-stu-id="f1ec8-126">--json</span></span>                           | <span data-ttu-id="f1ec8-127">顯示 JSON 輸出。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-127">Show JSON output.</span></span>           |
| <span data-ttu-id="f1ec8-128">-c</span><span class="sxs-lookup"><span data-stu-id="f1ec8-128">-c</span></span> | <span data-ttu-id="f1ec8-129">-內容\<DBCONTEXT ></span><span class="sxs-lookup"><span data-stu-id="f1ec8-129">--context \<DBCONTEXT></span></span>           | <span data-ttu-id="f1ec8-130">若要使用 DbContext。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-130">The DbContext to use.</span></span>       |
| <span data-ttu-id="f1ec8-131">-p</span><span class="sxs-lookup"><span data-stu-id="f1ec8-131">-p</span></span> | <span data-ttu-id="f1ec8-132">-專案\<專案 ></span><span class="sxs-lookup"><span data-stu-id="f1ec8-132">--project \<PROJECT></span></span>             | <span data-ttu-id="f1ec8-133">要使用的專案。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-133">The project to use.</span></span>         |
| <span data-ttu-id="f1ec8-134">-s</span><span class="sxs-lookup"><span data-stu-id="f1ec8-134">-s</span></span> | <span data-ttu-id="f1ec8-135">-啟始專案\<專案 ></span><span class="sxs-lookup"><span data-stu-id="f1ec8-135">--startup-project \<PROJECT></span></span>     | <span data-ttu-id="f1ec8-136">若要使用啟始專案。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-136">The startup project to use.</span></span> |
|    | <span data-ttu-id="f1ec8-137">-framework\<架構 ></span><span class="sxs-lookup"><span data-stu-id="f1ec8-137">--framework \<FRAMEWORK></span></span>         | <span data-ttu-id="f1ec8-138">目標 framework。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-138">The target framework.</span></span>       |
|    | <span data-ttu-id="f1ec8-139">-設定\<設定 ></span><span class="sxs-lookup"><span data-stu-id="f1ec8-139">--configuration \<CONFIGURATION></span></span> | <span data-ttu-id="f1ec8-140">要使用的組態。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-140">The configuration to use.</span></span>   |
|    | <span data-ttu-id="f1ec8-141">-執行階段\<識別項 ></span><span class="sxs-lookup"><span data-stu-id="f1ec8-141">--runtime \<IDENTIFIER></span></span>          | <span data-ttu-id="f1ec8-142">在執行階段使用。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-142">The runtime to use.</span></span>         |
| <span data-ttu-id="f1ec8-143">-h</span><span class="sxs-lookup"><span data-stu-id="f1ec8-143">-h</span></span> | <span data-ttu-id="f1ec8-144">-說明</span><span class="sxs-lookup"><span data-stu-id="f1ec8-144">--help</span></span>                           | <span data-ttu-id="f1ec8-145">顯示說明資訊。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-145">Show help information.</span></span>      |
| <span data-ttu-id="f1ec8-146">-v</span><span class="sxs-lookup"><span data-stu-id="f1ec8-146">-v</span></span> | <span data-ttu-id="f1ec8-147">-verbose</span><span class="sxs-lookup"><span data-stu-id="f1ec8-147">--verbose</span></span>                        | <span data-ttu-id="f1ec8-148">顯示詳細資訊輸出。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-148">Show verbose output.</span></span>        |
|    | <span data-ttu-id="f1ec8-149">--no-color</span><span class="sxs-lookup"><span data-stu-id="f1ec8-149">--no-color</span></span>                       | <span data-ttu-id="f1ec8-150">不要以色彩標示輸出。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-150">Don't colorize output.</span></span>      |
|    | <span data-ttu-id="f1ec8-151">--prefix-output</span><span class="sxs-lookup"><span data-stu-id="f1ec8-151">--prefix-output</span></span>                  | <span data-ttu-id="f1ec8-152">輸出層級的前置詞。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-152">Prefix output with level.</span></span>   |


> [!TIP]
> <span data-ttu-id="f1ec8-153">指定 ASP.NET Core 環境，請設定**ASPNETCORE_ENVIRONMENT**之前執行的環境變數。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-153">To specify the ASP.NET Core environment, set the **ASPNETCORE_ENVIRONMENT** environment variable before running.</span></span>

<a name="commands"></a><span data-ttu-id="f1ec8-154">命令</span><span class="sxs-lookup"><span data-stu-id="f1ec8-154">Commands</span></span>
--------

### <a name="dotnet-ef-database-drop"></a><span data-ttu-id="f1ec8-155">dotnet ef 資料庫卸除</span><span class="sxs-lookup"><span data-stu-id="f1ec8-155">dotnet ef database drop</span></span>

<span data-ttu-id="f1ec8-156">卸除資料庫。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-156">Drops the database.</span></span>

<span data-ttu-id="f1ec8-157">選項:</span><span class="sxs-lookup"><span data-stu-id="f1ec8-157">Options:</span></span>

|    |           |                                                          |
|:---|:----------|:---------------------------------------------------------|
| <span data-ttu-id="f1ec8-158">-f</span><span class="sxs-lookup"><span data-stu-id="f1ec8-158">-f</span></span> | <span data-ttu-id="f1ec8-159">--force</span><span class="sxs-lookup"><span data-stu-id="f1ec8-159">--force</span></span>   | <span data-ttu-id="f1ec8-160">未確認。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-160">Don't confirm.</span></span>                                           |
|    | <span data-ttu-id="f1ec8-161">-乾執行</span><span class="sxs-lookup"><span data-stu-id="f1ec8-161">--dry-run</span></span> | <span data-ttu-id="f1ec8-162">顯示哪一個資料庫會卸除，但不卸除它。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-162">Show which database would be dropped, but don't drop it.</span></span> |

### <a name="dotnet-ef-database-update"></a><span data-ttu-id="f1ec8-163">dotnet ef 資料庫更新</span><span class="sxs-lookup"><span data-stu-id="f1ec8-163">dotnet ef database update</span></span>

<span data-ttu-id="f1ec8-164">將資料庫更新為指定的移轉。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-164">Updates the database to a specified migration.</span></span>

<span data-ttu-id="f1ec8-165">引數：</span><span class="sxs-lookup"><span data-stu-id="f1ec8-165">Arguments:</span></span>

|              |                                                                                              |
|:-------------|:---------------------------------------------------------------------------------------------|
| <span data-ttu-id="f1ec8-166">\<移轉 &GT;</span><span class="sxs-lookup"><span data-stu-id="f1ec8-166">\<MIGRATION></span></span> | <span data-ttu-id="f1ec8-167">在目標移轉。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-167">The target migration.</span></span> <span data-ttu-id="f1ec8-168">如果為 0，就會還原所有移轉。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-168">If 0, all migrations will be reverted.</span></span> <span data-ttu-id="f1ec8-169">預設的最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-169">Defaults to the last migration.</span></span> |

### <a name="dotnet-ef-dbcontext-info"></a><span data-ttu-id="f1ec8-170">dotnet ef dbcontext 資訊</span><span class="sxs-lookup"><span data-stu-id="f1ec8-170">dotnet ef dbcontext info</span></span>

<span data-ttu-id="f1ec8-171">取得 DbContext 類型的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-171">Gets information about a DbContext type.</span></span>

### <a name="dotnet-ef-dbcontext-list"></a><span data-ttu-id="f1ec8-172">dotnet ef dbcontext 清單</span><span class="sxs-lookup"><span data-stu-id="f1ec8-172">dotnet ef dbcontext list</span></span>

<span data-ttu-id="f1ec8-173">列出可用的 DbContext 類型。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-173">Lists available DbContext types.</span></span>

### <a name="dotnet-ef-dbcontext-scaffold"></a><span data-ttu-id="f1ec8-174">dotnet ef dbcontext scaffold</span><span class="sxs-lookup"><span data-stu-id="f1ec8-174">dotnet ef dbcontext scaffold</span></span>

<span data-ttu-id="f1ec8-175">Scaffold DbContext 類型及實體類型的資料庫。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-175">Scaffolds a DbContext and entity types for a database.</span></span>

<span data-ttu-id="f1ec8-176">引數：</span><span class="sxs-lookup"><span data-stu-id="f1ec8-176">Arguments:</span></span>

|               |                                                                             |
|:--------------|:----------------------------------------------------------------------------|
| <span data-ttu-id="f1ec8-177">\<連接 &GT;</span><span class="sxs-lookup"><span data-stu-id="f1ec8-177">\<CONNECTION></span></span> | <span data-ttu-id="f1ec8-178">資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-178">The connection string to the database.</span></span>                                      |
| <span data-ttu-id="f1ec8-179">\<提供者 &GT;</span><span class="sxs-lookup"><span data-stu-id="f1ec8-179">\<PROVIDER></span></span>   | <span data-ttu-id="f1ec8-180">若要使用提供者。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-180">The provider to use.</span></span> <span data-ttu-id="f1ec8-181">（例如 Microsoft.EntityFrameworkCore.SqlServer）</span><span class="sxs-lookup"><span data-stu-id="f1ec8-181">(for example, Microsoft.EntityFrameworkCore.SqlServer)</span></span> |

<span data-ttu-id="f1ec8-182">選項:</span><span class="sxs-lookup"><span data-stu-id="f1ec8-182">Options:</span></span>

|                 |                                         |                                                                                                  |
|:----------------|:----------------------------------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f1ec8-183"><nobr>-d</nobr></span><span class="sxs-lookup"><span data-stu-id="f1ec8-183"><nobr>-d</nobr></span></span> | <span data-ttu-id="f1ec8-184">-資料註解</span><span class="sxs-lookup"><span data-stu-id="f1ec8-184">--data-annotations</span></span>                      | <span data-ttu-id="f1ec8-185">使用屬性，將模型設定 （如果可能的話）。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-185">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="f1ec8-186">如果省略，則會使用 fluent 應用程式開發的 API。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-186">If omitted, only the fluent API is used.</span></span> |
| <span data-ttu-id="f1ec8-187">-c</span><span class="sxs-lookup"><span data-stu-id="f1ec8-187">-c</span></span>              | <span data-ttu-id="f1ec8-188">-內容\<名稱 ></span><span class="sxs-lookup"><span data-stu-id="f1ec8-188">--context \<NAME></span></span>                       | <span data-ttu-id="f1ec8-189">DbContext 名稱。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-189">The name of the DbContext.</span></span>                                                                       |
|                 | <span data-ttu-id="f1ec8-190">-內容-dir\<路徑 ></span><span class="sxs-lookup"><span data-stu-id="f1ec8-190">--context-dir \<PATH></span></span>                   | <span data-ttu-id="f1ec8-191">將 DbContext 檔案放入目錄。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-191">The directory to put DbContext file in.</span></span> <span data-ttu-id="f1ec8-192">路徑是相對於專案目錄。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-192">Paths are relative to the project directory.</span></span>             |
| <span data-ttu-id="f1ec8-193">-f</span><span class="sxs-lookup"><span data-stu-id="f1ec8-193">-f</span></span>              | <span data-ttu-id="f1ec8-194">--force</span><span class="sxs-lookup"><span data-stu-id="f1ec8-194">--force</span></span>                                 | <span data-ttu-id="f1ec8-195">覆寫現有檔案。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-195">Overwrite existing files.</span></span>                                                                        |
| <span data-ttu-id="f1ec8-196">-o</span><span class="sxs-lookup"><span data-stu-id="f1ec8-196">-o</span></span>              | <span data-ttu-id="f1ec8-197">-輸出 dir\<路徑 ></span><span class="sxs-lookup"><span data-stu-id="f1ec8-197">--output-dir \<PATH></span></span>                    | <span data-ttu-id="f1ec8-198">將檔案放入目錄。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-198">The directory to put files in.</span></span> <span data-ttu-id="f1ec8-199">路徑是相對於專案目錄。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-199">Paths are relative to the project directory.</span></span>                      |
|                 | <span data-ttu-id="f1ec8-200"><nobr>-結構描述\<SCHEMA_NAME >...</nobr></span><span class="sxs-lookup"><span data-stu-id="f1ec8-200"><nobr>--schema \<SCHEMA_NAME>...</nobr></span></span> | <span data-ttu-id="f1ec8-201">要產生實體類型的資料表結構描述。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-201">The schemas of tables to generate entity types for.</span></span>                                              |
| <span data-ttu-id="f1ec8-202">-t</span><span class="sxs-lookup"><span data-stu-id="f1ec8-202">-t</span></span>              | <span data-ttu-id="f1ec8-203">-資料表\<TABLE_NAME >...</span><span class="sxs-lookup"><span data-stu-id="f1ec8-203">--table \<TABLE_NAME>...</span></span>                | <span data-ttu-id="f1ec8-204">要產生實體類型的資料表。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-204">The tables to generate entity types for.</span></span>                                                         |
|                 | <span data-ttu-id="f1ec8-205">-使用資料庫名稱</span><span class="sxs-lookup"><span data-stu-id="f1ec8-205">--use-database-names</span></span>                    | <span data-ttu-id="f1ec8-206">使用直接從資料庫資料表和資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-206">Use table and column names directly from the database.</span></span>                                           |

### <a name="dotnet-ef-migrations-add"></a><span data-ttu-id="f1ec8-207">dotnet ef 移轉新增</span><span class="sxs-lookup"><span data-stu-id="f1ec8-207">dotnet ef migrations add</span></span>

<span data-ttu-id="f1ec8-208">加入新的移轉。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-208">Adds a new migration.</span></span>

<span data-ttu-id="f1ec8-209">引數：</span><span class="sxs-lookup"><span data-stu-id="f1ec8-209">Arguments:</span></span>

|         |                            |
|:--------|:---------------------------|
| <span data-ttu-id="f1ec8-210">\<名稱 &GT;</span><span class="sxs-lookup"><span data-stu-id="f1ec8-210">\<NAME></span></span> | <span data-ttu-id="f1ec8-211">移轉程序的名稱。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-211">The name of the migration.</span></span> |

<span data-ttu-id="f1ec8-212">選項:</span><span class="sxs-lookup"><span data-stu-id="f1ec8-212">Options:</span></span>

|                 |                                   |                                                                                                                  |
|:----------------|:----------------------------------|:-----------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f1ec8-213"><nobr>-o</nobr></span><span class="sxs-lookup"><span data-stu-id="f1ec8-213"><nobr>-o</nobr></span></span> | <span data-ttu-id="f1ec8-214"><nobr>-輸出 dir\<路徑 ></nobr></span><span class="sxs-lookup"><span data-stu-id="f1ec8-214"><nobr>--output-dir \<PATH></nobr></span></span> | <span data-ttu-id="f1ec8-215">目錄 （以及子命名空間） 使用。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-215">The directory (and sub-namespace) to use.</span></span> <span data-ttu-id="f1ec8-216">路徑是相對於專案目錄。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-216">Paths are relative to the project directory.</span></span> <span data-ttu-id="f1ec8-217">預設為 「 移轉 」。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-217">Defaults to "Migrations".</span></span> |

### <a name="dotnet-ef-migrations-list"></a><span data-ttu-id="f1ec8-218">dotnet ef 移轉清單</span><span class="sxs-lookup"><span data-stu-id="f1ec8-218">dotnet ef migrations list</span></span>

<span data-ttu-id="f1ec8-219">列出可用的移轉。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-219">Lists available migrations.</span></span>

### <a name="dotnet-ef-migrations-remove"></a><span data-ttu-id="f1ec8-220">dotnet ef 移轉移除</span><span class="sxs-lookup"><span data-stu-id="f1ec8-220">dotnet ef migrations remove</span></span>

<span data-ttu-id="f1ec8-221">移除最後的移轉。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-221">Removes the last migration.</span></span>

<span data-ttu-id="f1ec8-222">選項:</span><span class="sxs-lookup"><span data-stu-id="f1ec8-222">Options:</span></span>

|    |         |                                                                       |
|:---|:--------|:----------------------------------------------------------------------|
| <span data-ttu-id="f1ec8-223">-f</span><span class="sxs-lookup"><span data-stu-id="f1ec8-223">-f</span></span> | <span data-ttu-id="f1ec8-224">--force</span><span class="sxs-lookup"><span data-stu-id="f1ec8-224">--force</span></span> | <span data-ttu-id="f1ec8-225">如果套用至資料庫，請還原移轉。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-225">Revert the migration if it has been applied to the database.</span></span> |

### <a name="dotnet-ef-migrations-script"></a><span data-ttu-id="f1ec8-226">dotnet ef 移轉指令碼</span><span class="sxs-lookup"><span data-stu-id="f1ec8-226">dotnet ef migrations script</span></span>

<span data-ttu-id="f1ec8-227">從移轉中產生 SQL 指令碼。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-227">Generates a SQL script from migrations.</span></span>

<span data-ttu-id="f1ec8-228">引數：</span><span class="sxs-lookup"><span data-stu-id="f1ec8-228">Arguments:</span></span>

|         |                                                               |
|:--------|:--------------------------------------------------------------|
| <span data-ttu-id="f1ec8-229">\<FROM></span><span class="sxs-lookup"><span data-stu-id="f1ec8-229">\<FROM></span></span> | <span data-ttu-id="f1ec8-230">開始移轉。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-230">The starting migration.</span></span> <span data-ttu-id="f1ec8-231">預設值為 0 （初始資料庫）。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-231">Defaults to 0 (the initial database).</span></span> |
| <span data-ttu-id="f1ec8-232">\<若要 &GT;</span><span class="sxs-lookup"><span data-stu-id="f1ec8-232">\<TO></span></span>   | <span data-ttu-id="f1ec8-233">結束的移轉。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-233">The ending migration.</span></span> <span data-ttu-id="f1ec8-234">預設的最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-234">Defaults to the last migration.</span></span>         |

<span data-ttu-id="f1ec8-235">選項:</span><span class="sxs-lookup"><span data-stu-id="f1ec8-235">Options:</span></span>

|    |                  |                                                                    |
|:---|:-----------------|:-------------------------------------------------------------------|
| <span data-ttu-id="f1ec8-236">-o</span><span class="sxs-lookup"><span data-stu-id="f1ec8-236">-o</span></span> | <span data-ttu-id="f1ec8-237">-輸出\<檔案 ></span><span class="sxs-lookup"><span data-stu-id="f1ec8-237">--output \<FILE></span></span> | <span data-ttu-id="f1ec8-238">若要將結果寫入檔案。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-238">The file to write the result to.</span></span>                                   |
| <span data-ttu-id="f1ec8-239">-i</span><span class="sxs-lookup"><span data-stu-id="f1ec8-239">-i</span></span> | <span data-ttu-id="f1ec8-240">-等冪</span><span class="sxs-lookup"><span data-stu-id="f1ec8-240">--idempotent</span></span>     | <span data-ttu-id="f1ec8-241">產生的指令碼，可用在任何移轉的資料庫。</span><span class="sxs-lookup"><span data-stu-id="f1ec8-241">Generate a script that can be used on a database at any migration.</span></span> |


  [1]: powershell.md
  [2]: https://www.microsoft.com/net/core
