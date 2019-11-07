---
title: 資料表分割-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 04/10/2019
ms.assetid: 0EC2CCE1-BD55-45D8-9EA9-20634987F094
uid: core/modeling/table-splitting
ms.openlocfilehash: a3a2e5842a6c6b4b490084d205a0d44bb46c17ee
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656032"
---
# <a name="table-splitting"></a><span data-ttu-id="38b69-102">資料表分割</span><span class="sxs-lookup"><span data-stu-id="38b69-102">Table Splitting</span></span>

>[!NOTE]
> <span data-ttu-id="38b69-103">這項功能在 EF Core 2.0 中是新的。</span><span class="sxs-lookup"><span data-stu-id="38b69-103">This feature is new in EF Core 2.0.</span></span>

<span data-ttu-id="38b69-104">EF Core 允許將兩個或多個實體對應至單一資料列。</span><span class="sxs-lookup"><span data-stu-id="38b69-104">EF Core allows to map two or more entities to a single row.</span></span> <span data-ttu-id="38b69-105">這稱為「_資料表分割_」或「_資料表共用_」。</span><span class="sxs-lookup"><span data-stu-id="38b69-105">This is called _table splitting_ or _table sharing_.</span></span>

## <a name="configuration"></a><span data-ttu-id="38b69-106">Configuration</span><span class="sxs-lookup"><span data-stu-id="38b69-106">Configuration</span></span>

<span data-ttu-id="38b69-107">若要使用資料表分割，必須將實體類型對應至相同的資料表，請將主鍵對應至相同的資料行，並且至少在一個實體類型的主鍵與相同資料表中的另一個關聯性之間設定一個關聯性。</span><span class="sxs-lookup"><span data-stu-id="38b69-107">To use table splitting the entity types need to be mapped to the same table, have the primary keys mapped to the same columns and at least one relationship configured between the primary key of one entity type and another in the same table.</span></span>

<span data-ttu-id="38b69-108">資料表分割的常見案例是只使用資料表中的資料行子集，以獲得更佳的效能或封裝。</span><span class="sxs-lookup"><span data-stu-id="38b69-108">A common scenario for table splitting is using only a subset of the columns in the table for greater performance or encapsulation.</span></span>

<span data-ttu-id="38b69-109">在此範例中 `Order` 代表 `DetailedOrder`的子集。</span><span class="sxs-lookup"><span data-stu-id="38b69-109">In this example `Order` represents a subset of `DetailedOrder`.</span></span>

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

<span data-ttu-id="38b69-110">除了所需的設定之外，我們還會呼叫 `Property(o => o.Status).HasColumnName("Status")` 將 `DetailedOrder.Status` 對應到與 `Order.Status`相同的資料行。</span><span class="sxs-lookup"><span data-stu-id="38b69-110">In addition to the required configuration we call `Property(o => o.Status).HasColumnName("Status")` to map `DetailedOrder.Status` to the same column as `Order.Status`.</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting&highlight=3)]

> [!TIP]
> <span data-ttu-id="38b69-111">如需詳細內容，請參閱[完整的範例專案](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting)。</span><span class="sxs-lookup"><span data-stu-id="38b69-111">See the [full sample project](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting) for more context.</span></span>

## <a name="usage"></a><span data-ttu-id="38b69-112">使用量</span><span class="sxs-lookup"><span data-stu-id="38b69-112">Usage</span></span>

<span data-ttu-id="38b69-113">使用資料表分割來儲存和查詢實體的方式與其他實體相同。</span><span class="sxs-lookup"><span data-stu-id="38b69-113">Saving and querying entities using table splitting is done in the same way as other entities.</span></span> <span data-ttu-id="38b69-114">從 EF Core 3.0 開始，可以 `null`相依的實體參考。</span><span class="sxs-lookup"><span data-stu-id="38b69-114">And starting with EF Core 3.0 the dependent entity reference can be `null`.</span></span> <span data-ttu-id="38b69-115">如果相依實體所使用的所有資料行都 `NULL` 是資料庫，則查詢時將不會建立它的實例。</span><span class="sxs-lookup"><span data-stu-id="38b69-115">If all of the columns used by the dependent entity are `NULL` is the database then no instance for it will be created when queried.</span></span> <span data-ttu-id="38b69-116">這也會發生在所有屬性都是選擇性的，而且設定為 `null`，這可能不是預期的。</span><span class="sxs-lookup"><span data-stu-id="38b69-116">This would also happen all of the properties are optional and set to `null`, which might not be expected.</span></span>

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="concurrency-tokens"></a><span data-ttu-id="38b69-117">並行標記</span><span class="sxs-lookup"><span data-stu-id="38b69-117">Concurrency tokens</span></span>

<span data-ttu-id="38b69-118">如果共用資料表的任何實體類型具有並行 token，則必須將它包含在所有其他實體類型中，以避免只有對應到相同資料表的其中一個實體更新時，才會有過時的並行標記值。</span><span class="sxs-lookup"><span data-stu-id="38b69-118">If any of the entity types sharing a table has a concurrency token then it must be included in all other entity types to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="38b69-119">為了避免將它公開給取用的程式碼，您可以在陰影狀態中建立一個。</span><span class="sxs-lookup"><span data-stu-id="38b69-119">To avoid exposing it to the consuming code it's possible the create one in shadow-state.</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]
