---
title: Microsoft SQL Server 資料庫提供者的記憶體最佳化資料表的 EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 2e007c82-c6e4-45bb-8129-851b79ec1a0a
ms.technology: entity-framework-core
uid: core/providers/sql-server/memory-optimized-tables
ms.openlocfilehash: 83a0decffc5946d036903b8b8add59f0ea31b21f
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052638"
---
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a><span data-ttu-id="f5f59-102">SQL Server EF Core資料庫提供者的記憶體最佳化資料表支援</span><span class="sxs-lookup"><span data-stu-id="f5f59-102">Memory-Optimized Tables support in SQL Server EF Core Database Provider</span></span>

> [!NOTE]  
>
> <span data-ttu-id="f5f59-103">在 EF Core 1.1 已引入此功能。</span><span class="sxs-lookup"><span data-stu-id="f5f59-103">This feature was introduced in EF Core 1.1.</span></span>

<span data-ttu-id="f5f59-104">[記憶體最佳化資料表](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/memory-optimized-tables)是整個資料表在記憶體中的所在位置的 SQL Server 的功能。</span><span class="sxs-lookup"><span data-stu-id="f5f59-104">[Memory-Optimized Tables](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/memory-optimized-tables) are a feature of SQL Server where the entire table resides in memory.</span></span> <span data-ttu-id="f5f59-105">資料表資料的第二個副本保留在磁碟上，但僅做為持久性用途。</span><span class="sxs-lookup"><span data-stu-id="f5f59-105">A second copy of the table data is maintained on disk, but only for durability purposes.</span></span> <span data-ttu-id="f5f59-106">記憶體最佳化資料表中的資料只會在資料庫復原時從磁碟中讀取。</span><span class="sxs-lookup"><span data-stu-id="f5f59-106">Data in memory-optimized tables is only read from disk during database recovery.</span></span> <span data-ttu-id="f5f59-107">例如，伺服器重新啟動後。</span><span class="sxs-lookup"><span data-stu-id="f5f59-107">For example, after a server restart.</span></span>

## <a name="configuring-a-memory-optimized-table"></a><span data-ttu-id="f5f59-108">設定記憶體最佳化資料表</span><span class="sxs-lookup"><span data-stu-id="f5f59-108">Configuring a memory-optimized table</span></span>

<span data-ttu-id="f5f59-109">您可以指定實體對應到資料表是記憶體最佳化。</span><span class="sxs-lookup"><span data-stu-id="f5f59-109">You can specify that the table an entity is mapped to is memory-optimized.</span></span> <span data-ttu-id="f5f59-110">當使用來建立並維護資料庫的 EF Core 根據您的模型 (不論是透過移轉或`Database.EnsureCreated()`)，記憶體最佳化資料表將會建立針對這些實體。</span><span class="sxs-lookup"><span data-stu-id="f5f59-110">When using EF Core to create and maintain a database based on your model (either with migrations or `Database.EnsureCreated()`), a memory-optimized table will be created for these entities.</span></span>

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .ForSqlServerIsMemoryOptimized();
}
```
