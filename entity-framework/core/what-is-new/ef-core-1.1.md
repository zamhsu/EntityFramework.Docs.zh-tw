---
title: EF Core 1.1 中的新增功能 - EF Core
author: divega
ms.date: 10/27/2016
ms.assetid: C7FE8C85-445A-4F0C-97EC-CC3F7F1D6F5E
uid: core/what-is-new/ef-core-1.1
ms.openlocfilehash: d582712ed62443318f4b9e209511fb2a557d667e
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656004"
---
# <a name="new-features-in-ef-core-11"></a>EF Core 1.1 中的新增功能

## <a name="modeling"></a>模型化

### <a name="field-mapping"></a>欄位對應

可讓您設定屬性的支援欄位。 對於唯讀屬性或具有 Get/Set 方法而非屬性的資料而言，這非常實用。

### <a name="mapping-to-memory-optimized-tables-in-sql-server"></a>對應到 SQL Server 中的記憶體最佳化資料表

您可以將實體所對應的資料表指定為記憶體最佳化。 根據您的模型使用 EF Core 來建立及維護資料庫 (透過移轉或 `Database.EnsureCreated()`)，將為這些實體建立記憶體最佳化資料表。

## <a name="change-tracking"></a>Change tracking

### <a name="additional-change-tracking-apis-from-ef6"></a>來自 EF6 的其他變更追蹤 API

例如 `Reload`、`GetModifiedProperties`、`GetDatabaseValues` 等。

## <a name="query"></a>查詢

### <a name="explicit-loading"></a>明確式載入

可讓您觸發先前從資料庫載入之實體上瀏覽屬性的母體擴展。

### <a name="dbsetfind"></a>DbSet.Find

提供根據實體主索引鍵值擷取實體的簡單方法。

## <a name="other"></a>其他

### <a name="connection-resiliency"></a>連線恢復功能

自動重試失敗的資料庫命令。 連線到其中的暫時性失敗為常見情況的 SQL Azure 時，這非常實用。

### <a name="simplified-service-replacement"></a>簡化的服務取代

讓您輕鬆取代 EF 所使用的服務。
