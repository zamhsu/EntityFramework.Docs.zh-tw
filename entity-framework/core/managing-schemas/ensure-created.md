---
title: 建立和卸載 Api-EF Core
description: 使用 Entity Framework Core 建立和卸載資料庫的 Api
author: bricelam
ms.date: 11/07/2018
uid: core/managing-schemas/ensure-created
ms.openlocfilehash: c23311fbb8254ba183a6fd1661bba915aedc9a97
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062330"
---
# <a name="create-and-drop-apis"></a>建立及卸除 API

EnsureCreated 和 EnsureDeleted [方法可提供](xref:core/managing-schemas/migrations/index) 輕量替代方式來管理資料庫架構。 當資料是暫時性的，而且可以在架構變更時卸載時，這些方法會很有用。 例如，在原型設計、測試中或本機快取中。

有些提供者 (特別是非關聯式的提供者) 不支援遷移。 對於這些提供者來說，EnsureCreated 通常是初始化資料庫架構的最簡單方式。

> [!WARNING]
> EnsureCreated 和遷移無法順利搭配運作。 如果您使用的是遷移，請不要使用 EnsureCreated 來初始化架構。

從 EnsureCreated 轉換到遷移不是順暢的體驗。 若要這麼做，最簡單的方式是卸載資料庫，並使用遷移重新建立資料庫。 如果您預期未來會使用遷移，最好是從遷移開始，而不是使用 EnsureCreated。

## <a name="ensuredeleted"></a>EnsureDeleted

EnsureDeleted 方法會卸載資料庫（如果存在的話）。 如果您沒有適當的許可權，則會擲回例外狀況。

```csharp
// Drop the database if it exists
dbContext.Database.EnsureDeleted();
```

## <a name="ensurecreated"></a>EnsureCreated

如果資料庫不存在，EnsureCreated 會建立資料庫，並將資料庫架構初始化。 如果有任何資料表 (包括) 其他 DbCoNtext 類別的資料表，則不會初始化架構。

```csharp
// Create the database if it doesn't exist
dbContext.Database.EnsureCreated();
```

> [!TIP]
> 您也可以使用這些方法的非同步版本。

## <a name="sql-script"></a>SQL 指令碼

若要取得 EnsureCreated 所使用的 SQL，您可以使用 GenerateCreateScript 方法。

```csharp
var sql = dbContext.Database.GenerateCreateScript();
```

## <a name="multiple-dbcontext-classes"></a>多個 DbCoNtext 類別

只有當資料庫中沒有任何資料表時，EnsureCreated 才適用。 如有需要，您可以撰寫自己的檢查，以查看架構是否需要初始化，並使用基礎 Irelationaldatabasecreator.hastables 服務來初始化架構。

```csharp
// TODO: Check whether the schema needs to be initialized

// Initialize the schema for this DbContext
var databaseCreator = dbContext.GetService<IRelationalDatabaseCreator>();
databaseCreator.CreateTables();
```
