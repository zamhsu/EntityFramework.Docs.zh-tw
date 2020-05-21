---
title: EF Core 工具參考（.NET CLI）-EF Core
author: bricelam
ms.author: bricelam
ms.date: 07/11/2019
uid: core/miscellaneous/cli/dotnet
ms.openlocfilehash: 6eb8b817a809dedf999ccb98307f5d8e2e41c0fb
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672952"
---
# <a name="entity-framework-core-tools-reference---net-cli"></a>Entity Framework Core 工具參考 - .NET CLI

適用于 Entity Framework Core 執行設計階段開發工作的命令列介面（CLI）工具。 例如，他們會針對以現有資料庫為基礎的模型建立[遷移](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0)、套用遷移，以及產生程式碼。 這些命令是跨平臺[dotnet](/dotnet/core/tools)命令的延伸模組，它是[.NET Core SDK](https://www.microsoft.com/net/core)的一部分。 這些工具會使用 .NET Core 專案。

如果您使用 Visual Studio，建議改用[套件管理員主控台工具](powershell.md)：

* 它們會自動使用在 [**套件管理員主控台**] 中選取的目前專案，而不需要您手動切換目錄。
* 在命令完成之後，它們會自動開啟命令所產生的檔案。

## <a name="installing-the-tools"></a>安裝工具

安裝程式視專案類型和版本而定：

* EF Core 3。x
* ASP.NET Core 2.1 版和更新版本
* EF Core 2。x
* EF Core 1。x

### <a name="ef-core-3x"></a>EF Core 3。x

* `dotnet ef`必須安裝為全域或本機工具。 大部分的開發人員都會 `dotnet ef` 使用下列命令來安裝為通用工具：

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

  您也可以使用 `dotnet ef` 做為本機工具。 若要使用它做為本機工具，請使用[工具資訊清單](https://github.com/dotnet/cli/issues/10288)檔，將宣告為工具相依性的專案的相依性還原。

* 安裝 [.NET Core SDK](https://www.microsoft.com/net/download/core)。

* 安裝最新的 `Microsoft.EntityFrameworkCore.Design` 套件。

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

### <a name="aspnet-core-21"></a>ASP.NET Core 2.1 +

* 安裝目前的[.NET Core SDK](https://www.microsoft.com/net/download/core)。 即便您有最新版本的 Visual Studio 2017 也必須安裝該 SDK。

  這是 ASP.NET Core 2.1 + 所需的所有功能，因為此 `Microsoft.EntityFrameworkCore.Design` 套件包含在[AspNetCore 應用程式中繼套件](/aspnet/core/fundamentals/metapackage-app)中。

### <a name="ef-core-2x-not-aspnet-core"></a>EF Core 2.x （不 ASP.NET Core）

這些 `dotnet ef` 命令包含在 .NET Core SDK 中，但若要啟用您必須安裝套件的命令 `Microsoft.EntityFrameworkCore.Design` 。

* 安裝目前的[.NET Core SDK](https://www.microsoft.com/net/download/core)。 即使您有最新版本的 Visual Studio，也必須安裝 SDK。

* 安裝最新的穩定 `Microsoft.EntityFrameworkCore.Design` 套件。

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

### <a name="ef-core-1x"></a>EF Core 1。x

* 安裝 .NET Core SDK 版本2.1.200。 較新的版本與適用于 EF Core 1.0 和1.1 的 CLI 工具不相容。

* 將應用程式設定為使用 2.1.200 SDK 版本，方法是修改其[global. json](/dotnet/core/tools/global-json)檔案。 這個檔案通常會包含在方案目錄中（專案上方的一個）。

* 編輯專案檔並加入 `Microsoft.EntityFrameworkCore.Tools.DotNet` 做為 `DotNetCliToolReference` 專案。 指定最新的1.x 版本，例如：1.1.6。 請參閱本節結尾的專案檔範例。

* 安裝套件的最新1.x 版 `Microsoft.EntityFrameworkCore.Design` ，例如：

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 1.1.6
  ```

  新增這兩個套件參考時，專案檔看起來會像這樣：

  ``` xml
  <Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
      <OutputType>Exe</OutputType>
      <TargetFramework>netcoreapp1.1</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
      <PackageReference Include="Microsoft.EntityFrameworkCore.Design"
                        Version="1.1.6"
                         PrivateAssets="All" />
    </ItemGroup>
    <ItemGroup>
       <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
                              Version="1.1.6" />
    </ItemGroup>
  </Project>
  ```

  具有的套件參考 `PrivateAssets="All"` 不會公開至參考此專案的專案。 這種限制特別適用于通常只會在開發期間使用的封裝。

### <a name="verify-installation"></a>確認安裝

執行下列命令，確認已正確安裝 EF Core CLI 工具：

  ```dotnetcli
  dotnet restore
  dotnet ef
  ```

命令的輸出會識別使用中的工具版本：

```console

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

Entity Framework Core .NET Command-line Tools 2.1.3-rtm-32065

<Usage documentation follows, not shown.>
```


## <a name="updating-the-tools"></a>更新工具

`dotnet tool update --global dotnet-ef`如果您的專案使用本機安裝工具，請使用將通用工具更新為最新的可用版本 `dotnet tool update dotnet-ef` 。 將附加至您的命令，以安裝特定版本 `--version <VERSION>` 。 如需詳細資訊，請參閱 dotnet 工具檔的[Update](/dotnet/core/tools/dotnet-tool-update)一節。


## <a name="using-the-tools"></a>使用工具

使用工具之前，您可能必須建立啟始專案或設定環境。

### <a name="target-project-and-startup-project"></a>目標專案和啟始專案

這些命令會參考*專案*和*啟始專案*。

* *專案*也稱為「*目標專案*」，因為它是命令新增或移除檔案的位置。 根據預設，目前目錄中的專案是目標專案。 您可以使用選項，將不同的專案指定為目標專案 <nobr>`--project`</nobr> 。

* *啟始專案*是工具所建立和執行的專案。 這些工具必須在設計階段執行應用程式程式碼，以取得專案的相關資訊，例如資料庫連接字串和模型的設定。 根據預設，目前目錄中的專案是啟始專案。 您可以使用選項，將不同的專案指定為啟始專案 <nobr>`--startup-project`</nobr> 。

啟始專案和目標專案通常是相同的專案。 一般情況下，它們是個別的專案，如下所示：

* EF Core 內容和實體類別位於 .NET Core 類別庫中。
* .NET Core 主控台應用程式或 web 應用程式會參考類別庫。

您也可以[將遷移程式碼放在與 EF Core 內容不同的類別庫中](xref:core/managing-schemas/migrations/projects)。

### <a name="other-target-frameworks"></a>其他目標 framework

CLI 工具適用于 .NET Core 專案和 .NET Framework 專案。 在 .NET Standard 類別庫中具有 EF Core 模型的應用程式，可能沒有 .NET Core 或 .NET Framework 專案。 例如，這適用于 Xamarin 和通用 Windows 平臺應用程式。 在這種情況下，您可以建立 .NET Core 主控台應用程式專案，其目的只是要作為工具的啟始專案。 專案可以是沒有實際程式碼的虛擬專案， &mdash; 只需要提供工具的目標。

為什麼需要虛擬專案？ 如先前所述，工具必須在設計階段執行應用程式程式碼。 若要這麼做，他們必須使用 .NET Core 執行時間。 當 EF Core 模型位於以 .NET Core 或 .NET Framework 為目標的專案中時，EF Core 工具會從專案借用執行時間。 如果 EF Core 模型位於 .NET Standard 類別庫中，他們就無法這麼做。 .NET Standard 不是實際的 .NET 執行;這是一組 .NET 部署必須支援之 Api 的規格。 因此 .NET Standard 並不足以讓 EF Core 工具執行應用程式程式碼。 您建立用來做為啟始專案的虛設專案，會提供可讓工具載入 .NET Standard 類別庫的具體目標平臺。

### <a name="aspnet-core-environment"></a>ASP.NET Core 環境

若要指定 ASP.NET Core 專案的環境，請先設定**ASPNETCORE_ENVIRONMENT**環境變數，再執行命令。

## <a name="common-options"></a>一般選項

|                   | 選項                            | 說明                                                                                                                                                                                                                                                   |
|:------------------|:----------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|                   | `--json`                          | 顯示 JSON 輸出。                                                                                                                                                                                                                                             |
| <nobr>`-c`</nobr> | `--context <DBCONTEXT>`           | 要使用的 `DbContext` 類別。 僅限類別名稱，或使用命名空間完整限定。  如果省略此選項，EF Core 會尋找內容類別。 如果有多個內容類別，則需要此選項。                                            |
| `-p`              | `--project <PROJECT>`             | 目標專案之專案資料夾的相對路徑。  預設值為目前的資料夾。                                                                                                                                                              |
| `-s`              | `--startup-project <PROJECT>`     | 啟始專案之專案資料夾的相對路徑。 預設值為目前的資料夾。                                                                                                                                                              |
|                   | `--framework <FRAMEWORK>`         | [目標 framework](/dotnet/standard/frameworks)的[目標 framework 標記](/dotnet/standard/frameworks#supported-target-framework-versions)。  當專案檔指定多個目標架構，而您想要選取其中一個時，請使用。 |
|                   | `--configuration <CONFIGURATION>` | 組建設定，例如： `Debug` 或 `Release` 。                                                                                                                                                                                                   |
|                   | `--runtime <IDENTIFIER>`          | 要為其還原封裝的目標執行時間識別碼。 如需執行階段識別項 (RID) 清單，請參閱 [RID 目錄](/dotnet/core/rid-catalog)。                                                                                                      |
| `-h`              | `--help`                          | 顯示說明資訊。                                                                                                                                                                                                                                        |
| `-v`              | `--verbose`                       | 顯示詳細資訊輸出。                                                                                                                                                                                                                                          |
|                   | `--no-color`                      | 不要將輸出著色。                                                                                                                                                                                                                                        |
|                   | `--prefix-output`                 | 具有層級的前置詞輸出。                                                                                                                                                                                                                                     |

## <a name="dotnet-ef-database-drop"></a>dotnet ef 資料庫捨棄

卸除資料庫。

選項：

|                   | 選項                   | 說明                                              |
|:------------------|:-------------------------|:---------------------------------------------------------|
| <nobr>`-f`</nobr> | <nobr>`--force`</nobr>   | 不要確認。                                           |
|                   | <nobr>`--dry-run`</nobr> | 顯示要捨棄的資料庫，但不要卸載它。 |

## <a name="dotnet-ef-database-update"></a>dotnet ef 資料庫更新

將資料庫更新為上次遷移或指定的遷移。

引數：

| 引數      | 描述                                                                                                                                                                                                                                                     |
|:--------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `<MIGRATION>` | 目標遷移。 可以依名稱或識別碼來識別遷移。 數位0是特殊案例，表示*第一次遷移之前*，並會還原所有遷移。 如果未指定任何遷移，此命令會預設為上次遷移。 |

選項：

|                   | 選項                   | 說明                                              |
|:------------------|:-------------------------|:---------------------------------------------------------|
| <nobr>    </nobr> |  `--connection <CONNECTION>`        | 資料庫的連接字串。 預設為或中指定的 `AddDbContext` 值 `OnConfiguring` 。 |


下列範例會將資料庫更新為指定的遷移。 第一個使用遷移名稱，第二個使用遷移識別碼和指定的連接：

```dotnetcli
dotnet ef database update InitialCreate
dotnet ef database update 20180904195021_InitialCreate --connection your_connection_string
```

## <a name="dotnet-ef-dbcontext-info"></a>dotnet ef dbcoNtext 資訊

取得類型的相關資訊 `DbContext` 。

## <a name="dotnet-ef-dbcontext-list"></a>dotnet ef dbcoNtext 清單

列出可用 `DbContext` 的類型。

## <a name="dotnet-ef-dbcontext-scaffold"></a>dotnet ef dbcoNtext scaffold

為 `DbContext` 資料庫的和實體類型產生程式碼。 為了讓這個命令產生實體類型，資料庫資料表必須有主鍵。

引數：

| 引數       | 描述                                                                                                                                                                                                             |
|:---------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `<CONNECTION>` | 資料庫的連接字串。 針對 ASP.NET Core 2.x 專案，此值可以是*name = \< 連接字串的名稱>*。 在此情況下，此名稱來自針對專案所設定的設定來源。 |
| `<PROVIDER>`   | 要使用的提供者。 這通常是 NuGet 套件的名稱，例如： `Microsoft.EntityFrameworkCore.SqlServer` 。                                                                                           |

選項：

|                 | 選項                                   | 說明                                                                                                                                                                    |
|:----------------|:-----------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`-d`</nobr> | `--data-annotations`                   | 使用屬性來設定模型（可能的話）。 如果省略此選項，則只會使用 Fluent API。                                                                |
| `-c`            | `--context <NAME>`                       | `DbContext`要產生的類別名稱。                                                                                                                                 |
|                 | `--context-dir <PATH>`                   | 要放置類別檔案的目錄 `DbContext` 。 路徑相對於專案目錄。 命名空間是從資料夾名稱衍生而來。                                 |
|                 | `--context-namespace <NAMESPACE>`        | 要用於所產生類別的命名空間 `DbContext` 。 注意：覆寫 `--namespace` 。                                 |
| `-f`            | `--force`                                | 覆寫現有檔案。                                                                                                                                                      |
| `-o`            | `--output-dir <PATH>`                    | 要在其中放置實體類別檔案的目錄。 路徑相對於專案目錄。                                                                                       |
| `-n`            | `--namespace <NAMESPACE>`                | 要用於所有產生之類別的命名空間。 預設為從根命名空間和輸出目錄產生。                    |
|                 | <nobr>`--schema <SCHEMA_NAME>...`</nobr> | 要為其產生實體類型的資料表架構。 若要指定多個架構，請 `--schema` 為每個架構重複。 如果省略此選項，則會包含所有架構。          |
| `-t`            | `--table <TABLE_NAME>`...                | 要為其產生實體類型的資料表。 若要指定多個資料表，請 `-t` `--table` 針對每個資料表重複或。 如果省略此選項，則會包含所有資料表。                |
|                 | `--use-database-names`                   | 使用資料表和資料行名稱，就像在資料庫中所出現的一樣。 如果省略此選項，資料庫名稱就會變更，以更符合 c # 名稱樣式慣例。 |

下列範例會 scaffold 所有架構和資料表，並將新檔案放在 [*模型*] 資料夾中。

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models
```

下列範例只會 scaffold 選取的資料表，並在具有指定之名稱和命名空間的個別資料夾中建立內容：

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models -t Blog -t Post --context-dir Context -c BlogContext --context-namespace New.Namespace
```

## <a name="dotnet-ef-migrations-add"></a>dotnet ef 遷移新增

加入新的遷移。

引數：

| 引數 | 描述                |
|:---------|:---------------------------|
| `<NAME>` | 遷移的名稱。 |

選項：

|                   | 選項                             | 說明                                                                                                      |
|:------------------|:-----------------------------------|:-----------------------------------------------------------------------------------------------------------------|
| <nobr>`-o`</nobr> | <nobr>`--output-dir <PATH>`</nobr> | 目錄用來輸出檔案。 路徑相對於目標專案目錄。 預設為「遷移」。 |
| <nobr>`-n`</nobr> | <nobr>`--namespace <NAMESPACE>`</nobr> | 要用於產生之類別的命名空間。 預設為從輸出目錄產生。 |

## <a name="dotnet-ef-migrations-list"></a>dotnet ef 遷移清單

列出可用的遷移。

## <a name="dotnet-ef-migrations-remove"></a>dotnet ef 遷移移除

移除上一次的遷移作業（復原已針對遷移完成的程式碼變更）。

選項：

|                   | 選項    | 說明                                                                     |
|:------------------|:----------|:--------------------------------------------------------------------------------|
| <nobr>`-f`</nobr> | `--force` | 還原遷移（復原已套用至資料庫的變更）。 |

## <a name="dotnet-ef-migrations-script"></a>dotnet ef 遷移腳本

從遷移產生 SQL 腳本。

引數：

| 引數 | 描述                                                                                                                                                   |
|:---------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `<FROM>` | 開始的遷移。 可以依名稱或識別碼來識別遷移。 數位0是特殊案例，表示*第一次遷移之前*。 預設為 0。 |
| `<TO>`   | 結束遷移。 預設為上次遷移。                                                                                                         |

選項：

|                   | 選項            | 說明                                                        |
|:------------------|:------------------|:-------------------------------------------------------------------|
| <nobr>`-o`</nobr> | `--output <FILE>` | 要寫入腳本的檔案。                                   |
| `-i`              | `--idempotent`    | 產生可在任何遷移的資料庫上使用的腳本。 |

下列範例會建立 InitialCreate 遷移的腳本：

```dotnetcli
dotnet ef migrations script 0 InitialCreate
```

下列範例會針對 InitialCreate 遷移之後的所有遷移建立腳本。

```dotnetcli
dotnet ef migrations script 20180904195021_InitialCreate
```

## <a name="additional-resources"></a>其他資源

* [移轉](xref:core/managing-schemas/migrations/index)
* [反向工程](xref:core/managing-schemas/scaffolding)
