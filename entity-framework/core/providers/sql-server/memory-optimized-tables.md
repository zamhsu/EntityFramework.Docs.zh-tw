---
title: Microsoft SQL Server 資料庫提供者-記憶體優化資料表-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2e007c82-c6e4-45bb-8129-851b79ec1a0a
uid: core/providers/sql-server/memory-optimized-tables
ms.openlocfilehash: 7383e74b3f83172f9b8e0eaf9bd09d4e187e87f8
ms.sourcegitcommit: 6c28926a1e35e392b198a8729fc13c1c1968a27b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71813487"
---
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a>SQL Server EF Core 資料庫提供者中的記憶體優化資料表支援

[記憶體優化資料表](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/memory-optimized-tables)是 SQL Server 的一項功能，其中整個資料表都位於記憶體中。 資料表資料的第二份複本會保留在磁片上，但僅適用于持久性用途。 記憶體優化資料表中的資料只會在資料庫復原期間從磁片讀取。 例如，在伺服器重新開機之後。

## <a name="configuring-a-memory-optimized-table"></a>設定記憶體優化資料表

您可以將實體所對應的資料表指定為記憶體最佳化。 根據您的模型使用 EF Core 來建立及維護資料庫 (透過移轉或 `Database.EnsureCreated()`)，將為這些實體建立記憶體最佳化資料表。

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .ForSqlServerIsMemoryOptimized();
}
```
