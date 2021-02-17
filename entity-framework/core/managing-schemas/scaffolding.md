---
title: 反向工程-EF Core
description: 使用 Entity Framework Core 從現有的資料庫反轉模型的工程
author: bricelam
ms.date: 11/13/2018
uid: core/managing-schemas/scaffolding
ms.openlocfilehash: 0f33ee23393819aa86bf1e28ee0eca48110203f6
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543376"
---
# <a name="reverse-engineering"></a>反向工程

反向工程是以資料庫架構為基礎的基礎結構實體型別類別和 DbCoNtext 類別的處理常式。 您可以使用 `Scaffold-DbContext` EF Core 封裝管理員主控台的命令 (PMC) 工具或 `dotnet ef dbcontext scaffold` .net 命令列介面的命令 (CLI) 工具來執行此命令。

## <a name="installing"></a>安裝

在進行反向工程之前，您必須安裝 [PMC 工具](xref:core/cli/powershell) (只 Visual Studio) 或 [CLI 工具](xref:core/cli/dotnet)。 如需詳細資訊，請參閱連結。

您也必須為想要還原工程的資料庫架構安裝適當的 [資料庫提供者](xref:core/providers/index) 。

## <a name="connection-string"></a>連接字串

命令的第一個引數是資料庫的連接字串。 這些工具會使用此連接字串來讀取資料庫架構。

您如何加上引號和 escape 連接字串，取決於您用來執行命令的 shell。 請參閱您的 shell 檔以取得詳細資訊。 例如，PowerShell 要求您必須將字元換用 `$` ，而不是 `\` 。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer
```

***

### <a name="configuration-and-user-secrets"></a>設定和使用者秘密

如果您有 ASP.NET Core 專案，可以使用 `Name=<connection-string>` 語法從設定讀取連接字串。

這與 [秘密管理員工具](/aspnet/core/security/app-secrets#secret-manager) 搭配使用，可讓您的資料庫密碼與程式碼基底分開。

```dotnetcli
dotnet user-secrets set ConnectionStrings:Chinook "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook"
dotnet ef dbcontext scaffold Name=ConnectionStrings:Chinook Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="provider-name"></a>提供者名稱

第二個引數是提供者名稱。 提供者名稱通常與提供者的 NuGet 套件名稱相同。

## <a name="specifying-tables"></a>指定資料表

依預設，資料庫架構中的所有資料表都會依預設為實體類型進行反向工程。 您可以藉由指定架構和資料表來限制哪些資料表會進行反向工程。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

`--schema`選項可用來包含架構內的每個資料表，但 `--table` 可以用來包含特定的資料表。

若要包含多個資料表，請多次指定選項：

```dotnetcli
dotnet ef dbcontext scaffold ... --table Artist --table Album
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

`-Schemas`選項可用來包含架構內的每個資料表，但 `-Tables` 可以用來包含特定的資料表。

若要包含多個資料表，請使用陣列：

```powershell
Scaffold-DbContext ... -Tables Artist, Album
```

***

## <a name="preserving-names"></a>保留名稱

根據預設，已修正資料表和資料行名稱，以更符合 .NET 命名慣例的類型和屬性。 `-UseDatabaseNames`在 PMC 中指定參數或 `--use-database-names` .NET Core CLI 中的選項，將會停用此行為，盡可能保留原始資料庫名稱。 不正確 .NET 識別碼仍會固定，且合成名稱（如導覽屬性）仍會符合 .NET 命名慣例。

## <a name="fluent-api-or-data-annotations"></a>流暢的 API 或資料批註

實體類型預設會使用流暢的 API 進行設定。 指定 `-DataAnnotations` (PMC) 或 `--data-annotations` ( .NET Core CLI) ，以改為在可能的情況下使用資料批註。

例如，使用流暢的 API 將會 scaffold：

```csharp
entity.Property(e => e.Title)
    .IsRequired()
    .HasMaxLength(160);
```

使用資料批註時，會 scaffold：

```csharp
[Required]
[StringLength(160)]
public string Title { get; set; }
```

## <a name="dbcontext-name"></a>DbCoNtext 名稱

根據預設，scaffold DbCoNtext 類別名稱將會是以 *內容* 為尾碼的資料庫名稱。 若要指定不同的，請 `-Context` 在 PMC 和 `--context` .NET Core CLI 中使用。

## <a name="directories-and-namespaces"></a>目錄和命名空間

實體類別和 DbCoNtext 類別會 scaffold 至專案的根目錄，並使用專案的預設命名空間。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

您可以指定使用 scaffold 類別的目錄 `--output-dir` ，而且 `--context-dir` 可以用來將 DbCoNtext 類別 scaffold 至實體類型類別的不同目錄中：

```dotnetcli
dotnet ef dbcontext scaffold ... --context-dir Data --output-dir Models
```

根據預設，命名空間會是根命名空間加上專案根目錄下任何子目錄的名稱。 不過，從 EFCore 5.0 開始，您可以使用來覆寫所有輸出類別的命名空間 `--namespace` 。 您也可以使用下列方式，只覆寫 DbCoNtext 類別的命名空間 `--context-namespace` ：

```dotnetcli
dotnet ef dbcontext scaffold ... --namespace Your.Namespace --context-namespace Your.DbContext.Namespace
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

您可以指定使用 scaffold 類別的目錄 `-OutputDir` ，而且 `-ContextDir` 可以用來將 DbCoNtext 類別 scaffold 至實體類型類別的不同目錄中：

```powershell
Scaffold-DbContext ... -ContextDir Data -OutputDir Models
```

根據預設，命名空間會是根命名空間加上專案根目錄下任何子目錄的名稱。 不過，從 EFCore 5.0 開始，您可以使用來覆寫所有輸出類別的命名空間 `-Namespace` 。 您也可以使用，只覆寫 DbCoNtext 類別的命名空間 `-ContextNamespace` 。

```powershell
Scaffold-DbContext ... -Namespace Your.Namespace -ContextNamespace Your.DbContext.Namespace
```

***

## <a name="how-it-works"></a>運作方式

反向工程從讀取資料庫架構開始。 它會讀取資料表、資料行、條件約束和索引的相關資訊。

接著，它會使用架構資訊來建立 EF Core 模型。 資料表是用來建立實體類型;資料行是用來建立屬性;和外鍵可用來建立關聯性。

最後，此模型會用來產生程式碼。 系統會 scaffold 對應的實體類型類別、流暢的 API 和資料批註，以便從您的應用程式重新建立相同的模型。

## <a name="limitations"></a>限制

* 您無法使用資料庫架構來表示模型的所有內容。 例如，資料庫架構中不會有 [**繼承**](xref:core/modeling/inheritance)階層、 [**擁有類型**](xref:core/modeling/owned-entities)和 [**資料表分割**](xref:core/modeling/table-splitting) 的相關資訊。 因此，這些結構將永遠不會進行反向工程。
* 此外，EF Core 提供者可能不支援 **某些資料行類型** 。 這些資料行不會包含在模型中。
* 您可以在 EF Core 模型中定義 [**並行權杖**](xref:core/modeling/concurrency)，以防止兩位使用者同時更新相同的實體。 某些資料庫具有特殊類型來表示這種類型的資料行 (例如，在 SQL Server) 中的 rowversion，在此情況下，我們可以將此資訊進行反向工程。不過，其他並行存取權杖將不會進行反向工程。
* 反向工程目前不支援[c # 8 可為 Null 的參考型別功能](/dotnet/csharp/tutorials/nullable-reference-types)： EF Core 一律會產生假設功能已停用的 c # 程式碼。 例如，可為 null 的文字資料行將 scaffold 為類型的屬性，而不是使用 `string` `string?` 流暢的 API 或資料批註來設定是否需要屬性。 您可以編輯 scaffold 程式碼，並以 c # 可 null 性注釋取代這些程式碼。 問題 [#15520](https://github.com/dotnet/efcore/issues/15520)會追蹤可為 null 之參考型別的型別支援。

## <a name="customizing-the-model"></a>自訂模型

EF Core 產生的程式碼就是您的程式碼。 您可以隨意變更它。 如果您再次對相同的模型進行反向工程，它只會重新產生。 Scaffold 程式碼代表 *一個* 可以用來存取資料庫的模型，但它當然不是 *唯一* 可以使用的模型。

自訂實體類型類別和 DbCoNtext 類別，以符合您的需求。 例如，您可以選擇重新命名類型和屬性、引進繼承階層，或將資料表分割成多個實體。 您也可以從模型中移除非唯一的索引、未使用的序列和導覽屬性、選擇性的純量屬性和條件約束名稱。

您也可以新增其他的函式、方法、屬性等。 在不同的檔案中使用另一個部分類別。 即使您想要再次對模型進行反向工程，此方法仍可運作。

## <a name="updating-the-model"></a>更新模型

對資料庫進行變更之後，您可能需要更新 EF Core 模型以反映這些變更。 如果資料庫變更很簡單，則只需要手動對 EF Core 模型進行變更，可能會是最簡單的做法。 例如，重新命名資料表或資料行、移除資料行或更新資料行的類型，是在程式碼中進行的簡單變更。

不過，更重要的變更並不容易以手動方式進行。 其中一個常見的工作流程是使用 `-Force` (PMC) 或 `--force` (CLI) ，使用更新的模型來覆寫現有的模型，以從資料庫反向工程模型。

另一個經常要求的功能是能夠從資料庫更新模型，同時保留像是重新命名、類型階層等自訂。使用 [問題 [#831](https://github.com/dotnet/efcore/issues/831) ] 來追蹤這項功能的進度。

> [!WARNING]
> 如果您再次從資料庫反向工程模型，您對檔案所做的任何變更都將遺失。
