---
title: 追蹤與無追蹤查詢-EF Core
description: Entity Framework Core 中追蹤和無追蹤查詢的資訊
author: smitpatel
ms.date: 10/10/2019
uid: core/querying/tracking
ms.openlocfilehash: a01446d7aec4d47eda23d4ac056e1c8286d2a281
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070961"
---
# <a name="tracking-vs-no-tracking-queries"></a><span data-ttu-id="9d611-103">追蹤與無追蹤查詢</span><span class="sxs-lookup"><span data-stu-id="9d611-103">Tracking vs. No-Tracking Queries</span></span>

<span data-ttu-id="9d611-104">追蹤行為會控制 Entity Framework Core 是否會在其變更追蹤程式中保留實體實例的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="9d611-104">Tracking behavior controls if Entity Framework Core will keep information about an entity instance in its change tracker.</span></span> <span data-ttu-id="9d611-105">如果追蹤實體，即會在 `SaveChanges()` 期間，將實體中偵測到的任何變更保存於資料庫。</span><span class="sxs-lookup"><span data-stu-id="9d611-105">If an entity is tracked, any changes detected in the entity will be persisted to the database during `SaveChanges()`.</span></span> <span data-ttu-id="9d611-106">EF Core 也會修正追蹤查詢結果中的實體與變更追蹤器中的實體之間的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="9d611-106">EF Core will also fix up navigation properties between the entities in a tracking query result and the entities that are in the change tracker.</span></span>

> [!NOTE]
> <span data-ttu-id="9d611-107">永遠不會追蹤[無索引鍵的實體類型](xref:core/modeling/keyless-entity-types)。</span><span class="sxs-lookup"><span data-stu-id="9d611-107">[Keyless entity types](xref:core/modeling/keyless-entity-types) are never tracked.</span></span> <span data-ttu-id="9d611-108">本文提及實體類型時，是指已定義索引鍵的實體類型。</span><span class="sxs-lookup"><span data-stu-id="9d611-108">Wherever this article mentions entity types, it refers to entity types which have a key defined.</span></span>

> [!TIP]  
> <span data-ttu-id="9d611-109">您可以在 GitHub 上檢視此文章的[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="9d611-109">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="tracking-queries"></a><span data-ttu-id="9d611-110">追蹤查詢</span><span class="sxs-lookup"><span data-stu-id="9d611-110">Tracking queries</span></span>

<span data-ttu-id="9d611-111">預設會追蹤傳回實體類型的查詢。</span><span class="sxs-lookup"><span data-stu-id="9d611-111">By default, queries that return entity types are tracking.</span></span> <span data-ttu-id="9d611-112">這表示您可以變更這些實體實例，並讓這些變更持續進行 `SaveChanges()` 。</span><span class="sxs-lookup"><span data-stu-id="9d611-112">Which means you can make changes to those entity instances and have those changes persisted by `SaveChanges()`.</span></span> <span data-ttu-id="9d611-113">在下列範例中，將會偵測到對部落格評等的變更，並在 `SaveChanges()` 期間將其保存於資料庫。</span><span class="sxs-lookup"><span data-stu-id="9d611-113">In the following example, the change to the blogs rating will be detected and persisted to the database during `SaveChanges()`.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#Tracking)]

## <a name="no-tracking-queries"></a><span data-ttu-id="9d611-114">無追蹤查詢</span><span class="sxs-lookup"><span data-stu-id="9d611-114">No-tracking queries</span></span>

<span data-ttu-id="9d611-115">如果要在唯讀案例中使用結果，則不追蹤的查詢很實用。</span><span class="sxs-lookup"><span data-stu-id="9d611-115">No tracking queries are useful when the results are used in a read-only scenario.</span></span> <span data-ttu-id="9d611-116">執行速度較快，因為不需要設定變更追蹤資訊。</span><span class="sxs-lookup"><span data-stu-id="9d611-116">They're quicker to execute because there's no need to set up the change tracking information.</span></span> <span data-ttu-id="9d611-117">如果您不需要更新從資料庫取出的實體，則應該使用無追蹤查詢。</span><span class="sxs-lookup"><span data-stu-id="9d611-117">If you don't need to update the entities retrieved from the database, then a no-tracking query should be used.</span></span> <span data-ttu-id="9d611-118">您可以將個別查詢交換為不追蹤。</span><span class="sxs-lookup"><span data-stu-id="9d611-118">You can swap an individual query to be no-tracking.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#NoTracking)]

<span data-ttu-id="9d611-119">您也可以在內容執行個體層級變更預設的追蹤行為：</span><span class="sxs-lookup"><span data-stu-id="9d611-119">You can also change the default tracking behavior at the context instance level:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ContextDefaultTrackingBehavior)]

## <a name="identity-resolution"></a><span data-ttu-id="9d611-120">身分識別解析</span><span class="sxs-lookup"><span data-stu-id="9d611-120">Identity resolution</span></span>

<span data-ttu-id="9d611-121">由於追蹤查詢會使用變更追蹤器，因此 EF Core 會在追蹤查詢中進行識別解析。</span><span class="sxs-lookup"><span data-stu-id="9d611-121">Since a tracking query uses the change tracker, EF Core will do identity resolution in a tracking query.</span></span> <span data-ttu-id="9d611-122">具體化實體時，如果已追蹤，EF Core 將會從變更追蹤器傳回相同的實體實例。</span><span class="sxs-lookup"><span data-stu-id="9d611-122">When materializing an entity, EF Core will return the same entity instance from the change tracker if it's already being tracked.</span></span> <span data-ttu-id="9d611-123">如果結果多次包含相同的實體，您就會在每次發生時取回相同的實例。</span><span class="sxs-lookup"><span data-stu-id="9d611-123">If the result contains same entity multiple times, you get back same instance for each occurrence.</span></span> <span data-ttu-id="9d611-124">無追蹤查詢不會使用變更追蹤器，也不會進行識別解析。</span><span class="sxs-lookup"><span data-stu-id="9d611-124">No-tracking queries don't use the change tracker and don't do identity resolution.</span></span> <span data-ttu-id="9d611-125">因此，即使相同的實體包含在結果中多次，您仍會取回實體的新實例。</span><span class="sxs-lookup"><span data-stu-id="9d611-125">So you get back new instance of entity even when the same entity is contained in the result multiple times.</span></span> <span data-ttu-id="9d611-126">在 EF Core 3.0 之前的版本中，這個行為不同，請參閱 [先前的版本](#previous-versions)。</span><span class="sxs-lookup"><span data-stu-id="9d611-126">This behavior was different in versions before EF Core 3.0, see [previous versions](#previous-versions).</span></span>

## <a name="tracking-and-custom-projections"></a><span data-ttu-id="9d611-127">追蹤和自訂投影</span><span class="sxs-lookup"><span data-stu-id="9d611-127">Tracking and custom projections</span></span>

<span data-ttu-id="9d611-128">即使查詢的結果型別不是實體型別，EF Core 仍會依預設追蹤包含在結果中的實體類型。</span><span class="sxs-lookup"><span data-stu-id="9d611-128">Even if the result type of the query isn't an entity type, EF Core will still track entity types contained in the result by default.</span></span> <span data-ttu-id="9d611-129">下列查詢會傳回匿名類型，並且將在結果集中追蹤 `Blog` 的執行個體。</span><span class="sxs-lookup"><span data-stu-id="9d611-129">In the following query, which returns an anonymous type, the instances of `Blog` in the result set will be tracked.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection1)]

<span data-ttu-id="9d611-130">如果結果集包含來自 LINQ 組合的實體類型，EF Core 將會追蹤這些型別。</span><span class="sxs-lookup"><span data-stu-id="9d611-130">If the result set contains entity types coming out from LINQ composition, EF Core will track them.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection2)]

<span data-ttu-id="9d611-131">如果結果集未包含任何實體類型，則不會進行追蹤。</span><span class="sxs-lookup"><span data-stu-id="9d611-131">If the result set doesn't contain any entity types, then no tracking is done.</span></span> <span data-ttu-id="9d611-132">在下列查詢中，我們會從實體 (傳回具有某些值的匿名型別，但不會) 實際實體類型的實例。</span><span class="sxs-lookup"><span data-stu-id="9d611-132">In the following query, we return an anonymous type with some of the values from the entity (but no instances of the actual entity type).</span></span> <span data-ttu-id="9d611-133">查詢沒有任何追蹤的實體。</span><span class="sxs-lookup"><span data-stu-id="9d611-133">There are no tracked entities coming out of the query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection3)]

 <span data-ttu-id="9d611-134">EF Core 支援在最上層投影中進行用戶端評估。</span><span class="sxs-lookup"><span data-stu-id="9d611-134">EF Core supports doing client evaluation in the top-level projection.</span></span> <span data-ttu-id="9d611-135">如果 EF Core 具體化實體實例以進行用戶端評估，則會進行追蹤。</span><span class="sxs-lookup"><span data-stu-id="9d611-135">If EF Core materializes an entity instance for client evaluation, it will be tracked.</span></span> <span data-ttu-id="9d611-136">在這裡，因為我們 `blog` 要將實體傳遞給用戶端方法 `StandardizeURL` ，EF Core 也會追蹤 blog 實例。</span><span class="sxs-lookup"><span data-stu-id="9d611-136">Here, since we're passing `blog` entities to the client method `StandardizeURL`, EF Core will track the blog instances too.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ClientProjection)]

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ClientMethod)]

<span data-ttu-id="9d611-137">EF Core 不會追蹤結果中包含的無索引鍵實體實例。</span><span class="sxs-lookup"><span data-stu-id="9d611-137">EF Core doesn't track the keyless entity instances contained in the result.</span></span> <span data-ttu-id="9d611-138">但是 EF Core 會根據上述規則，追蹤實體類型的所有其他實例與索引鍵。</span><span class="sxs-lookup"><span data-stu-id="9d611-138">But EF Core tracks all the other instances of entity types with key according to rules above.</span></span>

<span data-ttu-id="9d611-139">上述一些規則在 EF Core 3.0 之前的運作方式不同。</span><span class="sxs-lookup"><span data-stu-id="9d611-139">Some of the above rules worked differently before EF Core 3.0.</span></span> <span data-ttu-id="9d611-140">如需詳細資訊，請參閱 [先前的版本](#previous-versions)。</span><span class="sxs-lookup"><span data-stu-id="9d611-140">For more information, see [previous versions](#previous-versions).</span></span>

## <a name="previous-versions"></a><span data-ttu-id="9d611-141">舊版</span><span class="sxs-lookup"><span data-stu-id="9d611-141">Previous versions</span></span>

<span data-ttu-id="9d611-142">在3.0 版之前，EF Core 在追蹤的完成方式上有一些差異。</span><span class="sxs-lookup"><span data-stu-id="9d611-142">Before version 3.0, EF Core had some differences in how tracking was done.</span></span> <span data-ttu-id="9d611-143">值得注意的差異如下：</span><span class="sxs-lookup"><span data-stu-id="9d611-143">Notable differences are as follows:</span></span>

- <span data-ttu-id="9d611-144">如 [ [用戶端與伺服器評估](xref:core/querying/client-eval) ] 頁面中所述，在3.0 版之前的查詢的任何部分中 EF Core 支援的用戶端評估。</span><span class="sxs-lookup"><span data-stu-id="9d611-144">As explained in [Client vs Server Evaluation](xref:core/querying/client-eval) page, EF Core supported client evaluation in any part of the query before version 3.0.</span></span> <span data-ttu-id="9d611-145">用戶端評估會造成實體具體化，而不是結果的一部分。</span><span class="sxs-lookup"><span data-stu-id="9d611-145">Client evaluation caused materialization of entities, which weren't part of the result.</span></span> <span data-ttu-id="9d611-146">因此 EF Core 分析結果來偵測要追蹤的內容。此設計有一些差異，如下所示：</span><span class="sxs-lookup"><span data-stu-id="9d611-146">So EF Core analyzed the result to detect what to track. This design had certain differences as follows:</span></span>
  - <span data-ttu-id="9d611-147">投射中的用戶端評估，這會造成具體化但未傳回具體化實體實例。</span><span class="sxs-lookup"><span data-stu-id="9d611-147">Client evaluation in the projection, which caused materialization but didn't return the materialized entity instance wasn't tracked.</span></span> <span data-ttu-id="9d611-148">下列範例不會追蹤 `blog` 實體。</span><span class="sxs-lookup"><span data-stu-id="9d611-148">The following example didn't track `blog` entities.</span></span>
    [!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ClientProjection)]

  - <span data-ttu-id="9d611-149">在某些情況下，EF Core 不會追蹤 LINQ 組合所推出的物件。</span><span class="sxs-lookup"><span data-stu-id="9d611-149">EF Core didn't track the objects coming out of LINQ composition in certain cases.</span></span> <span data-ttu-id="9d611-150">下列範例沒有追蹤 `Post` 。</span><span class="sxs-lookup"><span data-stu-id="9d611-150">The following example didn't track `Post`.</span></span>
    [!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection2)]

- <span data-ttu-id="9d611-151">每當查詢結果包含無索引鍵的實體類型時，就會進行整個查詢進行非追蹤。</span><span class="sxs-lookup"><span data-stu-id="9d611-151">Whenever query results contained keyless entity types, the whole query was made non-tracking.</span></span> <span data-ttu-id="9d611-152">這表示不會追蹤具有索引鍵的實體類型，也就是結果。</span><span class="sxs-lookup"><span data-stu-id="9d611-152">That means that entity types with keys, which are in result weren't being tracked either.</span></span>
- <span data-ttu-id="9d611-153">EF Core 在無追蹤查詢中進行身分識別解析。</span><span class="sxs-lookup"><span data-stu-id="9d611-153">EF Core did identity resolution in no-tracking query.</span></span> <span data-ttu-id="9d611-154">它使用弱式參考來追蹤已經傳回的實體。</span><span class="sxs-lookup"><span data-stu-id="9d611-154">It used weak references to keep track of entities that had already been returned.</span></span> <span data-ttu-id="9d611-155">因此，如果結果集包含相同的實體多次，則每次出現時，您都會取得相同的實例。</span><span class="sxs-lookup"><span data-stu-id="9d611-155">So if a result set contained the same entity multiples times, you would get the same instance for each occurrence.</span></span> <span data-ttu-id="9d611-156">雖然先前具有相同身分識別的結果超出範圍，且已進行垃圾收集，但 EF Core 會傳回新的實例。</span><span class="sxs-lookup"><span data-stu-id="9d611-156">Though if a previous result with the same identity went out of scope and got garbage collected, EF Core returned a new instance.</span></span>
