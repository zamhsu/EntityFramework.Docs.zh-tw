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
# <a name="specifying-azure-sql-database-options"></a><span data-ttu-id="26f97-103">指定 Azure SQL Database 選項</span><span class="sxs-lookup"><span data-stu-id="26f97-103">Specifying Azure SQL Database Options</span></span>

>[!NOTE]
> <span data-ttu-id="26f97-104">這個 API 是 EF Core 3.1 中的新功能。</span><span class="sxs-lookup"><span data-stu-id="26f97-104">This API is new in EF Core 3.1.</span></span>

<span data-ttu-id="26f97-105">Azure SQL Database 提供[各種定價選項](https://azure.microsoft.com/pricing/details/sql-database/single/)，通常是透過 Azure 入口網站進行設定。</span><span class="sxs-lookup"><span data-stu-id="26f97-105">Azure SQL Database provides [a variety of pricing options](https://azure.microsoft.com/pricing/details/sql-database/single/) that are usually configured through the Azure Portal.</span></span> <span data-ttu-id="26f97-106">不過，如果您使用[EF Core 遷移](xref:core/managing-schemas/migrations/index)來管理架構，您可以在模型本身中指定所需的選項。</span><span class="sxs-lookup"><span data-stu-id="26f97-106">However if you are managing the schema using [EF Core migrations](xref:core/managing-schemas/migrations/index) you can specify the desired options in the model itself.</span></span>

<span data-ttu-id="26f97-107">您可以使用[HasServiceTier](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasServiceTier)來指定資料庫的服務層（版本）：</span><span class="sxs-lookup"><span data-stu-id="26f97-107">You can specify the service tier of the database (EDITION) using [HasServiceTier](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasServiceTier):</span></span>

[!code-csharp[HasServiceTier](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasServiceTier)]

<span data-ttu-id="26f97-108">您可以使用[HasDatabaseMaxSize](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasDatabaseMaxSize)來指定資料庫的大小上限：</span><span class="sxs-lookup"><span data-stu-id="26f97-108">You can specify the maximum size of the database using [HasDatabaseMaxSize](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasDatabaseMaxSize):</span></span>

[!code-csharp[HasDatabaseMaxSize](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasDatabaseMaxSize)]

<span data-ttu-id="26f97-109">您可以使用[HasPerformanceLevel](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevel)指定資料庫（SERVICE_OBJECTIVE）的效能層級：</span><span class="sxs-lookup"><span data-stu-id="26f97-109">You can specify the performance level of the database (SERVICE_OBJECTIVE) using [HasPerformanceLevel](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevel):</span></span>

[!code-csharp[HasPerformanceLevel](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasPerformanceLevel)]

<span data-ttu-id="26f97-110">使用[HasPerformanceLevelSql](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevelSql)來設定彈性集區，因為該值不是字串常值：</span><span class="sxs-lookup"><span data-stu-id="26f97-110">Use [HasPerformanceLevelSql](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevelSql) to configure the elastic pool, since the value is not a string literal:</span></span>

[!code-csharp[HasPerformanceLevel](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasPerformanceLevelSql)]


>[!TIP]
> <span data-ttu-id="26f97-111">您可以在[ALTER DATABASE 檔](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current)中找到所有支援的值。</span><span class="sxs-lookup"><span data-stu-id="26f97-111">You can find all the supported values in the [ALTER DATABASE documentation](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current).</span></span>