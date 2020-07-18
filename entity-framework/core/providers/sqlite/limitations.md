---
title: SQLite 資料庫提供者-限制-EF Core
author: bricelam
ms.date: 07/16/2020
ms.assetid: 94ab4800-c460-4caa-a5e8-acdfee6e6ce2
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 393f5e80ce2e11dcb11c2048e06effa27e48dc13
ms.sourcegitcommit: d85263b5d5d665dbaf94de8832e2917bce048b34
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2020
ms.locfileid: "86451225"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a>SQLite EF Core 資料庫提供者限制

SQLite 提供者有許多遷移限制。 這些限制大多是基礎 SQLite 資料庫引擎限制的結果，而不是 EF 特有的。

## <a name="modeling-limitations"></a>模型限制

通用關聯式程式庫（由 Entity Framework 關係資料庫提供者共用）定義了適用于大部分關係資料庫引擎通用概念的 Api。 SQLite 提供者不支援其中幾個概念。

* 結構描述
* 序列

## <a name="query-limitations"></a>查詢限制

SQLite 原本就不支援下列資料類型。 EF Core 可以讀取和寫入這些類型的值，也支援查詢是否相等（ `where e.Property == value` ）。 不過，其他作業（例如比較和順序）將需要用戶端上的評估。

* DateTimeOffset
* Decimal
* TimeSpan
* UInt64

`DateTimeOffset`我們建議使用 DateTime 值，而不是。 處理多個時區時，建議您先將值轉換成 UTC，再儲存，然後再轉換回適當的時區。

`Decimal`型別提供高程度的精確度。 不過，如果您不需要該有效位數層級，我們建議您改為使用 double。 您可以使用[值轉換器](../../modeling/value-conversions.md)，在您的類別中繼續使用 decimal。

``` csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a>遷移限制

SQLite 資料庫引擎不支援大多數其他關係資料庫所支援的許多架構作業。 如果您嘗試將其中一個不支援的作業套用至 SQLite 資料庫， `NotSupportedException` 將會擲回。

系統會嘗試重建，以便執行特定作業。 只有屬於 EF Core 模型之一部分的資料庫成品才可以重建。 如果資料庫成品不是模型的一部分（例如，它是在遷移內手動建立的），則仍會擲回 `NotSupportedException` 。

| 作業            | 是否支援？  | 需要版本 |
|:---------------------|:------------|:-----------------|
| AddCheckConstraint   | ✔（重建） | 5.0              |
| AddColumn            | ✔           | 1.0              |
| AddForeignKey        | ✔（重建） | 5.0              |
| AddPrimaryKey        | ✔（重建） | 5.0              |
| AddUniqueConstraint  | ✔（重建） | 5.0              |
| AlterColumn          | ✔（重建） | 5.0              |
| CreateIndex          | ✔           | 1.0              |
| CreateTable          | ✔           | 1.0              |
| DropCheckConstraint  | ✔（重建） | 5.0              |
| DropColumn           | ✔（重建） | 5.0              |
| DropForeignKey       | ✔（重建） | 5.0              |
| DropIndex            | ✔           | 1.0              |
| DropPrimaryKey       | ✔（重建） | 5.0              |
| DropTable            | ✔           | 1.0              |
| DropUniqueConstraint | ✔（重建） | 5.0              |
| RenameColumn         | ✔           | 2.2.2            |
| RenameIndex          | ✔（重建） | 2.1              |
| RenameTable          | ✔           | 1.0              |
| EnsureSchema         | ✔（無 op）   | 2.0              |
| DropSchema           | ✔（無 op）   | 2.0              |
| 插入               | ✔           | 2.0              |
| 更新               | ✔           | 2.0              |
| 刪除               | ✔           | 2.0              |

## <a name="migrations-limitations-workaround"></a>遷移限制解決方法

您可以藉由在您的遷移中手動撰寫程式碼來執行重建，以解決其中一些限制。 資料表重建牽涉到建立新的資料表、將資料複製到新的資料表、卸載舊的資料表、重新命名新的資料表。 您將需要使用 `Sql(string)` 方法來執行其中一些步驟。

如需詳細資訊，請參閱 SQLite 檔中的[建立其他類型的資料表架構變更](https://sqlite.org/lang_altertable.html#otheralter)。
