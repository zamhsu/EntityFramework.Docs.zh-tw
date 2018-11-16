---
title: 建立和卸除 Api-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/7/2018
ms.openlocfilehash: 40d9e3aa0aba1bf2bc341f01dd815ed7cb7b48fa
ms.sourcegitcommit: b3c2b34d5f006ee3b41d6668f16fe7dcad1b4317
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688625"
---
# <a name="create-and-drop-apis"></a>建立和卸除 Api

EnsureCreated 和 EnsureDeleted 方法提供的輕量型替代品[移轉](migrations/index.md)管理資料庫結構描述。 當資料是暫時性的且可以卸除結構描述變更時，這些方法是在案例中有用的。 比方說在原型設計，在測試中，或用於本機快取。

某些提供者 （尤其是非關聯式的） 不支援移轉。 對於這些提供者，EnsureCreated 通常是最簡單的方式，來初始化資料庫結構描述。

> [!WARNING]
> EnsureCreated 的移轉，以及不合作無間。 如果您使用移轉，請勿使用 EnsureCreated 來初始化結構描述。

從 EnsureCreated 轉換到移轉不是順暢的體驗。 簡單的方法是卸除資料庫，並重新建立它使用移轉。 如果您預期在未來使用移轉，最好是剛開始移轉，而不是使用 EnsureCreated。

## <a name="ensuredeleted"></a>EnsureDeleted

若有的話，EnsureDeleted 方法將會卸除資料庫。 如果您沒有適當的權限，則會擲回例外狀況。

``` csharp
// Drop the database if it exists
dbContext.Database.EnsureDeleted();
```

## <a name="ensurecreated"></a>EnsureCreated

EnsureCreated 會建立資料庫，如果它不存在，並初始化資料庫結構描述。 如果有任何資料表 （包括資料表的另一個 DbContext 類別），結構描述不會初始化。

``` csharp
// Create the database if it doesn't exist
dbContext.Database.EnsureCreated();
```

> [!TIP]
> 這些方法的非同步版本也會提供。

## <a name="sql-script"></a>SQL 指令碼

若要取得 EnsureCreated 所使用的 SQL，您可以使用 GenerateCreateScript 方法。

``` csharp
var sql = dbContext.Database.GenerateCreateScript();
```

## <a name="multiple-dbcontext-classes"></a>多個 DbContext 類別

在資料庫中有任何資料表時，僅適用於 EnsureCreated。 如有需要您可以撰寫您自己的檢查，以查看結構描述是否需要初始化，並使用基礎 IRelationalDatabaseCreator 服務來初始化結構描述。

``` csharp
// TODO: Check whether the schema needs to be initialized

// Initialize the schema for this DbContext
var databaseCreator = dbContext.GetService<IRelationalDatabaseCreator>();
databaseCreator.CreateTables();
```
