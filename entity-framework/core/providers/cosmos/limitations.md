---
title: Azure Cosmos DB 提供者-限制-EF Core
description: Entity Framework Core Azure Cosmos DB 提供者的限制
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/limitations
ms.openlocfilehash: 2631526b152d6ddcacf25173c8d51e4e3cb24500
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655978"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a>EF Core Azure Cosmos DB 提供者限制

Cosmos 提供者有一些限制。 其中有許多限制是基礎 Cosmos 資料庫引擎限制的結果，而不是 EF 特有的。 但最重要的是尚未[實行](https://github.com/aspnet/EntityFrameworkCore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc)。

## <a name="temporary-limitations"></a>暫時限制

- 即使只有一個實體類型沒有對應至容器的繼承，它仍然具有鑒別子屬性。
- 具有資料分割索引鍵的實體類型在某些情況下無法正常運作
- 不支援 `Include` 呼叫
- 不支援 `Join` 呼叫

## <a name="azure-cosmos-db-sdk-limitations"></a>Azure Cosmos DB SDK 限制

- 僅提供非同步方法

> [!WARNING]
> 由於 EF Core 依賴的低層級方法沒有同步處理版本，因此對應的功能目前是藉由在傳回的 `Task`上呼叫 `.Wait()` 來執行。 這表示使用 `SaveChanges`之類的方法，或是 `ToList` 而不是其非同步對應專案，可能會導致應用程式發生鎖死

## <a name="azure-cosmos-db-limitations"></a>Azure Cosmos DB 限制

與關係資料庫相較之下，您可以看到[Azure Cosmos DB 支援功能](/azure/cosmos-db/modeling-data)的完整總覽，這些是最顯著的差異：

- 不支援用戶端起始的交易
- 某些跨分割區查詢不受支援或速度較慢，視相關運算子而定
