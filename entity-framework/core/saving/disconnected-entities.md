---
title: "中斷連接的實體-EF 核心"
author: ajcvickers
ms.author: avickers
ms.date: 10/27/2016
ms.assetid: 2533b195-d357-4056-b0e0-8698971bc3b0
ms.technology: entity-framework-core
uid: core/saving/disconnected-entities
ms.openlocfilehash: 0ea02876b9594d54c971a7b70fcf7ce591e56ba0
ms.sourcegitcommit: ced2637bf8cc5964c6daa6c7fcfce501bf9ef6e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2017
---
# <a name="disconnected-entities"></a><span data-ttu-id="7c0bb-102">中斷連接的實體</span><span class="sxs-lookup"><span data-stu-id="7c0bb-102">Disconnected entities</span></span>

<span data-ttu-id="7c0bb-103">DbContext 執行個體將會自動追蹤從資料庫傳回的實體。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-103">A DbContext instance will automatically track entities returned from the database.</span></span> <span data-ttu-id="7c0bb-104">當呼叫 SaveChanges，並視需要將更新的資料庫，然後將偵測這些實體所做的變更。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-104">Changes made to these entities will then be detected when SaveChanges is called and the database will be updated as needed.</span></span> <span data-ttu-id="7c0bb-105">請參閱[基本儲存](basic.md)和[相關資料](related-data.md)如需詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-105">See [Basic Save](basic.md) and [Related Data](related-data.md) for details.</span></span>

<span data-ttu-id="7c0bb-106">不過，有時候實體會讓查詢使用一個內容執行個體，然後使用不同的執行個體來儲存。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-106">However, sometimes entities are queried using one context instance and then saved using a different instance.</span></span> <span data-ttu-id="7c0bb-107">這通常發生在 已中斷連線 」 的情況下，例如 web 應用程式，其中實體的查詢、 傳送至用戶端、 修改、 傳送至伺服器，在要求中，而且再儲存。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-107">This often happens in "disconnected" scenarios such as a web application where the entities are queried, sent to the client, modified, sent back to the server in a request, and then saved.</span></span> <span data-ttu-id="7c0bb-108">在此情況下，第二個內容執行個體需要知道實體是否為新 （應插入），或現有的 （應該更新）。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-108">In this case, the second context instance needs to know whether the entities are new (should be inserted) or existing (should be updated).</span></span>

> [!TIP]  
> <span data-ttu-id="7c0bb-109">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Disconnected/) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-109">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Disconnected/) on GitHub.</span></span>

## <a name="identifying-new-entities"></a><span data-ttu-id="7c0bb-110">識別新的實體</span><span class="sxs-lookup"><span data-stu-id="7c0bb-110">Identifying new entities</span></span>

### <a name="client-identifies-new-entities"></a><span data-ttu-id="7c0bb-111">用戶端識別出新的實體</span><span class="sxs-lookup"><span data-stu-id="7c0bb-111">Client identifies new entities</span></span>

<span data-ttu-id="7c0bb-112">簡單的情況下，處理時，用戶端通知伺服器實體是新的或現有。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-112">The simplest case to deal with is when the client informs the server whether the entity is new or existing.</span></span> <span data-ttu-id="7c0bb-113">例如，通常要插入新實體的要求與不同更新現有實體的要求。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-113">For example, often the request to insert a new entity is different from the request to update an existing entity.</span></span>

<span data-ttu-id="7c0bb-114">本章節的其餘部分涵蓋的情況下，必須決定是以其他某種方式來插入或更新。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-114">The remainder of this section covers the cases where it necessary to determine in some other way whether to insert or update.</span></span>

### <a name="with-auto-generated-keys"></a><span data-ttu-id="7c0bb-115">使用自動產生的索引鍵</span><span class="sxs-lookup"><span data-stu-id="7c0bb-115">With auto-generated keys</span></span>

<span data-ttu-id="7c0bb-116">自動產生的索引鍵的值通常可用來判斷實體是否需要插入或更新。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-116">The value of an automatically generated key can often be used to determine whether an entity needs to be inserted or updated.</span></span> <span data-ttu-id="7c0bb-117">如果機碼不具有已設定 （也就是它仍然具有 null、 零 」 等的 CLR 預設值），則必須是新實體，而且需要插入。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-117">If the key has not been set (i.e. it still has the CLR default value of null, zero, etc.), then the entity must be new and needs inserting.</span></span> <span data-ttu-id="7c0bb-118">相反地，如果已設定的索引鍵值，然後它必須已先前儲存，現在需要更新。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-118">On the other hand, if the key value has been set, then it must have already been previously saved and now needs updating.</span></span> <span data-ttu-id="7c0bb-119">換句話說，如果索引鍵的值，然後實體查詢，傳送至用戶端，而現在是回更新。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-119">In other words, if the key has a value, then entity was queried, sent to the client, and has now come back to be updated.</span></span>

<span data-ttu-id="7c0bb-120">很容易知道實體類型時，檢查為未設定的索引鍵：</span><span class="sxs-lookup"><span data-stu-id="7c0bb-120">It is easy to check for an unset key when the entity type is known:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#IsItNewSimple)]

<span data-ttu-id="7c0bb-121">不過，EF 也有內建的方法，若要這樣做的任何實體類型和索引鍵類型：</span><span class="sxs-lookup"><span data-stu-id="7c0bb-121">However, EF also has a built-in way to do this for any entity type and key type:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#IsItNewGeneral)]

> [!TIP]  
> <span data-ttu-id="7c0bb-122">索引鍵會設定為實體所追蹤的內容，即使實體處於 Added 狀態。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-122">Keys are set as soon as entities are tracked by the context, even if the entity is in the Added state.</span></span> <span data-ttu-id="7c0bb-123">這有助於周遊的實體，並決定如何處理每個，如為使用 TrackGraph API 時圖形時。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-123">This helps when traversing a graph of entities and deciding what to do with each, such as when using the TrackGraph API.</span></span> <span data-ttu-id="7c0bb-124">索引鍵的值應該只用於如下所示的方式_之前_進行任何呼叫，以追蹤實體。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-124">The key value should only be used in the way shown here _before_ any call is made to track the entity.</span></span>

### <a name="with-other-keys"></a><span data-ttu-id="7c0bb-125">其他索引鍵</span><span class="sxs-lookup"><span data-stu-id="7c0bb-125">With other keys</span></span>

<span data-ttu-id="7c0bb-126">需要其他一些機制來識別新的實體時不會自動產生索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-126">Some other mechanism is needed to identify new entities when key values are not generated automatically.</span></span> <span data-ttu-id="7c0bb-127">有兩種一般的方法，這個：</span><span class="sxs-lookup"><span data-stu-id="7c0bb-127">There are two general approaches to this:</span></span>
 * <span data-ttu-id="7c0bb-128">查詢實體</span><span class="sxs-lookup"><span data-stu-id="7c0bb-128">Query for the entity</span></span>
 * <span data-ttu-id="7c0bb-129">從用戶端傳遞旗標</span><span class="sxs-lookup"><span data-stu-id="7c0bb-129">Pass a flag from the client</span></span>

<span data-ttu-id="7c0bb-130">若要查詢的實體，只要使用 Find 方法：</span><span class="sxs-lookup"><span data-stu-id="7c0bb-130">To query for the entity, just use the Find method:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#IsItNewQuery)]

<span data-ttu-id="7c0bb-131">已超出本文件顯示旗標傳遞從用戶端的完整程式碼的範圍。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-131">It is beyond the scope of this document to show the full code for passing a flag from a client.</span></span> <span data-ttu-id="7c0bb-132">在 web 應用程式中，通常表示不同針對提出要求不同的動作，或傳遞要求中的某種狀態，然後擷取控制器中。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-132">In a web app, it usually means making different requests for different actions, or passing some state in the request then extracting it in the controller.</span></span>

## <a name="saving-single-entities"></a><span data-ttu-id="7c0bb-133">儲存單一實體</span><span class="sxs-lookup"><span data-stu-id="7c0bb-133">Saving single entities</span></span>

<span data-ttu-id="7c0bb-134">如果已知是否需要插入或更新，則可以適當地使用新增或更新：</span><span class="sxs-lookup"><span data-stu-id="7c0bb-134">If it is known whether or not an insert or update is needed, then either Add or Update can be used appropriately:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertAndUpdateSingleEntity)]

<span data-ttu-id="7c0bb-135">不過，如果實體使用自動產生索引鍵值，然後更新方法可以用於這兩種情況：</span><span class="sxs-lookup"><span data-stu-id="7c0bb-135">However, if the entity uses auto-generated key values, then the Update method can be used for both cases:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntity)]

<span data-ttu-id="7c0bb-136">更新方法通常會將針對更新、 不插入實體標記。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-136">The Update method normally marks the entity for update, not insert.</span></span> <span data-ttu-id="7c0bb-137">不過，如果實體具有自動產生的金鑰，而且沒有索引鍵的值已設定，則實體改為自動標示為進行插入。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-137">However, if the entity has a auto-generated key, and no key value has been set, then the entity is instead automatically marked for insert.</span></span>

> [!TIP]  
> <span data-ttu-id="7c0bb-138">此行為是在 EF 核心 2.0 引進。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-138">This behavior was introduced in EF Core 2.0.</span></span> <span data-ttu-id="7c0bb-139">較早的版本則一律需要明確地選擇 新增或更新。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-139">For earlier releases it is always necessary to explicitly choose either Add or Update.</span></span>

<span data-ttu-id="7c0bb-140">如果實體不使用自動產生的索引鍵，則應用程式必須決定是否應該插入或更新實體： 例如：</span><span class="sxs-lookup"><span data-stu-id="7c0bb-140">If the entity is not using auto-generated keys, then the application must decide whether the entity should be inserted or updated: For example:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntityWithFind)]

<span data-ttu-id="7c0bb-141">這裡的步驟如下：</span><span class="sxs-lookup"><span data-stu-id="7c0bb-141">The steps here are:</span></span>
* <span data-ttu-id="7c0bb-142">如果尋找傳回 null，則資料庫已不包含具有此 ID，部落格，讓我們呼叫將標示插入作業。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-142">If Find returns null, then the database doesn't already contain the blog with this ID, so we call Add mark it for insertion.</span></span>
* <span data-ttu-id="7c0bb-143">如果尋找傳回實體，然後它存在於資料庫中，且內容現在追蹤現有的實體</span><span class="sxs-lookup"><span data-stu-id="7c0bb-143">If Find returns an entity, then it exists in the database and the context is now tracking the existing entity</span></span>
  * <span data-ttu-id="7c0bb-144">然後使用 SetValues 來自用戶端的這個實體上設定的所有屬性的值。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-144">We then use SetValues to set the values for all properties on this entity to those that came from the client.</span></span>
  * <span data-ttu-id="7c0bb-145">SetValues 呼叫會將標示為要視需要更新實體。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-145">The SetValues call will mark the entity to be updated as needed.</span></span>

> [!TIP]  
> <span data-ttu-id="7c0bb-146">SetValues 只會將已修改具有不同的值中的追蹤實體的屬性。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-146">SetValues will only mark as modified the properties that have different values to those in the tracked entity.</span></span> <span data-ttu-id="7c0bb-147">這表示當更新傳送時，將會更新已實際變更這些資料行。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-147">This means that when the update is sent, only those columns that have actually changed will be updated.</span></span> <span data-ttu-id="7c0bb-148">（和如果沒有任何變更，則不會更新將會寄完全）。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-148">(And if nothing has changed, then no update will be sent at all.)</span></span>

## <a name="working-with-graphs"></a><span data-ttu-id="7c0bb-149">使用圖形</span><span class="sxs-lookup"><span data-stu-id="7c0bb-149">Working with graphs</span></span>

### <a name="all-newall-existing-entities"></a><span data-ttu-id="7c0bb-150">所有新的或全部現有的實體</span><span class="sxs-lookup"><span data-stu-id="7c0bb-150">All new/all existing entities</span></span>

<span data-ttu-id="7c0bb-151">使用圖形的範例是插入或更新的部落格與它相關聯的文章的集合。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-151">An example of working with graphs is inserting or updating a blog together with its collection of associated posts.</span></span> <span data-ttu-id="7c0bb-152">如果應該插入圖形中的所有實體，或應該更新所有，則處理程序會使用相同的單一實體的上述。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-152">If all the entities in the graph should be inserted, or all should be updated, then the process is the same as described above for single entities.</span></span> <span data-ttu-id="7c0bb-153">例如，部落格和文章像這樣建立的圖表：</span><span class="sxs-lookup"><span data-stu-id="7c0bb-153">For example, a graph of blogs and posts created like this:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#CreateBlogAndPosts)]

<span data-ttu-id="7c0bb-154">可插入像這樣：</span><span class="sxs-lookup"><span data-stu-id="7c0bb-154">can be inserted like this:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertGraph)]

<span data-ttu-id="7c0bb-155">部落格和要插入的所有文章時，會將標記加入呼叫。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-155">The call to Add will mark the blog and all the posts to be inserted.</span></span>

<span data-ttu-id="7c0bb-156">同樣地，如果在圖表中的所有實體都必須更新，然後更新可用：</span><span class="sxs-lookup"><span data-stu-id="7c0bb-156">Likewise, if all the entities in a graph need to be updated, then Update can be used:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#UpdateGraph)]

<span data-ttu-id="7c0bb-157">部落格和其所有的文章將會標示要更新。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-157">The blog and all its posts will be marked to be updated.</span></span>

### <a name="mix-of-new-and-existing-entities"></a><span data-ttu-id="7c0bb-158">混用新的和現有的實體</span><span class="sxs-lookup"><span data-stu-id="7c0bb-158">Mix of new and existing entities</span></span>

<span data-ttu-id="7c0bb-159">具有自動產生索引鍵更新一次可用進行插入和更新，即使圖形包含混用需要插入的實體和需要更新：</span><span class="sxs-lookup"><span data-stu-id="7c0bb-159">With auto-generated keys, Update can again be used for both inserts and updates, even if the graph contains a mix of entities that require inserting and those that require updating:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertOrUpdateGraph)]

<span data-ttu-id="7c0bb-160">更新會將標示為圖形、 部落格或 post，如果它沒有機碼值組，而其他所有實體會都標示為更新的插入作業中的任何實體。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-160">Update will mark any entity in the graph, blog or post, for insertion if it does not have a key value set, while all other entities are marked for update.</span></span>

<span data-ttu-id="7c0bb-161">之前，不使用自動產生索引鍵時的查詢和某些處理，可以使用：</span><span class="sxs-lookup"><span data-stu-id="7c0bb-161">As before, when not using auto-generated keys, a query and some processing can be used:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertOrUpdateGraphWithFind)]

## <a name="handling-deletes"></a><span data-ttu-id="7c0bb-162">處理刪除</span><span class="sxs-lookup"><span data-stu-id="7c0bb-162">Handling deletes</span></span>

<span data-ttu-id="7c0bb-163">刪除可能很困難，因為處理通常不存在的實體表示，應該刪除。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-163">Delete can be tricky to handle since often the absence of an entity means that it should be deleted.</span></span> <span data-ttu-id="7c0bb-164">為了解決這個問題的一個方式是使用 「 虛刪除 」 的實體標記為刪除，而不實際刪除。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-164">One way to deal with this is to use "soft deletes" such that the entity is marked as deleted rather than actually being deleted.</span></span> <span data-ttu-id="7c0bb-165">刪除，然後更新一樣。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-165">Deletes then becomes the same as updates.</span></span> <span data-ttu-id="7c0bb-166">非永久性刪除可以實作在使用[查詢篩選器](xref:core/querying/filters)。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-166">Soft deletes can be implemented in using [query filters](xref:core/querying/filters).</span></span>

<span data-ttu-id="7c0bb-167">為 true 的刪除，常見的模式是用於執行為何基本上 graph 差異查詢模式的延伸模組例如: </span><span class="sxs-lookup"><span data-stu-id="7c0bb-167">For true deletes, a common pattern is to use an extension of the query pattern to perform what is essentially a graph diff. For example:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertUpdateOrDeleteGraphWithFind)]

## <a name="trackgraph"></a><span data-ttu-id="7c0bb-168">TrackGraph</span><span class="sxs-lookup"><span data-stu-id="7c0bb-168">TrackGraph</span></span>

<span data-ttu-id="7c0bb-169">就內部而言，新增、 Attach 和更新使用圖形周遊決定，對未變更對於是否它應該標記為加入 （若要插入），（若要更新） 的修改日期，每個實體 （不執行任何動作），或刪除 （若要刪除）。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-169">Internally, Add, Attach, and Update use graph-traversal with a determination made for each entity as to whether it should be marked as Added (to insert), Modified (to update), Unchanged (do nothing), or Deleted (to delete).</span></span> <span data-ttu-id="7c0bb-170">這項機制會經由 TrackGraph API 公開。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-170">This mechanism is exposed via the TrackGraph API.</span></span> <span data-ttu-id="7c0bb-171">例如，假設，當用戶端傳送回之實體的圖表，它會設定某些旗標指出它應該要如何處理每個實體。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-171">For example, let's assume that when the client sends back a graph of entities it sets some flag on each entity indicating how it should be handled.</span></span> <span data-ttu-id="7c0bb-172">TrackGraph 可用來處理這個旗標：</span><span class="sxs-lookup"><span data-stu-id="7c0bb-172">TrackGraph can then be used to process this flag:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#TrackGraph)]

<span data-ttu-id="7c0bb-173">旗標只會顯示為實體為了簡單起見，此範例的一部分。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-173">The flags are only shown as part of the entity for simplicity of the example.</span></span> <span data-ttu-id="7c0bb-174">通常旗標會是 DTO 或要求中包含的其他特定狀態的一部分。</span><span class="sxs-lookup"><span data-stu-id="7c0bb-174">Typically the flags would be part of a DTO or some other state included in the request.</span></span>
