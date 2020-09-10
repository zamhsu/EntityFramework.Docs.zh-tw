---
title: 自訂遷移歷程記錄資料表-EF Core
description: 自訂歷程記錄資料表，以用於搭配 Entity Framework Core 的遷移
author: bricelam
ms.author: bricelam
ms.date: 11/07/2017
uid: core/managing-schemas/migrations/history-table
ms.openlocfilehash: deed86b2023557036eb14ca5a66f04ab5bb4139f
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617983"
---
# <a name="custom-migrations-history-table"></a><span data-ttu-id="cb069-103">自訂遷移歷程記錄資料表</span><span class="sxs-lookup"><span data-stu-id="cb069-103">Custom Migrations History Table</span></span>

<span data-ttu-id="cb069-104">根據預設，EF Core 會在名為的資料表中記錄，以追蹤已套用至資料庫的遷移 `__EFMigrationsHistory` 。</span><span class="sxs-lookup"><span data-stu-id="cb069-104">By default, EF Core keeps track of which migrations have been applied to the database by recording them in a table named `__EFMigrationsHistory`.</span></span> <span data-ttu-id="cb069-105">基於各種原因，您可能會想要自訂此資料表，以更符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="cb069-105">For various reasons, you may want to customize this table to better suit your needs.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="cb069-106">如果您在套用遷移 *之後* 自訂遷移歷程記錄資料表，您必須負責更新資料庫中的現有資料表。</span><span class="sxs-lookup"><span data-stu-id="cb069-106">If you customize the Migrations history table *after* applying migrations, you are responsible for updating the existing table in the database.</span></span>

## <a name="schema-and-table-name"></a><span data-ttu-id="cb069-107">架構和資料表名稱</span><span class="sxs-lookup"><span data-stu-id="cb069-107">Schema and table name</span></span>

<span data-ttu-id="cb069-108">您可以使用 `MigrationsHistoryTable()` (中的方法 `OnConfiguring()` 或 ASP.NET Core) 來變更架構和資料表名稱 `ConfigureServices()` 。</span><span class="sxs-lookup"><span data-stu-id="cb069-108">You can change the schema and table name using the `MigrationsHistoryTable()` method in `OnConfiguring()` (or `ConfigureServices()` on ASP.NET Core).</span></span> <span data-ttu-id="cb069-109">以下是使用 SQL Server EF Core 提供者的範例。</span><span class="sxs-lookup"><span data-stu-id="cb069-109">Here is an example using the SQL Server EF Core provider.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MigrationTableNameContext.cs#TableNameContext)]

## <a name="other-changes"></a><span data-ttu-id="cb069-110">其他變更</span><span class="sxs-lookup"><span data-stu-id="cb069-110">Other changes</span></span>

<span data-ttu-id="cb069-111">若要設定資料表的其他層面，請覆寫並取代提供者特定的 `IHistoryRepository` 服務。</span><span class="sxs-lookup"><span data-stu-id="cb069-111">To configure additional aspects of the table, override and replace the provider-specific `IHistoryRepository` service.</span></span> <span data-ttu-id="cb069-112">以下是在 SQL Server 上將 MigrationId 資料行名稱變更為 *Id* 的範例。</span><span class="sxs-lookup"><span data-stu-id="cb069-112">Here is an example of changing the MigrationId column name to *Id* on SQL Server.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepositoryContext)]

> [!WARNING]
> <span data-ttu-id="cb069-113">`SqlServerHistoryRepository` 位於內部命名空間內部，未來的版本可能會變更。</span><span class="sxs-lookup"><span data-stu-id="cb069-113">`SqlServerHistoryRepository` is inside an internal namespace and may change in future releases.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepository)]
