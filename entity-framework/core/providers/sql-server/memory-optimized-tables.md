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
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a>SQL Server EF Core 資料庫提供者的記憶體最佳化資料表支援

> [!NOTE]  
>
> 在 EF Core 1.1 已引入此功能。

[記憶體最佳化資料表](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/memory-optimized-tables)是整個資料表在記憶體中的所在位置的 SQL Server 的功能。 資料表資料的第二個副本保留在磁碟上，但僅做為持久性用途。 記憶體最佳化資料表中的資料只會在資料庫復原時從磁碟中讀取。 例如，伺服器重新啟動後。

## <a name="configuring-a-memory-optimized-table"></a>設定記憶體最佳化資料表

您可以指定實體對應到資料表是記憶體最佳化。 當使用來建立並維護資料庫的 EF Core 根據您的模型 (不論是透過移轉或`Database.EnsureCreated()`)，記憶體最佳化資料表將會建立針對這些實體。

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .ForSqlServerIsMemoryOptimized();
}
```
