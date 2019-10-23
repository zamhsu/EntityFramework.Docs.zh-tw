---
title: 移轉 - EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/05/2018
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: e9c4013d17a2d41772822f77b3ceba15702ffc48
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72812054"
---
# <a name="migrations"></a>移轉

資料模型在開發期間變更，而且與資料庫不同步。 您可以卸除資料庫，然後讓 EF 建立符合模型的新資料庫，但此程序會導致資料遺失。 EF Core 中的移轉功能讓您能夠以累加方式來更新資料庫結構描述，讓它與應用程式的資料模型保持同步，同時將現有的資料保留在資料庫中。

移轉包含命令列工具和 API，可協助執行下列工作：

* [建立移轉](#create-a-migration)。 產生程式碼，該程式碼可以更新資料庫，以便與一組模型變更同步。
* [更新資料庫](#update-the-database)。 套用擱置移轉來更新資料庫結構描述。
* [自訂移轉程式碼](#customize-migration-code)。 有時候產生的程式碼需要修改或補充。
* [移除移轉](#remove-a-migration)。 刪除產生的程式碼。
* [還原移轉](#revert-a-migration)。 復原資料庫變更。
* [產生 SQL 指令碼](#generate-sql-scripts)。 您需要指令碼以更新生產環境資料庫，或者針對移轉程式碼進行疑難排解。
* [在執行階段套用移轉](#apply-migrations-at-runtime)。 當設計階段更新以及執行指令碼都不是最佳選項時，請呼叫 `Migrate()` 方法。

> [!TIP]
> 如果 `DbContext` 與啟始專案位於不同的組件中，您可以在[套件管理員主控台工具](xref:core/miscellaneous/cli/powershell#target-and-startup-project)或 [.NET Core CLI 工具](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project)中明確指定目標和啟始專案。

## <a name="install-the-tools"></a>安裝工具

安裝[命令列工具](xref:core/miscellaneous/cli/index)：

* 針對 Visual Studio，我們建議使用[套件管理員主控台工具](xref:core/miscellaneous/cli/powershell)。
* 針對其他開發環境，請選擇 [.NET Core CLI 工具](xref:core/miscellaneous/cli/dotnet)。

## <a name="create-a-migration"></a>建立移轉

在您[定義起始模型](xref:core/modeling/index)之後，即可開始建立資料庫。 若要新增初始移轉，請執行下列命令。

``` powershell
Add-Migration InitialCreate
```

``` Console
dotnet ef migrations add InitialCreate
```

會新增三個檔案到您**移轉**目錄下的專案：

* **XXXXXXXXXXXXXX_InitialCreate.cs** - 主要移轉檔案。 包含套用移轉 (在 `Up()` 中) 及予以反轉 (在 `Down()` 中) 的必要作業。
* **XXXXXXXXXXXXXX_InitialCreate.Designer.cs** - 移轉中繼資料檔案。 包含 EF 使用的資訊。
* **MyContextModelSnapshot.cs** - 您目前模型的快照集。 用來決定新增下一個移轉時所要變更的項目。

檔案名稱中的時間戳記有助於使其依時間先後順序排列，以便您查看變更的進展。

> [!TIP]
> 您可以自由移動移轉檔案和變更其命名空間。 新的移轉會作為最後一個移轉的同層級建立。

## <a name="update-the-database"></a>更新資料庫

接下來，將移轉套用到資料庫以建立結構描述。

``` powershell
Update-Database
```

``` Console
dotnet ef database update
```

## <a name="customize-migration-code"></a>自訂移轉程式碼

對您的 EF Core 模型進行變更後，資料庫結構描述會失去同步。若要將其更新為最新狀態，請新增另一個移轉。 您能夠以類似版本控制系統中認可訊息的方式來使用移轉名稱。 例如，如果變更是要檢閱的新實體類別，您可以選擇像是 AddProductReviews  的名稱。

``` powershell
Add-Migration AddProductReviews
```

``` Console
dotnet ef migrations add AddProductReviews
```

一旦建立移轉 (為其產生程式碼) 後，請檢閱程式碼的正確性，以及視需要新增、移除或修改任何作業，以便正確套用。

例如，移轉可能包含下列作業：

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
> 當作業可能導致資料遺失時 (例如卸除資料行)，移轉建立程序會引發警告。 如果您看到該警告，請務必檢閱移轉程式碼的正確性。

使用適當的命令將移轉套用到資料庫。

``` powershell
Update-Database
```

``` Console
dotnet ef database update
```

### <a name="empty-migrations"></a>空白的移轉

新增移轉而不進行任何模型變更有時很實用。 在這種情況下，新增移轉會建立具有空白類別的程式碼檔案。 您可以自訂此移轉來自訂未與 EF Core 模型直接相關的作業。 您可能會想要以這種方式處理的項目如下：

* 全文檢索搜尋
* 函式
* 預存程序
* 觸發程序
* 檢視

## <a name="remove-a-migration"></a>移除移轉

在您新增移轉時，有時候會發現您必須在套用 EF Core 模型之前對其進行其他變更。 若要移除上一個移轉，請使用此命令。

``` powershell
Remove-Migration
```

``` Console
dotnet ef migrations remove
```

移除移轉後，您可以進行其他模型變更並再次予以新增。

## <a name="revert-a-migration"></a>還原移轉

若您已經套用移轉 (或多個移轉) 到資料庫但需要還原，您可以使用相同命令來套用移轉，但必須指定所要復原到的移轉名稱。

``` powershell
Update-Database LastGoodMigration
```

``` Console
dotnet ef database update LastGoodMigration
```

## <a name="generate-sql-scripts"></a>產生 SQL 指令碼

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

## <a name="apply-migrations-at-runtime"></a>在執行階段套用移轉

某些應用程式在啟動或初次執行期間，可能會想要在執行階段套用移轉。 請使用 `Migrate()` 方法來執行此動作。

此方法的基礎建立在 `IMigrator` 服務之上，其可用於更進階的案例。 您可以使用 `myDbContext.GetInfrastructure().GetService<IMigrator>()` 來加以存取。

``` csharp
myDbContext.Database.Migrate();
```

> [!WARNING]
>
> * 並非所有應用程式都適合此方法。 雖然這相當適合具本機資料庫的應用程式，但大多數應用程式會需要更強固的部署策略，例如產生 SQL 指令碼。
> * 請勿在 `Migrate()` 之前呼叫 `EnsureCreated()`。 `EnsureCreated()` 會略過移轉而建立結構描述，因此造成 `Migrate()` 失敗。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 <xref:core/miscellaneous/cli/index>。
