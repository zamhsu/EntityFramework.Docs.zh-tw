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
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a><span data-ttu-id="2671d-102">EF Core Azure Cosmos DB 提供者限制</span><span class="sxs-lookup"><span data-stu-id="2671d-102">EF Core Azure Cosmos DB Provider Limitations</span></span>

<span data-ttu-id="2671d-103">Cosmos 提供者有一些限制。</span><span class="sxs-lookup"><span data-stu-id="2671d-103">The Cosmos provider has a number of limitations.</span></span> <span data-ttu-id="2671d-104">其中有許多限制是基礎 Cosmos 資料庫引擎限制的結果，而不是 EF 特有的。</span><span class="sxs-lookup"><span data-stu-id="2671d-104">Many of these limitations are a result of limitations in the underlying Cosmos database engine and are not specific to EF.</span></span> <span data-ttu-id="2671d-105">但最重要的是尚未[實行](https://github.com/aspnet/EntityFrameworkCore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc)。</span><span class="sxs-lookup"><span data-stu-id="2671d-105">But most simply [haven't been implemented yet](https://github.com/aspnet/EntityFrameworkCore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span></span>

## <a name="temporary-limitations"></a><span data-ttu-id="2671d-106">暫時限制</span><span class="sxs-lookup"><span data-stu-id="2671d-106">Temporary limitations</span></span>

- <span data-ttu-id="2671d-107">即使只有一個實體類型沒有對應至容器的繼承，它仍然具有鑒別子屬性。</span><span class="sxs-lookup"><span data-stu-id="2671d-107">Even if there is only one entity type without inheritance mapped to a container it still has a discriminator property.</span></span>
- <span data-ttu-id="2671d-108">具有資料分割索引鍵的實體類型在某些情況下無法正常運作</span><span class="sxs-lookup"><span data-stu-id="2671d-108">Entity types with partition keys don't work correctly in some scenarios</span></span>
- <span data-ttu-id="2671d-109">`Include`不支援呼叫</span><span class="sxs-lookup"><span data-stu-id="2671d-109">`Include` calls are not supported</span></span>
- <span data-ttu-id="2671d-110">`Join`不支援呼叫</span><span class="sxs-lookup"><span data-stu-id="2671d-110">`Join` calls are not supported</span></span>

## <a name="azure-cosmos-db-sdk-limitations"></a><span data-ttu-id="2671d-111">Azure Cosmos DB SDK 限制</span><span class="sxs-lookup"><span data-stu-id="2671d-111">Azure Cosmos DB SDK limitations</span></span>

- <span data-ttu-id="2671d-112">僅提供非同步方法</span><span class="sxs-lookup"><span data-stu-id="2671d-112">Only async methods are provided</span></span>

> [!WARNING]
> <span data-ttu-id="2671d-113">由於 EF Core 依賴的低層級方法沒有同步處理版本，因此對應的功能目前是藉由在傳回`.Wait()` `Task`的上呼叫來執行。</span><span class="sxs-lookup"><span data-stu-id="2671d-113">Since there are no sync versions of the low level methods EF Core relies on, the corresponding functionality is currently implemented by calling `.Wait()` on the returned `Task`.</span></span> <span data-ttu-id="2671d-114">這表示使用之類`SaveChanges`的方法，或`ToList`而不是其非同步對應項，可能會導致應用程式的鎖死</span><span class="sxs-lookup"><span data-stu-id="2671d-114">This means that using methods like `SaveChanges`, or `ToList` instead of their async counterparts could lead to a deadlock in your application</span></span>

## <a name="azure-cosmos-db-limitations"></a><span data-ttu-id="2671d-115">Azure Cosmos DB 限制</span><span class="sxs-lookup"><span data-stu-id="2671d-115">Azure Cosmos DB limitations</span></span>

<span data-ttu-id="2671d-116">與關係資料庫相較之下，您可以看到[Azure Cosmos DB 支援功能](https://docs.microsoft.com/en-us/azure/cosmos-db/modeling-data)的完整總覽，這些是最顯著的差異：</span><span class="sxs-lookup"><span data-stu-id="2671d-116">You can see the full overview of [Azure Cosmos DB supported features](https://docs.microsoft.com/en-us/azure/cosmos-db/modeling-data), these are the most notable differences compared to a relational database:</span></span>

- <span data-ttu-id="2671d-117">不支援用戶端起始的交易</span><span class="sxs-lookup"><span data-stu-id="2671d-117">Client-initiated transactions are not supported</span></span>
- <span data-ttu-id="2671d-118">某些跨分割區查詢不受支援或速度較慢，視相關運算子而定</span><span class="sxs-lookup"><span data-stu-id="2671d-118">Some cross-partition queries are either not supported or much slower depending on the operators involved</span></span>