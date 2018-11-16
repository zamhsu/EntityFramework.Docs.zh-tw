---
title: 反向工程-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/13/2018
ms.assetid: 6263EF7D-4989-42E6-BDEE-45DA770342FB
uid: core/managing-schemas/scaffolding
ms.openlocfilehash: ef729c0c26d5a1f57099f339eb51cda7e83289df
ms.sourcegitcommit: b3c2b34d5f006ee3b41d6668f16fe7dcad1b4317
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688676"
---
# <a name="reverse-engineering"></a>反向工程

反向工程是 scaffolding 實體類型類別和資料庫結構描述為基礎的 DbContext 類別的程序。 可以使用來執行`Scaffold-DbContext`EF Core 套件管理員主控台 (PMC) 工具的命令或`dotnet ef dbcontext scaffold`.NET 命令列介面 (CLI) 工具的命令。

## <a name="installing"></a>安裝

反向工程、 之前您必須安裝[PMC 工具](xref:core/miscellaneous/cli/powershell)(僅限 Visual Studio) 或[CLI 工具](xref:core/miscellaneous/cli/dotnet)。 請參閱詳細資料的連結。

您也需要安裝適當[資料庫提供者](xref:core/providers/index)資料庫結構描述您想要反向工程。

## <a name="connection-string"></a>連接字串

命令的第一個引數是資料庫的連接字串。 讀取資料庫結構描述，工具會使用此連接字串。

加上引號和逸出的連接字串的方式而定的 shell 您用來執行命令。 請參閱您的殼層文件，如需詳細資訊。 例如，PowerShell 會要求您以逸出`$`字元，而非`\`。

``` powershell
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer
```

``` Console
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="configuration-and-user-secrets"></a>組態和使用者祕密

如果您有 ASP.NET Core 專案，您可以使用`Name=<connection-string>`從組態讀取連接字串的語法。

這適用於[Secret Manager 工具](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager)區隔您的資料庫密碼從您的程式碼基底。

``` Console
dotnet user-secrets set ConnectionStrings.Chinook "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook"
dotnet ef dbcontext scaffold Name=Chinook Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="provider-name"></a>提供者名稱

第二個引數是提供者名稱。 提供者名稱通常是提供者的 NuGet 套件名稱相同。

## <a name="specifying-tables"></a>指定的資料表

資料庫結構描述中的所有資料表都都會反向工程到實體類型中，依預設。 您可以限制哪些資料表進行反向工程藉由指定結構描述和資料表。

`-Schemas`在 PMC 中的參數和`--schema`CLI 中的選項可以用來包含結構描述中的每個資料表。

`-Tables` (PMC) 和`--table`(CLI) 可以用來包含特定的資料表。

若要在 PMC 中包含多個資料表，使用的陣列。

``` powershell
Scaffold-DbContext ... -Tables Artist, Album
```

若要在 CLI 中包含多個資料表，請指定選項多次。

``` Console
dotnet ef dbcontext scaffold ... --table Artist --table Album
```

## <a name="preserving-names"></a>保留的名稱

資料表和資料行名稱被固定的以更加符合的型別和屬性的.NET 命名慣例預設。 指定`-UseDatabaseNames`在 PMC 中切換或`--use-database-names`CLI 中的選項會停用保留原始的資料庫名稱的最大的這種行為。 無效的.NET 識別項仍將會修正，並合成的名稱，例如導覽屬性將仍會符合.NET 命名慣例。

## <a name="fluent-api-or-data-annotations"></a>Fluent API 或資料註解

實體類型會使用 Fluent API 預設設定。 指定`-DataAnnotations`(PMC) 或`--data-annotations`(CLI)，要改為使用資料註解時可能。

例如，使用 Fluent API 會 scaffold this。

``` csharp
entity.Property(e => e.Title)
    .IsRequired()
    .HasMaxLength(160);
```

使用資料註解時將 scaffold 這。

``` csharp
[Required]
[StringLength(160)]
public string Title { get; set; }
```

## <a name="dbcontext-name"></a>DbContext 名稱

包含 scaffold 的 DbContext 類別名稱會加上資料庫的名稱*內容*預設。 若要指定一個不同，請使用`-Context`在 PMC 中和`--context`CLI 中。

## <a name="directories-and-namespaces"></a>目錄和命名空間

實體類別與 DbContext 類別 scaffold 到專案的根目錄，並使用專案的預設命名空間。 您可以指定的目錄，類別會使用包含 scaffold `-OutputDir` (PMC) 或`--output-dir`(CLI)。 根命名空間加上專案的根目錄下的任何子目錄的名稱，將會在命名空間。

您也可以使用`-ContextDir`(PMC) 和`--context-dir`(CLI) 來建立結構的 DbContext 類別，在個別的目錄，從實體型別類別。

``` powershell
Scaffold-DbContext ... -ContextDir Data -OutputDir Models
```

``` Console
dotnet ef dbcontext scaffold ... --context-dir Data --output-dir Models
```

## <a name="how-it-works"></a>它的運作方式

反向工程一開始會讀取資料庫結構描述。 它會讀取資料表、 資料行、 條件約束，以及索引的相關資訊。

接下來，它會建立 EF Core 模型使用的結構描述資訊。 資料表用來建立實體類型;資料行用來建立 [屬性]，與外部索引鍵用來建立關聯性。

最後，此模型用來產生程式碼。 對應的 「 實體類型 」 類別，Fluent API，與資料註解會 scaffold 才能重新建立相同的模型，從您的應用程式。

## <a name="what-doesnt-work"></a>不可行

並非所有相關模型可以使用資料庫結構描述來表示。 例如，有關的資訊**繼承階層架構**，**擁有的類型**，並**資料表分割**不存在於資料庫結構描述。 因為這個緣故，這些建構將永遠不會反向工程。

颾魤 ㄛ**某些資料行類型**EF Core 提供者可能不支援。 在模型中，將不會包含這些資料行。

EF Core 需要有索引鍵，每個實體類型。 資料表，不過，不需要指定主索引鍵。 **資料表沒有主索引鍵**是目前無法反向工程。

您可以定義**並行語彙基元**在 EF Core 模型中，以防止兩位使用者同時更新相同的實體。 某些資料庫具有一種特殊類型來表示這種類型的資料行 (例如，SQL Server 中的 rowversion) 在這種情況下，我們可以反向工程這項資訊;不過，其他並行語彙基元將不會反向工程。

## <a name="customizing-the-model"></a>自訂模型

EF Core 產生的程式碼是您的程式碼。 請隨意變更。 它將只會重新產生您一次反向工程的相同模型。 Scaffold 程式碼代表*一個*模型，可用來存取資料庫，但它不一定*只*可用的模型。

自訂實體類型類別和 DbContext 類別，以符合您的需求。 例如，您可以選擇重新命名類型和屬性，導入繼承階層架構，或將資料表分割成多個實體。 您也可以從模型移除非唯一索引、 未使用的序列和導覽屬性、 選擇性的純量屬性和條件約束名稱。

您也可以新增額外的建構函式、 方法、 屬性等等。 使用另一個部分類別在不同的檔案。 即使您想要一次反向工程模型，適用於這種方法。

## <a name="updating-the-model"></a>更新模型

之後對資料庫進行變更，您可能需要更新您的 EF Core 模型，以反映這些變更。 如果資料庫變更很簡單，它可能只是為了您的 EF Core 模型進行手動變更最簡單。 例如，重新命名資料表或資料行、 移除資料行，或更新資料行的類型是要在程式碼中不重要的變更。

更重要的變更，不過，不是以輕鬆進行手動。 其中一個常見的工作流程是將反向工程的模型，一次使用的資料庫`-Force`(PMC) 或`--force`(CLI) 來使用更新的一個覆寫現有的模型。

另一個常被要求的功能是能夠同時保留重新命名、 型別階層架構等的自訂更新資料庫中的模型。使用問題[#831](https://github.com/aspnet/EntityFrameworkCore/issues/831)來追蹤這項功能的進度。

> [!WARNING]
> 如果您反向重新設計資料庫中的模型，您已對檔案進行任何變更將會遺失。
