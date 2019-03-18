---
title: SQLite 資料庫提供者-限制-EF Core
author: rowanmiller
ms.date: 04/09/2017
ms.assetid: 94ab4800-c460-4caa-a5e8-acdfee6e6ce2
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 53262bc926d79f42c4418a62717a462564dc80bf
ms.sourcegitcommit: 6c4e06bc62d98442530e93a44725e38e59483d42
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58131407"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a>SQLite EF Core 資料庫提供者的限制

SQLite 提供者有幾項移轉限制。 這些限制大部分都是基礎的 SQLite 資料庫引擎中的限制的結果，並不是特別針對 EF。

## <a name="modeling-limitations"></a>模型限制

（Entity Framework 的關聯式資料庫提供者所共用） 的通用關聯式程式庫會定義 Api 模型化概念通用於大部分的關聯式資料庫引擎。 SQLite 提供者不支援幾個這些概念。

* 結構描述
* 序列

## <a name="migrations-limitations"></a>移轉限制

SQLite 資料庫引擎不支援結構描述作業大部分的其他關聯式資料庫所支援的數量。 如果您嘗試將其中一個不支援的作業套用至 SQLite 資料庫則`NotSupportedException`就會擲回。

| 運算            | 支援？ | 需要版本 |
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
| 更新               | ✔          | 2.0              |
| 刪除               | ✔          | 2.0              |

## <a name="migrations-limitations-workaround"></a>移轉限制的因應措施

您可以因應措施是一些手動撰寫的程式碼在您移轉來執行資料表中的這些限制的重建。 重建資料表需要重新命名現有的資料表、建立新的資料表、將資料複製到新的資料表，以及卸除舊的資料表。 您必須使用`Sql(string)`來執行這些步驟的某些方法。

請參閱[進行其他類型的資料表結構描述變更](http://sqlite.org/lang_altertable.html#otheralter)SQLite 文件以取得詳細資料中。

在未來，EF 可能支援這些作業的一些使用在幕後的資料表重建方法。 您可以[追蹤這項功能，在我們的 GitHub 專案](https://github.com/aspnet/EntityFrameworkCore/issues/329)。
