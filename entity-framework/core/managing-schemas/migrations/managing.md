---
title: 管理遷移-EF Core
description: 新增、移除及以其他方式管理使用 Entity Framework Core 的資料庫架構遷移
author: bricelam
ms.date: 10/27/2020
uid: core/managing-schemas/migrations/managing
ms.openlocfilehash: a6e64bde753ae34b6dccb10b228d1ea87a79357b
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024169"
---
# <a name="managing-migrations"></a>管理遷移

當您的模型變更時，會在一般開發過程中加入和移除遷移，並將遷移檔案簽入您專案的原始檔控制中。 若要管理遷移，您必須先安裝 [EF Core 命令列工具](xref:core/cli/index)。

> [!TIP]
> 如果 `DbContext` 與啟始專案位於不同的組件中，您可以在[套件管理員主控台工具](xref:core/cli/powershell#target-and-startup-project)或 [.NET Core CLI 工具](xref:core/cli/dotnet#target-project-and-startup-project)中明確指定目標和啟始專案。

## <a name="add-a-migration"></a>新增移轉

變更您的模型之後，您可以新增該變更的遷移：

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration AddBlogCreatedTimestamp
```

***

您能夠以類似版本控制系統中認可訊息的方式來使用移轉名稱。 例如，如果變更是您的實體上的新屬性，您可以選擇 *AddBlogCreatedTimestamp* 之類的名稱 `CreatedTimestamp` `Blog` 。

會新增三個檔案到您 **移轉** 目錄下的專案：

* **XXXXXXXXXXXXXX_AddCreatedTimestamp .cs**--主要的遷移檔。 包含套用移轉 (在 `Up` 中) 及予以反轉 (在 `Down` 中) 的必要作業。
* **XXXXXXXXXXXXXX_AddCreatedTimestamp .cs**--遷移中繼資料檔案。 包含 EF 使用的資訊。
* **MyContextModelSnapshot.cs** - 您目前模型的快照集。 用來決定新增下一個移轉時所要變更的項目。

檔案名稱中的時間戳記有助於使其依時間先後順序排列，以便您查看變更的進展。

### <a name="namespaces"></a>命名空間

您可以自由地手動移動移轉檔案及變更其命名空間。 新的移轉會作為最後一個移轉的同層級建立。 或者，您可以在產生時期指定目錄，如下所示：

#### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --output-dir Your/Directory
```

> [!NOTE]
> 在 EF Core 5.0 中，您也可以使用來個別變更目錄的命名空間 `--namespace` 。

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration InitialCreate -OutputDir Your\Directory
```

> [!NOTE]
> 在 EF Core 5.0 中，您也可以使用來個別變更目錄的命名空間 `-Namespace` 。

***

## <a name="customize-migration-code"></a>自訂移轉程式碼

雖然 EF Core 通常會建立精確的遷移，但您應該一律檢查程式碼，並確定其對應至所需的變更;在某些情況下，甚至必須這樣做。

### <a name="column-renames"></a>資料行重新命名

其中一個值得注意的範例是，當您重新命名屬性時，需要進行自訂的遷移。 例如，如果您將屬性從重新命名 `Name` 為 `FullName` ，EF Core 將會產生下列遷移：

```csharp
migrationBuilder.DropColumn(
    name: "Name",
    table: "Customers");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customers",
    nullable: true);
```

EF Core 通常無法知道何時要卸載資料行，並建立新的資料行 (兩個不同的變更) ，以及何時應重新命名資料行。 如果上述的遷移依原樣套用，您所有的客戶名稱都會遺失。 若要重新命名資料行，請以下列方式取代上述產生的遷移：

```csharp
migrationBuilder.RenameColumn(
    name: "Name",
    table: "Customers",
    newName: "FullName");
```

> [!TIP]
> 當作業可能導致資料遺失時 (例如卸除資料行)，移轉建立程序會引發警告。 如果您看到該警告，請務必檢閱移轉程式碼的正確性。

### <a name="adding-raw-sql"></a>新增原始 SQL

雖然您可以透過內建 API 來重新命名資料行，但在許多情況下都無法這麼做。 例如，我們可能想要 `FirstName` 使用單一的新屬性來取代現有和 `LastName` 屬性 `FullName` 。 EF Core 所產生的遷移將如下所示：

```csharp
migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customer",
    nullable: true);
```

同樣地，這會導致不必要的資料遺失。 若要從舊的資料行傳送資料，我們會重新排列遷移，並引進原始 SQL 作業，如下所示：

```csharp
migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customer",
    nullable: true);

migrationBuilder.Sql(
@"
    UPDATE Customer
    SET FullName = FirstName + ' ' + LastName;
");

migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");
```

### <a name="arbitrary-changes-via-raw-sql"></a>透過原始 SQL 的任意變更

原始 SQL 也可以用來管理 EF Core 不知道的資料庫物件。 若要這樣做，請新增遷移，而不進行任何模型變更;將會產生空白的遷移，然後您可以使用原始的 SQL 作業來填入。

例如，下列遷移會建立 SQL Server 預存程式：

```csharp
migrationBuilder.Sql(
@"
    EXEC ('CREATE PROCEDURE getFullName
        @LastName nvarchar(50),
        @FirstName nvarchar(50)
    AS
        RETURN @LastName + @FirstName;')");
```

> [!TIP]
> `EXEC` 當語句必須是 SQL 批次中的第一個語句或只有一個語句時，就會使用。 它也可以用來解決等冪性遷移腳本中的剖析器錯誤，這些錯誤可能會在參考的資料行目前不存在於資料表上時發生。

這可以用來管理資料庫的任何層面，包括：

* 預存程序
* 全文檢索搜尋
* 函式
* 觸發程序
* 檢視

在大部分情況下，EF Core 會在套用遷移時，自動將每個遷移包裝在自己的交易中。 可惜的是，有些遷移作業無法在某些資料庫的交易中執行;在這些情況下，您可以藉由傳遞 `suppressTransaction: true` 到來退出交易 `migrationBuilder.Sql` 。

如果 `DbContext` 與啟始專案位於不同的組件中，您可以在[套件管理員主控台工具](xref:core/cli/powershell#target-and-startup-project)或 [.NET Core CLI 工具](xref:core/cli/dotnet#target-project-and-startup-project)中明確指定目標和啟始專案。

## <a name="remove-a-migration"></a>移除移轉

在您新增移轉時，有時候會發現您必須在套用 EF Core 模型之前對其進行其他變更。 若要移除上一個移轉，請使用此命令。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations remove
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Remove-Migration
```

***

移除移轉後，您可以進行其他模型變更並再次予以新增。

> [!WARNING]
> 避免移除已套用至生產環境資料庫的任何遷移。 如此一來，您將無法從資料庫還原這些遷移，而且可能會中斷後續遷移所做的假設。

## <a name="listing-migrations"></a>列出遷移

您可以列出所有現有的遷移，如下所示：

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations list
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

> [!NOTE]
> 此命令是在 EF Core 5.0 中引進。

```powershell
Get-Migration
```

***

## <a name="resetting-all-migrations"></a>正在重設所有遷移

在某些極端的情況下，可能需要移除所有的遷移並重新開始。 這可以藉由刪除您的 [ **遷移** ] 資料夾並卸載您的資料庫來輕鬆完成;屆時，您可以建立新的初始遷移，其中將包含您目前的整個架構。

您也可以重設所有遷移，並建立單一的遷移，而不會遺失您的資料。 這有時稱為「抓」，並牽涉到一些手動工作：

* 刪除您的 **遷移** 資料夾
* 建立新的遷移，並為其產生 SQL 腳本
* 在您的資料庫中，刪除「遷移記錄」資料表中的所有資料列
* 將單一資料列插入至「遷移」記錄，以記錄已套用第一個遷移，因為您的資料表已經存在。 Insert SQL 是上面所產生之 SQL 腳本中的最後一個作業。

> [!WARNING]
> 刪除 **遷移** 資料夾時，任何 [自訂的遷移程式碼](#customize-migration-code)都將遺失。  您必須手動將任何自訂套用至新的初始遷移，才能予以保留。

## <a name="additional-resources"></a>其他資源

* [Entity Framework Core 工具參考-.Net CORE CLI](xref:core/cli/dotnet) ：包含用來更新、卸載、新增、移除等等的命令。
* [Entity Framework Core 工具參考-Visual Studio 中的套件管理員主控台](xref:core/cli/powershell) ：包含用來更新、卸載、新增、移除等等的命令。
