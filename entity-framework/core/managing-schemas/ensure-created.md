---
title: 建立和卸載 Api-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/07/2018
uid: core/managing-schemas/ensure-created
ms.openlocfilehash: 32ac6cd043df73cd041780ec4c8805675adc5ab1
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72811782"
---
# <a name="create-and-drop-apis"></a>建立及卸除 API

EnsureCreated 和 EnsureDeleted 方法提供了輕量替代方案，可用於管理資料庫架構的[遷移](migrations/index.md)。 當資料是暫時性的，而且可以在架構變更時卸載，這些方法就很有用。 例如，在原型設計期間、測試中，或用於本機快取。

某些提供者（尤其是非關聯式的）不支援遷移。 對於這些提供者來說，EnsureCreated 通常是初始化資料庫架構的最簡單方式。

> [!WARNING]
> EnsureCreated 和遷移無法妥善搭配運作。 如果您使用的是「遷移」，請勿使用 EnsureCreated 來初始化架構。

從 EnsureCreated 轉換到遷移並不是順暢的體驗。 若要這麼做，最簡單的方法是卸載資料庫，然後使用遷移來重新建立它。 如果您未來想要使用遷移，最好只開始進行遷移，而不是使用 EnsureCreated。

## <a name="ensuredeleted"></a>EnsureDeleted

EnsureDeleted 方法會卸載資料庫（如果有的話）。 如果您沒有適當的許可權，則會擲回例外狀況。

``` csharp
// Drop the database if it exists
dbContext.Database.EnsureDeleted();
```

## <a name="ensurecreated"></a>EnsureCreated

EnsureCreated 將會建立資料庫（如果不存在），並初始化資料庫架構。 如果有任何資料表存在（包括另一個 DbCoNtext 類別的資料表），則不會初始化架構。

``` csharp
// Create the database if it doesn't exist
dbContext.Database.EnsureCreated();
```

> [!TIP]
> 這些方法的非同步版本也可供使用。

## <a name="sql-script"></a>SQL 腳本

若要取得 EnsureCreated 所使用的 SQL，您可以使用 GenerateCreateScript 方法。

``` csharp
var sql = dbContext.Database.GenerateCreateScript();
```

## <a name="multiple-dbcontext-classes"></a>多個 DbCoNtext 類別

只有在資料庫中沒有任何資料表時，EnsureCreated 才適用。 如有需要，您可以撰寫自己的檢查，以查看架構是否需要初始化，並使用基礎 IRelationalDatabaseCreator 服務來初始化架構。

``` csharp
// TODO: Check whether the schema needs to be initialized

// Initialize the schema for this DbContext
var databaseCreator = dbContext.GetService<IRelationalDatabaseCreator>();
databaseCreator.CreateTables();
```
