---
title: Microsoft SQL Server 資料庫提供者-Azure SQL Database 選項-EF Core
description: 如何使用 SQL Server Entity Framework Core 資料庫提供者來指定 Azure SQL Database 的服務層級和效能等級
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/azure-sql-database
ms.openlocfilehash: c4f7b91110a0e700ed06130661e611cf45bee05f
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824782"
---
# <a name="specifying-azure-sql-database-options"></a>指定 Azure SQL Database 選項

>[!NOTE]
> 這個 API 是 EF Core 3.1 中的新功能。

Azure SQL Database 提供[各種定價選項](https://azure.microsoft.com/pricing/details/sql-database/single/)，通常是透過 Azure 入口網站進行設定。 不過，如果您使用[EF Core 遷移](xref:core/managing-schemas/migrations/index)來管理架構，您可以在模型本身中指定所需的選項。

您可以使用[HasServiceTier](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasServiceTier)來指定資料庫的服務層（版本）：

[!code-csharp[HasServiceTier](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasServiceTier)]

您可以使用[HasDatabaseMaxSize](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasDatabaseMaxSize)來指定資料庫的大小上限：

[!code-csharp[HasDatabaseMaxSize](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasDatabaseMaxSize)]

您可以使用[HasPerformanceLevel](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevel)指定資料庫（SERVICE_OBJECTIVE）的效能層級：

[!code-csharp[HasPerformanceLevel](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasPerformanceLevel)]

使用[HasPerformanceLevelSql](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevelSql)來設定彈性集區，因為該值不是字串常值：

[!code-csharp[HasPerformanceLevel](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasPerformanceLevelSql)]


>[!TIP]
> 您可以在[ALTER DATABASE 檔](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current)中找到所有支援的值。