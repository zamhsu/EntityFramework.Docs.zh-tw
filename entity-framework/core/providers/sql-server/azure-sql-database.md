---
title: Microsoft SQL Server 資料庫提供者-Azure SQL Database 選項-EF Core
description: 如何使用 SQL Server Entity Framework Core 資料庫提供者來指定 Azure SQL Database 的服務層級和效能層級
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/azure-sql-database
ms.openlocfilehash: ad202336c2c2efdfe17776952f2a65e98222ecc0
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003584"
---
# <a name="specifying-azure-sql-database-options"></a><span data-ttu-id="c5fe9-103">指定 Azure SQL Database 選項</span><span class="sxs-lookup"><span data-stu-id="c5fe9-103">Specifying Azure SQL Database Options</span></span>

>[!NOTE]
> <span data-ttu-id="c5fe9-104">此 API 是在 EF Core 3.1 中引進。</span><span class="sxs-lookup"><span data-stu-id="c5fe9-104">This API was introduced in EF Core 3.1.</span></span>

<span data-ttu-id="c5fe9-105">Azure SQL Database 提供了通常透過 Azure 入口網站設定的 [各種定價選項](https://azure.microsoft.com/pricing/details/sql-database/single/) 。</span><span class="sxs-lookup"><span data-stu-id="c5fe9-105">Azure SQL Database provides [a variety of pricing options](https://azure.microsoft.com/pricing/details/sql-database/single/) that are usually configured through the Azure Portal.</span></span> <span data-ttu-id="c5fe9-106">但是，如果您使用 [EF Core 的遷移](xref:core/managing-schemas/migrations/index) 來管理架構，您可以在模型本身中指定所需的選項。</span><span class="sxs-lookup"><span data-stu-id="c5fe9-106">However if you are managing the schema using [EF Core migrations](xref:core/managing-schemas/migrations/index) you can specify the desired options in the model itself.</span></span>

<span data-ttu-id="c5fe9-107">您可以使用 [HasServiceTier](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasServiceTier)來指定資料庫 (版本) 的服務層級：</span><span class="sxs-lookup"><span data-stu-id="c5fe9-107">You can specify the service tier of the database (EDITION) using [HasServiceTier](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasServiceTier):</span></span>

[!code-csharp[HasServiceTier](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasServiceTier)]

<span data-ttu-id="c5fe9-108">您可以使用 [HasDatabaseMaxSize](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasDatabaseMaxSize)來指定資料庫的大小上限：</span><span class="sxs-lookup"><span data-stu-id="c5fe9-108">You can specify the maximum size of the database using [HasDatabaseMaxSize](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasDatabaseMaxSize):</span></span>

[!code-csharp[HasDatabaseMaxSize](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasDatabaseMaxSize)]

<span data-ttu-id="c5fe9-109">您可以使用 [HasPerformanceLevel](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevel)來指定資料庫的效能層級 (SERVICE_OBJECTIVE) ：</span><span class="sxs-lookup"><span data-stu-id="c5fe9-109">You can specify the performance level of the database (SERVICE_OBJECTIVE) using [HasPerformanceLevel](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevel):</span></span>

[!code-csharp[HasPerformanceLevel](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasPerformanceLevel)]

<span data-ttu-id="c5fe9-110">使用 [HasPerformanceLevelSql](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevelSql) 設定彈性集區，因為此值不是字串常值：</span><span class="sxs-lookup"><span data-stu-id="c5fe9-110">Use [HasPerformanceLevelSql](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevelSql) to configure the elastic pool, since the value is not a string literal:</span></span>

[!code-csharp[HasPerformanceLevel](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasPerformanceLevelSql)]

>[!TIP]
> <span data-ttu-id="c5fe9-111">您可以在 [ALTER DATABASE 檔](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true)中找到所有支援的值。</span><span class="sxs-lookup"><span data-stu-id="c5fe9-111">You can find all the supported values in the [ALTER DATABASE documentation](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true).</span></span>
