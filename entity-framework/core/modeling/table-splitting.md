---
title: 資料表分割-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 04/10/2019
ms.assetid: 0EC2CCE1-BD55-45D8-9EA9-20634987F094
uid: core/modeling/table-splitting
ms.openlocfilehash: 4a0bfaf017106a0bfdff084b1c472bdc17459a89
ms.sourcegitcommit: 8f801993c9b8cd8a8fbfa7134818a8edca79e31a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2019
ms.locfileid: "59562582"
---
# <a name="table-splitting"></a><span data-ttu-id="d08c8-102">資料表分割</span><span class="sxs-lookup"><span data-stu-id="d08c8-102">Table Splitting</span></span>

>[!NOTE]
> <span data-ttu-id="d08c8-103">這項功能是在 EF Core 2.0 的新功能。</span><span class="sxs-lookup"><span data-stu-id="d08c8-103">This feature is new in EF Core 2.0.</span></span>

<span data-ttu-id="d08c8-104">EF Core，可讓兩個或多個實體對應至單一資料列。</span><span class="sxs-lookup"><span data-stu-id="d08c8-104">EF Core allows to map two or more entities to a single row.</span></span> <span data-ttu-id="d08c8-105">這就叫做_資料表分割_或是_資料表共用_。</span><span class="sxs-lookup"><span data-stu-id="d08c8-105">This is called _table splitting_ or _table sharing_.</span></span>

## <a name="configuration"></a><span data-ttu-id="d08c8-106">組態</span><span class="sxs-lookup"><span data-stu-id="d08c8-106">Configuration</span></span>

<span data-ttu-id="d08c8-107">若要使用資料表分割的實體類型必須對應至相同的資料表，具有主索引鍵對應至相同的資料行和至少一個設定一個實體類型的主索引鍵與另一個在相同的資料表之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="d08c8-107">To use table splitting the entity types need to be mapped to the same table, have the primary keys mapped to the same columns and at least one relationship configured between the primary key of one entity type and another in the same table.</span></span>

<span data-ttu-id="d08c8-108">資料表分割的常見案例更高的效能或封裝，以使用資料表中的資料行子集。</span><span class="sxs-lookup"><span data-stu-id="d08c8-108">A common scenario for table splitting is using only a subset of the columns in the table for greater performance or encapsulation.</span></span>

<span data-ttu-id="d08c8-109">在此範例中`Order`代表子集`DetailedOrder`。</span><span class="sxs-lookup"><span data-stu-id="d08c8-109">In this example `Order` represents a subset of `DetailedOrder`.</span></span>

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

<span data-ttu-id="d08c8-110">在我們呼叫除了必要的組態`HasBaseType((string)null)`不必對應`DetailedOrder`中的相同階層`Order`。</span><span class="sxs-lookup"><span data-stu-id="d08c8-110">In addition to the required configuration we call `HasBaseType((string)null)` to avoid mapping `DetailedOrder` in the same hierarchy as `Order`.</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting&highlight=3)]

<span data-ttu-id="d08c8-111">請參閱[完整的範例專案](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting)詳細內容。</span><span class="sxs-lookup"><span data-stu-id="d08c8-111">See the [full sample project](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting) for more context.</span></span>

## <a name="usage"></a><span data-ttu-id="d08c8-112">使用量</span><span class="sxs-lookup"><span data-stu-id="d08c8-112">Usage</span></span>

<span data-ttu-id="d08c8-113">儲存及查詢使用資料表分割的實體都在相同的方式與其他實體，唯一的差別在於，必須插入追蹤共用資料列的所有實體。</span><span class="sxs-lookup"><span data-stu-id="d08c8-113">Saving and querying entities using table splitting is done in the same way as other entities, the only difference is that all entities sharing a row must be tracked for the insert.</span></span>

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="concurrency-tokens"></a><span data-ttu-id="d08c8-114">並行語彙基元</span><span class="sxs-lookup"><span data-stu-id="d08c8-114">Concurrency tokens</span></span>

<span data-ttu-id="d08c8-115">如果任何共用資料表的實體類型並行語彙基元它必須包含在所有其他實體類型，以避免過時的並行語彙基元的值，只是其中一個實體對應至相同的資料表更新時。</span><span class="sxs-lookup"><span data-stu-id="d08c8-115">If any of the entity types sharing a table has a concurrency token then it must be included in all other entity types to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="d08c8-116">若要避免將其公開到使用的程式碼就可以建立一個陰影狀態中的。</span><span class="sxs-lookup"><span data-stu-id="d08c8-116">To avoid exposing it to the consuming code it's possible the create one in shadow-state.</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]