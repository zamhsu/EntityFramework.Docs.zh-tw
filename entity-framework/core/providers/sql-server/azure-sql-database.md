---
title: Microsoft SQL Server 資料庫提供者-Azure SQL Database 選項-EF Core
description: 如何使用 SQL Server Entity Framework Core 資料庫提供者來指定 Azure SQL Database 的服務層級和效能層級
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/azure-sql-database
ms.openlocfilehash: 9b2cc5e0790beb41295dcea6ae30c4ad15b537fa
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065364"
---
# <a name="specifying-azure-sql-database-options"></a>指定 Azure SQL Database 選項

>[!NOTE]
> 此 API 是 EF Core 3.1 中的新功能。

Azure SQL Database 提供了通常透過 Azure 入口網站設定的 [各種定價選項](https://azure.microsoft.com/pricing/details/sql-database/single/) 。 但是，如果您使用 [EF Core 的遷移](xref:core/managing-schemas/migrations/index) 來管理架構，您可以在模型本身中指定所需的選項。

您可以使用 [HasServiceTier](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasServiceTier)來指定資料庫 (版本) 的服務層級：

[!code-csharp[HasServiceTier](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasServiceTier)]

您可以使用 [HasDatabaseMaxSize](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasDatabaseMaxSize)來指定資料庫的大小上限：

[!code-csharp[HasDatabaseMaxSize](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasDatabaseMaxSize)]

您可以使用 [HasPerformanceLevel](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevel)來指定資料庫的效能層級 (SERVICE_OBJECTIVE) ：

[!code-csharp[HasPerformanceLevel](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasPerformanceLevel)]

使用 [HasPerformanceLevelSql](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevelSql) 設定彈性集區，因為此值不是字串常值：

[!code-csharp[HasPerformanceLevel](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasPerformanceLevelSql)]

>[!TIP]
> 您可以在 [ALTER DATABASE 檔](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true)中找到所有支援的值。
