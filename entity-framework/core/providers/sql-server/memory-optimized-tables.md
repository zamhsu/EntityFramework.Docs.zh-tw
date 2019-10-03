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
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a><span data-ttu-id="0acd2-102">SQL Server EF Core 資料庫提供者中的記憶體優化資料表支援</span><span class="sxs-lookup"><span data-stu-id="0acd2-102">Memory-Optimized Tables support in SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="0acd2-103">[記憶體優化資料表](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/memory-optimized-tables)是 SQL Server 的一項功能，其中整個資料表都位於記憶體中。</span><span class="sxs-lookup"><span data-stu-id="0acd2-103">[Memory-Optimized Tables](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/memory-optimized-tables) are a feature of SQL Server where the entire table resides in memory.</span></span> <span data-ttu-id="0acd2-104">資料表資料的第二份複本會保留在磁片上，但僅適用于持久性用途。</span><span class="sxs-lookup"><span data-stu-id="0acd2-104">A second copy of the table data is maintained on disk, but only for durability purposes.</span></span> <span data-ttu-id="0acd2-105">記憶體優化資料表中的資料只會在資料庫復原期間從磁片讀取。</span><span class="sxs-lookup"><span data-stu-id="0acd2-105">Data in memory-optimized tables is only read from disk during database recovery.</span></span> <span data-ttu-id="0acd2-106">例如，在伺服器重新開機之後。</span><span class="sxs-lookup"><span data-stu-id="0acd2-106">For example, after a server restart.</span></span>

## <a name="configuring-a-memory-optimized-table"></a><span data-ttu-id="0acd2-107">設定記憶體優化資料表</span><span class="sxs-lookup"><span data-stu-id="0acd2-107">Configuring a memory-optimized table</span></span>

<span data-ttu-id="0acd2-108">您可以將實體所對應的資料表指定為記憶體最佳化。</span><span class="sxs-lookup"><span data-stu-id="0acd2-108">You can specify that the table an entity is mapped to is memory-optimized.</span></span> <span data-ttu-id="0acd2-109">根據您的模型使用 EF Core 來建立及維護資料庫 (透過移轉或 `Database.EnsureCreated()`)，將為這些實體建立記憶體最佳化資料表。</span><span class="sxs-lookup"><span data-stu-id="0acd2-109">When using EF Core to create and maintain a database based on your model (either with migrations or `Database.EnsureCreated()`), a memory-optimized table will be created for these entities.</span></span>

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .ForSqlServerIsMemoryOptimized();
}
```
