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
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a><span data-ttu-id="5219c-103">EF Core Azure Cosmos DB 提供者限制</span><span class="sxs-lookup"><span data-stu-id="5219c-103">EF Core Azure Cosmos DB Provider Limitations</span></span>

<span data-ttu-id="5219c-104">Cosmos 提供者有一些限制。</span><span class="sxs-lookup"><span data-stu-id="5219c-104">The Cosmos provider has a number of limitations.</span></span> <span data-ttu-id="5219c-105">其中有許多限制是基礎 Cosmos 資料庫引擎中的限制，而且並非 EF 特有的結果。</span><span class="sxs-lookup"><span data-stu-id="5219c-105">Many of these limitations are a result of limitations in the underlying Cosmos database engine and are not specific to EF.</span></span> <span data-ttu-id="5219c-106">但大部分都尚未 [實行](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc)。</span><span class="sxs-lookup"><span data-stu-id="5219c-106">But most simply [haven't been implemented yet](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span></span>

<span data-ttu-id="5219c-107">以下是一些常見的要求功能：</span><span class="sxs-lookup"><span data-stu-id="5219c-107">These are some of the commonly requested features:</span></span>

- [<span data-ttu-id="5219c-108">`Include` 支援</span><span class="sxs-lookup"><span data-stu-id="5219c-108">`Include` support</span></span>](https://github.com/dotnet/efcore/issues/16920)
- [<span data-ttu-id="5219c-109">`Join` 支援</span><span class="sxs-lookup"><span data-stu-id="5219c-109">`Join` support</span></span>](https://github.com/dotnet/efcore/issues/17314)
- [<span data-ttu-id="5219c-110">基本類型的集合支援</span><span class="sxs-lookup"><span data-stu-id="5219c-110">Collections of primitive types support</span></span>](https://github.com/dotnet/efcore/issues/14762)

## <a name="azure-cosmos-db-sdk-limitations"></a><span data-ttu-id="5219c-111">Azure Cosmos DB SDK 限制</span><span class="sxs-lookup"><span data-stu-id="5219c-111">Azure Cosmos DB SDK limitations</span></span>

- <span data-ttu-id="5219c-112">僅提供非同步方法</span><span class="sxs-lookup"><span data-stu-id="5219c-112">Only async methods are provided</span></span>

> [!WARNING]
> <span data-ttu-id="5219c-113">由於沒有 EF Core 依賴的低層級方法的同步版本，因此對應的功能目前是藉由 `.Wait()` 在傳回的上呼叫來實作為 `Task` 。</span><span class="sxs-lookup"><span data-stu-id="5219c-113">Since there are no sync versions of the low level methods EF Core relies on, the corresponding functionality is currently implemented by calling `.Wait()` on the returned `Task`.</span></span> <span data-ttu-id="5219c-114">這表示，使用類似 `SaveChanges` 或 `ToList` 非其非同步對應的方法，可能會導致您的應用程式發生鎖死</span><span class="sxs-lookup"><span data-stu-id="5219c-114">This means that using methods like `SaveChanges`, or `ToList` instead of their async counterparts could lead to a deadlock in your application</span></span>

## <a name="azure-cosmos-db-limitations"></a><span data-ttu-id="5219c-115">Azure Cosmos DB 限制</span><span class="sxs-lookup"><span data-stu-id="5219c-115">Azure Cosmos DB limitations</span></span>

<span data-ttu-id="5219c-116">您可以查看 [Azure Cosmos DB 支援功能](/azure/cosmos-db/modeling-data)的完整總覽，相較于關係資料庫，這些是最顯著的差異：</span><span class="sxs-lookup"><span data-stu-id="5219c-116">You can see the full overview of [Azure Cosmos DB supported features](/azure/cosmos-db/modeling-data), these are the most notable differences compared to a relational database:</span></span>

- <span data-ttu-id="5219c-117">不支援用戶端起始的交易</span><span class="sxs-lookup"><span data-stu-id="5219c-117">Client-initiated transactions are not supported</span></span>
- <span data-ttu-id="5219c-118">某些跨資料分割的查詢會根據 (的相關運算子（例如 `Skip/Take` 或 `OFFSET LIMIT`) ）而變慢。</span><span class="sxs-lookup"><span data-stu-id="5219c-118">Some cross-partition queries are slower depending on the operators involved (for example `Skip/Take` or `OFFSET LIMIT`)</span></span>
