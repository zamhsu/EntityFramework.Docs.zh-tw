---
title: SQLite 資料庫提供者-限制-EF 核心
author: rowanmiller
ms.author: divega
ms.date: 04/09/2017
ms.assetid: 94ab4800-c460-4caa-a5e8-acdfee6e6ce2
ms.technology: entity-framework-core
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 8a60ccfc61a5757df8ebedf257379d4d3dbffbf6
ms.sourcegitcommit: 60b831318c4f5ec99061e8af6a7c9e7c03b3469c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2018
ms.locfileid: "29719481"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a>SQLite EF 核心資料庫提供者的限制

SQLite 提供者有幾項移轉限制。 大部分的這些限制是限制基礎 SQLite database engine 中的結果，並不專屬於 EF。

## <a name="modeling-limitations"></a>模型限制

通用的關聯式程式庫 （Entity Framework 關聯式資料庫提供者所共用） 會定義應用程式開發介面模型通用於大多數的關聯式資料庫引擎的概念。 SQLite 提供者不支援幾個這些概念。

* 結構描述
* 序列

## <a name="migrations-limitations"></a>移轉限制

SQLite database engine 不支援大多數的其他關聯式資料庫所支援的結構描述作業的數目。 如果您嘗試以不支援作業的其中一套用於 SQLite 資料庫則`NotSupportedException`就會擲回。

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
| RenameColumn         | ✗          |                  |
| RenameIndex          | ✔          | 2.1              |
| RenameTable          | ✔          | 1.0              |
| EnsureSchema         | ✔ （無作業）  | 2.0              |
| DropSchema           | ✔ （無作業）  | 2.0              |
| Insert               | ✔          | 2.0              |
| 更新               | ✔          | 2.0              |
| 刪除               | ✔          | 2.0              |

## <a name="migrations-limitations-workaround"></a>移轉限制因應措施

您可以解決一些手動中撰寫程式碼來執行資料表移轉這些限制的重建。 資料表重建牽涉到重新命名現有的資料表、 建立新的資料表、 將資料複製到新的資料表，以及卸除舊的資料表。 您必須使用`Sql(string)`方法，以執行這些步驟。

請參閱[進行其他種類的資料表結構描述變更](http://sqlite.org/lang_altertable.html#otheralter)SQLite 文件以取得詳細資料中。

未來，EF 可能支援這些作業的某些使用背後的資料表重建方法。 您可以[我們的 GitHub 專案上追蹤這項功能](https://github.com/aspnet/EntityFrameworkCore/issues/329)。
