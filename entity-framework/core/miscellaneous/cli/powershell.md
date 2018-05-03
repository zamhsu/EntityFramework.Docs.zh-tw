---
title: Package Manager Console (Visual Studio)-EF 核心
author: bricelam
ms.author: bricelam
ms.date: 11/6/2017
ms.technology: entity-framework-core
ms.openlocfilehash: a53455a78db4bc504c45abafdacf9a15381f608e
ms.sourcegitcommit: 507a40ed050fee957bcf8cf05f6e0ec8a3b1a363
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2018
---
<a name="ef-core-package-manager-console-tools"></a>EF 核心封裝管理員主控台工具
=====================================
EF 核心封裝管理員主控台 (PMC) 工具，請執行 Visual Studio 內使用 NuGet 的[Package Manager Console][2]。
這些工具會使用 .NET Framework 和 .NET Core 專案。

> [!TIP]
> 不使用 Visual Studio 嗎？ [EF 核心命令列工具][ 1]是跨平台和在命令提示字元內執行。

<a name="installing-the-tools"></a>安裝工具
--------------------
安裝 EF 核心封裝管理員主控台工具安裝 Microsoft.EntityFrameworkCore.Tools NuGet 封裝。
您可以執行內的下列命令來安裝[Package Manager Console][2]。

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

如果一切正常運作，您應該能夠執行這個命令：

``` powershell
Get-Help about_EntityFrameworkCore
```
> [!TIP]
> 如果您的啟始專案的目標.NET 標準[跨目標受支援的 framework] [ 3]之前使用的工具。

> [!IMPORTANT]
> 如果您使用**通用 Windows**或**Xamarin**，將您的 EF 程式碼移至.NET 標準的類別庫和[跨目標受支援的 framework] [ 3]之前使用的工具。 指定的類別庫做為您的啟始專案。

<a name="using-the-tools"></a>使用的工具
---------------
當您叫用命令時，有兩個專案相關：

目標專案就是新增 (在某些情況下會移除) 任何檔案的位置。 目標專案預設值為**預設專案**Package Manager Console 中選取，但您也可以指定使用-專案參數。

啟始專案是工具在執行您的專案程式碼時所模擬的專案。 預設為其中一個**設定為啟始專案**方案總管 中。 它也可以指定使用-啟始專案參數。

一般參數：

|                           |                             |
|:--------------------------|:----------------------------|
| 內容\<字串 >        | 若要使用 DbContext。       |
| -專案\<字串 >        | 要使用的專案。         |
| -啟始專案\<字串 > | 若要使用啟始專案。 |
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
| ***-名稱***\<字串 >             | 移轉程序的名稱。                                                                                       |
| <nobr>-OutputDir\<字串 ></nobr> | 目錄 （以及子命名空間） 使用。 路徑是相對於專案目錄。 預設為 「 移轉 」。 |

> [!NOTE]
> 中的參數**粗體**是必要的以及在*斜體*是位置。

### <a name="drop-database"></a>卸除資料庫

卸除資料庫。

參數：

|         |                                                          |
|:--------|:---------------------------------------------------------|
| -WhatIf | 顯示哪一個資料庫會卸除，但不卸除它。 |

### <a name="get-dbcontext"></a>Get-DbContext

取得 DbContext 類型的相關資訊。

### <a name="remove-migration"></a>移除移轉

移除最後的移轉。

參數：

|        |                                                              |
|:-------|:-------------------------------------------------------------|
| -Force | 如果套用至資料庫，請還原移轉。 |

### <a name="scaffold-dbcontext"></a>Scaffold DbContext

Scaffold DbContext 類型及實體類型的資料庫。

參數：

|                                          |                                                                                                  |
|:-----------------------------------------|:-------------------------------------------------------------------------------------------------|
| <nobr>***連接***\<字串 ></nobr> | 資料庫的連接字串。                                                           |
| ***-Provider*** \<String>                | 若要使用提供者。 (例如， Microsoft.EntityFrameworkCore.SqlServer)                              |
| -OutputDir\<字串 >                     | 將檔案放入目錄。 路徑是相對於專案目錄。                      |
| -ContextDir\<字串 >                    | 將 DbContext 檔案放入目錄。 路徑是相對於專案目錄。             |
| 內容\<字串 >                       | 若要產生 DbContext 名稱。                                                           |
| -結構描述\<String [] >                     | 要產生實體類型的資料表結構描述。                                              |
| -資料表\<String [] >                      | 要產生實體類型的資料表。                                                         |
| -DataAnnotations                         | 使用屬性，將模型設定 （如果可能的話）。 如果省略，則會使用 fluent 應用程式開發的 API。 |
| -UseDatabaseNames                        | 使用直接從資料庫資料表和資料行名稱。                                           |
| -Force                                   | 覆寫現有檔案。                                                                        |

### <a name="script-migration"></a>Script-Migration

從移轉中產生 SQL 指令碼。

參數：

|                   |                                                                    |
|:------------------|:-------------------------------------------------------------------|
| *-從*\<字串 > | 開始移轉。 預設值為 0 （初始資料庫）。      |
| *-到*\<字串 >   | 結束的移轉。 預設的最後一個移轉。              |
| 為等冪       | 產生的指令碼，可用在任何移轉的資料庫。 |
| -輸出\<字串 > | 若要將結果寫入檔案。                                   |

> [!TIP]
> To、 From、 和輸出參數支援 tab 鍵擴充。

### <a name="update-database"></a>更新資料庫

|                                     |                                                                                                |
|:------------------------------------|:-----------------------------------------------------------------------------------------------|
| <nobr>*移轉*\<字串 ></nobr> | 在目標移轉。 如果為 '0'，將會還原所有移轉。 預設的最後一個移轉。 |

> [!TIP]
> 移轉參數支援 tab 鍵擴充。


  [1]: dotnet.md
  [2]: https://docs.microsoft.com/nuget/tools/package-manager-console
  [3]: index.md#frameworks
