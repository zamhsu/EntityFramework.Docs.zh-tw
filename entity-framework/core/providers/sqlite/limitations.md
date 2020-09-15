---
title: SQLite 資料庫提供者-限制-EF Core
description: 與其他提供者相較之下，Entity Framework Core SQLite 資料庫提供者的限制
author: bricelam
ms.date: 07/16/2020
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 546910afb9c97a93a7cc471bb813be0b9874a4bd
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071221"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a>SQLite EF Core 資料庫提供者限制

SQLite 提供者有許多的遷移限制。 這些限制大多是基礎 SQLite 資料庫引擎中的限制所造成的結果，而且不是 EF 特有的。

## <a name="modeling-limitations"></a>模型限制

Entity Framework 關係資料庫提供者所共用的通用關聯式程式庫 () 會定義 Api 來模型化大部分關係資料庫引擎的常見概念。 SQLite 提供者不支援其中的一些概念。

* 結構描述
* 序列

## <a name="query-limitations"></a>查詢限制

SQLite 本身並不支援下列資料類型。 EF Core 可以讀取和寫入這些類型的值，也支援查詢是否相等 (`where e.Property == value`) 。 不過，其他作業（例如比較和排序）將需要在用戶端上進行評估。

* DateTimeOffset
* Decimal
* TimeSpan
* UInt64

`DateTimeOffset`我們建議您不要使用日期時間值。 處理多個時區時，建議您先將值轉換成 UTC，再儲存，然後再轉換回適當的時區。

此 `Decimal` 類型提供高等級的精確度。 但是，如果您不需要該層級的精確度，我們建議您改為使用 double。 您可以使用 [值轉換器](xref:core/modeling/value-conversions) 來繼續在您的類別中使用 decimal。

``` csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a>遷移限制

SQLite 資料庫引擎不支援大部分其他關係資料庫都支援的一些架構作業。 如果您嘗試將其中一項不支援的作業套用至 SQLite 資料庫， `NotSupportedException` 將會擲回。

將會嘗試重建，以便執行某些作業。 只有屬於 EF Core 模型一部分的資料庫成品才能重建。 如果資料庫成品不是模型的一部分，例如，如果是在遷移內部手動建立的，則 `NotSupportedException` 仍會擲回。

| 作業            | 是否支援？  | 需要版本 |
|:---------------------|:------------|:-----------------|
| AddCheckConstraint   | ✔ (重建)  | 5.0              |
| AddColumn            | ✔           | 1.0              |
| AddForeignKey        | ✔ (重建)  | 5.0              |
| AddPrimaryKey        | ✔ (重建)  | 5.0              |
| AddUniqueConstraint  | ✔ (重建)  | 5.0              |
| AlterColumn          | ✔ (重建)  | 5.0              |
| CreateIndex          | ✔           | 1.0              |
| CreateTable          | ✔           | 1.0              |
| DropCheckConstraint  | ✔ (重建)  | 5.0              |
| DropColumn           | ✔ (重建)  | 5.0              |
| DropForeignKey       | ✔ (重建)  | 5.0              |
| DropIndex            | ✔           | 1.0              |
| DropPrimaryKey       | ✔ (重建)  | 5.0              |
| DropTable            | ✔           | 1.0              |
| DropUniqueConstraint | ✔ (重建)  | 5.0              |
| RenameColumn         | ✔           | 2.2.2            |
| RenameIndex          | ✔ (重建)  | 2.1              |
| RenameTable          | ✔           | 1.0              |
| EnsureSchema         | ✔ (無 op)    | 2.0              |
| DropSchema           | ✔ (無 op)    | 2.0              |
| 插入               | ✔           | 2.0              |
| 更新               | ✔           | 2.0              |
| 刪除               | ✔           | 2.0              |

## <a name="migrations-limitations-workaround"></a>遷移限制解決方法

您可以藉由在您的遷移中手動撰寫程式碼來執行重建，以解決其中一些限制。 資料表重建牽涉到建立新的資料表、將資料複製到新的資料表、卸載舊的資料表、重新命名新的資料表。 您將需要使用 `Sql(string)` 方法來執行這些步驟的其中一部分。

如需詳細資訊，請參閱在 SQLite 檔中 [進行其他類型的資料表架構變更](https://sqlite.org/lang_altertable.html#otheralter) 。
