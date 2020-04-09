---
title: 追蹤與無追蹤查詢 - EF 核心
author: smitpatel
ms.date: 10/10/2019
ms.assetid: e17e060c-929f-4180-8883-40c438fbcc01
uid: core/querying/tracking
ms.openlocfilehash: a6c71c12f429f1324abe91d1b2cef96312bec051
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "78417646"
---
# <a name="tracking-vs-no-tracking-queries"></a><span data-ttu-id="15e74-102">追蹤與無追蹤查詢</span><span class="sxs-lookup"><span data-stu-id="15e74-102">Tracking vs. No-Tracking Queries</span></span>

<span data-ttu-id="15e74-103">跟蹤行為控制實體框架核心是否在其更改跟蹤器中保留有關實體實例的資訊。</span><span class="sxs-lookup"><span data-stu-id="15e74-103">Tracking behavior controls if Entity Framework Core will keep information about an entity instance in its change tracker.</span></span> <span data-ttu-id="15e74-104">如果追蹤實體，即會在 `SaveChanges()` 期間，將實體中偵測到的任何變更保存於資料庫。</span><span class="sxs-lookup"><span data-stu-id="15e74-104">If an entity is tracked, any changes detected in the entity will be persisted to the database during `SaveChanges()`.</span></span> <span data-ttu-id="15e74-105">EF Core 還將修復追蹤查詢結果中的實體和更改跟蹤器中的實體之間的導航屬性。</span><span class="sxs-lookup"><span data-stu-id="15e74-105">EF Core will also fix up navigation properties between the entities in a tracking query result and the entities that are in the change tracker.</span></span>

> [!NOTE]
> <span data-ttu-id="15e74-106">從不追蹤[無鍵實體型態](xref:core/modeling/keyless-entity-types)。</span><span class="sxs-lookup"><span data-stu-id="15e74-106">[Keyless entity types](xref:core/modeling/keyless-entity-types) are never tracked.</span></span> <span data-ttu-id="15e74-107">無論本文提到實體類型,它是指定義了密鑰的實體類型。</span><span class="sxs-lookup"><span data-stu-id="15e74-107">Wherever this article mentions entity types, it refers to entity types which have a key defined.</span></span>

> [!TIP]  
> <span data-ttu-id="15e74-108">您可以在 GitHub 上查看本文[的範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying)。</span><span class="sxs-lookup"><span data-stu-id="15e74-108">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="tracking-queries"></a><span data-ttu-id="15e74-109">追蹤查詢</span><span class="sxs-lookup"><span data-stu-id="15e74-109">Tracking queries</span></span>

<span data-ttu-id="15e74-110">預設會追蹤傳回實體類型的查詢。</span><span class="sxs-lookup"><span data-stu-id="15e74-110">By default, queries that return entity types are tracking.</span></span> <span data-ttu-id="15e74-111">這意味著您可以對這些實體實例進行更改,並保留這些變更`SaveChanges()`。</span><span class="sxs-lookup"><span data-stu-id="15e74-111">Which means you can make changes to those entity instances and have those changes persisted by `SaveChanges()`.</span></span> <span data-ttu-id="15e74-112">在下列範例中，將會偵測到對部落格評等的變更，並在 `SaveChanges()` 期間將其保存於資料庫。</span><span class="sxs-lookup"><span data-stu-id="15e74-112">In the following example, the change to the blogs rating will be detected and persisted to the database during `SaveChanges()`.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#Tracking)]

## <a name="no-tracking-queries"></a><span data-ttu-id="15e74-113">無追蹤查詢</span><span class="sxs-lookup"><span data-stu-id="15e74-113">No-tracking queries</span></span>

<span data-ttu-id="15e74-114">如果要在唯讀案例中使用結果，則不追蹤的查詢很實用。</span><span class="sxs-lookup"><span data-stu-id="15e74-114">No tracking queries are useful when the results are used in a read-only scenario.</span></span> <span data-ttu-id="15e74-115">它們執行得更快,因為無需設置更改跟蹤資訊。</span><span class="sxs-lookup"><span data-stu-id="15e74-115">They're quicker to execute because there's no need to set up the change tracking information.</span></span> <span data-ttu-id="15e74-116">如果不需要更新從資料庫中檢索的實體,則應使用無跟蹤查詢。</span><span class="sxs-lookup"><span data-stu-id="15e74-116">If you don't need to update the entities retrieved from the database, then a no-tracking query should be used.</span></span> <span data-ttu-id="15e74-117">您可以將單個查詢交換為無跟蹤。</span><span class="sxs-lookup"><span data-stu-id="15e74-117">You can swap an individual query to be no-tracking.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#NoTracking)]

<span data-ttu-id="15e74-118">您也可以在內容執行個體層級變更預設的追蹤行為：</span><span class="sxs-lookup"><span data-stu-id="15e74-118">You can also change the default tracking behavior at the context instance level:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ContextDefaultTrackingBehavior)]

## <a name="identity-resolution"></a><span data-ttu-id="15e74-119">身分識別解析</span><span class="sxs-lookup"><span data-stu-id="15e74-119">Identity resolution</span></span>

<span data-ttu-id="15e74-120">由於追蹤查詢使用更改跟蹤器,EF Core 將在追蹤查詢中執行標識解析。</span><span class="sxs-lookup"><span data-stu-id="15e74-120">Since a tracking query uses the change tracker, EF Core will do identity resolution in a tracking query.</span></span> <span data-ttu-id="15e74-121">實體具體化時,如果已跟蹤該實體,EF Core 將從更改跟蹤器返回同一實體實例。</span><span class="sxs-lookup"><span data-stu-id="15e74-121">When materializing an entity, EF Core will return the same entity instance from the change tracker if it's already being tracked.</span></span> <span data-ttu-id="15e74-122">如果結果多次包含同一實體,則每個匹配項都會返回同一實例。</span><span class="sxs-lookup"><span data-stu-id="15e74-122">If the result contains same entity multiple times, you get back same instance for each occurrence.</span></span> <span data-ttu-id="15e74-123">無追蹤查詢不使用更改跟蹤器,也不會執行標識解析。</span><span class="sxs-lookup"><span data-stu-id="15e74-123">No-tracking queries don't use the change tracker and don't do identity resolution.</span></span> <span data-ttu-id="15e74-124">因此,即使同一實體多次包含在結果中,您也會返回實體的新實例。</span><span class="sxs-lookup"><span data-stu-id="15e74-124">So you get back new instance of entity even when the same entity is contained in the result multiple times.</span></span> <span data-ttu-id="15e74-125">此行為在 EF Core 3.0 之前的版本不同,請參閱[以前的版本](#previous-versions)。</span><span class="sxs-lookup"><span data-stu-id="15e74-125">This behavior was different in versions before EF Core 3.0, see [previous versions](#previous-versions).</span></span>

## <a name="tracking-and-custom-projections"></a><span data-ttu-id="15e74-126">追蹤並自訂投影</span><span class="sxs-lookup"><span data-stu-id="15e74-126">Tracking and custom projections</span></span>

<span data-ttu-id="15e74-127">即使查詢的結果類型不是實體類型,EF Core 仍將在預設情況下跟蹤結果中包含的實體類型。</span><span class="sxs-lookup"><span data-stu-id="15e74-127">Even if the result type of the query isn't an entity type, EF Core will still track entity types contained in the result by default.</span></span> <span data-ttu-id="15e74-128">下列查詢會傳回匿名類型，並且將在結果集中追蹤 `Blog` 的執行個體。</span><span class="sxs-lookup"><span data-stu-id="15e74-128">In the following query, which returns an anonymous type, the instances of `Blog` in the result set will be tracked.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection1)]

<span data-ttu-id="15e74-129">如果結果集包含來自 LINQ 組合的實體類型,EF Core 將追蹤它們。</span><span class="sxs-lookup"><span data-stu-id="15e74-129">If the result set contains entity types coming out from LINQ composition, EF Core will track them.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection2)]

<span data-ttu-id="15e74-130">如果結果集不包含任何實體類型,則不執行任何跟蹤。</span><span class="sxs-lookup"><span data-stu-id="15e74-130">If the result set doesn't contain any entity types, then no tracking is done.</span></span> <span data-ttu-id="15e74-131">在下面的查詢中,我們返回一個匿名類型,其中包含來自實體的某些值(但沒有實際實體類型的實例)。</span><span class="sxs-lookup"><span data-stu-id="15e74-131">In the following query, we return an anonymous type with some of the values from the entity (but no instances of the actual entity type).</span></span> <span data-ttu-id="15e74-132">查詢中沒有跟蹤的實體。</span><span class="sxs-lookup"><span data-stu-id="15e74-132">There are no tracked entities coming out of the query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection3)]

 <span data-ttu-id="15e74-133">EF Core 支援在頂級投影中執行客戶端評估。</span><span class="sxs-lookup"><span data-stu-id="15e74-133">EF Core supports doing client evaluation in the top-level projection.</span></span> <span data-ttu-id="15e74-134">如果 EF Core 實現了用於客戶端評估的實體實例,則將跟蹤該實例。</span><span class="sxs-lookup"><span data-stu-id="15e74-134">If EF Core materializes an entity instance for client evaluation, it will be tracked.</span></span> <span data-ttu-id="15e74-135">在這裏,由於我們將實體傳遞`blog`給`StandardizeURL`用戶端方法 ,EF Core 也將跟蹤博客實例。</span><span class="sxs-lookup"><span data-stu-id="15e74-135">Here, since we're passing `blog` entities to the client method `StandardizeURL`, EF Core will track the blog instances too.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ClientProjection)]

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ClientMethod)]

<span data-ttu-id="15e74-136">EF Core 不跟蹤結果中包含的無鑰匙實體實例。</span><span class="sxs-lookup"><span data-stu-id="15e74-136">EF Core doesn't track the keyless entity instances contained in the result.</span></span> <span data-ttu-id="15e74-137">但是,EF Core 根據上述規則使用密鑰跟蹤實體類型的所有其他實例。</span><span class="sxs-lookup"><span data-stu-id="15e74-137">But EF Core tracks all the other instances of entity types with key according to rules above.</span></span>

<span data-ttu-id="15e74-138">在 EF Core 3.0 之前,上述某些規則的工作方式不同。</span><span class="sxs-lookup"><span data-stu-id="15e74-138">Some of the above rules worked differently before EF Core 3.0.</span></span> <span data-ttu-id="15e74-139">有關詳細資訊,請參閱[以前的版本](#previous-versions)。</span><span class="sxs-lookup"><span data-stu-id="15e74-139">For more information, see [previous versions](#previous-versions).</span></span>

## <a name="previous-versions"></a><span data-ttu-id="15e74-140">舊版本</span><span class="sxs-lookup"><span data-stu-id="15e74-140">Previous versions</span></span>

<span data-ttu-id="15e74-141">在版本 3.0 之前,EF Core 在追蹤方式上存在一些差異。</span><span class="sxs-lookup"><span data-stu-id="15e74-141">Before version 3.0, EF Core had some differences in how tracking was done.</span></span> <span data-ttu-id="15e74-142">顯著差異如下:</span><span class="sxs-lookup"><span data-stu-id="15e74-142">Notable differences are as follows:</span></span>

- <span data-ttu-id="15e74-143">如[在用戶端與伺服器評估](xref:core/querying/client-eval)頁中所述,EF Core 支援在版本 3.0 之前查詢的任何部分中的客戶端評估。</span><span class="sxs-lookup"><span data-stu-id="15e74-143">As explained in [Client vs Server Evaluation](xref:core/querying/client-eval) page, EF Core supported client evaluation in any part of the query before version 3.0.</span></span> <span data-ttu-id="15e74-144">用戶端評估導致實體的物化,而實體不是結果的一部分。</span><span class="sxs-lookup"><span data-stu-id="15e74-144">Client evaluation caused materialization of entities, which weren't part of the result.</span></span> <span data-ttu-id="15e74-145">因此,EF Core 分析了結果,以檢測要跟蹤的內容。此設計存在以下某些差異:</span><span class="sxs-lookup"><span data-stu-id="15e74-145">So EF Core analyzed the result to detect what to track. This design had certain differences as follows:</span></span>
  - <span data-ttu-id="15e74-146">投影中的客戶端評估導致具體化,但沒有返回具體化實體實例。</span><span class="sxs-lookup"><span data-stu-id="15e74-146">Client evaluation in the projection, which caused materialization but didn't return the materialized entity instance wasn't tracked.</span></span> <span data-ttu-id="15e74-147">以下示例未跟蹤`blog`實體。</span><span class="sxs-lookup"><span data-stu-id="15e74-147">The following example didn't track `blog` entities.</span></span>
    [!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ClientProjection)]

  - <span data-ttu-id="15e74-148">在某些情況下,EF Core 未跟蹤 LINQ 組合物中的物件。</span><span class="sxs-lookup"><span data-stu-id="15e74-148">EF Core didn't track the objects coming out of LINQ composition in certain cases.</span></span> <span data-ttu-id="15e74-149">下面的範例未追蹤`Post`。</span><span class="sxs-lookup"><span data-stu-id="15e74-149">The following example didn't track `Post`.</span></span>
    [!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection2)]

- <span data-ttu-id="15e74-150">每當查詢結果包含無鍵實體類型時,整個查詢都會被非跟蹤。</span><span class="sxs-lookup"><span data-stu-id="15e74-150">Whenever query results contained keyless entity types, the whole query was made non-tracking.</span></span> <span data-ttu-id="15e74-151">這意味著實體類型與鍵,結果也沒有被跟蹤。</span><span class="sxs-lookup"><span data-stu-id="15e74-151">That means that entity types with keys, which are in result weren't being tracked either.</span></span>
- <span data-ttu-id="15e74-152">EF Core 在無跟蹤查詢中做了標識解析。</span><span class="sxs-lookup"><span data-stu-id="15e74-152">EF Core did identity resolution in no-tracking query.</span></span> <span data-ttu-id="15e74-153">它使用弱引用來跟蹤已返回的實體。</span><span class="sxs-lookup"><span data-stu-id="15e74-153">It used weak references to keep track of entities that had already been returned.</span></span> <span data-ttu-id="15e74-154">因此,如果結果集多次包含相同的實體,則每個匹配項都會獲得相同的實例。</span><span class="sxs-lookup"><span data-stu-id="15e74-154">So if a result set contained the same entity multiples times, you would get the same instance for each occurrence.</span></span> <span data-ttu-id="15e74-155">儘管如果以前具有相同標識的結果已出範圍並回收了垃圾,但 EF Core 返回了一個新實例。</span><span class="sxs-lookup"><span data-stu-id="15e74-155">Though if a previous result with the same identity went out of scope and got garbage collected, EF Core returned a new instance.</span></span>
