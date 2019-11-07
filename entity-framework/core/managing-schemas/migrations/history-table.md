---
title: 自訂遷移歷程記錄資料表-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/07/2017
uid: core/managing-schemas/migrations/history-table
ms.openlocfilehash: 0007da7ce3d78fd8f17007ac79a395bb2e6efd86
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655710"
---
# <a name="custom-migrations-history-table"></a><span data-ttu-id="ad420-102">自訂遷移記錄資料表</span><span class="sxs-lookup"><span data-stu-id="ad420-102">Custom Migrations History Table</span></span>

<span data-ttu-id="ad420-103">根據預設，EF Core 會藉由在名為 `__EFMigrationsHistory`的資料表中記錄，來追蹤已套用至資料庫的遷移。</span><span class="sxs-lookup"><span data-stu-id="ad420-103">By default, EF Core keeps track of which migrations have been applied to the database by recording them in a table named `__EFMigrationsHistory`.</span></span> <span data-ttu-id="ad420-104">基於各種原因，您可能會想要自訂此資料表，使其更符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="ad420-104">For various reasons, you may want to customize this table to better suit your needs.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ad420-105">如果您在套用遷移*之後*自訂「遷移記錄」資料表，您就必須負責更新資料庫中的現有資料表。</span><span class="sxs-lookup"><span data-stu-id="ad420-105">If you customize the Migrations history table *after* applying migrations, you are responsible for updating the existing table in the database.</span></span>

## <a name="schema-and-table-name"></a><span data-ttu-id="ad420-106">架構和資料表名稱</span><span class="sxs-lookup"><span data-stu-id="ad420-106">Schema and table name</span></span>

<span data-ttu-id="ad420-107">您可以使用 `OnConfiguring()` 中的 `MigrationsHistoryTable()` 方法（或 ASP.NET Core 上的 `ConfigureServices()`）來變更架構和資料表名稱。</span><span class="sxs-lookup"><span data-stu-id="ad420-107">You can change the schema and table name using the `MigrationsHistoryTable()` method in `OnConfiguring()` (or `ConfigureServices()` on ASP.NET Core).</span></span> <span data-ttu-id="ad420-108">以下是使用 SQL Server EF Core 提供者的範例。</span><span class="sxs-lookup"><span data-stu-id="ad420-108">Here is an example using the SQL Server EF Core provider.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MigrationTableNameContext.cs#TableNameContext)]

## <a name="other-changes"></a><span data-ttu-id="ad420-109">其他變更</span><span class="sxs-lookup"><span data-stu-id="ad420-109">Other changes</span></span>

<span data-ttu-id="ad420-110">若要設定資料表的其他層面，請覆寫並取代提供者特定的 `IHistoryRepository` 服務。</span><span class="sxs-lookup"><span data-stu-id="ad420-110">To configure additional aspects of the table, override and replace the provider-specific `IHistoryRepository` service.</span></span> <span data-ttu-id="ad420-111">以下是將 SQL Server 上的 MigrationId 資料行名稱變更為*Id*的範例。</span><span class="sxs-lookup"><span data-stu-id="ad420-111">Here is an example of changing the MigrationId column name to *Id* on SQL Server.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepositoryContext)]

> [!WARNING]
> <span data-ttu-id="ad420-112">`SqlServerHistoryRepository` 位於內部命名空間內部，未來的版本可能會變更。</span><span class="sxs-lookup"><span data-stu-id="ad420-112">`SqlServerHistoryRepository` is inside an internal namespace and may change in future releases.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepository)]
