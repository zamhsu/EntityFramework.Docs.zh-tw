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
# <a name="custom-migrations-history-table"></a>自訂遷移歷程記錄資料表

根據預設，EF Core 會在名為的資料表中記錄，以追蹤已套用至資料庫的遷移 `__EFMigrationsHistory` 。 基於各種原因，您可能會想要自訂此資料表，以更符合您的需求。

> [!IMPORTANT]
> 如果您在套用遷移 *之後* 自訂遷移歷程記錄資料表，您必須負責更新資料庫中的現有資料表。

## <a name="schema-and-table-name"></a>架構和資料表名稱

您可以使用 `MigrationsHistoryTable()` (中的方法 `OnConfiguring()` 或 ASP.NET Core) 來變更架構和資料表名稱 `ConfigureServices()` 。 以下是使用 SQL Server EF Core 提供者的範例。

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MigrationTableNameContext.cs#TableNameContext)]

## <a name="other-changes"></a>其他變更

若要設定資料表的其他層面，請覆寫並取代提供者特定的 `IHistoryRepository` 服務。 以下是在 SQL Server 上將 MigrationId 資料行名稱變更為 *Id* 的範例。

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepositoryContext)]

> [!WARNING]
> `SqlServerHistoryRepository` 位於內部命名空間內部，未來的版本可能會變更。

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepository)]
