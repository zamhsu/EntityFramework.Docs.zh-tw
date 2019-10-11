---
title: SQLite 資料庫提供者-限制-EF Core
author: rowanmiller
ms.date: 04/09/2017
ms.assetid: 94ab4800-c460-4caa-a5e8-acdfee6e6ce2
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 2f80dc195265787318ac4925dd937da45ffad011
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72179776"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a>SQLite EF Core 資料庫提供者的限制

SQLite 提供者有許多遷移限制。 這些限制大多是基礎 SQLite 資料庫引擎限制的結果，而不是 EF 特有的。

## <a name="modeling-limitations"></a>模型限制

通用關聯式程式庫（由 Entity Framework 關係資料庫提供者共用）定義了適用于大部分關係資料庫引擎通用概念的 Api。 SQLite 提供者不支援其中幾個概念。

* 結構描述
* 序列
* 計算資料行

## <a name="query-limitations"></a>查詢限制

SQLite 原本就不支援下列資料類型。 EF Core 可以讀取和寫入這些類型的值，也支援查詢是否相等（`where e.Property == value`）。 不過，其他作業（例如比較和順序）將需要用戶端上的評估。

* DateTimeOffset
* Decimal
* TimeSpan
* UInt64

我們建議使用 DateTime 值，而不是 `DateTimeOffset`。 處理多個時區時，建議您先將值轉換成 UTC，再儲存，然後再轉換回適當的時區。

@No__t-0 類型提供高的精確度。 不過，如果您不需要該有效位數層級，我們建議您改為使用 double。 您可以使用[值轉換器](../../modeling/value-conversions.md)，在您的類別中繼續使用 decimal。

``` csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a>遷移限制

SQLite 資料庫引擎不支援大多數其他關係資料庫所支援的許多架構作業。 如果您嘗試將其中一個不支援的作業套用至 SQLite 資料庫，將會擲回 `NotSupportedException`。

| 運算            | 支援? | 需要版本 |
|:---------------------|:-----------|:-----------------|
| AddColumn            | ✔          | 1.0              |
| AddForeignKey        | ✗          |                  |
| AddPrimaryKey        | ✗          |                  |
| AddUniqueConstraint  | ✗          |                  |
| AlterColumn          | ✗          |                  |
| CreateIndex          | ✔          | 1.0              |
| CreateTable          | ✔          | 1.0              |
| DropColumn           | ✗          |                  |
| DropForeignKey       | ✗          |                  |
| DropIndex            | ✔          | 1.0              |
| DropPrimaryKey       | ✗          |                  |
| DropTable            | ✔          | 1.0              |
| DropUniqueConstraint | ✗          |                  |
| RenameColumn         | ✔          | 2.2.2            |
| RenameIndex          | ✔          | 2.1              |
| RenameTable          | ✔          | 1.0              |
| EnsureSchema         | ✔ (no-op)  | 2.0              |
| DropSchema           | ✔ (no-op)  | 2.0              |
| Insert               | ✔          | 2.0              |
| Update               | ✔          | 2.0              |
| DELETE               | ✔          | 2.0              |

## <a name="migrations-limitations-workaround"></a>遷移限制解決方法

您可以藉由在您的遷移中手動撰寫程式碼來執行資料表重建，以解決其中一些限制。 重建資料表需要重新命名現有的資料表、建立新的資料表、將資料複製到新的資料表，以及卸除舊的資料表。 您將需要使用 `Sql(string)` 方法來執行其中一些步驟。

如需詳細資訊，請參閱 SQLite 檔中的[建立其他類型的資料表架構變更](https://sqlite.org/lang_altertable.html#otheralter)。

在未來，EF 可能會使用部分的資料表重建方法來支援其中某些作業。 您可以[在我們的 GitHub 專案上追蹤這項功能](https://github.com/aspnet/EntityFrameworkCore/issues/329)。
