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
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a>SQL Server EF Core 資料庫提供者中的記憶體優化資料表支援

[記憶體優化資料表](/sql/relational-databases/in-memory-oltp/memory-optimized-tables)是 SQL Server 的一項功能，其中整個資料表都位於記憶體中。 資料表資料的第二個副本保留在磁碟上，但僅做為持久性用途。 記憶體最佳化資料表中的資料只會在資料庫復原時從磁碟中讀取。 例如，伺服器重新啟動後。

## <a name="configuring-a-memory-optimized-table"></a>設定記憶體優化資料表

您可以將實體所對應的資料表指定為記憶體最佳化。 當您使用 EF Core 根據您的模型建立和維護資料庫時（不論是透過[遷移](xref:core/managing-schemas/migrations/index)或[EnsureCreated](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreated)），將會為這些實體建立記憶體優化資料表。

[!code-csharp[IsMemoryOptimized](../../../../samples/core/SqlServer/InMemory/InMemoryContext.cs?name=IsMemoryOptimized)]
