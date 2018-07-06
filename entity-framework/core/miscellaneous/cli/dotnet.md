---
title: .NET core CLI-EF Core
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
<a name="ef-core-net-command-line-tools"></a>EF Core.NET 命令列工具
===============================
Entity Framework Core.NET 命令列工具是跨平台擴充**dotnet**命令，也就是組件的[.NET Core SDK][2]。

> [!TIP]
> 如果您使用 Visual Studio，我們建議您[PMC 工具][ 1]相反地，因為它們提供更加整合的經驗。

<a name="installing-the-tools"></a>安裝工具
--------------------
> [!NOTE]
> .NET Core SDK 版本 2.1.300 和更新版本包含**dotnet ef**與 EF Core 2.0 和更新版本相容的命令。 因此如果您使用最新版.NET Core SDK 和 EF Core 執行階段，不需安裝作業，而且您可以忽略此章節的其餘部分。
>
> 相反地， **dotnet ef**工具包含在.NET Core SDK 版本 2.1.300 及更新版本不相容的 EF Core 版本 1.0 和 1.1 版。 您可以使用.NET Core sdk 2.1.300 的電腦使用的 EF Core 這些舊版本的專案或較新的安裝之前，您也必須安裝 2.1.200 版本或更舊版本的 sdk 和設定應用程式使用該較舊的版本修改其 [global.json](https://docs.microsoft.com/en-us/dotnet/core/tools/global-json)檔案。 這個檔案通常包含 （一個以上專案） 的方案目錄中。 然後您可以繼續以下列的 installlation 指令。

舊版的.NET Core SDK，您可以安裝 EF Core.NET 命令列工具使用下列步驟：

1. 編輯專案檔，並新增 Microsoft.EntityFrameworkCore.Tools.DotNet 為 DotNetCliToolReference 項目 （請參閱下文）
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
> 封裝參考`PrivateAssets="All"`表示它不公開給專案參考此專案中，這是特別適用於通常只在開發期間使用的封裝。

如果您這樣做的所有項目權限，您應該能夠成功地在命令提示字元中執行下列命令。

``` Console
dotnet ef
```

<a name="using-the-tools"></a>使用的工具
---------------
當您叫用命令時，有兩個專案相關：

目標專案就是新增 (在某些情況下會移除) 任何檔案的位置。 目標專案預設值為目前的目錄中的專案，但可以使用變更<nobr> **-專案**</nobr>選項。

啟始專案是工具在執行您的專案程式碼時所模擬的專案。 它也會預設為目前的目錄中的專案，但可以使用變更 **-啟始專案**選項。

> [!NOTE]
> 比方說，更新 web 應用程式的 EF Core 安裝在不同的專案中的資料庫看起來會像這樣： `dotnet ef database update --project {project-path}` （從您的 web 應用程式目錄）

常用選項：

|    |                                  |                             |
|:---|:---------------------------------|:----------------------------|
|    | --json                           | 顯示 JSON 輸出。           |
| -c | -內容\<DBCONTEXT >           | 若要使用 DbContext。       |
| -p | -專案\<專案 >             | 要使用的專案。         |
| -s | -啟始專案\<專案 >     | 若要使用啟始專案。 |
|    | -framework\<架構 >         | 目標 framework。       |
|    | -設定\<設定 > | 要使用的組態。   |
|    | -執行階段\<識別項 >          | 在執行階段使用。         |
| -h | -說明                           | 顯示說明資訊。      |
| -v | -verbose                        | 顯示詳細資訊輸出。        |
|    | --no-color                       | 不要以色彩標示輸出。      |
|    | --prefix-output                  | 輸出層級的前置詞。   |


> [!TIP]
> 指定 ASP.NET Core 環境，請設定**ASPNETCORE_ENVIRONMENT**之前執行的環境變數。

<a name="commands"></a>命令
--------

### <a name="dotnet-ef-database-drop"></a>dotnet ef 資料庫卸除

卸除資料庫。

選項:

|    |           |                                                          |
|:---|:----------|:---------------------------------------------------------|
| -f | --force   | 未確認。                                           |
|    | -乾執行 | 顯示哪一個資料庫會卸除，但不卸除它。 |

### <a name="dotnet-ef-database-update"></a>dotnet ef 資料庫更新

將資料庫更新為指定的移轉。

引數：

|              |                                                                                              |
|:-------------|:---------------------------------------------------------------------------------------------|
| \<移轉 &GT; | 在目標移轉。 如果為 0，就會還原所有移轉。 預設的最後一個移轉。 |

### <a name="dotnet-ef-dbcontext-info"></a>dotnet ef dbcontext 資訊

取得 DbContext 類型的相關資訊。

### <a name="dotnet-ef-dbcontext-list"></a>dotnet ef dbcontext 清單

列出可用的 DbContext 類型。

### <a name="dotnet-ef-dbcontext-scaffold"></a>dotnet ef dbcontext scaffold

Scaffold DbContext 類型及實體類型的資料庫。

引數：

|               |                                                                             |
|:--------------|:----------------------------------------------------------------------------|
| \<連接 &GT; | 資料庫的連接字串。                                      |
| \<提供者 &GT;   | 若要使用提供者。 （例如 Microsoft.EntityFrameworkCore.SqlServer） |

選項:

|                 |                                         |                                                                                                  |
|:----------------|:----------------------------------------|:-------------------------------------------------------------------------------------------------|
| <nobr>-d</nobr> | -資料註解                      | 使用屬性，將模型設定 （如果可能的話）。 如果省略，則會使用 fluent 應用程式開發的 API。 |
| -c              | -內容\<名稱 >                       | DbContext 名稱。                                                                       |
|                 | -內容-dir\<路徑 >                   | 將 DbContext 檔案放入目錄。 路徑是相對於專案目錄。             |
| -f              | --force                                 | 覆寫現有檔案。                                                                        |
| -o              | -輸出 dir\<路徑 >                    | 將檔案放入目錄。 路徑是相對於專案目錄。                      |
|                 | <nobr>-結構描述\<SCHEMA_NAME >...</nobr> | 要產生實體類型的資料表結構描述。                                              |
| -t              | -資料表\<TABLE_NAME >...                | 要產生實體類型的資料表。                                                         |
|                 | -使用資料庫名稱                    | 使用直接從資料庫資料表和資料行名稱。                                           |

### <a name="dotnet-ef-migrations-add"></a>dotnet ef 移轉新增

加入新的移轉。

引數：

|         |                            |
|:--------|:---------------------------|
| \<名稱 &GT; | 移轉程序的名稱。 |

選項:

|                 |                                   |                                                                                                                  |
|:----------------|:----------------------------------|:-----------------------------------------------------------------------------------------------------------------|
| <nobr>-o</nobr> | <nobr>-輸出 dir\<路徑 ></nobr> | 目錄 （以及子命名空間） 使用。 路徑是相對於專案目錄。 預設為 「 移轉 」。 |

### <a name="dotnet-ef-migrations-list"></a>dotnet ef 移轉清單

列出可用的移轉。

### <a name="dotnet-ef-migrations-remove"></a>dotnet ef 移轉移除

移除最後的移轉。

選項:

|    |         |                                                                       |
|:---|:--------|:----------------------------------------------------------------------|
| -f | --force | 如果套用至資料庫，請還原移轉。 |

### <a name="dotnet-ef-migrations-script"></a>dotnet ef 移轉指令碼

從移轉中產生 SQL 指令碼。

引數：

|         |                                                               |
|:--------|:--------------------------------------------------------------|
| \<FROM> | 開始移轉。 預設值為 0 （初始資料庫）。 |
| \<若要 &GT;   | 結束的移轉。 預設的最後一個移轉。         |

選項:

|    |                  |                                                                    |
|:---|:-----------------|:-------------------------------------------------------------------|
| -o | -輸出\<檔案 > | 若要將結果寫入檔案。                                   |
| -i | -等冪     | 產生的指令碼，可用在任何移轉的資料庫。 |


  [1]: powershell.md
  [2]: https://www.microsoft.com/net/core
