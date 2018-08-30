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
<a name="ef-core-net-command-line-tools"></a>EF Core.NET 命令列工具
===============================
Entity Framework Core.NET 命令列工具是跨平台擴充**dotnet**命令，也就是組件的[.NET Core SDK][2]。

> [!TIP]
> 如果您使用 Visual Studio，我們建議您[PMC 工具][ 1]而是因為它們提供更加整合的體驗。

<a name="installing-the-tools"></a>安裝工具
--------------------
> [!NOTE]
> .NET Core SDK 版本 2.1.300 和更新版本包含**dotnet ef**與 EF Core 2.0 和更新版本相容的命令。 因此如果您使用最新版.NET Core SDK 和 EF Core 執行階段，不需安裝作業，而且您可以忽略此章節的其餘部分。
>
> 相反地， **dotnet ef**工具包含在.NET Core SDK 版本 2.1.300 及更新版本不相容的 EF Core 版本 1.0 和 1.1 版。 您可以使用.NET Core sdk 2.1.300 的電腦使用的 EF Core 這些舊版本的專案或較新的安裝之前，您也必須安裝 2.1.200 版本或更舊版本的 sdk 和設定應用程式使用該較舊的版本修改其 [global.json](https://docs.microsoft.com/en-us/dotnet/core/tools/global-json)檔案。 此檔案通常會包含在方案目錄 （一個以上專案） 中。 然後您可以繼續使用下面的 installlation 指令。

舊版的.NET Core SDK，您可以安裝 EF Core.NET 命令列工具使用下列步驟：

1. 編輯專案檔，並加入 Microsoft.EntityFrameworkCore.Tools.DotNet 為 DotNetCliToolReference 項目 （如下所示）
2. 執行下列命令：

       dotnet add package Microsoft.EntityFrameworkCore.Design
       dotnet restore

產生的專案看起來應該像這樣：

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
> 使用的套件參考`PrivateAssets="All"`表示它不公開給專案參考此專案中，這特別適用於通常只能用在開發期間的套件。

如果您這樣做的所有項目權限，您應該能夠成功地在命令提示字元中執行下列命令。

``` Console
dotnet ef
```

<a name="using-the-tools"></a>使用的工具
---------------
每當您叫用命令時，有兩個專案包含：

目標專案就是新增 (在某些情況下會移除) 任何檔案的位置。 目標專案預設為目前的目錄中的專案，但可以使用來變更<nobr> **`--project`** </nobr>選項。

啟始專案是工具在執行您的專案程式碼時所模擬的專案。 它也會預設為目前的目錄中的專案，但可以使用來變更**`--startup-project`** 選項。

> [!NOTE]
> 比方說，更新 web 應用程式的 EF Core 安裝在不同的專案中的資料庫看起來會像這樣： `dotnet ef database update --project {project-path}` （從您的 web 應用程式目錄）

常見的選項：

|    |                                  |                             |
|:---|:---------------------------------|:----------------------------|
|    | `--json`                           | 顯示 JSON 輸出。           |
| -c | `--context <DBCONTEXT>`           | 要使用的 DbContext。       |
| -p | `--project <PROJECT>`             | 要使用的專案。         |
| -s | `--startup-project <PROJECT>`     | 要使用的啟始專案。 |
|    | `--framework <FRAMEWORK>`         | 目標 framework。       |
|    | `--configuration <CONFIGURATION>` | 要使用的組態。   |
|    | `--runtime <IDENTIFIER>`          | 要使用的執行階段使         |
| -h | `--help`                           | 顯示說明資訊。      |
| -v | `--verbose`                        | 顯示詳細資訊輸出。        |
|    | `--no-color`                       | 沒有以色彩標示輸出。      |
|    | `--prefix-output`                  | 輸出層級的前置詞。   |


> [!TIP]
> 指定 ASP.NET Core 環境，請設定**ASPNETCORE_ENVIRONMENT**之前執行的環境變數。

<a name="commands"></a>命令
--------

### <a name="dotnet-ef-database-drop"></a>dotnet ef 資料庫卸除

卸除資料庫。

選項:

|    |           |                                                          |
|:---|:----------|:---------------------------------------------------------|
| -f | `--force`   | 未確認。                                           |
|    | `--dry-run` | 顯示哪個資料庫會卸除，但不會卸除它。 |

### <a name="dotnet-ef-database-update"></a>dotnet ef 資料庫更新

將資料庫更新為指定的移轉。

引數：

|              |                                                                                              |
|:-------------|:---------------------------------------------------------------------------------------------|
| `<MIGRATION>` | 目標移轉。 如果為 0，會還原所有移轉。 預設為最後一個移轉。 |

### <a name="dotnet-ef-dbcontext-info"></a>dotnet ef dbcontext 資訊

取得 DbContext 類型的相關資訊。

### <a name="dotnet-ef-dbcontext-list"></a>dotnet ef dbcontext 清單

列出可用的 DbContext 類型。

### <a name="dotnet-ef-dbcontext-scaffold"></a>dotnet ef dbcontext scaffold

建立資料庫的 DbContext 和實體類型。

引數：

|               |                                                                             |
|:--------------|:----------------------------------------------------------------------------|
| `<CONNECTION>` | 資料庫連接字串。                                      |
| `<PROVIDER>`   | 要使用提供者 (例如 Microsoft.EntityFrameworkCore.SqlServer)。 |

選項:

|                 |                                         |                                                                                                  |
|:----------------|:----------------------------------------|:-------------------------------------------------------------------------------------------------|
| <nobr>-d</nobr> | `--data-annotations`                      | 使用屬性來設定模型 (如果可能的話)。 如果省略，則只會使用 fluent API。 |
| -c              | `--context <NAME>`                       | DbContext 的名稱。                                                                       |
|                 | `--context-dir <PATH>`                   | 若要將 DbContext 檔案放在目錄。 路徑是相對於專案目錄。             |
| -f              | `--force`                                 | 覆寫現有的檔案。                                                                        |
| -o              | `--output-dir <PATH>`                    | 將檔案放入目錄。 路徑是相對於專案目錄。                      |
|                 | <nobr>`--schema <SCHEMA_NAME>...`</nobr> | 要產生的實體類型的資料表結構描述。                                              |
| -t              | `--table <TABLE_NAME>`...                | 要產生的實體類型的資料表。                                                         |
|                 | `--use-database-names`                    | 直接使用資料庫的資料表與資料行名稱。                                           |

### <a name="dotnet-ef-migrations-add"></a>新增 dotnet ef 移轉

加入新的移轉。

引數：

|         |                            |
|:--------|:---------------------------|
| `<NAME>` | 移轉名稱。 |

選項:

|                 |                                   |                                                                                                                  |
|:----------------|:----------------------------------|:-----------------------------------------------------------------------------------------------------------------|
| <nobr>-o</nobr> | <nobr> `--output-dir <PATH>` </nobr> | 目錄 （及子命名空間） 使用。 路徑是相對於專案目錄。 預設為 「 移轉 」。 |

### <a name="dotnet-ef-migrations-list"></a>dotnet ef migrations 清單

列出可用的移轉。

### <a name="dotnet-ef-migrations-remove"></a>dotnet ef migrations 移除

移除最後一個移轉。

選項:

|    |         |                                                                       |
|:---|:--------|:----------------------------------------------------------------------|
| -f | `--force` | 如果套用至資料庫，請還原移轉。 |

### <a name="dotnet-ef-migrations-script"></a>dotnet ef migrations 指令碼

產生 SQL 指令碼從移轉。

引數：

|         |                                                               |
|:--------|:--------------------------------------------------------------|
| `<FROM>` | 在開始移轉。 預設值為 0 （初始資料庫）。 |
| `<TO>`   | 結束的移轉。 預設為最後一個移轉。         |

選項:

|    |                  |                                                                    |
|:---|:-----------------|:-------------------------------------------------------------------|
| -o | `--output <FILE>` | 要將結果寫入其中的檔案。                                   |
| -i | `--idempotent`     | 產生可在任何移轉隨時用在資料庫的指令碼。 |


  [1]: powershell.md
  [2]: https://www.microsoft.com/net/core
