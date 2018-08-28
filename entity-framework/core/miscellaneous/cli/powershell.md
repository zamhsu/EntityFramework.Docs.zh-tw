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
<a name="ef-core-package-manager-console-tools"></a>EF Core 封裝管理員主控台工具
=====================================
EF Core 封裝管理員主控台 (PMC) 工具，請執行 Visual Studio 內使用 NuGet 的[Package Manager Console][2]。
這些工具會使用 .NET Framework 和 .NET Core 專案。

> [!TIP]
> 不使用 Visual Studio 嗎？ [EF Core 命令列工具][ 1]是跨平台和在命令提示字元內執行。

<a name="installing-the-tools"></a>安裝工具
--------------------
安裝 EF Core 封裝管理員主控台工具安裝 Microsoft.EntityFrameworkCore.Tools NuGet 封裝。
您可以藉由執行下列命令安裝它[Package Manager Console][2]。

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

如果一切正常運作，您應該能夠執行此命令：

``` powershell
Get-Help about_EntityFrameworkCore
```
> [!TIP]
> 如果您的啟始專案目標設為.NET Standard[跨目標的受支援的 framework] [ 3]之前使用的工具。

> [!IMPORTANT]
> 如果您使用**通用 Windows**或是**Xamarin**，將您的 EF 程式碼移至.NET Standard 類別庫和[跨目標的受支援的 framework] [ 3]之前使用的工具。 指定類別庫做為您的啟始專案。

<a name="using-the-tools"></a>使用的工具
---------------
每當您叫用命令時，有兩個專案包含：

目標專案就是新增 (在某些情況下會移除) 任何檔案的位置。 目標專案的預設值為**預設專案**在 Package Manager Console 中選取，但也可以指定使用-專案參數。

啟始專案是工具在執行您的專案程式碼時所模擬的專案。 它會預設為其中一個**設定為啟始專案**方案總管 中。 它也可以指定使用-啟始專案參數。

一般參數：

|                           |                             |
|:--------------------------|:----------------------------|
| 內容\<字串 >        | 要使用的 DbContext。       |
| -專案\<字串 >        | 要使用的專案。         |
| -啟始專案\<字串 > | 要使用的啟始專案。 |
| -Verbose                  | 顯示詳細資訊輸出。        |

若要顯示命令的說明資訊，請使用 PowerShell 的`Get-Help`命令。

> [!TIP]
> 內容、 專案和啟始專案參數支援 tab 鍵擴充。

> [!TIP]
> 設定**env:ASPNETCORE_ENVIRONMENT**之前指定 ASP.NET Core 環境執行。

<a name="commands"></a>命令
--------

### <a name="add-migration"></a>新增移轉

加入新的移轉。

參數：

|                                   |                                                                                                                  |
|:----------------------------------|:-----------------------------------------------------------------------------------------------------------------|
| ***-Name*** \<字串 >             | 移轉名稱。                                                                                       |
| <nobr>-OutputDir\<字串 ></nobr> | 目錄 （及子命名空間） 使用。 路徑是相對於專案目錄。 預設為 「 移轉 」。 |

> [!NOTE]
> 中的參數**粗體**是必要的並於*斜體*是位置。

### <a name="drop-database"></a>卸除資料庫

卸除資料庫。

參數：

|         |                                                          |
|:--------|:---------------------------------------------------------|
| -WhatIf | 顯示哪個資料庫會卸除，但不會卸除它。 |

### <a name="get-dbcontext"></a>Get-DbContext

取得 DbContext 類型的相關資訊。

### <a name="remove-migration"></a>移除移轉

移除最後一個移轉。

參數：

|        |                                                              |
|:-------|:-------------------------------------------------------------|
| -Force | 如果套用至資料庫，請還原移轉。 |

### <a name="scaffold-dbcontext"></a>Scaffold DbContext

建立資料庫的 DbContext 和實體類型。

參數：

|                                          |                                                                                                  |
|:-----------------------------------------|:-------------------------------------------------------------------------------------------------|
| <nobr>***Test-connection*** \<字串 ></nobr> | 資料庫連接字串。                                                           |
| ***-Provider*** \<String>                | 要使用提供者 (例如 Microsoft.EntityFrameworkCore.SqlServer)。                      |
| -OutputDir\<字串 >                     | 將檔案放入目錄。 路徑是相對於專案目錄。                      |
| -ContextDir\<字串 >                    | 若要將 DbContext 檔案放在目錄。 路徑是相對於專案目錄。             |
| 內容\<字串 >                       | 若要產生 DbContext 的名稱。                                                           |
| 結構描述\<String [] >                     | 要產生的實體類型的資料表結構描述。                                              |
| -資料表\<String [] >                      | 要產生的實體類型的資料表。                                                         |
| -DataAnnotations                         | 使用屬性來設定模型 (如果可能的話)。 如果省略，則只會使用 fluent API。 |
| -UseDatabaseNames                        | 直接使用資料庫的資料表與資料行名稱。                                           |
| -Force                                   | 覆寫現有的檔案。                                                                        |

### <a name="script-migration"></a>Script-Migration

產生 SQL 指令碼從移轉。

參數：

|                   |                                                                    |
|:------------------|:-------------------------------------------------------------------|
| *-從*\<字串 > | 在開始移轉。 預設值為 0 （初始資料庫）。      |
| *-至*\<字串 >   | 結束的移轉。 預設為最後一個移轉。              |
| -具有等冪性       | 產生可在任何移轉隨時用在資料庫的指令碼。 |
| -輸出\<字串 > | 要將結果寫入其中的檔案。                                   |

> [!TIP]
> 收件者，，和輸出參數支援 tab 鍵擴充。

### <a name="update-database"></a>更新資料庫

|                                     |                                                                                                |
|:------------------------------------|:-----------------------------------------------------------------------------------------------|
| <nobr>*-移轉*\<字串 ></nobr> | 目標移轉。 如果為 '0'，就會還原所有移轉作業。 預設為最後一個移轉。 |

> [!TIP]
> 移轉參數支援 tab 鍵擴充。


  [1]: dotnet.md
  [2]: https://docs.microsoft.com/nuget/tools/package-manager-console
  [3]: index.md#frameworks
