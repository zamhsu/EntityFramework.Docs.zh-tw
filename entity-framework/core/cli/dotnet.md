---
title: EF Core 工具參考 ( .NET CLI) -EF Core
description: Entity Framework Core .NET Core CLI 工具的參考指南
author: bricelam
ms.date: 10/27/2020
uid: core/cli/dotnet
ms.openlocfilehash: 83989b8690236dbec3466cda78c204ab67fd10c4
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431475"
---
# <a name="entity-framework-core-tools-reference---net-core-cli"></a>Entity Framework Core 工具參考-.NET Core CLI

命令列介面 (CLI) 工具 Entity Framework Core 執行設計階段開發工作。 例如，他們會建立 [遷移](/aspnet/core/data/ef-mvc/migrations)、套用遷移，並根據現有的資料庫產生模型的程式碼。 這些命令是跨平臺 [dotnet](/dotnet/core/tools) 命令的延伸模組，這是 [.NET Core SDK](https://www.microsoft.com/net/core)的一部分。 這些工具適用于 .NET Core 專案。

使用 Visual Studio 時，請考慮使用 [封裝管理員主控台工具](xref:core/cli/powershell) 代替 CLI 工具。 自動封裝管理員主控台工具：

* 適用于 **封裝管理員主控台** 中選取的目前專案，不需要您手動切換目錄。
* 在命令完成之後，開啟命令所產生的檔案。
* 提供命令、參數、專案名稱、內容類型和遷移名稱的 tab 鍵自動完成。

## <a name="installing-the-tools"></a>安裝工具

`dotnet ef` 可以安裝為全域或本機工具。 大部分的開發人員偏好 `dotnet ef` 使用下列命令安裝為通用工具：

```dotnetcli
dotnet tool install --global dotnet-ef
```

若要使用它作為本機工具，請使用 [工具資訊清單](/dotnet/core/tools/global-tools#install-a-local-tool)檔，還原將它宣告為工具相依性之專案的相依性。

使用下列命令來更新工具工具：

```dotnetcli
dotnet tool update --global dotnet-ef
```

在特定專案上使用工具之前，您必須先將 `Microsoft.EntityFrameworkCore.Design` 套件新增至其中。

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Design
```

### <a name="verify-installation"></a>驗證安裝

執行下列命令，確認已正確安裝 EF Core CLI 工具：

  ```dotnetcli
  dotnet ef
  ```

命令的輸出會識別使用中的工具版本：

```output

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

Entity Framework Core .NET Command-line Tools 2.1.3-rtm-32065

<Usage documentation follows, not shown.>
```

## <a name="update-the-tools"></a>更新工具

使用 `dotnet tool update --global dotnet-ef` 將通用工具更新為最新可用版本。 如果您已在專案中本機安裝工具，請使用 `dotnet tool update dotnet-ef` 。 附加 `--version <VERSION>` 至命令以安裝特定版本。 如需詳細資訊，請參閱 dotnet 工具檔的 [更新](/dotnet/core/tools/dotnet-tool-update) 一節。

## <a name="using-the-tools"></a>使用工具

使用工具之前，您可能必須建立啟始專案或設定環境。

### <a name="target-project-and-startup-project"></a>目標專案和啟始專案

這些命令會參考 *專案* 和 *啟始專案* 。

* *專案* 也稱為 *目標專案* ，因為這是命令新增或移除檔案的位置。 根據預設，目前目錄中的專案是目標專案。 您可以使用選項，將不同的專案指定為目標專案 <nobr>`--project`</nobr> 。

* *啟始專案* 是工具建立和執行的專案。 這些工具必須在設計階段執行應用程式程式碼，以取得專案的相關資訊，例如資料庫連接字串和模型的設定。 根據預設，目前目錄中的專案是啟始專案。 您可以使用選項，將不同的專案指定為啟始專案 <nobr>`--startup-project`</nobr> 。

啟始專案和目標專案通常是相同的專案。 一般情況下，它們是不同的專案：

* EF Core 內容和實體類別位於 .NET Core 類別庫中。
* .NET Core 主控台應用程式或 web 應用程式會參考類別庫。

您也可以將與 [EF Core 內容分開的類別庫中的遷移程式碼](xref:core/managing-schemas/migrations/projects)。

### <a name="other-target-frameworks"></a>其他目標 framework

CLI 工具適用于 .NET Core 專案和 .NET Framework 專案。 在 .NET Standard 類別庫中具有 EF Core 模型的應用程式可能沒有 .NET Core 或 .NET Framework 專案。 例如，這適用于 Xamarin 和通用 Windows 平臺應用程式。 在這種情況下，您可以建立 .NET Core 主控台應用程式專案，其唯一目的是作為工具的啟始專案。 專案可以是沒有真正程式碼的虛擬專案， &mdash; 只需要提供工具的目標。

為什麼需要虛擬專案？ 如先前所述，這些工具必須在設計階段執行應用程式程式碼。 若要這樣做，他們必須使用 .NET Core 執行時間。 當 EF Core 模型位於以 .NET Core 或 .NET Framework 為目標的專案中時，EF Core 工具會從專案借用執行時間。 如果 EF Core 模型位於 .NET Standard 類別庫中，則無法這麼做。 .NET Standard 不是實際的 .NET 執行;它是一組 .NET 開發人員必須支援的 Api 的規格。 因此 .NET Standard 不足以執行應用程式程式碼 EF Core 工具。 您建立用來做為啟始專案的虛擬專案提供可讓工具載入 .NET Standard 類別庫的具體目標平臺。

### <a name="aspnet-core-environment"></a>ASP.NET Core 環境

若要指定 ASP.NET Core 專案 [的環境](/aspnet/core/fundamentals/environments) ，請先設定 **ASPNETCORE_ENVIRONMENT** 環境變數，然後再執行命令。

從 EF Core 5.0 開始，還可以將額外的引數傳遞至 CreateHostBuilder，讓您在命令列上指定環境：

```dotnetcli
dotnet ef database update -- --environment Production
```

> [!TIP]
> `--`標記會指示 `dotnet ef` 將後面的所有專案視為引數，而不會嘗試將它們剖析為選項。 未使用的任何額外引數 `dotnet ef` 都會轉送至應用程式。

## <a name="common-options"></a>一般選項

| 選項                                         | Short             | 說明                                                                                                                                                                                                                                                   |
|:-----------------------------------------------|:------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--json`                                       |                   | 顯示 JSON 輸出。                                                                                                                                                                                                                                             |
| `--context <DBCONTEXT>`                        | <nobr>`-c`</nobr> | 要使用的 `DbContext` 類別。 僅限類別名稱或完整限定命名空間。  如果省略此選項，EF Core 會尋找內容類別。 如果有多個內容類別，則需要此選項。                                            |
| `--project <PROJECT>`                          | `-p`              | 目標專案之專案資料夾的相對路徑。  預設值為目前的資料夾。                                                                                                                                                              |
| `--startup-project <PROJECT>`                  | `-s`              | 啟始專案專案資料夾的相對路徑。 預設值為目前的資料夾。                                                                                                                                                              |
| `--framework <FRAMEWORK>`                      |                   | [目標 framework](/dotnet/standard/frameworks)的[目標 framework 標記](/dotnet/standard/frameworks#supported-target-framework-versions)。  當專案檔指定多個目標 framework，而您想要選取其中一個 framework 時，請使用。 |
| <nobr>`--configuration <CONFIGURATION>`</nobr> |                   | 組建設定，例如： `Debug` 或 `Release` 。                                                                                                                                                                                                   |
| `--runtime <IDENTIFIER>`                       |                   | 要還原封裝之目標執行時間的識別碼。 如需執行階段識別項 (RID) 清單，請參閱 [RID 目錄](/dotnet/core/rid-catalog)。                                                                                                      |
| `--no-build`                                   |                   | 請勿建立專案。 要在組建為最新版本時使用。                                                                                                                                                                                    |
| `--help`                                       | `-h`              | 顯示說明資訊。                                                                                                                                                                                                                                        |
| `--verbose`                                    | `-v`              | 顯示詳細資訊輸出。                                                                                                                                                                                                                                          |
| `--no-color`                                   |                   | 請勿將輸出著色。                                                                                                                                                                                                                                        |
| `--prefix-output`                              |                   | 具有層級的前置詞輸出。                                                                                                                                                                                                                                     |

從 EF Core 5.0 開始，任何額外的引數都會傳遞至應用程式。

## <a name="dotnet-ef-database-drop"></a>dotnet ef 資料庫 drop

刪除資料庫。

選項：

| 選項                   | Short             | 說明                                              |
|:-------------------------|:------------------|:---------------------------------------------------------|
| `--force`                | <nobr>`-f`</nobr> | 不要確認。                                           |
| <nobr>`--dry-run`</nobr> |                   | 顯示要卸載的資料庫，但不要卸載它。 |

上述的 [通用選項](#common-options) 如下所示。

## <a name="dotnet-ef-database-update"></a>dotnet ef 資料庫更新

將資料庫更新為上次遷移或指定的遷移。

引數：

| 引數                   | 說明                                                                                                                                                                                                                                                     |
|:---------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`<MIGRATION>`</nobr> | 目標遷移。 遷移可依名稱或識別碼來識別。 數位0是特殊案例，這表示在 *第一次遷移之前* ，會將所有遷移還原。 如果未指定任何遷移，則命令會預設為上次的遷移。 |

選項：

| 選項                                    | 說明                                                                                                                      |
|:------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------|
|  <nobr>`--connection <CONNECTION>`</nobr> | 資料庫的連接字串。 預設為或中所指定 `AddDbContext` 的 `OnConfiguring` 。 在 EF Core 5.0 中新增。 |

上述的 [通用選項](#common-options) 如下所示。

下列範例會將資料庫更新為指定的遷移。 第一個使用遷移名稱，第二個使用遷移識別碼和指定的連接：

```dotnetcli
dotnet ef database update InitialCreate
dotnet ef database update 20180904195021_InitialCreate --connection your_connection_string
```

## <a name="dotnet-ef-dbcontext-info"></a>dotnet ef dbcoNtext info

取得型別的相關資訊 `DbContext` 。

上述的 [通用選項](#common-options) 如下所示。

## <a name="dotnet-ef-dbcontext-list"></a>dotnet ef dbcoNtext 清單

列出可用 `DbContext` 的類型。

上述的 [通用選項](#common-options) 如下所示。

## <a name="dotnet-ef-dbcontext-scaffold"></a>dotnet ef dbcoNtext scaffold

`DbContext`針對資料庫的和實體類型產生程式碼。 為了讓這個命令產生實體型別，資料庫資料表必須有主鍵。

引數：

| 引數                    | 說明                                                                                                                                                                                                             |
|:----------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`<CONNECTION>`</nobr> | 資料庫的連接字串。 針對 ASP.NET Core 2.x 專案，值可以是 *名稱 = \<name of connection string>* 。 在該情況下，名稱來自為專案設定的設定來源。 |
| `<PROVIDER>`                | 要使用的提供者。 這通常是 NuGet 套件的名稱，例如： `Microsoft.EntityFrameworkCore.SqlServer` 。                                                                                           |

選項：

| 選項                                   | Short             | 說明                                                                                                                                                                    |
|:-----------------------------------------|:------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--data-annotations`                     | <nobr>`-d`</nobr> | 您可以使用屬性來設定模型 (可能的) 。 如果省略此選項，則只會使用流暢的 API。                                                                |
| `--context <NAME>`                       | `-c`              | `DbContext`要產生的類別名稱。                                                                                                                                 |
| `--context-dir <PATH>`                   |                   | 要放置類別檔案的目錄 `DbContext` 。 路徑是相對於專案目錄。 命名空間衍生自資料夾名稱。                                 |
| `--context-namespace <NAMESPACE>`        |                   | 要用於產生之類別的命名空間 `DbContext` 。 注意：覆寫 `--namespace` 。 在 EF Core 5.0 中新增。                                                                 |
| `--force`                                | `-f`              | 覆寫現有檔案。                                                                                                                                                      |
| `--output-dir <PATH>`                    | `-o`              | 要放置實體類別檔案的目錄。 路徑是相對於專案目錄。                                                                                       |
| `--namespace <NAMESPACE>`                | `-n`              | 要用於所有產生之類別的命名空間。 預設為從根命名空間和輸出目錄產生。 在 EF Core 5.0 中新增。                                  |
| <nobr>`--schema <SCHEMA_NAME>...`</nobr> |                   | 要產生之實體類型的資料表架構。 若要指定多個架構，請 `--schema` 針對每一個架構重複執行。 如果省略這個選項，則會包含所有架構。          |
| `--table <TABLE_NAME>`...                | `-t`              | 要產生之實體類型的資料表。 若要指定多個資料表，請 `-t` `--table` 針對每一個資料表重複或。 如果省略這個選項，則會包含所有資料表。                |
| `--use-database-names`                   |                   | 使用資料表和資料行名稱，如同它們出現在資料庫中一樣。 如果省略這個選項，則會變更資料庫名稱，以更緊密符合 c # 名稱樣式慣例。 |
| `--no-onconfiguring`                     |                   | 隱藏 `OnConfiguring` 產生的類別中的方法產生 `DbContext` 。 在 EF Core 5.0 中新增。                                                                  |
| `--no-pluralize`                         |                   | 請勿使用 pluralizer。 新增于 EF Core 5。0                                                                                                                                 |

上述的 [通用選項](#common-options) 如下所示。

下列範例會 scaffold 所有架構和資料表，並將新檔案放在 [ *模型* ] 資料夾中。

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models
```

下列範例只會 scaffold 選取的資料表，並在具有指定之名稱和命名空間的個別資料夾中建立內容：

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models -t Blog -t Post --context-dir Context -c BlogContext --context-namespace New.Namespace
```

下列範例會使用 [Secret Manager 工具](/aspnet/core/security/app-secrets#secret-manager)，從專案的設定集讀取連接字串。

```dotnetcli
dotnet user-secrets set ConnectionStrings.Blogging "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Blogging"
dotnet ef dbcontext scaffold Name=ConnectionStrings.Blogging Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="dotnet-ef-dbcontext-script"></a>dotnet ef dbcoNtext 腳本

從 DbCoNtext 產生 SQL 腳本。 略過任何遷移。 在 EF Core 3.0 中新增。

選項：

| 選項                         | Short             | 說明                      |
| ------------------------------ | ----------------- | -------------------------------- |
| <nobr>`--output <FILE>`</nobr> | <nobr>`-o`</nobr> | 要寫入結果的檔案。 |

上述的 [通用選項](#common-options) 如下所示。

## <a name="dotnet-ef-migrations-add"></a>dotnet ef 遷移新增

加入新的遷移。

引數：

| 引數              | 說明                |
|:----------------------|:---------------------------|
| <nobr>`<NAME>`</nobr> | 遷移的名稱。 |

選項：

| 選項                                 | Short             | 說明                                                                                                            |
|:---------------------------------------|:------------------|:-----------------------------------------------------------------------------------------------------------------------|
| `--output-dir <PATH>`                  | <nobr>`-o`</nobr> | 用來輸出檔案的目錄。 路徑是相對於目標專案目錄。 預設為「遷移」。   |
| <nobr>`--namespace <NAMESPACE>`</nobr> | `-n`              | 要用於產生之類別的命名空間。 預設為從輸出目錄產生。 在 EF Core 5.0 中新增。 |

上述的 [通用選項](#common-options) 如下所示。

## <a name="dotnet-ef-migrations-list"></a>dotnet ef 遷移清單

列出可用的遷移。

選項：

| 選項                                   | 說明                                                                                                                  |
| ---------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| <nobr>`--connection <CONNECTION>`</nobr> | 資料庫的連接字串。 預設為 AddDbCoNtext 或 OnConfiguring 中指定的值。 在 EF Core 5.0 中新增。 |
| `--no-connect`                           | 請勿連接至資料庫。 在 EF Core 5.0 中新增。                                                                         |

上述的 [通用選項](#common-options) 如下所示。

## <a name="dotnet-ef-migrations-remove"></a>dotnet ef 遷移移除

移除最後一個遷移 (復原針對遷移) 所做的程式碼變更。

選項：

| 選項                 | Short             | 說明                                                                     |
|:-----------------------|:------------------|:--------------------------------------------------------------------------------|
| <nobr>`--force`</nobr> | <nobr>`-f`</nobr> | 還原遷移 (復原已套用至資料庫) 的變更。 |

上述的 [通用選項](#common-options) 如下所示。

## <a name="dotnet-ef-migrations-script"></a>dotnet ef 遷移腳本

從遷移產生 SQL 腳本。

引數：

| 引數              | 說明                                                                                                                                                   |
|:----------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`<FROM>`</nobr> | 開始遷移。 遷移可依名稱或識別碼來識別。 數位0是特殊案例，這表示在 *第一次遷移之前* 。 預設為 0。 |
| `<TO>`                | 結束的遷移。 預設為上次的遷移。                                                                                                         |

選項：

| 選項                           | Short             | 說明                                                        |
|:---------------------------------|:------------------|:-------------------------------------------------------------------|
| `--output <FILE>`                | <nobr>`-o`</nobr> | 要寫入腳本的檔案。                                   |
| `--idempotent`                   | `-i`              | 產生可在任何遷移時用於資料庫的腳本。 |
| <nobr>`--no-transactions`</nobr> |                   | 不要產生 SQL 交易語句。 在 EF Core 5.0 中新增。   |

上述的 [通用選項](#common-options) 如下所示。

下列範例會建立 InitialCreate 遷移的腳本：

```dotnetcli
dotnet ef migrations script 0 InitialCreate
```

下列範例會在 InitialCreate 遷移之後，建立所有遷移的腳本。

```dotnetcli
dotnet ef migrations script 20180904195021_InitialCreate
```

## <a name="additional-resources"></a>其他資源

* [移轉](xref:core/managing-schemas/migrations/index)
* [反向工程](xref:core/managing-schemas/scaffolding)
