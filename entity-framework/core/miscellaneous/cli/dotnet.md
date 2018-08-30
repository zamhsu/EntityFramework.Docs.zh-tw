---
title: .NET core CLI-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/6/2017
uid: core/miscellaneous/cli/dotnet
ms.openlocfilehash: c70003fc7e2c5381a22d26baacd3d76f32489328
ms.sourcegitcommit: 0cef7d448e1e47bdb333002e2254ed42d57b45b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2018
ms.locfileid: "43152410"
---
<a name="ef-core-net-command-line-tools"></a><span data-ttu-id="1a4e4-102">EF Core.NET 命令列工具</span><span class="sxs-lookup"><span data-stu-id="1a4e4-102">EF Core .NET Command-line Tools</span></span>
===============================
<span data-ttu-id="1a4e4-103">Entity Framework Core.NET 命令列工具是跨平台擴充**dotnet**命令，也就是組件的[.NET Core SDK][2]。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-103">The Entity Framework Core .NET Command-line Tools are an extension to the cross-platform **dotnet** command, which is part of the [.NET Core SDK][2].</span></span>

> [!TIP]
> <span data-ttu-id="1a4e4-104">如果您使用 Visual Studio，我們建議您[PMC 工具][ 1]而是因為它們提供更加整合的體驗。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-104">If you're using Visual Studio, we recommend [the PMC Tools][1] instead since they provide a more integrated experience.</span></span>

<a name="installing-the-tools"></a><span data-ttu-id="1a4e4-105">安裝工具</span><span class="sxs-lookup"><span data-stu-id="1a4e4-105">Installing the tools</span></span>
--------------------
> [!NOTE]
> <span data-ttu-id="1a4e4-106">.NET Core SDK 版本 2.1.300 和更新版本包含**dotnet ef**與 EF Core 2.0 和更新版本相容的命令。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-106">The .NET Core SDK version 2.1.300 and newer includes **dotnet ef** commands that are compatible with EF Core 2.0 and later versions.</span></span> <span data-ttu-id="1a4e4-107">因此如果您使用最新版.NET Core SDK 和 EF Core 執行階段，不需安裝作業，而且您可以忽略此章節的其餘部分。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-107">Therefore if you are using recent versions of the .NET Core SDK and the EF Core runtime, no installation is required and you can ignore the rest of this section.</span></span>
>
> <span data-ttu-id="1a4e4-108">相反地， **dotnet ef**工具包含在.NET Core SDK 版本 2.1.300 及更新版本不相容的 EF Core 版本 1.0 和 1.1 版。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-108">On the other hand, the **dotnet ef** tool contained in .NET Core SDK version 2.1.300 and newer is not compatible with EF Core version 1.0 and 1.1.</span></span> <span data-ttu-id="1a4e4-109">您可以使用.NET Core sdk 2.1.300 的電腦使用的 EF Core 這些舊版本的專案或較新的安裝之前，您也必須安裝 2.1.200 版本或更舊版本的 sdk 和設定應用程式使用該較舊的版本修改其 [global.json](https://docs.microsoft.com/en-us/dotnet/core/tools/global-json)檔案。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-109">Before you can work with a project that uses these earlier versions of EF Core on a computer that has .NET Core SDK 2.1.300 or newer installed, you must also install version 2.1.200 or older of the SDK and configure the application to use that older version by modifying its [global.json](https://docs.microsoft.com/en-us/dotnet/core/tools/global-json) file.</span></span> <span data-ttu-id="1a4e4-110">此檔案通常會包含在方案目錄 （一個以上專案） 中。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-110">This file is normally included in the solution directory (one above the project).</span></span> <span data-ttu-id="1a4e4-111">然後您可以繼續使用下面的 installlation 指令。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-111">Then you can proceed with the installlation instruction below.</span></span>

<span data-ttu-id="1a4e4-112">舊版的.NET Core SDK，您可以安裝 EF Core.NET 命令列工具使用下列步驟：</span><span class="sxs-lookup"><span data-stu-id="1a4e4-112">For previous versions of the .NET Core SDK, you can install the EF Core .NET Command-line Tools using these steps:</span></span>

1. <span data-ttu-id="1a4e4-113">編輯專案檔，並加入 Microsoft.EntityFrameworkCore.Tools.DotNet 為 DotNetCliToolReference 項目 （如下所示）</span><span class="sxs-lookup"><span data-stu-id="1a4e4-113">Edit the project file and add Microsoft.EntityFrameworkCore.Tools.DotNet as a DotNetCliToolReference item (See below)</span></span>
2. <span data-ttu-id="1a4e4-114">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="1a4e4-114">Run the following commands:</span></span>

       dotnet add package Microsoft.EntityFrameworkCore.Design
       dotnet restore

<span data-ttu-id="1a4e4-115">產生的專案看起來應該像這樣：</span><span class="sxs-lookup"><span data-stu-id="1a4e4-115">The resulting project should look something like this:</span></span>

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
> <span data-ttu-id="1a4e4-116">使用的套件參考`PrivateAssets="All"`表示它不公開給專案參考此專案中，這特別適用於通常只能用在開發期間的套件。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-116">A package reference with `PrivateAssets="All"` means it isn't exposed to projects that reference this project, which is especially useful for packages that are typically only used during development.</span></span>

<span data-ttu-id="1a4e4-117">如果您這樣做的所有項目權限，您應該能夠成功地在命令提示字元中執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-117">If you did everything right, you should be able to successfully run the following command in a command prompt.</span></span>

``` Console
dotnet ef
```

<a name="using-the-tools"></a><span data-ttu-id="1a4e4-118">使用的工具</span><span class="sxs-lookup"><span data-stu-id="1a4e4-118">Using the tools</span></span>
---------------
<span data-ttu-id="1a4e4-119">每當您叫用命令時，有兩個專案包含：</span><span class="sxs-lookup"><span data-stu-id="1a4e4-119">Whenever you invoke a command, there are two projects involved:</span></span>

<span data-ttu-id="1a4e4-120">目標專案就是新增 (在某些情況下會移除) 任何檔案的位置。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-120">The target project is where any files are added (or in some cases removed).</span></span> <span data-ttu-id="1a4e4-121">目標專案預設為目前的目錄中的專案，但可以使用來變更<nobr> **`--project`** </nobr>選項。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-121">The target project defaults to the project in the current directory, but can be changed using the <nobr>**`--project`**</nobr> option.</span></span>

<span data-ttu-id="1a4e4-122">啟始專案是工具在執行您的專案程式碼時所模擬的專案。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-122">The startup project is the one emulated by the tools when executing your project's code.</span></span> <span data-ttu-id="1a4e4-123">它也會預設為目前的目錄中的專案，但可以使用來變更**`--startup-project`** 選項。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-123">It also defaults to the project in the current directory, but can be changed using the **`--startup-project`** option.</span></span>

> [!NOTE]
> <span data-ttu-id="1a4e4-124">比方說，更新 web 應用程式的 EF Core 安裝在不同的專案中的資料庫看起來會像這樣： `dotnet ef database update --project {project-path}` （從您的 web 應用程式目錄）</span><span class="sxs-lookup"><span data-stu-id="1a4e4-124">For instance, updating the database of your web application that has EF Core installed in a different project would look like this: `dotnet ef database update --project {project-path}` (from your web app directory)</span></span>

<span data-ttu-id="1a4e4-125">常見的選項：</span><span class="sxs-lookup"><span data-stu-id="1a4e4-125">Common options:</span></span>

|    |                                  |                             |
|:---|:---------------------------------|:----------------------------|
|    | `--json`                           | <span data-ttu-id="1a4e4-126">顯示 JSON 輸出。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-126">Show JSON output.</span></span>           |
| <span data-ttu-id="1a4e4-127">-c</span><span class="sxs-lookup"><span data-stu-id="1a4e4-127">-c</span></span> | `--context <DBCONTEXT>`           | <span data-ttu-id="1a4e4-128">要使用的 DbContext。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-128">The DbContext to use.</span></span>       |
| <span data-ttu-id="1a4e4-129">-p</span><span class="sxs-lookup"><span data-stu-id="1a4e4-129">-p</span></span> | `--project <PROJECT>`             | <span data-ttu-id="1a4e4-130">要使用的專案。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-130">The project to use.</span></span>         |
| <span data-ttu-id="1a4e4-131">-s</span><span class="sxs-lookup"><span data-stu-id="1a4e4-131">-s</span></span> | `--startup-project <PROJECT>`     | <span data-ttu-id="1a4e4-132">要使用的啟始專案。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-132">The startup project to use.</span></span> |
|    | `--framework <FRAMEWORK>`         | <span data-ttu-id="1a4e4-133">目標 framework。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-133">The target framework.</span></span>       |
|    | `--configuration <CONFIGURATION>` | <span data-ttu-id="1a4e4-134">要使用的組態。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-134">The configuration to use.</span></span>   |
|    | `--runtime <IDENTIFIER>`          | <span data-ttu-id="1a4e4-135">要使用的執行階段使</span><span class="sxs-lookup"><span data-stu-id="1a4e4-135">The runtime to use.</span></span>         |
| <span data-ttu-id="1a4e4-136">-h</span><span class="sxs-lookup"><span data-stu-id="1a4e4-136">-h</span></span> | `--help`                           | <span data-ttu-id="1a4e4-137">顯示說明資訊。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-137">Show help information.</span></span>      |
| <span data-ttu-id="1a4e4-138">-v</span><span class="sxs-lookup"><span data-stu-id="1a4e4-138">-v</span></span> | `--verbose`                        | <span data-ttu-id="1a4e4-139">顯示詳細資訊輸出。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-139">Show verbose output.</span></span>        |
|    | `--no-color`                       | <span data-ttu-id="1a4e4-140">沒有以色彩標示輸出。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-140">Don't colorize output.</span></span>      |
|    | `--prefix-output`                  | <span data-ttu-id="1a4e4-141">輸出層級的前置詞。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-141">Prefix output with level.</span></span>   |


> [!TIP]
> <span data-ttu-id="1a4e4-142">指定 ASP.NET Core 環境，請設定**ASPNETCORE_ENVIRONMENT**之前執行的環境變數。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-142">To specify the ASP.NET Core environment, set the **ASPNETCORE_ENVIRONMENT** environment variable before running.</span></span>

<a name="commands"></a><span data-ttu-id="1a4e4-143">命令</span><span class="sxs-lookup"><span data-stu-id="1a4e4-143">Commands</span></span>
--------

### <a name="dotnet-ef-database-drop"></a><span data-ttu-id="1a4e4-144">dotnet ef 資料庫卸除</span><span class="sxs-lookup"><span data-stu-id="1a4e4-144">dotnet ef database drop</span></span>

<span data-ttu-id="1a4e4-145">卸除資料庫。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-145">Drops the database.</span></span>

<span data-ttu-id="1a4e4-146">選項:</span><span class="sxs-lookup"><span data-stu-id="1a4e4-146">Options:</span></span>

|    |           |                                                          |
|:---|:----------|:---------------------------------------------------------|
| <span data-ttu-id="1a4e4-147">-f</span><span class="sxs-lookup"><span data-stu-id="1a4e4-147">-f</span></span> | `--force`   | <span data-ttu-id="1a4e4-148">未確認。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-148">Don't confirm.</span></span>                                           |
|    | `--dry-run` | <span data-ttu-id="1a4e4-149">顯示哪個資料庫會卸除，但不會卸除它。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-149">Show which database would be dropped, but don't drop it.</span></span> |

### <a name="dotnet-ef-database-update"></a><span data-ttu-id="1a4e4-150">dotnet ef 資料庫更新</span><span class="sxs-lookup"><span data-stu-id="1a4e4-150">dotnet ef database update</span></span>

<span data-ttu-id="1a4e4-151">將資料庫更新為指定的移轉。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-151">Updates the database to a specified migration.</span></span>

<span data-ttu-id="1a4e4-152">引數：</span><span class="sxs-lookup"><span data-stu-id="1a4e4-152">Arguments:</span></span>

|              |                                                                                              |
|:-------------|:---------------------------------------------------------------------------------------------|
| `<MIGRATION>` | <span data-ttu-id="1a4e4-153">目標移轉。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-153">The target migration.</span></span> <span data-ttu-id="1a4e4-154">如果為 0，會還原所有移轉。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-154">If 0, all migrations will be reverted.</span></span> <span data-ttu-id="1a4e4-155">預設為最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-155">Defaults to the last migration.</span></span> |

### <a name="dotnet-ef-dbcontext-info"></a><span data-ttu-id="1a4e4-156">dotnet ef dbcontext 資訊</span><span class="sxs-lookup"><span data-stu-id="1a4e4-156">dotnet ef dbcontext info</span></span>

<span data-ttu-id="1a4e4-157">取得 DbContext 類型的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-157">Gets information about a DbContext type.</span></span>

### <a name="dotnet-ef-dbcontext-list"></a><span data-ttu-id="1a4e4-158">dotnet ef dbcontext 清單</span><span class="sxs-lookup"><span data-stu-id="1a4e4-158">dotnet ef dbcontext list</span></span>

<span data-ttu-id="1a4e4-159">列出可用的 DbContext 類型。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-159">Lists available DbContext types.</span></span>

### <a name="dotnet-ef-dbcontext-scaffold"></a><span data-ttu-id="1a4e4-160">dotnet ef dbcontext scaffold</span><span class="sxs-lookup"><span data-stu-id="1a4e4-160">dotnet ef dbcontext scaffold</span></span>

<span data-ttu-id="1a4e4-161">建立資料庫的 DbContext 和實體類型。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-161">Scaffolds a DbContext and entity types for a database.</span></span>

<span data-ttu-id="1a4e4-162">引數：</span><span class="sxs-lookup"><span data-stu-id="1a4e4-162">Arguments:</span></span>

|               |                                                                             |
|:--------------|:----------------------------------------------------------------------------|
| `<CONNECTION>` | <span data-ttu-id="1a4e4-163">資料庫連接字串。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-163">The connection string to the database.</span></span>                                      |
| `<PROVIDER>`   | <span data-ttu-id="1a4e4-164">要使用提供者</span><span class="sxs-lookup"><span data-stu-id="1a4e4-164">The provider to use.</span></span> <span data-ttu-id="1a4e4-165">(例如 Microsoft.EntityFrameworkCore.SqlServer)。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-165">(for example, Microsoft.EntityFrameworkCore.SqlServer)</span></span> |

<span data-ttu-id="1a4e4-166">選項:</span><span class="sxs-lookup"><span data-stu-id="1a4e4-166">Options:</span></span>

|                 |                                         |                                                                                                  |
|:----------------|:----------------------------------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="1a4e4-167"><nobr>-d</nobr></span><span class="sxs-lookup"><span data-stu-id="1a4e4-167"><nobr>-d</nobr></span></span> | `--data-annotations`                      | <span data-ttu-id="1a4e4-168">使用屬性來設定模型 (如果可能的話)。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-168">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="1a4e4-169">如果省略，則只會使用 fluent API。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-169">If omitted, only the fluent API is used.</span></span> |
| <span data-ttu-id="1a4e4-170">-c</span><span class="sxs-lookup"><span data-stu-id="1a4e4-170">-c</span></span>              | `--context <NAME>`                       | <span data-ttu-id="1a4e4-171">DbContext 的名稱。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-171">The name of the DbContext.</span></span>                                                                       |
|                 | `--context-dir <PATH>`                   | <span data-ttu-id="1a4e4-172">若要將 DbContext 檔案放在目錄。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-172">The directory to put DbContext file in.</span></span> <span data-ttu-id="1a4e4-173">路徑是相對於專案目錄。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-173">Paths are relative to the project directory.</span></span>             |
| <span data-ttu-id="1a4e4-174">-f</span><span class="sxs-lookup"><span data-stu-id="1a4e4-174">-f</span></span>              | `--force`                                 | <span data-ttu-id="1a4e4-175">覆寫現有的檔案。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-175">Overwrite existing files.</span></span>                                                                        |
| <span data-ttu-id="1a4e4-176">-o</span><span class="sxs-lookup"><span data-stu-id="1a4e4-176">-o</span></span>              | `--output-dir <PATH>`                    | <span data-ttu-id="1a4e4-177">將檔案放入目錄。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-177">The directory to put files in.</span></span> <span data-ttu-id="1a4e4-178">路徑是相對於專案目錄。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-178">Paths are relative to the project directory.</span></span>                      |
|                 | <nobr>`--schema <SCHEMA_NAME>...`</nobr> | <span data-ttu-id="1a4e4-179">要產生的實體類型的資料表結構描述。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-179">The schemas of tables to generate entity types for.</span></span>                                              |
| <span data-ttu-id="1a4e4-180">-t</span><span class="sxs-lookup"><span data-stu-id="1a4e4-180">-t</span></span>              | <span data-ttu-id="1a4e4-181">`--table <TABLE_NAME>`...</span><span class="sxs-lookup"><span data-stu-id="1a4e4-181">`--table <TABLE_NAME>`...</span></span>                | <span data-ttu-id="1a4e4-182">要產生的實體類型的資料表。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-182">The tables to generate entity types for.</span></span>                                                         |
|                 | `--use-database-names`                    | <span data-ttu-id="1a4e4-183">直接使用資料庫的資料表與資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-183">Use table and column names directly from the database.</span></span>                                           |

### <a name="dotnet-ef-migrations-add"></a><span data-ttu-id="1a4e4-184">新增 dotnet ef 移轉</span><span class="sxs-lookup"><span data-stu-id="1a4e4-184">dotnet ef migrations add</span></span>

<span data-ttu-id="1a4e4-185">加入新的移轉。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-185">Adds a new migration.</span></span>

<span data-ttu-id="1a4e4-186">引數：</span><span class="sxs-lookup"><span data-stu-id="1a4e4-186">Arguments:</span></span>

|         |                            |
|:--------|:---------------------------|
| `<NAME>` | <span data-ttu-id="1a4e4-187">移轉名稱。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-187">The name of the migration.</span></span> |

<span data-ttu-id="1a4e4-188">選項:</span><span class="sxs-lookup"><span data-stu-id="1a4e4-188">Options:</span></span>

|                 |                                   |                                                                                                                  |
|:----------------|:----------------------------------|:-----------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="1a4e4-189"><nobr>-o</nobr></span><span class="sxs-lookup"><span data-stu-id="1a4e4-189"><nobr>-o</nobr></span></span> | <span data-ttu-id="1a4e4-190"><nobr> `--output-dir <PATH>` </nobr></span><span class="sxs-lookup"><span data-stu-id="1a4e4-190"><nobr> `--output-dir <PATH>` </nobr></span></span> | <span data-ttu-id="1a4e4-191">目錄 （及子命名空間） 使用。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-191">The directory (and sub-namespace) to use.</span></span> <span data-ttu-id="1a4e4-192">路徑是相對於專案目錄。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-192">Paths are relative to the project directory.</span></span> <span data-ttu-id="1a4e4-193">預設為 「 移轉 」。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-193">Defaults to "Migrations".</span></span> |

### <a name="dotnet-ef-migrations-list"></a><span data-ttu-id="1a4e4-194">dotnet ef migrations 清單</span><span class="sxs-lookup"><span data-stu-id="1a4e4-194">dotnet ef migrations list</span></span>

<span data-ttu-id="1a4e4-195">列出可用的移轉。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-195">Lists available migrations.</span></span>

### <a name="dotnet-ef-migrations-remove"></a><span data-ttu-id="1a4e4-196">dotnet ef migrations 移除</span><span class="sxs-lookup"><span data-stu-id="1a4e4-196">dotnet ef migrations remove</span></span>

<span data-ttu-id="1a4e4-197">移除最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-197">Removes the last migration.</span></span>

<span data-ttu-id="1a4e4-198">選項:</span><span class="sxs-lookup"><span data-stu-id="1a4e4-198">Options:</span></span>

|    |         |                                                                       |
|:---|:--------|:----------------------------------------------------------------------|
| <span data-ttu-id="1a4e4-199">-f</span><span class="sxs-lookup"><span data-stu-id="1a4e4-199">-f</span></span> | `--force` | <span data-ttu-id="1a4e4-200">如果套用至資料庫，請還原移轉。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-200">Revert the migration if it has been applied to the database.</span></span> |

### <a name="dotnet-ef-migrations-script"></a><span data-ttu-id="1a4e4-201">dotnet ef migrations 指令碼</span><span class="sxs-lookup"><span data-stu-id="1a4e4-201">dotnet ef migrations script</span></span>

<span data-ttu-id="1a4e4-202">產生 SQL 指令碼從移轉。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-202">Generates a SQL script from migrations.</span></span>

<span data-ttu-id="1a4e4-203">引數：</span><span class="sxs-lookup"><span data-stu-id="1a4e4-203">Arguments:</span></span>

|         |                                                               |
|:--------|:--------------------------------------------------------------|
| `<FROM>` | <span data-ttu-id="1a4e4-204">在開始移轉。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-204">The starting migration.</span></span> <span data-ttu-id="1a4e4-205">預設值為 0 （初始資料庫）。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-205">Defaults to 0 (the initial database).</span></span> |
| `<TO>`   | <span data-ttu-id="1a4e4-206">結束的移轉。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-206">The ending migration.</span></span> <span data-ttu-id="1a4e4-207">預設為最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-207">Defaults to the last migration.</span></span>         |

<span data-ttu-id="1a4e4-208">選項:</span><span class="sxs-lookup"><span data-stu-id="1a4e4-208">Options:</span></span>

|    |                  |                                                                    |
|:---|:-----------------|:-------------------------------------------------------------------|
| <span data-ttu-id="1a4e4-209">-o</span><span class="sxs-lookup"><span data-stu-id="1a4e4-209">-o</span></span> | `--output <FILE>` | <span data-ttu-id="1a4e4-210">要將結果寫入其中的檔案。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-210">The file to write the result to.</span></span>                                   |
| <span data-ttu-id="1a4e4-211">-i</span><span class="sxs-lookup"><span data-stu-id="1a4e4-211">-i</span></span> | `--idempotent`     | <span data-ttu-id="1a4e4-212">產生可在任何移轉隨時用在資料庫的指令碼。</span><span class="sxs-lookup"><span data-stu-id="1a4e4-212">Generate a script that can be used on a database at any migration.</span></span> |


  [1]: powershell.md
  [2]: https://www.microsoft.com/net/core
