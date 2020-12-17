---
title: Azure Cosmos DB 提供者-限制-EF Core
description: 與其他提供者相較之下，Entity Framework Core Azure Cosmos DB 提供者的限制
author: AndriySvyryd
ms.date: 11/05/2020
uid: core/providers/cosmos/limitations
ms.openlocfilehash: 088f593dddd9b5691d87566d7e31a699ba90d7c5
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635714"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a>EF Core Azure Cosmos DB 提供者限制

Cosmos 提供者有一些限制。 其中有許多限制是基礎 Cosmos 資料庫引擎中的限制，而且並非 EF 特有的結果。 但大部分都尚未 [實行](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc)。

以下是一些常見的要求功能：

- [`Include` 支援](https://github.com/dotnet/efcore/issues/16920)
- [`Join` 支援](https://github.com/dotnet/efcore/issues/17314)
- [基本類型的集合支援](https://github.com/dotnet/efcore/issues/14762)

## <a name="azure-cosmos-db-sdk-limitations"></a>Azure Cosmos DB SDK 限制

- 僅提供非同步方法

> [!WARNING]
> 由於沒有 EF Core 依賴的低層級方法的同步版本，因此對應的功能目前是藉由 `.Wait()` 在傳回的上呼叫來實作為 `Task` 。 這表示，使用類似 `SaveChanges` 或 `ToList` 非其非同步對應的方法，可能會導致您的應用程式發生鎖死

## <a name="azure-cosmos-db-limitations"></a>Azure Cosmos DB 限制

您可以查看 [Azure Cosmos DB 支援功能](/azure/cosmos-db/modeling-data)的完整總覽，相較于關係資料庫，這些是最顯著的差異：

- 不支援用戶端起始的交易
- 某些跨資料分割的查詢會根據 (的相關運算子（例如 `Skip/Take` 或 `OFFSET LIMIT`) ）而變慢。
