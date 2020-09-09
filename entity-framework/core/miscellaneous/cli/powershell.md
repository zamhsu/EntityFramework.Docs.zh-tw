---
title: EF Core 工具參考 (封裝管理員主控台) -EF Core
description: Entity Framework Core Visual Studio 封裝管理員主控台的參考指南
author: bricelam
ms.author: bricelam
ms.date: 09/18/2018
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: 84ca41dc08f7bc813ee9491b66fc91b2854c7632
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617866"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a>Visual Studio 中的 Entity Framework Core 工具參考-封裝管理員主控台

封裝管理員主控台 (PMC) 工具，以 Entity Framework Core 執行設計階段開發工作。 例如，他們會建立 [遷移](/aspnet/core/data/ef-mvc/migrations)、套用遷移，並根據現有的資料庫產生模型的程式碼。 這些命令會使用 [封裝管理員主控台](/nuget/tools/package-manager-console)在 Visual Studio 內部執行。 這些工具會使用 .NET Framework 和 .NET Core 專案。

如果您不是使用 Visual Studio，建議您改用 [EF Core 命令列工具](xref:core/miscellaneous/cli/dotnet) 。 .NET Core CLI 工具是跨平臺，並在命令提示字元內執行。

## <a name="installing-the-tools"></a>安裝工具

安裝和更新工具的程式會因 ASP.NET Core 2.1 + 和舊版或其他專案類型而有所不同。

### <a name="aspnet-core-version-21-and-later"></a>ASP.NET Core 2.1 版和更新版本

這些工具會自動包含在 ASP.NET Core 2.1 + 專案中，因為該 `Microsoft.EntityFrameworkCore.Tools` 套件包含在 [AspNetCore 中繼套件](/aspnet/core/fundamentals/metapackage-app)中。

因此，您不需要執行任何動作來安裝工具，但是您必須：

* 使用新專案中的工具之前，請先還原套件。
* 安裝套件以將工具更新至較新的版本。

若要確定您是取得最新版本的工具，建議您也執行下列步驟：

* 編輯您的 *.csproj* 檔案，並新增指定最新版 [microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) 的行。 例如， *.csproj* 檔案可能包含如下所示的 `ItemGroup` ：

  ```xml
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="3.1.3" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="3.1.2" />
  </ItemGroup>
  ```

當您收到類似下列範例的訊息時，請更新工具：

> EF Core 工具版本 ' 2.1.1-rtm-30846 ' 早于執行時間 ' 2.1.3-rtm-32065 '。 更新工具以取得最新功能和 bug 修正。

若要更新工具：

* 安裝最新的 .NET Core SDK。
* 將 Visual Studio 更新為最新版本。
* 編輯 *.csproj* 檔案，使其包含最新工具套件的套件參考，如先前所示。

### <a name="other-versions-and-project-types"></a>其他版本和專案類型

在 **封裝管理員主控台**中執行下列命令，以安裝封裝管理員主控台工具：

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

在 **封裝管理員主控台**中執行下列命令，以更新工具。

``` powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a>驗證安裝

執行下列命令，確認是否已安裝這些工具：

``` powershell
Get-Help about_EntityFrameworkCore
```

輸出看起來會像這樣 (不會告訴您所使用的工具是哪個版本) ：

```console

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

TOPIC
    about_EntityFrameworkCore

SHORT DESCRIPTION
    Provides information about the Entity Framework Core Package Manager Console Tools.

<A list of available commands follows, omitted here.>
```

## <a name="using-the-tools"></a>使用工具

使用工具之前：

* 瞭解目標和啟始專案之間的差異。
* 瞭解如何使用 .NET Standard 類別庫的工具。
* 針對 ASP.NET Core 專案，請設定環境。

### <a name="target-and-startup-project"></a>目標和啟始專案

這些命令會參考 *專案* 和 *啟始專案*。

* *專案*也稱為*目標專案*，因為這是命令新增或移除檔案的位置。 依預設，**封裝管理員主控台**中選取的**預設專案**是目標專案。 您可以使用選項，將不同的專案指定為目標專案 <nobr>`--project`</nobr> 。

* *啟始專案*是工具建立和執行的專案。 這些工具必須在設計階段執行應用程式程式碼，以取得專案的相關資訊，例如資料庫連接字串和模型的設定。 根據預設，**方案總管**中的**啟始專案**是啟始專案。 您可以使用選項，將不同的專案指定為啟始專案 <nobr>`--startup-project`</nobr> 。

啟始專案和目標專案通常是相同的專案。 一般情況下，它們是不同的專案：

* EF Core 內容和實體類別位於 .NET Core 類別庫中。
* .NET Core 主控台應用程式或 web 應用程式會參考類別庫。

您也可以將與 [EF Core 內容分開的類別庫中的遷移程式碼](xref:core/managing-schemas/migrations/projects)。

### <a name="other-target-frameworks"></a>其他目標 framework

封裝管理員主控台工具適用于 .NET Core 或 .NET Framework 專案。 在 .NET Standard 類別庫中具有 EF Core 模型的應用程式可能沒有 .NET Core 或 .NET Framework 專案。 例如，這適用于 Xamarin 和通用 Windows 平臺應用程式。 在這種情況下，您可以建立 .NET Core 或 .NET Framework 主控台應用程式專案，其唯一目的是作為工具的啟始專案。 專案可以是沒有真正程式碼的虛擬專案， &mdash; 只需要提供工具的目標。

為什麼需要虛擬專案？ 如先前所述，這些工具必須在設計階段執行應用程式程式碼。 若要這樣做，他們必須使用 .NET Core 或 .NET Framework 執行時間。 當 EF Core 模型位於以 .NET Core 或 .NET Framework 為目標的專案中時，EF Core 工具會從專案借用執行時間。 如果 EF Core 模型位於 .NET Standard 類別庫中，則無法這麼做。 .NET Standard 不是實際的 .NET 執行;它是一組 .NET 開發人員必須支援的 Api 的規格。 因此 .NET Standard 不足以執行應用程式程式碼 EF Core 工具。 您建立用來做為啟始專案的虛擬專案提供可讓工具載入 .NET Standard 類別庫的具體目標平臺。

### <a name="aspnet-core-environment"></a>ASP.NET Core 環境

若要指定 ASP.NET Core 專案的環境，請先設定 **env： ASPNETCORE_ENVIRONMENT** ，再執行命令。

## <a name="common-parameters"></a>一般參數

下表顯示所有 EF Core 命令通用的參數：

| 參數                 | 描述                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| -CoNtext \<String>        | 要使用的 `DbContext` 類別。 僅限類別名稱或完整限定命名空間。  如果省略此參數，EF Core 會尋找內容類別。 如果有多個內容類別，則需要此參數。 |
| -Project \<String>        | 目標專案。 如果省略此參數，則會使用**封裝管理員主控台**的**預設專案**做為目標專案。                                                                             |
| <nobr>-啟始專案</nobr>\<String> | 啟始專案。 如果省略此參數，則會使用**方案屬性**中的**啟始專案**做為目標專案。                                                                                 |
| -Verbose                  | 顯示詳細資訊輸出。                                                                                                                                                                                                 |

若要顯示命令的說明資訊，請使用 PowerShell 的 `Get-Help` 命令。

> [!TIP]
> CoNtext、Project 和啟始專案參數支援 tab 鍵展開。

## <a name="add-migration"></a>新增-遷移

加入新的遷移。

參數：

| 參數                         | 描述                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <nobr>-Name \<String><nobr>       | 遷移的名稱。 這是位置參數，而且是必要的。                                              |
| <nobr>-OutputDir \<String></nobr> | 用來輸出檔案的目錄。 路徑是相對於目標專案目錄。 預設為「遷移」。 |
| <nobr>-命名空間 \<String></nobr> | 要用於產生之類別的命名空間。 預設為從輸出目錄產生。  (可從 EFCore 5.0.0 開始取得。 )  |

## <a name="drop-database"></a>捨棄資料庫

卸除資料庫。

參數：

| 參數 | 描述                                              |
|:----------|:---------------------------------------------------------|
| -WhatIf   | 顯示要卸載的資料庫，但不要卸載它。 |

## <a name="get-dbcontext"></a>DbCoNtext

取得型別的相關資訊 `DbContext` 。

## <a name="remove-migration"></a>移除移轉

移除最後一個遷移 (復原針對遷移) 所做的程式碼變更。

參數：

| 參數 | 描述                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| -Force    | 還原遷移 (復原已套用至資料庫) 的變更。 |

## <a name="scaffold-dbcontext"></a>Scaffold-DbCoNtext

`DbContext`針對資料庫的和實體類型產生程式碼。 為了 `Scaffold-DbContext` 產生實體型別，資料庫資料表必須有主鍵。

參數：

| 參數                          | 描述                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>-連接 \<String></nobr> | 資料庫的連接字串。 針對 ASP.NET Core 2.x 專案，值可以是*名稱 = \<name of connection string> *。 在該情況下，名稱來自為專案設定的設定來源。 這是位置參數，而且是必要的。 |
| <nobr>-提供者 \<String></nobr>   | 要使用的提供者。 這通常是 NuGet 套件的名稱，例如： `Microsoft.EntityFrameworkCore.SqlServer` 。 這是位置參數，而且是必要的。                                                                                           |
| -OutputDir \<String>               | 要放置檔案的目錄。 路徑是相對於專案目錄。                                                                                                                                                                                             |
| -CoNtextDir \<String>              | 要放置檔案的目錄 `DbContext` 。 路徑是相對於專案目錄。                                                                                                                                                               |
| -命名空間 \<String>               | 要用於所有產生之類別的命名空間。 預設為從根命名空間和輸出目錄產生。  (可從 EFCore 5.0.0 開始取得。 )  |
| -CoNtextNamespace \<String>        | 要用於產生之類別的命名空間 `DbContext` 。 注意：覆寫 `-Namespace` 。  (可從 EFCore 5.0.0 開始取得。 )  |
| -CoNtext \<String>                 | `DbContext`要產生的類別名稱。                                                                                                                                                                                                                          |
| -架構 \<String[]>               | 要產生之實體類型的資料表架構。 如果省略此參數，則會包含所有架構。                                                                                                                                                             |
| -資料表 \<String[]>                | 要產生之實體類型的資料表。 如果省略此參數，則會包含所有資料表。                                                                                                                                                                         |
| -DataAnnotations                   | 您可以使用屬性來設定模型 (可能的) 。 如果省略此參數，則只會使用流暢的 API。                                                                                                                                                      |
| -UseDatabaseNames                  | 使用資料表和資料行名稱，如同它們出現在資料庫中一樣。 如果省略此參數，則會變更資料庫名稱，以更緊密符合 c # 名稱樣式慣例。                                                                                       |
| -Force                             | 覆寫現有檔案。                                                                                                                                                                                                                                               |
| -NoOnConfiguring                   | 隱藏 `OnConfiguring` 產生的類別中的方法產生 `DbContext` 。  (可從 EFCore 5.0.0 開始取得。 )  |

範例：

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

僅 scaffold 選取的資料表，並在具有指定之名稱和命名空間的個別資料夾中建立內容的範例：

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext -ContextNamespace New.Namespace
```

## <a name="script-migration"></a>腳本-遷移

產生 SQL 腳本，將所選遷移的所有變更套用到另一個選取的遷移。

參數：

| 參數                | 描述                                                                                                                                                                                                                |
|:-------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| *-從*\<String>        | 開始遷移。 遷移可依名稱或識別碼來識別。 數位0是特殊案例，這表示在 *第一次遷移之前*。 預設為 0。                                                              |
| *-至*\<String>          | 結束的遷移。 預設為上次的遷移。                                                                                                                                                                      |
| <nobr>-等冪</nobr> | 產生可在任何遷移時用於資料庫的腳本。                                                                                                                                                         |
| -Output \<String>        | 要寫入結果的檔案。 如果省略此參數，則會在建立應用程式的執行時間檔案時，在相同的資料夾中建立檔案，例如： */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*。 |

> [!TIP]
> To、From 和 Output 參數支援 tab 鍵展開。

下列範例會使用遷移名稱，建立 InitialCreate 遷移的腳本。

```powershell
Script-Migration -To InitialCreate
```

下列範例會使用遷移識別碼，在 InitialCreate 遷移之後建立所有遷移的腳本。

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a>更新-資料庫

將資料庫更新為上次遷移或指定的遷移。

| 參數                           | 描述                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>*-遷移*\<String></nobr> | 目標遷移。 遷移可依名稱或識別碼來識別。 數位0是特殊案例，這表示在 *第一次遷移之前* ，會將所有遷移還原。 如果未指定任何遷移，則命令會預設為上次的遷移。 |
| <nobr>-連接 \<String></nobr>  | 資料庫的連接字串。 預設為或中所指定 `AddDbContext` 的 `OnConfiguring` 。 |

> [!TIP]
> 遷移參數支援 tab 鍵展開。

下列範例會還原所有的遷移。

```powershell
Update-Database -Migration 0
```

下列範例會將資料庫更新為指定的遷移。 第一個使用遷移名稱，第二個使用遷移識別碼和指定的連接：

```powershell
Update-Database -Migration InitialCreate
Update-Database -Migration 20180904195021_InitialCreate -Connection your_connection_string
```

## <a name="additional-resources"></a>其他資源

* [移轉](xref:core/managing-schemas/migrations/index)
* [反向工程](xref:core/managing-schemas/scaffolding)
