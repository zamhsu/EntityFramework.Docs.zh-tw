---
title: EF Core 工具參考（套件管理員主控台）-EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/18/2018
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: 0676475d46a8d21dee7bd10e25dd273a11e96ac3
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538395"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a>Entity Framework Core 工具參考-Visual Studio 中的套件管理員主控台

適用于 Entity Framework Core 的套件管理員主控台（PMC）工具可執行設計階段開發工作。 例如，他們會針對以現有資料庫為基礎的模型建立[遷移](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0)、套用遷移，以及產生程式碼。 命令會使用[套件管理員主控台](/nuget/tools/package-manager-console)在 Visual Studio 內執行。 這些工具會使用 .NET Framework 和 .NET Core 專案。

如果您不是使用 Visual Studio，建議改用[EF Core 命令列工具](dotnet.md)。 CLI 工具是跨平臺的，並在命令提示字元中執行。

## <a name="installing-the-tools"></a>安裝工具

安裝和更新工具的程式在 ASP.NET Core 2.1 + 和更早版本或其他專案類型之間有所不同。

### <a name="aspnet-core-version-21-and-later"></a>ASP.NET Core 2.1 版和更新版本

這些工具會自動包含在 ASP.NET Core 2.1 + 專案中，因為`Microsoft.EntityFrameworkCore.Tools`此套件包含在[AspNetCore 應用程式中繼套件](/aspnet/core/fundamentals/metapackage-app)中。

因此，您不需要執行任何動作來安裝工具，但是您必須：

* 在新專案中使用工具之前，請先還原套件。
* 安裝套件，將工具更新為較新的版本。

若要確定您是取得最新版本的工具，建議您同時執行下列步驟：

* 編輯您的 *.csproj*檔案，並加入一行，指定最新版本的[microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/)套件。 例如， *.csproj*檔案可能包含`ItemGroup`如下所示的：

  ```xml
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="2.1.3" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="2.1.1" />
  </ItemGroup>
  ```

當您收到類似下列範例的訊息時，請更新工具：

> EF Core 工具版本 ' 2.1.1-30846 ' 早于執行時間 ' 2.1.3-rtm-32065 '。 更新工具以取得最新功能和錯誤修正。

若要更新工具：

* 安裝最新的 .NET Core SDK。
* 將 Visual Studio 更新為最新版本。
* 編輯 *.csproj*檔案，使其包含最新工具套件的套件參考，如先前所示。

### <a name="other-versions-and-project-types"></a>其他版本和專案類型

在 [**套件管理員主控台**] 中執行下列命令，以安裝套件管理員主控台工具：

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

在 [**套件管理員主控台**] 中執行下列命令，以更新工具。

``` powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a>驗證安裝

執行下列命令來確認是否已安裝工具：

``` powershell
Get-Help about_EntityFrameworkCore
```

輸出看起來像這樣（它並不會告訴您所使用的工具版本）：

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
* 瞭解如何搭配使用工具與 .NET Standard 類別庫。
* 針對 ASP.NET Core 專案，設定環境。

### <a name="target-and-startup-project"></a>目標和啟始專案

這些命令會參考*專案*和*啟始專案*。

* *專案*也稱為「*目標專案*」，因為它是命令新增或移除檔案的位置。 根據預設，在 [**套件管理員主控台**] 中選取的**預設專案**是目標專案。 您可以使用<nobr>`--project`</nobr>選項，將不同的專案指定為目標專案。

* *啟始專案*是工具所建立和執行的專案。 這些工具必須在設計階段執行應用程式程式碼，以取得專案的相關資訊，例如資料庫連接字串和模型的設定。 根據預設，**方案總管**中的**啟始專案**是啟始專案。 您可以使用<nobr>`--startup-project`</nobr>選項，將不同的專案指定為啟始專案。

啟始專案和目標專案通常是相同的專案。 一般情況下，它們是個別的專案，如下所示：

* EF Core 內容和實體類別位於 .NET Core 類別庫中。
* .NET Core 主控台應用程式或 web 應用程式會參考類別庫。

您也可以[將遷移程式碼放在與 EF Core 內容不同的類別庫中](xref:core/managing-schemas/migrations/projects)。

### <a name="other-target-frameworks"></a>其他目標 framework

套件管理員主控台工具適用于 .NET Core 或 .NET Framework 專案。 在 .NET Standard 類別庫中具有 EF Core 模型的應用程式，可能沒有 .NET Core 或 .NET Framework 專案。 例如，這適用于 Xamarin 和通用 Windows 平臺應用程式。 在這種情況下，您可以建立 .NET Core 或 .NET Framework 主控台應用程式專案，其目的只是要作為工具的啟始專案。 專案可以是沒有實際程式碼&mdash;的虛擬專案，只需要提供工具的目標。

為什麼需要虛擬專案？ 如先前所述，工具必須在設計階段執行應用程式程式碼。 若要這麼做，他們必須使用 .NET Core 或 .NET Framework 執行時間。 當 EF Core 模型位於以 .NET Core 或 .NET Framework 為目標的專案中時，EF Core 工具會從專案借用執行時間。 如果 EF Core 模型位於 .NET Standard 類別庫中，他們就無法這麼做。 .NET Standard 不是實際的 .NET 執行;這是一組 .NET 部署必須支援之 Api 的規格。 因此 .NET Standard 並不足以讓 EF Core 工具執行應用程式程式碼。 您建立用來做為啟始專案的虛設專案，會提供可讓工具載入 .NET Standard 類別庫的具體目標平臺。

### <a name="aspnet-core-environment"></a>ASP.NET Core 環境

若要指定 ASP.NET Core 專案的環境，請在執行命令之前，先設定**env： ASPNETCORE_ENVIRONMENT** 。

## <a name="common-parameters"></a>一般參數

下表顯示所有 EF Core 命令通用的參數：

| 參數                 | 描述                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| -內容\<字串>        | 要使用的 `DbContext` 類別。 僅限類別名稱，或使用命名空間完整限定。  如果省略此參數，EF Core 會尋找內容類別。 如果有多個內容類別，則需要此參數。 |
| -專案\<字串>        | 目標專案。 如果省略此參數，則會使用 [**封裝管理員主控台**] 的**預設專案**做為目標專案。                                                                             |
| <nobr>-啟始專案</nobr> \<字串> | 啟始專案。 如果省略此參數，則會使用 [**方案屬性**] 中的**啟始專案**做為目標專案。                                                                                 |
| -Verbose                  | 顯示詳細資訊輸出。                                                                                                                                                                                                 |

若要顯示命令的說明資訊，請使用 PowerShell `Get-Help`的命令。

> [!TIP]
> CoNtext、Project 和啟始專案參數支援 tab 鍵展開。

## <a name="add-migration"></a>新增-遷移

加入新的遷移。

參數：

| 參數                         | 描述                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <nobr>-Name \<String><nobr>       | 遷移的名稱。 這是位置參數，而且是必要的。                                              |
| <nobr>-OutputDir \<字串></nobr> | 目錄用來輸出檔案。 路徑相對於目標專案目錄。 預設為「遷移」。 |
| <nobr>-Namespace \<字串></nobr> | 要用於產生之類別的命名空間。 預設為從輸出目錄產生。 |

## <a name="drop-database"></a>放置資料庫

卸除資料庫。

參數：

| 參數 | 描述                                              |
|:----------|:---------------------------------------------------------|
| -WhatIf   | 顯示要捨棄的資料庫，但不要卸載它。 |

## <a name="get-dbcontext"></a>DbCoNtext

取得類型的`DbContext`相關資訊。

## <a name="remove-migration"></a>移除移轉

移除上一次的遷移作業（復原已針對遷移完成的程式碼變更）。

參數：

| 參數 | 描述                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| -Force    | 還原遷移（復原已套用至資料庫的變更）。 |

## <a name="scaffold-dbcontext"></a>Scaffold-DbCoNtext

為資料庫的`DbContext`和實體類型產生程式碼。 為了讓`Scaffold-DbContext`產生實體類型，資料庫資料表必須有主鍵。

參數：

| 參數                          | 描述                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>-連接\<字串></nobr> | 資料庫的連接字串。 針對 ASP.NET Core 2.x 專案，此值可以是*name =\<連接字串的名稱>*。 在此情況下，此名稱來自針對專案所設定的設定來源。 這是位置參數，而且是必要的。 |
| <nobr>-提供\<者字串></nobr>   | 要使用的提供者。 這通常是 NuGet 套件的名稱，例如： `Microsoft.EntityFrameworkCore.SqlServer`。 這是位置參數，而且是必要的。                                                                                           |
| -OutputDir \<字串>               | 要用來放置檔案的目錄。 路徑相對於專案目錄。                                                                                                                                                                                             |
| -CoNtextDir \<字串>              | 要放置`DbContext`檔案的目錄。 路徑相對於專案目錄。                                                                                                                                                               |
| -Namespace \<字串>               | 要用於所有產生之類別的命名空間。 預設為從根命名空間和輸出目錄產生。                                                                                                                                                                                             |
| -CoNtextNamespace \<字串>        | 要用於所產生`DbContext`類別的命名空間。 注意：覆`-Namespace`寫。                                                                                                                                                                              |
| -內容\<字串>                 | 要產生的`DbContext`類別名稱。                                                                                                                                                                                                                          |
| -架構\<字串 [] >               | 要為其產生實體類型的資料表架構。 如果省略此參數，則會包含所有架構。                                                                                                                                                             |
| -Tables \<String [] >                | 要為其產生實體類型的資料表。 如果省略此參數，則會包含所有資料表。                                                                                                                                                                         |
| -DataAnnotations                   | 使用屬性來設定模型（可能的話）。 如果省略此參數，則只會使用 Fluent API。                                                                                                                                                      |
| -UseDatabaseNames                  | 使用資料表和資料行名稱，就像在資料庫中所出現的一樣。 如果省略這個參數，資料庫名稱就會變更，以更符合 c # 名稱樣式慣例。                                                                                       |
| -Force                             | 覆寫現有檔案。                                                                                                                                                                                                                                               |

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
| *-從* \<字串>        | 開始的遷移。 可以依名稱或識別碼來識別遷移。 數位0是特殊案例，表示*第一次遷移之前*。 預設為 0。                                                              |
| *-到* \<字串>          | 結束遷移。 預設為上次遷移。                                                                                                                                                                      |
| <nobr>-等冪</nobr> | 產生可在任何遷移的資料庫上使用的腳本。                                                                                                                                                         |
| -輸出\<字串>        | 要寫入結果的檔案。 如果省略此參數，則會在建立應用程式的執行時間檔案所在的相同資料夾中建立具有所產生名稱的檔案，例如： */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*。 |

> [!TIP]
> To、From 和 Output 參數支援 tab 鍵展開。

下列範例會使用遷移名稱來建立 InitialCreate 遷移的腳本。

```powershell
Script-Migration -To InitialCreate
```

下列範例會使用遷移識別碼，針對 InitialCreate 遷移之後的所有遷移建立腳本。

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a>更新-資料庫

將資料庫更新為上次遷移或指定的遷移。

| 參數                           | 描述                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>*-遷移* \<字串></nobr> | 目標遷移。 可以依名稱或識別碼來識別遷移。 數位0是特殊案例，表示*第一次遷移之前*，並會還原所有遷移。 如果未指定任何遷移，此命令會預設為上次遷移。 |
| <nobr>-連接\<字串></nobr>  | 資料庫的連接字串。 預設為或`AddDbContext` `OnConfiguring`中指定的值。 |

> [!TIP]
> [遷移] 參數支援 tab 鍵展開。

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
