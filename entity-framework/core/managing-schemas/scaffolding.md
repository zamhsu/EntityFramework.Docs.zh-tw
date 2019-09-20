---
title: 反向工程-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/13/2018
ms.assetid: 6263EF7D-4989-42E6-BDEE-45DA770342FB
uid: core/managing-schemas/scaffolding
ms.openlocfilehash: 775a929982b9f4fb10aad9cd43bbb555ce632ad1
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149021"
---
# <a name="reverse-engineering"></a>反向工程

反向工程是基類庫實體類型類別的程式，以及以資料庫架構為基礎的 DbCoNtext 類別。 您可以使用`Scaffold-DbContext` EF Core 套件管理員主控台（PMC）工具的命令`dotnet ef dbcontext scaffold`或 .net 命令列介面（CLI）工具的命令來執行此作業。

## <a name="installing"></a>安裝

在反向工程之前，您必須安裝[PMC 工具](xref:core/miscellaneous/cli/powershell)（僅限 Visual Studio）或[CLI 工具](xref:core/miscellaneous/cli/dotnet)。 如需詳細資訊，請參閱連結。

您也需要為您想要反向工程的資料庫架構安裝適當的[資料庫提供者](xref:core/providers/index)。

## <a name="connection-string"></a>連接字串

命令的第一個引數是資料庫的連接字串。 這些工具會使用此連接字串來讀取資料庫架構。

您如何加上引號和 escape 連接字串的方式，取決於您用來執行命令的 shell。 如需詳細資訊，請參閱您的命令介面檔。 例如，PowerShell 會要求您將字元換`$`用，而不`\`是。

``` powershell
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer
```

``` Console
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="configuration-and-user-secrets"></a>設定和使用者秘密

如果您有 ASP.NET Core 專案，您可以使用`Name=<connection-string>`語法來讀取設定中的連接字串。

這適用于[密碼管理員工具](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager)，可讓您的資料庫密碼與程式碼基底分開。

``` Console
dotnet user-secrets set ConnectionStrings.Chinook "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook"
dotnet ef dbcontext scaffold Name=Chinook Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="provider-name"></a>提供者名稱

第二個引數是提供者名稱。 提供者名稱通常與提供者的 NuGet 套件名稱相同。

## <a name="specifying-tables"></a>指定資料表

根據預設，資料庫架構中的所有資料表都會反向工程成實體類型。 您可以藉由指定架構和資料表來限制哪些資料表會進行反向工程。

PMC `-Schemas`中的參數和 CLI `--schema`中的選項，可以用來包含架構中的每個資料表。

`-Tables`（PMC）和`--table` （CLI）可以用來包含特定的資料表。

若要在 PMC 中包含多個資料表，請使用陣列。

``` powershell
Scaffold-DbContext ... -Tables Artist, Album
```

若要在 CLI 中包含多個資料表，請多次指定選項。

``` Console
dotnet ef dbcontext scaffold ... --table Artist --table Album
```

## <a name="preserving-names"></a>保留名稱

根據預設，資料表和資料行名稱已修正，以便更符合類型和屬性的 .NET 命名慣例。 在 PMC 中指定`--use-database-names` 參數或CLI中的選項，將會停用此行為，盡可能保留原始的資料庫名稱。`-UseDatabaseNames` 不正確 .NET 識別碼仍然會固定，而合成名稱（例如導覽屬性）仍會符合 .NET 命名慣例。

## <a name="fluent-api-or-data-annotations"></a>流暢的 API 或資料批註

實體類型預設會使用流暢的 API 來設定。 如果`-DataAnnotations`可能的話，請`--data-annotations`指定（PMC）或（CLI）來改為使用資料批註。

例如，使用流暢的 API 將會 scaffold：

``` csharp
entity.Property(e => e.Title)
    .IsRequired()
    .HasMaxLength(160);
```

使用資料批註時，會 scaffold 下列內容：

``` csharp
[Required]
[StringLength(160)]
public string Title { get; set; }
```

## <a name="dbcontext-name"></a>DbCoNtext 名稱

根據預設，scaffold DbCoNtext 類別名稱會是以*內容*尾碼的資料庫名稱。 若要指定不同的帳戶， `-Context`請在 PMC `--context`和 CLI 中使用。

## <a name="directories-and-namespaces"></a>目錄和命名空間

實體類別和 DbCoNtext 類別會 scaffold 至專案的根目錄，並使用專案的預設命名空間。 您可以使用`-OutputDir` （PMC）或`--output-dir` （CLI）來指定 scaffold 類別的目錄。 命名空間會是根命名空間，加上專案根目錄下任何子目錄的名稱。

您也可以使用`-ContextDir` （PMC）和`--context-dir` （CLI），將 DbCoNtext 類別 scaffold 至實體類型類別的不同目錄中。

``` powershell
Scaffold-DbContext ... -ContextDir Data -OutputDir Models
```

``` Console
dotnet ef dbcontext scaffold ... --context-dir Data --output-dir Models
```

## <a name="how-it-works"></a>運作方式

反向工程一開始會先閱讀資料庫架構。 它會讀取資料表、資料行、條件約束和索引的相關資訊。

接下來，它會使用架構資訊來建立 EF Core 模型。 資料表是用來建立實體類型;資料行是用來建立屬性;和外鍵是用來建立關聯性。

最後，模型是用來產生程式碼。 對應的實體類型類別、流暢的 API 和資料批註會 scaffold，以便從您的應用程式重新建立相同的模型。

## <a name="what-doesnt-work"></a>什麼不適用

並非有關模型的所有專案都可以使用資料庫架構來表示。 例如，有關[**繼承**](../modeling/inheritance.md)階層、[**擁有類型**](../modeling/owned-entities.md)和[**資料表分割**](../modeling/table-splitting.md)的資訊並不會出現在資料庫架構中。 因此，這些結構永遠不會進行反向工程。

此外，EF Core 提供者可能不支援**某些資料行類型**。 這些資料行不會包含在模型中。

您可以在 EF Core 模型中定義[**並行標記**](../modeling/concurrency.md)，以防止兩個使用者同時更新相同的實體。 某些資料庫具有特殊類型來表示這種類型的資料行（例如，SQL Server 中的 rowversion），在此情況下，我們可以對這項資訊進行反向工程。不過，其他並行存取權杖將不會進行反向工程。

## <a name="customizing-the-model"></a>自訂模型

EF Core 所產生的程式碼就是您的程式碼。 您可以隨意變更它。 只有當您再次對相同的模型進行反向工程時，才會重新產生。 Scaffold 程式碼代表*一個*可用於存取資料庫的模型，但它不是*唯一*可以使用的模型。

自訂實體類型類別和 DbCoNtext 類別，以符合您的需求。 例如，您可以選擇重新命名類型和屬性、引進繼承階層，或將資料表分割成多個實體。 您也可以從模型中移除非唯一的索引、未使用的序列和導覽屬性、選擇性純量屬性和條件約束名稱。

您也可以加入其他的函數、方法、屬性等。 在不同的檔案中使用另一個部分類別。 即使您想要再次對模型進行反向工程，此方法仍可運作。

## <a name="updating-the-model"></a>更新模型

對資料庫進行變更之後，您可能需要更新 EF Core 模型以反映這些變更。 如果資料庫變更很簡單，只需要手動對 EF Core 模型進行變更，可能是最簡單的方法。 例如，重新命名資料表或資料行、移除資料行或更新資料行的類型，都是在程式碼中進行的簡單變更。

不過，更重要的變更並不是手動進行。 其中一個常見的工作流程是使用`-Force` （PMC）或`--force` （CLI），再次從資料庫對模型進行反向工程，以覆寫現有的模型並更新一個。

另一個常見的要求功能是能夠從資料庫更新模型，同時保留自訂，例如重新命名、類型階層等等。使用 [問題[#831](https://github.com/aspnet/EntityFrameworkCore/issues/831) ] 追蹤此功能的進度。

> [!WARNING]
> 如果您再次從資料庫對模型進行反向工程，您對檔案所做的任何變更都將遺失。
