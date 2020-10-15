---
title: Azure Cosmos DB 提供者-限制-EF Core
description: 與其他提供者相較之下，Entity Framework Core Azure Cosmos DB 提供者的限制
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/limitations
ms.openlocfilehash: e4d1c38da14d1e722797f8543313a69c7fb088cc
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064033"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a><span data-ttu-id="a34dc-103">EF Core Azure Cosmos DB 提供者限制</span><span class="sxs-lookup"><span data-stu-id="a34dc-103">EF Core Azure Cosmos DB Provider Limitations</span></span>

<span data-ttu-id="a34dc-104">Cosmos 提供者有一些限制。</span><span class="sxs-lookup"><span data-stu-id="a34dc-104">The Cosmos provider has a number of limitations.</span></span> <span data-ttu-id="a34dc-105">其中有許多限制是基礎 Cosmos 資料庫引擎中的限制，而且並非 EF 特有的結果。</span><span class="sxs-lookup"><span data-stu-id="a34dc-105">Many of these limitations are a result of limitations in the underlying Cosmos database engine and are not specific to EF.</span></span> <span data-ttu-id="a34dc-106">但大部分都尚未 [實行](https://github.com/aspnet/EntityFrameworkCore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc)。</span><span class="sxs-lookup"><span data-stu-id="a34dc-106">But most simply [haven't been implemented yet](https://github.com/aspnet/EntityFrameworkCore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span></span>

## <a name="temporary-limitations"></a><span data-ttu-id="a34dc-107">暫時限制</span><span class="sxs-lookup"><span data-stu-id="a34dc-107">Temporary limitations</span></span>

- <span data-ttu-id="a34dc-108">即使只有一個實體型別沒有對應至容器的繼承，它仍有鑒別子屬性。</span><span class="sxs-lookup"><span data-stu-id="a34dc-108">Even if there is only one entity type without inheritance mapped to a container it still has a discriminator property.</span></span>
- <span data-ttu-id="a34dc-109">在某些案例中，具有分割區索引鍵的實體類型無法正常運作</span><span class="sxs-lookup"><span data-stu-id="a34dc-109">Entity types with partition keys don't work correctly in some scenarios</span></span>
- <span data-ttu-id="a34dc-110">`Include` 不支援呼叫</span><span class="sxs-lookup"><span data-stu-id="a34dc-110">`Include` calls are not supported</span></span>
- <span data-ttu-id="a34dc-111">`Join` 不支援呼叫</span><span class="sxs-lookup"><span data-stu-id="a34dc-111">`Join` calls are not supported</span></span>

## <a name="azure-cosmos-db-sdk-limitations"></a><span data-ttu-id="a34dc-112">Azure Cosmos DB SDK 限制</span><span class="sxs-lookup"><span data-stu-id="a34dc-112">Azure Cosmos DB SDK limitations</span></span>

- <span data-ttu-id="a34dc-113">僅提供非同步方法</span><span class="sxs-lookup"><span data-stu-id="a34dc-113">Only async methods are provided</span></span>

> [!WARNING]
> <span data-ttu-id="a34dc-114">由於沒有 EF Core 依賴的低層級方法的同步版本，因此對應的功能目前是藉由 `.Wait()` 在傳回的上呼叫來實作為 `Task` 。</span><span class="sxs-lookup"><span data-stu-id="a34dc-114">Since there are no sync versions of the low level methods EF Core relies on, the corresponding functionality is currently implemented by calling `.Wait()` on the returned `Task`.</span></span> <span data-ttu-id="a34dc-115">這表示，使用類似 `SaveChanges` 或 `ToList` 非其非同步對應的方法，可能會導致您的應用程式發生鎖死</span><span class="sxs-lookup"><span data-stu-id="a34dc-115">This means that using methods like `SaveChanges`, or `ToList` instead of their async counterparts could lead to a deadlock in your application</span></span>

## <a name="azure-cosmos-db-limitations"></a><span data-ttu-id="a34dc-116">Azure Cosmos DB 限制</span><span class="sxs-lookup"><span data-stu-id="a34dc-116">Azure Cosmos DB limitations</span></span>

<span data-ttu-id="a34dc-117">您可以查看 [Azure Cosmos DB 支援功能](/azure/cosmos-db/modeling-data)的完整總覽，相較于關係資料庫，這些是最顯著的差異：</span><span class="sxs-lookup"><span data-stu-id="a34dc-117">You can see the full overview of [Azure Cosmos DB supported features](/azure/cosmos-db/modeling-data), these are the most notable differences compared to a relational database:</span></span>

- <span data-ttu-id="a34dc-118">不支援用戶端起始的交易</span><span class="sxs-lookup"><span data-stu-id="a34dc-118">Client-initiated transactions are not supported</span></span>
- <span data-ttu-id="a34dc-119">某些跨分割區查詢不受支援，或根據相關運算子而變得很慢</span><span class="sxs-lookup"><span data-stu-id="a34dc-119">Some cross-partition queries are either not supported or much slower depending on the operators involved</span></span>
