---
title: Azure Cosmos DB 提供者-限制-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 09/12/2019
ms.assetid: 9d02a2cd-484e-4687-b8a8-3748ba46dbc9
uid: core/providers/cosmos/limitations
ms.openlocfilehash: 8dcc82a68c89e21ad1902a0bbbce8ebbc3535801
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/20/2019
ms.locfileid: "71150806"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a>EF Core Azure Cosmos DB 提供者限制

Cosmos 提供者有一些限制。 其中有許多限制是基礎 Cosmos 資料庫引擎限制的結果，而不是 EF 特有的。 但最重要的是尚未[實行](https://github.com/aspnet/EntityFrameworkCore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc)。

## <a name="temporary-limitations"></a>暫時限制

- 即使只有一個實體類型沒有對應至容器的繼承，它仍然具有鑒別子屬性。
- 具有資料分割索引鍵的實體類型在某些情況下無法正常運作
- `Include`不支援呼叫
- `Join`不支援呼叫

## <a name="azure-cosmos-db-sdk-limitations"></a>Azure Cosmos DB SDK 限制

- 僅提供非同步方法

> [!WARNING]
> 由於 EF Core 依賴的低層級方法沒有同步處理版本，因此對應的功能目前是藉由在傳回`.Wait()` `Task`的上呼叫來執行。 這表示使用之類`SaveChanges`的方法，或`ToList`而不是其非同步對應項，可能會導致應用程式的鎖死

## <a name="azure-cosmos-db-limitations"></a>Azure Cosmos DB 限制

與關係資料庫相較之下，您可以看到[Azure Cosmos DB 支援功能](https://docs.microsoft.com/en-us/azure/cosmos-db/modeling-data)的完整總覽，這些是最顯著的差異：

- 不支援用戶端起始的交易
- 某些跨分割區查詢不受支援或速度較慢，視相關運算子而定