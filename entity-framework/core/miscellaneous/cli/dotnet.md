---
title: EF Core 工具參考 (.NET CLI)-EF Core
author: bricelam
ms.author: bricelam
ms.date: 07/11/2019
uid: core/miscellaneous/cli/dotnet
ms.openlocfilehash: 05c5f89fc79556e72a7e629c147aa817fe7d1a6b
ms.sourcegitcommit: e90d6cfa3e96f10b8b5275430759a66a0c714ed1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2019
ms.locfileid: "68286458"
---
# <a name="entity-framework-core-tools-reference---net-cli"></a>Entity Framework Core 工具參考.NET CLI

Entity Framework Core 的命令列介面 (CLI) 工具會執行設計階段開發工作。 例如，建立[移轉](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0#introduction-to-migrations)、 套用移轉，並產生程式碼在現有的資料庫為基礎的模型。 命令是跨平台的延伸模組[dotnet](/dotnet/core/tools)命令，也就是組件的[.NET Core SDK](https://www.microsoft.com/net/core)。 這些工具會使用.NET Core 專案。

如果您使用 Visual Studio，我們建議您[Package Manager Console 工具](powershell.md)改為：
* 它們會自動使用目前的專案中選取**Package Manager Console**而不需要您手動切換目錄。
* 它們會自動開啟命令完成之後，命令所產生的檔案。

## <a name="installing-the-tools"></a>安裝工具

安裝程序需視專案類型和版本而定：

* EF Core 3.x
* ASP.NET Core 2.1 和更新版本
* EF Core 2.x
* EF Core 1.x

### <a name="ef-core-3x"></a>EF Core 3.x

* `dotnet ef` 必須安裝做為全域或本機的工具。 大部分的開發人員將會安裝`dotnet ef`做為全域的工具，使用下列命令：

  ``` console
    $ dotnet tool install --global dotnet-ef --version 3.0.0-*
  ```

  您也可以使用`dotnet ef`為本機的工具。 若要使用它做為本機的工具，還原專案相依性，將其宣告為使用工具相依性[工具資訊清單檔](https://github.com/dotnet/cli/issues/10288)。

* 安裝[.NET Core SDK 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0))。 SDK 有即使您擁有最新版本的 Visual Studio 安裝。

* 安裝最新`Microsoft.EntityFrameworkCore.Design`封裝。

  ``` Console
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

### <a name="aspnet-core-21"></a>ASP.NET Core 2.1 +

* 安裝目前[.NET Core SDK](https://www.microsoft.com/net/download/core)。 即便您有最新版本的 Visual Studio 2017 也必須安裝該 SDK。

  這是因為，只需要針對 ASP.NET Core 2.1 +`Microsoft.EntityFrameworkCore.Design`套件包含在[Microsoft.AspNetCore.App 中繼套件](/aspnet/core/fundamentals/metapackage-app)。

### <a name="ef-core-2x-not-aspnet-core"></a>EF Core 2.x (非 ASP.NET Core)

`dotnet ef`命令會包含在.NET Core SDK，但若要啟用命令，您必須安裝`Microsoft.EntityFrameworkCore.Design`封裝。

* 安裝目前[.NET Core SDK](https://www.microsoft.com/net/download/core)。 SDK 有即使您擁有最新版本的 Visual Studio 安裝。

* 安裝最新的穩定`Microsoft.EntityFrameworkCore.Design`封裝。

  ``` Console
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

### <a name="ef-core-1x"></a>EF Core 1.x

* 安裝.NET Core SDK 版本 2.1.200。 更新的版本不相容的 EF Core 1.0 和 1.1 版的 CLI 工具。

* 設定要使用 2.1.200 SDK 版本的應用程式修改其[global.json](/dotnet/core/tools/global-json)檔案。 此檔案通常會包含在方案目錄 （一個以上專案） 中。

* 編輯專案檔，並新增`Microsoft.EntityFrameworkCore.Tools.DotNet`做為`DotNetCliToolReference`項目。 指定的最新的 1.x 版本，例如：1.1.6。 請參閱專案檔範例，本節結尾處。

* 安裝的最新的 1.x 版本`Microsoft.EntityFrameworkCore.Design`套件，例如：

  ```console
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 1.1.6
  ```

  加入這兩個套件參考，專案檔看起來像這樣：

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

  使用的套件參考`PrivateAssets="All"`不公開參考此專案的專案。 這項限制是特別適用於通常只能用在開發期間的套件。

### <a name="verify-installation"></a>確認安裝

執行下列命令來確認已正確安裝 EF Core CLI 工具：

  ``` Console
  dotnet restore
  dotnet ef
  ```

命令的輸出會識別使用中工具的版本：

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

## <a name="using-the-tools"></a>使用的工具

之前使用的工具，您可能必須建立啟始專案，或將環境設定。

### <a name="target-project-and-startup-project"></a>目標專案和啟始專案

命令是指*專案*並*啟始專案*。

* *專案*也稱為*目標專案*因為它是命令新增或移除檔案的位置。 根據預設，在目前的目錄中的專案會是目標專案。 您也可以使用為目標的專案指定不同的專案<nobr> `--project` </nobr>選項。

* *啟始專案*是指的工具建置和執行。 工具必須執行的應用程式程式碼，在設計階段，以取得專案中，例如資料庫連接字串和設定模型的相關資訊。 根據預設，在目前的目錄中的專案是啟始專案。 您也可以使用為啟始專案指定不同的專案<nobr> `--startup-project` </nobr>選項。

啟始專案和目標專案通常是相同的專案。 典型的案例，其中兩者就會不同的專案時：

* EF Core 內容和實體類別是.NET Core 類別庫中。
* .NET Core 主控台應用程式或 web 應用程式參考類別庫。

它也可[移轉程式碼置於不同於 EF Core 內容的類別庫](xref:core/managing-schemas/migrations/projects)。

### <a name="other-target-frameworks"></a>其他的目標架構

CLI 工具適用於.NET Core 專案和.NET Framework 專案。 .NET Standard 類別庫中有 EF Core 模型的應用程式可能沒有將.NET Core 或.NET Framework 專案。 比方說，這是 Xamarin 與通用 Windows 平台的應用程式，則為 true。 在此情況下，您可以建立其唯一目的是要做為啟始專案之工具的.NET Core 主控台應用程式專案。 專案可以是使用實際沒有程式碼的虛設專案&mdash;只需要用它來提供這項工具的目標。

為何需要一個 dummy 專案？ 如先前所述，這些工具必須在設計階段中執行應用程式程式碼。 若要這樣做，請他們需要使用.NET Core 執行階段。 .NET Core 或.NET Framework 為目標的專案中的 EF Core 模型時，EF Core 工具會借用從專案的執行階段。 如果是.NET Standard 類別庫中的 EF Core 模型，它們不能這麼做。 .NET Standard 並不是實際的.NET 實作;它是一組.NET 實作必須支援的 Api 的規格。 因此.NET Standard 是不夠的 EF Core 工具來執行應用程式程式碼。 您建立來做為啟始專案的虛設專案提供具體的目標平台工具可以在其中載入.NET Standard 類別庫。

### <a name="aspnet-core-environment"></a>ASP.NET Core 環境

指定 ASP.NET Core 專案的環境，請設定**ASPNETCORE_ENVIRONMENT**執行命令之前的環境變數。

## <a name="common-options"></a>常見的選項

|                   | 選項                            | 說明                                                                                                                                                                                                                                                   |
|:------------------|:----------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|                   | `--json`                          | 顯示 JSON 輸出。                                                                                                                                                                                                                                             |
| <nobr>`-c`</nobr> | `--context <DBCONTEXT>`           | 要使用的 `DbContext` 類別。 只有或與命名空間完整限定的類別名稱。  如果省略這個選項，EF Core 會發現內容類別。 如果有多個內容類別，這個選項是必要的。                                            |
| `-p`              | `--project <PROJECT>`             | 目標專案的專案資料夾的相對路徑。  預設值為目前的資料夾。                                                                                                                                                              |
| `-s`              | `--startup-project <PROJECT>`     | 啟始專案的專案資料夾的相對路徑。 預設值為目前的資料夾。                                                                                                                                                              |
|                   | `--framework <FRAMEWORK>`         | [目標 Framework Moniker](/dotnet/standard/frameworks#supported-target-framework-versions) for[目標 framework](/dotnet/standard/frameworks)。  專案檔會指定多個目標架構，而且您想要選取其中一個時，會使用它。 |
|                   | `--configuration <CONFIGURATION>` | 組建組態，例如：`Debug`或`Release`。                                                                                                                                                                                                   |
|                   | `--runtime <IDENTIFIER>`          | 若要還原套件的目標執行階段識別項。 如需執行階段識別項 (RID) 清單，請參閱 [RID 目錄](/dotnet/core/rid-catalog)。                                                                                                      |
| `-h`              | `--help`                          | 顯示說明資訊。                                                                                                                                                                                                                                        |
| `-v`              | `--verbose`                       | 顯示詳細資訊輸出。                                                                                                                                                                                                                                          |
|                   | `--no-color`                      | 沒有以色彩標示輸出。                                                                                                                                                                                                                                        |
|                   | `--prefix-output`                 | 輸出層級的前置詞。                                                                                                                                                                                                                                     |

## <a name="dotnet-ef-database-drop"></a>dotnet ef 資料庫卸除

卸除資料庫。

選項:

|                   | 選項                   | 描述                                              |
|:------------------|:-------------------------|:---------------------------------------------------------|
| <nobr>`-f`</nobr> | <nobr>`--force`</nobr>   | 未確認。                                           |
|                   | <nobr>`--dry-run`</nobr> | 顯示哪個資料庫會卸除，但不會卸除它。 |

## <a name="dotnet-ef-database-update"></a>dotnet ef 資料庫更新

最後一個移轉，或指定的移轉，請更新資料庫。

引數：

| 引數      | 描述                                                                                                                                                                                                                                                     |
|:--------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `<MIGRATION>` | 目標移轉。 移轉可能會識別，依名稱或識別碼。 數字 0 是特殊案例，這表示*第一個移轉之前，先*並造成所有移轉到還原作業。 如果未不指定任何移轉，則此命令預設的最後一個移轉。 |

下列範例會更新資料庫，以指定的移轉。 第一個是使用移轉名稱和第二個會使用移轉識別碼：

```console
dotnet ef database update InitialCreate
dotnet ef database update 20180904195021_InitialCreate
```

## <a name="dotnet-ef-dbcontext-info"></a>dotnet ef dbcontext 資訊

取得有關的資訊`DbContext`型別。

## <a name="dotnet-ef-dbcontext-list"></a>dotnet ef dbcontext 清單

列出可用`DbContext`型別。

## <a name="dotnet-ef-dbcontext-scaffold"></a>dotnet ef dbcontext scaffold

產生的程式碼`DbContext`和資料庫的實體類型。 為了讓這個命令來產生實體類型，資料庫資料表必須有主索引鍵。

引數：

| 引數       | 說明                                                                                                                                                                                                             |
|:---------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `<CONNECTION>` | 資料庫連接字串。 針對 ASP.NET Core 2.x 專案，此值可以是*名稱 =\<的連接字串名稱 >* 。 在此情況下，名稱來自於 專案設定的組態來源。 |
| `<PROVIDER>`   | 要使用提供者 通常這是 NuGet 套件的名稱，例如： `Microsoft.EntityFrameworkCore.SqlServer`。                                                                                           |

選項:

|                 | 選項                                   | 說明                                                                                                                                                                    |
|:----------------|:-----------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>-d</nobr> | `--data-annotations`                     | 使用屬性來設定模型 (如果可能的話)。 如果省略這個選項，則會使用 fluent API。                                                                |
| `-c`            | `--context <NAME>`                       | 名稱`DbContext`類別來產生。                                                                                                                                 |
|                 | `--context-dir <PATH>`                   | 將目錄`DbContext`類別檔案中的。 路徑是相對於專案目錄。 命名空間被衍生自資料夾名稱。                                 |
| `-f`            | `--force`                                | 覆寫現有的檔案。                                                                                                                                                      |
| `-o`            | `--output-dir <PATH>`                    | 若要將實體類別檔案放在目錄。 路徑是相對於專案目錄。                                                                                       |
|                 | <nobr>`--schema <SCHEMA_NAME>...`</nobr> | 要產生的實體類型的資料表結構描述。 若要指定多個結構描述，請重複`--schema`針對每個。 如果省略這個選項，則會包含 所有結構描述。          |
| `-t`            | `--table <TABLE_NAME>`...                | 要產生的實體類型的資料表。 若要指定多個資料表，請重複`-t`或`--table`針對每個。 如果省略這個選項，則所有資料表都都包含在內。                |
|                 | `--use-database-names`                   | 完全依照其出現在資料庫中，請使用資料表和資料行的名稱。 如果省略這個選項，則資料庫名稱會變更為更密切符合 C# 命名樣式慣例。 |

下列範例則所有的結構描述和資料表，並將新的檔案放在*模型*資料夾。

```console
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models
```

下列範例則選取的資料表，並建立具有指定名稱的個別資料夾的內容：

```console
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models -t Blog -t Post --context-dir Context -c BlogContext
```

## <a name="dotnet-ef-migrations-add"></a>新增 dotnet ef 移轉

加入新的移轉。

引數：

| 引數 | 描述                |
|:---------|:---------------------------|
| `<NAME>` | 移轉名稱。 |

選項:

|                   | 選項                             | 說明                                                                                                      |
|:------------------|:-----------------------------------|:-----------------------------------------------------------------------------------------------------------------|
| <nobr>`-o`</nobr> | <nobr>`--output-dir <PATH>`</nobr> | 目錄 （及子命名空間） 使用。 路徑是相對於專案目錄。 預設為 「 移轉 」。 |

## <a name="dotnet-ef-migrations-list"></a>dotnet ef migrations 清單

列出可用的移轉。

## <a name="dotnet-ef-migrations-remove"></a>dotnet ef migrations 移除

移除最後一個移轉 （回復移轉已完成的程式碼變更）。

選項:

|                   | 選項    | 說明                                                                     |
|:------------------|:----------|:--------------------------------------------------------------------------------|
| <nobr>`-f`</nobr> | `--force` | 還原移轉 （回復已套用至資料庫的變更）。 |

## <a name="dotnet-ef-migrations-script"></a>dotnet ef migrations 指令碼

產生 SQL 指令碼從移轉。

引數：

| 引數 | 描述                                                                                                                                                   |
|:---------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `<FROM>` | 在開始移轉。 移轉可能會識別，依名稱或識別碼。 數字 0 是特殊案例，這表示*第一個移轉之前，先*。 預設值為 0。 |
| `<TO>`   | 結束的移轉。 預設為最後一個移轉。                                                                                                         |

選項:

|                   | 選項            | 描述                                                        |
|:------------------|:------------------|:-------------------------------------------------------------------|
| <nobr>`-o`</nobr> | `--output <FILE>` | 要寫入的指令碼檔案。                                   |
| `-i`              | `--idempotent`    | 產生可在任何移轉隨時用在資料庫的指令碼。 |

下列範例會建立為 InitialCreate 移轉指令碼：

```console
dotnet ef migrations script 0 InitialCreate
```

下列範例會建立為 InitialCreate 移轉之後用於所有移轉的指令碼。

```console
dotnet ef migrations script 20180904195021_InitialCreate
```

## <a name="additional-resources"></a>其他資源

* [移轉](xref:core/managing-schemas/migrations/index)
* [反向工程](xref:core/managing-schemas/scaffolding)
