---
title: 資料表分割-EF Core
description: 如何使用 Entity Framework Core 設定資料表分割
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 01/03/2020
uid: core/modeling/table-splitting
ms.openlocfilehash: e7428bc516a69310b6a6f521acc49aee0ba9f802
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526495"
---
# <a name="table-splitting"></a><span data-ttu-id="fbeab-103">資料表分割</span><span class="sxs-lookup"><span data-stu-id="fbeab-103">Table Splitting</span></span>

<span data-ttu-id="fbeab-104">EF Core 允許將兩個或多個實體對應至單一資料列。</span><span class="sxs-lookup"><span data-stu-id="fbeab-104">EF Core allows to map two or more entities to a single row.</span></span> <span data-ttu-id="fbeab-105">這稱為「_資料表分割_」或「_資料表共用_」。</span><span class="sxs-lookup"><span data-stu-id="fbeab-105">This is called _table splitting_ or _table sharing_.</span></span>

## <a name="configuration"></a><span data-ttu-id="fbeab-106">組態</span><span class="sxs-lookup"><span data-stu-id="fbeab-106">Configuration</span></span>

<span data-ttu-id="fbeab-107">若要使用資料表分割，必須將實體類型對應至相同的資料表，請將主鍵對應至相同的資料行，並且至少在一個實體類型的主鍵與相同資料表中的另一個關聯性之間設定一個關聯性。</span><span class="sxs-lookup"><span data-stu-id="fbeab-107">To use table splitting the entity types need to be mapped to the same table, have the primary keys mapped to the same columns and at least one relationship configured between the primary key of one entity type and another in the same table.</span></span>

<span data-ttu-id="fbeab-108">資料表分割的常見案例是只使用資料表中的資料行子集，以獲得更佳的效能或封裝。</span><span class="sxs-lookup"><span data-stu-id="fbeab-108">A common scenario for table splitting is using only a subset of the columns in the table for greater performance or encapsulation.</span></span>

<span data-ttu-id="fbeab-109">在此範例中， `Order` 表示的子集 `DetailedOrder` 。</span><span class="sxs-lookup"><span data-stu-id="fbeab-109">In this example `Order` represents a subset of `DetailedOrder`.</span></span>

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

<span data-ttu-id="fbeab-110">除了所需的設定之外，我們 `Property(o => o.Status).HasColumnName("Status")` 也會呼叫來對應 `DetailedOrder.Status` 至與相同的資料行 `Order.Status` 。</span><span class="sxs-lookup"><span data-stu-id="fbeab-110">In addition to the required configuration we call `Property(o => o.Status).HasColumnName("Status")` to map `DetailedOrder.Status` to the same column as `Order.Status`.</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting)]

> [!TIP]
> <span data-ttu-id="fbeab-111">如需詳細內容，請參閱[完整的範例專案](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting)。</span><span class="sxs-lookup"><span data-stu-id="fbeab-111">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting) for more context.</span></span>

## <a name="usage"></a><span data-ttu-id="fbeab-112">使用量</span><span class="sxs-lookup"><span data-stu-id="fbeab-112">Usage</span></span>

<span data-ttu-id="fbeab-113">使用資料表分割來儲存和查詢實體的方式與其他實體相同：</span><span class="sxs-lookup"><span data-stu-id="fbeab-113">Saving and querying entities using table splitting is done in the same way as other entities:</span></span>

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="optional-dependent-entity"></a><span data-ttu-id="fbeab-114">選擇性的相依實體</span><span class="sxs-lookup"><span data-stu-id="fbeab-114">Optional dependent entity</span></span>

> [!NOTE]
> <span data-ttu-id="fbeab-115">這項功能是在 EF Core 3.0 中引進。</span><span class="sxs-lookup"><span data-stu-id="fbeab-115">This feature was introduced in EF Core 3.0.</span></span>

<span data-ttu-id="fbeab-116">如果相依實體所使用的所有資料行都 `NULL` 在資料庫中，則查詢時不會建立它的實例。</span><span class="sxs-lookup"><span data-stu-id="fbeab-116">If all of the columns used by a dependent entity are `NULL` in the database, then no instance for it will be created when queried.</span></span> <span data-ttu-id="fbeab-117">這可讓您建立選擇性相依實體的模型，其中主體上的關聯性屬性會是 null。</span><span class="sxs-lookup"><span data-stu-id="fbeab-117">This allows modeling an optional dependent entity, where the relationship property on the principal would be null.</span></span> <span data-ttu-id="fbeab-118">請注意，如果所有相依的屬性都是選擇性的，而且設定為 `null` （可能不是預期的），也會發生這種情況。</span><span class="sxs-lookup"><span data-stu-id="fbeab-118">Note that this would also happen if all of the dependent's properties are optional and set to `null`, which might not be expected.</span></span>

## <a name="concurrency-tokens"></a><span data-ttu-id="fbeab-119">並行權杖</span><span class="sxs-lookup"><span data-stu-id="fbeab-119">Concurrency tokens</span></span>

<span data-ttu-id="fbeab-120">如果共用資料表的任何實體類型有並行 token，則也必須將它包含在所有其他實體類型中。</span><span class="sxs-lookup"><span data-stu-id="fbeab-120">If any of the entity types sharing a table has a concurrency token then it must be included in all other entity types as well.</span></span> <span data-ttu-id="fbeab-121">這是必要的，以便在只更新對應至相同資料表的其中一個實體時，避免過時的並行標記值。</span><span class="sxs-lookup"><span data-stu-id="fbeab-121">This is necessary in order to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="fbeab-122">若要避免將並行權杖公開給取用程式碼，您可以建立一個做為[陰影屬性](xref:core/modeling/shadow-properties)：</span><span class="sxs-lookup"><span data-stu-id="fbeab-122">To avoid exposing the concurrency token to the consuming code, it's possible the create one as a [shadow property](xref:core/modeling/shadow-properties):</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]
