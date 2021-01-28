---
title: EF Core 工具參考 (封裝管理員主控台) -EF Core
description: Entity Framework Core Visual Studio 封裝管理員主控台的參考指南
author: bricelam
ms.date: 10/27/2020
uid: core/cli/powershell
ms.openlocfilehash: 51f2dbcad0606a0d5571e96d7a7951cf595e2b9e
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983504"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a>Visual Studio 中的 Entity Framework Core 工具參考-封裝管理員主控台

封裝管理員主控台 (PMC) 工具，以 Entity Framework Core 執行設計階段開發工作。 例如，他們會建立 [遷移](/aspnet/core/data/ef-mvc/migrations)、套用遷移，並根據現有的資料庫產生模型的程式碼。 這些命令會使用 [封裝管理員主控台](/nuget/tools/package-manager-console)在 Visual Studio 內部執行。 這些工具會使用 .NET Framework 和 .NET Core 專案。

如果您不是使用 Visual Studio，建議您改用 [EF Core 命令列工具](xref:core/cli/dotnet) 。 .NET Core CLI 工具是跨平臺，並在命令提示字元內執行。

## <a name="installing-the-tools"></a>安裝工具

在 **封裝管理員主控台** 中執行下列命令，以安裝封裝管理員主控台工具：

```powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

在 **封裝管理員主控台** 中執行下列命令，以更新工具。

```powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a>驗證安裝

執行下列命令，確認是否已安裝這些工具：

```powershell
Get-Help about_EntityFrameworkCore
```

輸出看起來會像這樣 (不會告訴您所使用的工具是哪個版本) ：

```output

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

* *專案* 也稱為 *目標專案*，因為這是命令新增或移除檔案的位置。 依預設，**封裝管理員主控台** 中選取的 **預設專案** 是目標專案。 您可以使用選項，將不同的專案指定為目標專案 <nobr>`--project`</nobr> 。

* *啟始專案* 是工具建立和執行的專案。 這些工具必須在設計階段執行應用程式程式碼，以取得專案的相關資訊，例如資料庫連接字串和模型的設定。 根據預設，**方案總管** 中的 **啟始專案** 是啟始專案。 您可以使用選項，將不同的專案指定為啟始專案 <nobr>`--startup-project`</nobr> 。

啟始專案和目標專案通常是相同的專案。 一般情況下，它們是不同的專案：

* EF Core 內容和實體類別位於 .NET Core 類別庫中。
* .NET Core 主控台應用程式或 web 應用程式會參考類別庫。

您也可以將與 [EF Core 內容分開的類別庫中的遷移程式碼](xref:core/managing-schemas/migrations/projects)。

### <a name="other-target-frameworks"></a>其他目標 framework

封裝管理員主控台工具適用于 .NET Core 或 .NET Framework 專案。 在 .NET Standard 類別庫中具有 EF Core 模型的應用程式可能沒有 .NET Core 或 .NET Framework 專案。 例如，這適用于 Xamarin 和通用 Windows 平臺應用程式。 在這種情況下，您可以建立 .NET Core 或 .NET Framework 主控台應用程式專案，其唯一目的是作為工具的啟始專案。 專案可以是沒有真正程式碼的虛擬專案， &mdash; 只需要提供工具的目標。

為什麼需要虛擬專案？ 如先前所述，這些工具必須在設計階段執行應用程式程式碼。 若要這樣做，他們必須使用 .NET Core 或 .NET Framework 執行時間。 當 EF Core 模型位於以 .NET Core 或 .NET Framework 為目標的專案中時，EF Core 工具會從專案借用執行時間。 如果 EF Core 模型位於 .NET Standard 類別庫中，則無法這麼做。 .NET Standard 不是實際的 .NET 執行;它是一組 .NET 開發人員必須支援的 Api 的規格。 因此 .NET Standard 不足以執行應用程式程式碼 EF Core 工具。 您建立用來做為啟始專案的虛擬專案提供可讓工具載入 .NET Standard 類別庫的具體目標平臺。

### <a name="aspnet-core-environment"></a>ASP.NET Core 環境

若要指定 ASP.NET Core 專案 [的環境](/aspnet/core/fundamentals/environments) ，請先設定 **env： ASPNETCORE_ENVIRONMENT** ，再執行命令。

從 EF Core 5.0 開始，還可以將額外的引數傳遞至 CreateHostBuilder，讓您在命令列上指定環境：

```powershell
Update-Database -Args '--environment Production'
```

## <a name="common-parameters"></a>一般參數

下表顯示所有 EF Core 命令通用的參數：

| 參數                 | Description                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| -CoNtext \<String>        | 要使用的 `DbContext` 類別。 僅限類別名稱或完整限定命名空間。  如果省略此參數，EF Core 會尋找內容類別。 如果有多個內容類別，則需要此參數。 |
| -Project \<String>        | 目標專案。 如果省略此參數，則會使用 **封裝管理員主控台** 的 **預設專案** 做為目標專案。                                                                             |
| <nobr>-啟始專案</nobr>\<String> | 啟始專案。 如果省略此參數，則會使用 **方案屬性** 中的 **啟始專案** 做為目標專案。                                                                                 |
| -Args \<String>           | 傳遞至應用程式的引數。 在 EF Core 5.0 中新增。                                                                                                                                                           |
| -Verbose                  | 顯示詳細資訊輸出。                                                                                                                                                                                                 |

若要顯示命令的說明資訊，請使用 PowerShell 的 `Get-Help` 命令。

> [!TIP]
> CoNtext、Project 和啟始專案參數支援 tab 鍵展開。

## <a name="add-migration"></a>Add-Migration

加入新的遷移。

參數：

| 參數                         | Description                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <nobr>-Name \<String><nobr>       | 遷移的名稱。 這是位置參數，而且是必要的。                                              |
| <nobr>-OutputDir \<String></nobr> | 用來輸出檔案的目錄。 路徑是相對於目標專案目錄。 預設為「遷移」。 |
| <nobr>-命名空間 \<String></nobr> | 要用於產生之類別的命名空間。 預設為從輸出目錄產生。 在 EF Core 5.0 中新增。  |

上面列出 [一般參數](#common-parameters) 。

## <a name="drop-database"></a>Drop-Database

卸除資料庫。

參數：

| 參數 | Description                                              |
|:----------|:---------------------------------------------------------|
| -WhatIf   | 顯示要卸載的資料庫，但不要卸載它。 |

上面列出 [一般參數](#common-parameters) 。

## <a name="get-dbcontext"></a>Get-DbContext

列出並取得可用 `DbContext` 類型的相關資訊。

上面列出 [一般參數](#common-parameters) 。

## <a name="get-migration"></a>Get-Migration

列出可用的遷移。 在 EF Core 5.0 中新增。

參數：

| 參數                          | Description                                                                                            |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------ |
| <nobr>-連接 \<String></nobr> | 資料庫的連接字串。 預設為 AddDbCoNtext 或 OnConfiguring 中指定的值。 |
| -NoConnect                         | 請勿連接至資料庫。                                                                         |

上面列出 [一般參數](#common-parameters) 。

## <a name="remove-migration"></a>移除移轉

移除最後一個遷移 (復原針對遷移) 所做的程式碼變更。

參數：

| 參數 | Description                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| -Force    | 還原遷移 (復原已套用至資料庫) 的變更。 |

上面列出 [一般參數](#common-parameters) 。

## <a name="scaffold-dbcontext"></a>Scaffold-DbContext

`DbContext`針對資料庫的和實體類型產生程式碼。 為了 `Scaffold-DbContext` 產生實體型別，資料庫資料表必須有主鍵。

參數：

| 參數                          | Description                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>-連接 \<String></nobr> | 資料庫的連接字串。 針對 ASP.NET Core 2.x 專案，值可以是 *名稱 = \<name of connection string>*。 在該情況下，名稱來自為專案設定的設定來源。 這是位置參數，而且是必要的。 |
| <nobr>-提供者 \<String></nobr>   | 要使用的提供者。 這通常是 NuGet 套件的名稱，例如： `Microsoft.EntityFrameworkCore.SqlServer` 。 這是位置參數，而且是必要的。                                                                                           |
| -OutputDir \<String>               | 要放置檔案的目錄。 路徑是相對於專案目錄。                                                                                                                                                                                             |
| -CoNtextDir \<String>              | 要放置檔案的目錄 `DbContext` 。 路徑是相對於專案目錄。                                                                                                                                                               |
| -命名空間 \<String>               | 要用於所有產生之類別的命名空間。 預設為從根命名空間和輸出目錄產生。 在 EF Core 5.0 中新增。                                                                                                                           |
| -CoNtextNamespace \<String>        | 要用於產生之類別的命名空間 `DbContext` 。 注意：覆寫 `-Namespace` 。 在 EF Core 5.0 中新增。                                                                                                                                                           |
| -CoNtext \<String>                 | `DbContext`要產生的類別名稱。                                                                                                                                                                                                                          |
| -架構 \<String[]>               | 要產生之實體類型的資料表架構。 如果省略此參數，則會包含所有架構。                                                                                                                                                             |
| -資料表 \<String[]>                | 要產生之實體類型的資料表。 如果省略此參數，則會包含所有資料表。                                                                                                                                                                         |
| -DataAnnotations                   | 您可以使用屬性來設定模型 (可能的) 。 如果省略此參數，則只會使用流暢的 API。                                                                                                                                                      |
| -UseDatabaseNames                  | 使用資料表和資料行名稱，如同它們出現在資料庫中一樣。 如果省略此參數，則會變更資料庫名稱，以更緊密符合 c # 名稱樣式慣例。                                                                                       |
| -Force                             | 覆寫現有檔案。                                                                                                                                                                                                                                               |
| -NoOnConfiguring                   | 不要產生 `DbContext.OnConfiguring` 。 在 EF Core 5.0 中新增。                                                                                                                                                                                                         |
| -NoPluralize                       | 請勿使用 pluralizer。 在 EF Core 5.0 中新增。                                                                                                                                                                                                                         |

上面列出 [一般參數](#common-parameters) 。

範例：

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

僅 scaffold 選取的資料表，並在具有指定之名稱和命名空間的個別資料夾中建立內容的範例：

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext -ContextNamespace New.Namespace
```

下列範例會從專案的設定讀取連接字串，可能會使用「 [秘密管理員」工具](/aspnet/core/security/app-secrets#secret-manager)來設定。

```powershell
Scaffold-DbContext "Name=ConnectionStrings:Blogging" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="script-dbcontext"></a>Script-DbContext

從 DbCoNtext 產生 SQL 腳本。 略過任何遷移。 在 EF Core 3.0 中新增。

參數：

| 參數                      | Description                      |
| ------------------------------ | -------------------------------- |
| <nobr>-Output \<String></nobr> | 要寫入結果的檔案。 |

上面列出 [一般參數](#common-parameters) 。

## <a name="script-migration"></a>Script-Migration

產生 SQL 腳本，將所選遷移的所有變更套用到另一個選取的遷移。

參數：

| 參數                    | Description                                                                                                                                                                                                                |
|:---------------------------- |:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| *-從*\<String>            | 開始遷移。 遷移可依名稱或識別碼來識別。 數位0是特殊案例，這表示在 *第一次遷移之前*。 預設為 0。                                                              |
| *-至*\<String>              | 結束的遷移。 預設為上次的遷移。                                                                                                                                                                      |
| -等冪                  | 產生可在任何遷移時用於資料庫的腳本。                                                                                                                                                         |
| <nobr>-NoTransactions</nobr> | 不要產生 SQL 交易語句。 在 EF Core 5.0 中新增。                                                                                                                                                           |
| -Output \<String>            | 要寫入結果的檔案。 如果省略此參數，則會在建立應用程式的執行時間檔案時，在相同的資料夾中建立檔案，例如： */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*。 |

上面列出 [一般參數](#common-parameters) 。

> [!TIP]
> To、From 和 Output 參數支援 tab 鍵展開。

下列範例會使用遷移名稱，從資料庫建立 InitialCreate 遷移 (的腳本，而不需任何遷移) 。

```powershell
Script-Migration 0 InitialCreate
```

下列範例會使用遷移識別碼，在 InitialCreate 遷移之後建立所有遷移的腳本。

```powershell
Script-Migration 20180904195021_InitialCreate
```

## <a name="update-database"></a>Update-Database

將資料庫更新為上次遷移或指定的遷移。

| 參數                           | Description                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>*-遷移*\<String></nobr> | 目標遷移。 遷移可依名稱或識別碼來識別。 數位0是特殊案例，這表示在 *第一次遷移之前* ，會將所有遷移還原。 如果未指定任何遷移，則命令會預設為上次的遷移。 |
| <nobr>-連接 \<String></nobr>  | 資料庫的連接字串。 預設為或中所指定 `AddDbContext` 的 `OnConfiguring` 。 在 EF Core 5.0 中新增。                                                                                                                                |

上面列出 [一般參數](#common-parameters) 。

> [!TIP]
> 遷移參數支援 tab 鍵展開。

下列範例會還原所有的遷移。

```powershell
Update-Database 0
```

下列範例會將資料庫更新為指定的遷移。 第一個使用遷移名稱，第二個使用遷移識別碼和指定的連接：

```powershell
Update-Database InitialCreate
Update-Database 20180904195021_InitialCreate -Connection your_connection_string
```

## <a name="additional-resources"></a>其他資源

* [移轉](xref:core/managing-schemas/migrations/index)
* [反向工程](xref:core/managing-schemas/scaffolding)
