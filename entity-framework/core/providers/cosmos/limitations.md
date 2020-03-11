---
title: Azure Cosmos DB 提供者-限制-EF Core
description: Entity Framework Core Azure Cosmos DB 提供者的限制
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/limitations
ms.openlocfilehash: 2631526b152d6ddcacf25173c8d51e4e3cb24500
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417206"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a><span data-ttu-id="b90dc-103">EF Core Azure Cosmos DB 提供者限制</span><span class="sxs-lookup"><span data-stu-id="b90dc-103">EF Core Azure Cosmos DB Provider Limitations</span></span>

<span data-ttu-id="b90dc-104">Cosmos 提供者有一些限制。</span><span class="sxs-lookup"><span data-stu-id="b90dc-104">The Cosmos provider has a number of limitations.</span></span> <span data-ttu-id="b90dc-105">其中有許多限制是基礎 Cosmos 資料庫引擎限制的結果，而不是 EF 特有的。</span><span class="sxs-lookup"><span data-stu-id="b90dc-105">Many of these limitations are a result of limitations in the underlying Cosmos database engine and are not specific to EF.</span></span> <span data-ttu-id="b90dc-106">但最重要的是尚未[實行](https://github.com/aspnet/EntityFrameworkCore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc)。</span><span class="sxs-lookup"><span data-stu-id="b90dc-106">But most simply [haven't been implemented yet](https://github.com/aspnet/EntityFrameworkCore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span></span>

## <a name="temporary-limitations"></a><span data-ttu-id="b90dc-107">暫時限制</span><span class="sxs-lookup"><span data-stu-id="b90dc-107">Temporary limitations</span></span>

- <span data-ttu-id="b90dc-108">即使只有一個實體類型沒有對應至容器的繼承，它仍然具有鑒別子屬性。</span><span class="sxs-lookup"><span data-stu-id="b90dc-108">Even if there is only one entity type without inheritance mapped to a container it still has a discriminator property.</span></span>
- <span data-ttu-id="b90dc-109">具有資料分割索引鍵的實體類型在某些情況下無法正常運作</span><span class="sxs-lookup"><span data-stu-id="b90dc-109">Entity types with partition keys don't work correctly in some scenarios</span></span>
- <span data-ttu-id="b90dc-110">不支援 `Include` 呼叫</span><span class="sxs-lookup"><span data-stu-id="b90dc-110">`Include` calls are not supported</span></span>
- <span data-ttu-id="b90dc-111">不支援 `Join` 呼叫</span><span class="sxs-lookup"><span data-stu-id="b90dc-111">`Join` calls are not supported</span></span>

## <a name="azure-cosmos-db-sdk-limitations"></a><span data-ttu-id="b90dc-112">Azure Cosmos DB SDK 限制</span><span class="sxs-lookup"><span data-stu-id="b90dc-112">Azure Cosmos DB SDK limitations</span></span>

- <span data-ttu-id="b90dc-113">僅提供非同步方法</span><span class="sxs-lookup"><span data-stu-id="b90dc-113">Only async methods are provided</span></span>

> [!WARNING]
> <span data-ttu-id="b90dc-114">由於 EF Core 依賴的低層級方法沒有同步處理版本，因此對應的功能目前是藉由在傳回的 `Task`上呼叫 `.Wait()` 來執行。</span><span class="sxs-lookup"><span data-stu-id="b90dc-114">Since there are no sync versions of the low level methods EF Core relies on, the corresponding functionality is currently implemented by calling `.Wait()` on the returned `Task`.</span></span> <span data-ttu-id="b90dc-115">這表示使用 `SaveChanges`之類的方法，或是 `ToList` 而不是其非同步對應專案，可能會導致應用程式發生鎖死</span><span class="sxs-lookup"><span data-stu-id="b90dc-115">This means that using methods like `SaveChanges`, or `ToList` instead of their async counterparts could lead to a deadlock in your application</span></span>

## <a name="azure-cosmos-db-limitations"></a><span data-ttu-id="b90dc-116">Azure Cosmos DB 限制</span><span class="sxs-lookup"><span data-stu-id="b90dc-116">Azure Cosmos DB limitations</span></span>

<span data-ttu-id="b90dc-117">與關係資料庫相較之下，您可以看到[Azure Cosmos DB 支援功能](/azure/cosmos-db/modeling-data)的完整總覽，這些是最顯著的差異：</span><span class="sxs-lookup"><span data-stu-id="b90dc-117">You can see the full overview of [Azure Cosmos DB supported features](/azure/cosmos-db/modeling-data), these are the most notable differences compared to a relational database:</span></span>

- <span data-ttu-id="b90dc-118">不支援用戶端起始的交易</span><span class="sxs-lookup"><span data-stu-id="b90dc-118">Client-initiated transactions are not supported</span></span>
- <span data-ttu-id="b90dc-119">某些跨分割區查詢不受支援或速度較慢，視相關運算子而定</span><span class="sxs-lookup"><span data-stu-id="b90dc-119">Some cross-partition queries are either not supported or much slower depending on the operators involved</span></span>
