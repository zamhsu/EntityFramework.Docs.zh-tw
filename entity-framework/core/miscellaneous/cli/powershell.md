---
title: EF Core 工具參考 (Package Manager Console)-EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/18/2018
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: 9a57b58f8569ee1241e40c3809b03487d1d88e02
ms.sourcegitcommit: 7a7da65404c9338e1e3df42576a13be536a6f95f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2018
ms.locfileid: "48834756"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a>Entity Framework Core 工具的參考-Visual Studio 中的套件管理員主控台

Entity Framework Core 套件管理員主控台 (PMC) 工具會執行設計階段開發工作。 例如，建立[移轉](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0#introduction-to-migrations)、 套用移轉，並產生程式碼在現有的資料庫為基礎的模型。 使用 Visual Studio 內執行的命令[Package Manager Console](/nuget/tools/package-manager-console)。 這些工具會使用 .NET Framework 和 .NET Core 專案。

如果您未使用 Visual Studio，我們建議您[EF Core 命令列工具](dotnet.md)改。 CLI 工具是跨平台和在命令提示字元內執行。

## <a name="installing-the-tools"></a>安裝工具

安裝和更新工具的程序是根據 ASP.NET Core 2.1 + 和更早版本或其他專案類型而不同。

### <a name="aspnet-core-version-21-and-later"></a>ASP.NET Core 2.1 和更新版本

工具會自動包含在 ASP.NET Core 2.1 + 專案因為`Microsoft.EntityFrameworkCore.Tools`套件包含在[Microsoft.AspNetCore.App 中繼套件](/aspnet/core/fundamentals/metapackage-app)。

因此，您不必採取任何動作來安裝工具，但您需要：
* 新的專案中使用工具之前先還原套件。
* 將封裝安裝到更新為較新版本的工具。

若要確定您取得最新版本的工具，我們建議，您也可以下列步驟：

* 編輯您 *.csproj*檔案，並新增一行，指定最新版[Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/)封裝。 例如， *.csproj*檔案可能包含`ItemGroup`看起來像這樣：

  ```xml
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="2.1.3" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="2.1.1" />
  </ItemGroup>
  ```

更新工具，當您收到訊息，如下列範例所示：

> EF Core 工具版本 '2.1.1-rtm-30846' 會超過執行階段 '2.1.3-rtm-32065'。 更新適用於最新的功能和 bug 修正的工具。

若要更新的工具：
* 安裝最新的.NET Core SDK。
* Visual Studio 更新為最新版本。
* 編輯 *.csproj*檔案使其包含最新的工具套件的套件參考，如先前所示。

### <a name="other-versions-and-project-types"></a>其他版本和專案類型

在中執行下列命令安裝套件管理員主控台工具**Package Manager Console**:

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

在中執行下列命令更新工具**Package Manager Console**。

``` powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a>確認安裝

確認安裝工具時，則執行下列命令：

``` powershell
Get-Help about_EntityFrameworkCore
```

輸出看起來像這樣 （它不會告訴您正在使用的工具版本）：

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

## <a name="using-the-tools"></a>使用的工具

之前使用的工具：
* 了解目標和啟動專案之間的差異。
* 了解如何使用.NET Standard 類別庫的工具。
* 針對 ASP.NET Core 專案，設定環境。

### <a name="target-and-startup-project"></a>目標和啟動專案

命令是指*專案*並*啟始專案*。

* *專案*也稱為*目標專案*因為它是命令新增或移除檔案的位置。 根據預設，**預設專案**中選取**Package Manager Console**是目標專案。 您也可以使用為目標的專案指定不同的專案<nobr> `--project` </nobr>選項。

* *啟始專案*是指的工具建置和執行。 工具必須執行的應用程式程式碼，在設計階段，以取得專案中，例如資料庫連接字串和設定模型的相關資訊。 根據預設，**啟始專案**中**方案總管 中**是啟始專案。 您也可以使用為啟始專案指定不同的專案<nobr> `--startup-project` </nobr>選項。

啟始專案和目標專案通常是相同的專案。 典型的案例，其中兩者就會不同的專案時：

* EF Core 內容和實體類別是.NET Core 類別庫中。
* .NET Core 主控台應用程式或 web 應用程式參考類別庫。

它也可[移轉程式碼置於不同於 EF Core 內容的類別庫](xref:core/managing-schemas/migrations/projects)。

### <a name="other-target-frameworks"></a>其他的目標架構

套件管理員主控台工具會使用.NET Core 或.NET Framework 的專案。 .NET Standard 類別庫中有 EF Core 模型的應用程式可能沒有將.NET Core 或.NET Framework 專案。 比方說，這是 Xamarin 與通用 Windows 平台的應用程式，則為 true。 在此情況下，您可以建立.NET Core 或.NET Framework 主控台應用程式專案的唯一目的是要做為啟始專案的工具。 專案可以是使用實際沒有程式碼的虛設專案&mdash;只需要用它來提供這項工具的目標。

為何需要一個 dummy 專案？ 如先前所述，這些工具必須在設計階段中執行應用程式程式碼。 若要這樣做，請他們需要使用.NET Core 或.NET Framework 執行階段。 .NET Core 或.NET Framework 為目標的專案中的 EF Core 模型時，EF Core 工具會借用從專案的執行階段。 如果是.NET Standard 類別庫中的 EF Core 模型，它們不能這麼做。 .NET Standard 並不是實際的.NET 實作;它是一組.NET 實作必須支援的 Api 的規格。 因此.NET Standard 是不夠的 EF Core 工具來執行應用程式程式碼。 您建立來做為啟始專案的虛設專案提供具體的目標平台工具可以在其中載入.NET Standard 類別庫。

### <a name="aspnet-core-environment"></a>ASP.NET Core 環境

指定 ASP.NET Core 專案的環境，請設定**env:ASPNETCORE_ENVIRONMENT**之前執行命令。

## <a name="common-parameters"></a>一般參數

下表顯示通用於所有的 EF Core 命令的參數：

| 參數                 | 描述                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 內容\<字串 >        | `DbContext`類別使用。 只有或與命名空間完整限定的類別名稱。  如果省略這個參數時，EF Core 會尋找內容類別。 如果有多個內容類別，這是必要參數。 |
| -專案\<字串 >        | 目標專案。 如果省略這個參數，則**預設專案**for **Package Manager Console**做為目標的專案。                                                                             |
| -啟始專案\<字串 > | 啟始專案。 如果省略這個參數，則**啟始專案**中**方案屬性**做為目標的專案。                                                                                 |
| -Verbose                  | 顯示詳細資訊輸出。                                                                                                                                                                                                 |

若要顯示命令的說明資訊，請使用 PowerShell 的`Get-Help`命令。

> [!TIP]
> 內容、 專案和啟始專案參數支援 tab 鍵擴充。

## <a name="add-migration"></a>新增移轉

加入新的移轉。

參數：

| 參數                         | 描述                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <nobr>-Name\<字串 ><nobr>       | 移轉名稱。 這是位置參數，而且為必要。                                              |
| <nobr>-OutputDir\<字串 ></nobr> | 目錄 （及子命名空間） 使用。 路徑是相對於目標的專案目錄。 預設為 「 移轉 」。 |

## <a name="drop-database"></a>卸除資料庫

卸除資料庫。

參數：

| 參數 | 描述                                              |
|:----------|:---------------------------------------------------------|
| -WhatIf   | 顯示哪個資料庫會卸除，但不會卸除它。 |

## <a name="get-dbcontext"></a>Get-DbContext

列出可用`DbContext`型別。

## <a name="remove-migration"></a>移除移轉

移除最後一個移轉 （回復移轉已完成的程式碼變更）。

參數：

| 參數 | 描述                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| -Force    | 還原移轉 （回復已套用至資料庫的變更）。 |

## <a name="scaffold-dbcontext"></a>Scaffold DbContext

產生的程式碼`DbContext`和資料庫的實體類型。 為了讓`Scaffold-DbContext`來產生實體類型、 資料庫資料表必須有主索引鍵。

參數：

| 參數                          | 描述                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>Test-connection\<字串 ></nobr> | 資料庫連接字串。 針對 ASP.NET Core 2.x 專案，此值可以是*名稱 =\<的連接字串名稱 >*。 在此情況下，名稱來自於 專案設定的組態來源。 這是位置參數，而且為必要。 |
| <nobr>提供者\<字串 ></nobr>   | 要使用提供者 通常這是 NuGet 套件的名稱，例如： `Microsoft.EntityFrameworkCore.SqlServer`。 這是位置參數，而且為必要。                                                                                           |
| -OutputDir\<字串 >               | 將檔案放入目錄。 路徑是相對於專案目錄。                                                                                                                                                                                             |
| -ContextDir\<字串 >              | 將目錄`DbContext`檔案中。 路徑是相對於專案目錄。                                                                                                                                                                              |
| 內容\<字串 >                 | 名稱`DbContext`類別來產生。                                                                                                                                                                                                                          |
| 結構描述\<String [] >               | 要產生的實體類型的資料表結構描述。 如果省略這個參數，則會包含 所有結構描述。                                                                                                                                                             |
| -資料表\<String [] >                | 要產生的實體類型的資料表。 如果省略這個參數，則所有資料表都都包含在內。                                                                                                                                                                         |
| -DataAnnotations                   | 使用屬性來設定模型 (如果可能的話)。 如果省略這個參數，則會使用 fluent API。                                                                                                                                                      |
| -UseDatabaseNames                  | 完全依照其出現在資料庫中，請使用資料表和資料行的名稱。 如果省略這個參數，則資料庫名稱會變更為更密切符合 C# 命名樣式慣例。                                                                                       |
| -Force                             | 覆寫現有的檔案。                                                                                                                                                                                                                                               |

範例：

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

範例中，則只有選取的資料表，並建立具有指定名稱的個別資料夾的內容：

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext
```

## <a name="script-migration"></a>Script-Migration

產生 SQL 指令碼從一個所選移轉至另一個所選的移轉套用的所有變更。

參數：

| 參數                | 描述                                                                                                                                                                                                                |
|:-------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| *-從*\<字串 >        | 在開始移轉。 移轉可能會識別，依名稱或識別碼。 數字 0 是特殊案例，這表示*第一個移轉之前，先*。 預設值為 0。                                                              |
| *-至*\<字串 >          | 結束的移轉。 預設為最後一個移轉。                                                                                                                                                                      |
| <nobr>-具有等冪性</nobr> | 產生可在任何移轉隨時用在資料庫的指令碼。                                                                                                                                                         |
| -輸出\<字串 >        | 要將結果寫入其中的檔案。 應用程式的執行階段檔案建立時，比方說，如果省略這個參數，則要檔案建立產生的名稱相同的資料夾中： */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*。 |

> [!TIP]
> 收件者，，和輸出參數支援 tab 鍵擴充。

下列範例會建立指令碼為 InitialCreate 移轉時，使用移轉名稱。

```powershell
Script-Migration -To InitialCreate
```

下列範例會建立所有移轉的指令碼為 InitialCreate 移轉之後，使用 移轉識別碼。

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a>更新資料庫

最後一個移轉，或指定的移轉，請更新資料庫。

| 參數                           | 描述                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>*-移轉*\<字串 ></nobr> | 目標移轉。 移轉可能會識別，依名稱或識別碼。 數字 0 是特殊案例，這表示*第一個移轉之前，先*並造成所有移轉到還原作業。 如果未不指定任何移轉，則此命令預設的最後一個移轉。 |

> [!TIP]
> 移轉參數支援 tab 鍵擴充。

下列範例會還原所有移轉作業。

```powershell
Update-Database -Migration 0
```

下列範例會更新資料庫，以指定的移轉。 第一個是使用移轉名稱和第二個會使用移轉識別碼：

```powershell
Update-Database -Migration InitialCreate
Update-Database -Migration 20180904195021_InitialCreate
```
