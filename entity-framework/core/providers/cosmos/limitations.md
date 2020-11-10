---
title: Azure Cosmos DB 提供者-限制-EF Core
description: 與其他提供者相較之下，Entity Framework Core Azure Cosmos DB 提供者的限制
author: AndriySvyryd
ms.date: 11/05/2020
uid: core/providers/cosmos/limitations
ms.openlocfilehash: f7517954b854c8ea1020c039e490b4fb0dea7181
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430192"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a><span data-ttu-id="1f015-103">EF Core Azure Cosmos DB 提供者限制</span><span class="sxs-lookup"><span data-stu-id="1f015-103">EF Core Azure Cosmos DB Provider Limitations</span></span>

<span data-ttu-id="1f015-104">Cosmos 提供者有一些限制。</span><span class="sxs-lookup"><span data-stu-id="1f015-104">The Cosmos provider has a number of limitations.</span></span> <span data-ttu-id="1f015-105">其中有許多限制是基礎 Cosmos 資料庫引擎中的限制，而且並非 EF 特有的結果。</span><span class="sxs-lookup"><span data-stu-id="1f015-105">Many of these limitations are a result of limitations in the underlying Cosmos database engine and are not specific to EF.</span></span> <span data-ttu-id="1f015-106">但大部分都尚未 [實行](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc)。</span><span class="sxs-lookup"><span data-stu-id="1f015-106">But most simply [haven't been implemented yet](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span></span>

## <a name="temporary-limitations"></a><span data-ttu-id="1f015-107">暫時限制</span><span class="sxs-lookup"><span data-stu-id="1f015-107">Temporary limitations</span></span>

- <span data-ttu-id="1f015-108">`Include` 不支援呼叫</span><span class="sxs-lookup"><span data-stu-id="1f015-108">`Include` calls are not supported</span></span>
- <span data-ttu-id="1f015-109">`Join` 不支援呼叫</span><span class="sxs-lookup"><span data-stu-id="1f015-109">`Join` calls are not supported</span></span>

## <a name="azure-cosmos-db-sdk-limitations"></a><span data-ttu-id="1f015-110">Azure Cosmos DB SDK 限制</span><span class="sxs-lookup"><span data-stu-id="1f015-110">Azure Cosmos DB SDK limitations</span></span>

- <span data-ttu-id="1f015-111">僅提供非同步方法</span><span class="sxs-lookup"><span data-stu-id="1f015-111">Only async methods are provided</span></span>

> [!WARNING]
> <span data-ttu-id="1f015-112">由於沒有 EF Core 依賴的低層級方法的同步版本，因此對應的功能目前是藉由 `.Wait()` 在傳回的上呼叫來實作為 `Task` 。</span><span class="sxs-lookup"><span data-stu-id="1f015-112">Since there are no sync versions of the low level methods EF Core relies on, the corresponding functionality is currently implemented by calling `.Wait()` on the returned `Task`.</span></span> <span data-ttu-id="1f015-113">這表示，使用類似 `SaveChanges` 或 `ToList` 非其非同步對應的方法，可能會導致您的應用程式發生鎖死</span><span class="sxs-lookup"><span data-stu-id="1f015-113">This means that using methods like `SaveChanges`, or `ToList` instead of their async counterparts could lead to a deadlock in your application</span></span>

## <a name="azure-cosmos-db-limitations"></a><span data-ttu-id="1f015-114">Azure Cosmos DB 限制</span><span class="sxs-lookup"><span data-stu-id="1f015-114">Azure Cosmos DB limitations</span></span>

<span data-ttu-id="1f015-115">您可以查看 [Azure Cosmos DB 支援功能](/azure/cosmos-db/modeling-data)的完整總覽，相較于關係資料庫，這些是最顯著的差異：</span><span class="sxs-lookup"><span data-stu-id="1f015-115">You can see the full overview of [Azure Cosmos DB supported features](/azure/cosmos-db/modeling-data), these are the most notable differences compared to a relational database:</span></span>

- <span data-ttu-id="1f015-116">不支援用戶端起始的交易</span><span class="sxs-lookup"><span data-stu-id="1f015-116">Client-initiated transactions are not supported</span></span>
- <span data-ttu-id="1f015-117">某些跨資料分割的查詢會根據 (的相關運算子（例如 `Skip/Take` 或 `OFFSET LIMIT`) ）而變慢。</span><span class="sxs-lookup"><span data-stu-id="1f015-117">Some cross-partition queries are slower depending on the operators involved (for example `Skip/Take` or `OFFSET LIMIT`)</span></span>
