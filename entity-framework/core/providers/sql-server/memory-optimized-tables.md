---
title: Microsoft SQL Server 資料庫提供者-記憶體優化資料表-EF Core
description: 如何搭配 SQL Server Entity Framework Core 資料庫提供者使用記憶體優化資料表
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/memory-optimized-tables
ms.openlocfilehash: a504fb3487aea6dd36abf204a7427095e3d29118
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824644"
---
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a><span data-ttu-id="27d20-103">SQL Server EF Core 資料庫提供者中的記憶體優化資料表支援</span><span class="sxs-lookup"><span data-stu-id="27d20-103">Memory-Optimized Tables support in SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="27d20-104">[記憶體優化資料表](/sql/relational-databases/in-memory-oltp/memory-optimized-tables)是 SQL Server 的一項功能，其中整個資料表都位於記憶體中。</span><span class="sxs-lookup"><span data-stu-id="27d20-104">[Memory-Optimized Tables](/sql/relational-databases/in-memory-oltp/memory-optimized-tables) are a feature of SQL Server where the entire table resides in memory.</span></span> <span data-ttu-id="27d20-105">資料表資料的第二個副本保留在磁碟上，但僅做為持久性用途。</span><span class="sxs-lookup"><span data-stu-id="27d20-105">A second copy of the table data is maintained on disk, but only for durability purposes.</span></span> <span data-ttu-id="27d20-106">記憶體最佳化資料表中的資料只會在資料庫復原時從磁碟中讀取。</span><span class="sxs-lookup"><span data-stu-id="27d20-106">Data in memory-optimized tables is only read from disk during database recovery.</span></span> <span data-ttu-id="27d20-107">例如，伺服器重新啟動後。</span><span class="sxs-lookup"><span data-stu-id="27d20-107">For example, after a server restart.</span></span>

## <a name="configuring-a-memory-optimized-table"></a><span data-ttu-id="27d20-108">設定記憶體優化資料表</span><span class="sxs-lookup"><span data-stu-id="27d20-108">Configuring a memory-optimized table</span></span>

<span data-ttu-id="27d20-109">您可以將實體所對應的資料表指定為記憶體最佳化。</span><span class="sxs-lookup"><span data-stu-id="27d20-109">You can specify that the table an entity is mapped to is memory-optimized.</span></span> <span data-ttu-id="27d20-110">當您使用 EF Core 根據您的模型建立和維護資料庫時（不論是透過[遷移](xref:core/managing-schemas/migrations/index)或[EnsureCreated](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreated)），將會為這些實體建立記憶體優化資料表。</span><span class="sxs-lookup"><span data-stu-id="27d20-110">When using EF Core to create and maintain a database based on your model (either with [migrations](xref:core/managing-schemas/migrations/index) or [EnsureCreated](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreated)), a memory-optimized table will be created for these entities.</span></span>

[!code-csharp[IsMemoryOptimized](../../../../samples/core/SqlServer/InMemory/InMemoryContext.cs?name=IsMemoryOptimized)]
