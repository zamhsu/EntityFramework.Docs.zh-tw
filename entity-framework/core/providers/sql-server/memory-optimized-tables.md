---
title: Microsoft SQL Server 資料庫提供者-Memory-Optimized 資料表-EF Core
description: 如何搭配 SQL Server Entity Framework Core 資料庫提供者使用 Memory-Optimized 資料表
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/memory-optimized-tables
ms.openlocfilehash: 42b2a2ffafb2234e1ce7a6d0844234509a4b5b94
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063916"
---
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a>SQL Server EF Core 資料庫提供者中的 Memory-Optimized 資料表支援

[記憶體優化資料表](/sql/relational-databases/in-memory-oltp/memory-optimized-tables) 是 SQL Server 的功能，其中整個資料表都位於記憶體中。 資料表資料的第二個副本保留在磁碟上，但僅做為持久性用途。 記憶體最佳化資料表中的資料只會在資料庫復原時從磁碟中讀取。 例如，伺服器重新啟動後。

## <a name="configuring-a-memory-optimized-table"></a>設定記憶體優化資料表

您可以將實體所對應的資料表指定為記憶體最佳化。 使用 EF Core 根據您的模型建立和維護資料庫時 (使用 [遷移](xref:core/managing-schemas/migrations/index) 或 [EnsureCreated](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreated)) ，將會為這些實體建立記憶體優化資料表。

[!code-csharp[IsMemoryOptimized](../../../../samples/core/SqlServer/InMemory/InMemoryContext.cs?name=IsMemoryOptimized)]
