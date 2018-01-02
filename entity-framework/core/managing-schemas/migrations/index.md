---
title: "移轉 - EF Core"
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
ms.technology: entity-framework-core
ms.openlocfilehash: 3e75947e65b8e7707292fec8d74b170aff191395
ms.sourcegitcommit: c72d85805db0aa95f980514a18381fdc5e17c786
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2017
---
<a name="migrations"></a>移轉
==========
移轉提供的方式可讓您以累加的方式將結構描述變更套用到資料庫，使其能夠與您的 EF Core 模型保持同步，同時將現有的資料保留在資料庫中。

<a name="creating-the-database"></a>建立資料庫
---------------------
當您[定義起始模型][1]後，即可開始建立資料庫。 若要這麼做，請新增起始移轉。
安裝 [EF Core 工具][2]並執行適當的命令。

``` powershell
Add-Migration InitialCreate
```
``` Console
dotnet ef migrations add InitialCreate
```

會新增三個檔案到您**移轉**目錄下的專案：

* **00000000000000_InitialCreate.cs** - 主要移轉檔案。 包含套用移轉 (在 `Up()` 中) 及予以反轉 (在 `Down()` 中) 的必要作業。
* **00000000000000_InitialCreate.Designer.cs** - 移轉中繼資料檔案。 包含 EF 使用的資訊。
* **MyContextModelSnapshot.cs** - 您目前模型的快照集。 用來決定新增下一個移轉時所要變更的項目。

檔案名稱中的時間戳記有助於使其依時間先後順序排列，以便您查看變更的進展。

> [!TIP]
> 您可以自由移動移轉檔案和變更其命名空間。 新的移轉會作為最後一個移轉的同層級建立。

接下來，將移轉套用到資料庫以建立結構描述。

``` powershell
Update-Database
```
``` Console
dotnet ef database update
```

<a name="adding-another-migration"></a>新增另一個移轉
------------------------
對您的 EF Core 模型進行變更後，資料庫結構描述會失去同步。若要將其更新為最新狀態，請新增另一個移轉。 您能夠以類似版本控制系統中認可訊息的方式來使用移轉名稱。 例如，如果我進行變更以儲存客戶對產品的評論，我可能會選擇類似 *AddProductReviews* 的項目。

``` powershell
Add-Migration AddProductReviews
```
``` Console
dotnet ef migrations add AddProductReviews
```

建構移轉後，您應該檢視其精確度，並新增任何其他必要作業以正確予以套用。 例如，您的移轉可能包含下列作業：

``` csharp
migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");

migrationBuilder.AddColumn<string>(
    name: "Name",
    table: "Customer",
    nullable: true);
```

雖然這些作業會使資料庫結構描述符合規範，但不會保存現有的客戶名稱。 若要加以改善，請以下列格式重寫。

``` csharp
migrationBuilder.AddColumn<string>(
    name: "Name",
    table: "Customer",
    nullable: true);

migrationBuilder.Sql(
@"
    UPDATE Customer
    SET Name = FirstName + ' ' + LastName;
");

migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");
```

> [!TIP]
> 當作業已建構且可能導致資料遺失時 (例如卸除資料行)，新增移轉會引發警告。 請務必確認這些移轉的正確性。

使用適當的命令將移轉套用到資料庫。

``` powershell
Update-Database
```
``` Console
dotnet ef database update
```

<a name="removing-a-migration"></a>移除移轉
--------------------
在您新增移轉時，有時候會發現您必須在套用 EF Core 模型之前對其進行其他變更。
若要移除上一個移轉，請使用此命令。

``` powershell
Remove-Migration
```
``` Console
dotnet ef migrations remove
```

移除移轉後，您可以進行其他模型變更並再次予以新增。

<a name="reverting-a-migration"></a>還原移轉
---------------------
若您已經套用移轉 (或多個移轉) 到資料庫但需要還原，您可以使用相同命令來套用移轉，但必須指定所要復原到的移轉名稱。

``` powershell
Update-Database LastGoodMigration
```
``` Console
dotnet ef database update LastGoodMigration
```

<a name="empty-migrations"></a>空白的移轉
----------------
新增移轉而不進行任何模型變更有時很實用。 在這種情況下，新增新的移轉會建立一個空白的移轉。 您可以自訂此移轉來自訂未與 EF Core 模型直接相關的作業。
您可能會想要以這種方式處理的項目如下：

* 全文檢索搜尋
* 函數
* 預存程序
* 觸發程序
* 檢視
* 等。

<a name="generating-a-sql-script"></a>產生 SQL 指令碼
-----------------------
對移轉進行偵錯或將其部署到生產資料庫時，產生 SQL 指令碼很實用。 您可以進一步檢閱程式碼的精確度，並對其進行微調以符合生產資料庫的需求。 指令碼也可以搭配部署技術使用。 基本命令如下。

``` powershell
Script-Migration
```
``` Console
dotnet ef migrations script
```

這個命令有多個選項。

執行指令碼之前，**from** 移轉應該是套用到資料庫的最後一個移轉。 若未套用任何移轉，請指定 `0` (此為預設)。

執行指令碼之後，**to** 移轉是套用到資料庫的最後一個移轉。 預設為您專案中的最後一個移轉。

您可以選擇產生**等冪**指令碼。 當移轉尚未套用到資料庫時，此指令碼才適用於移轉。 當您不確定套用到資料庫的上一個移轉是哪項，或是要部署到可能在不同移轉的多個資料庫時，這個方法很實用。

<a name="applying-migrations-at-runtime"></a>在執行階段套用移轉
------------------------------
某些應用程式在啟動或初次執行期間，可能會想要在執行階段套用移轉。 請使用 `Migrate()` 方法來執行此動作。

請注意，並非所有應用程式都適合此方法。 雖然這相當適合具本機資料庫的應用程式，但大多數應用程式會需要更強固的部署策略，例如產生 SQL 指令碼。

``` csharp
myDbContext.Database.Migrate();
```

> [!WARNING]
> 請勿在 `Migrate()` 之前呼叫 `EnsureCreated()`。 `EnsureCreated()` 會略過移轉建立結構描述，並導致 `Migrate()` 失敗。

> [!NOTE]
> 此方法的基礎建立在 `IMigrator` 服務之上，其可用於更進階的案例。 您可以使用 `DbContext.GetService<IMigrator>()` 來加以存取。


  [1]: ../../modeling/index.md
  [2]: ../../miscellaneous/cli/index.md
