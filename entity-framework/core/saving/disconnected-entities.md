---
title: 已中斷連線的實體 - EF Core
description: 在 Entity Framework Core 中使用已中斷連線、未追蹤的實體（跨多個內容實例）
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/disconnected-entities
ms.openlocfilehash: f21cc71ef11ee4ef37618a68d5a5219e8174bf8b
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063565"
---
# <a name="disconnected-entities"></a><span data-ttu-id="83d2a-103">已中斷連線的實體</span><span class="sxs-lookup"><span data-stu-id="83d2a-103">Disconnected entities</span></span>

<span data-ttu-id="83d2a-104">DbContext 執行個體會自動追蹤從資料庫傳回的實體。</span><span class="sxs-lookup"><span data-stu-id="83d2a-104">A DbContext instance will automatically track entities returned from the database.</span></span> <span data-ttu-id="83d2a-105">接著，在呼叫 SaveChanges 時，就會偵測已對這些實體進行的變更，然後將會視需要更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="83d2a-105">Changes made to these entities will then be detected when SaveChanges is called and the database will be updated as needed.</span></span> <span data-ttu-id="83d2a-106">如需詳細資料，請參閱[基本儲存](xref:core/saving/basic)和[相關資料](xref:core/saving/related-data)。</span><span class="sxs-lookup"><span data-stu-id="83d2a-106">See [Basic Save](xref:core/saving/basic) and [Related Data](xref:core/saving/related-data) for details.</span></span>

<span data-ttu-id="83d2a-107">不過，有時會在查詢實體時使用一個內容執行個體，然後儲存時又使用不同的執行個體。</span><span class="sxs-lookup"><span data-stu-id="83d2a-107">However, sometimes entities are queried using one context instance and then saved using a different instance.</span></span> <span data-ttu-id="83d2a-108">這通常發生在「已中斷連線」的案例中，例如 Web 應用程式，其中會在要求中對實體進行查詢、傳送給用戶端、修改、傳回給伺服器，然後再儲存。</span><span class="sxs-lookup"><span data-stu-id="83d2a-108">This often happens in "disconnected" scenarios such as a web application where the entities are queried, sent to the client, modified, sent back to the server in a request, and then saved.</span></span> <span data-ttu-id="83d2a-109">在此情況下，第二個內容執行個體必須知道實體是新的 (應該插入) 還是現有的 (應該更新)。</span><span class="sxs-lookup"><span data-stu-id="83d2a-109">In this case, the second context instance needs to know whether the entities are new (should be inserted) or existing (should be updated).</span></span>

<!-- markdownlint-disable MD028 -->
> [!TIP]
> <span data-ttu-id="83d2a-110">您可以在 GitHub 上檢視此文章的[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Disconnected/) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="83d2a-110">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Disconnected/) on GitHub.</span></span>

> [!TIP]
> <span data-ttu-id="83d2a-111">對於具有指定主索引鍵值的任何實體，EF Core 只能追蹤其中一個執行個體。</span><span class="sxs-lookup"><span data-stu-id="83d2a-111">EF Core can only track one instance of any entity with a given primary key value.</span></span> <span data-ttu-id="83d2a-112">若要避免此情況成為問題，最佳方式就是針對每個工作單位都使用短期內容，讓內容從空白開始、有實體與其連結、儲存這些實體，然後再處置及捨棄內容。</span><span class="sxs-lookup"><span data-stu-id="83d2a-112">The best way to avoid this being an issue is to use a short-lived context for each unit-of-work such that the context starts empty, has entities attached to it, saves those entities, and then the context is disposed and discarded.</span></span>
<!-- markdownlint-enable MD028 -->

## <a name="identifying-new-entities"></a><span data-ttu-id="83d2a-113">識別新實體</span><span class="sxs-lookup"><span data-stu-id="83d2a-113">Identifying new entities</span></span>

### <a name="client-identifies-new-entities"></a><span data-ttu-id="83d2a-114">用戶端識別新實體</span><span class="sxs-lookup"><span data-stu-id="83d2a-114">Client identifies new entities</span></span>

<span data-ttu-id="83d2a-115">最簡單的處理情況是由用戶端通知伺服器該實體是新的還是現有的。</span><span class="sxs-lookup"><span data-stu-id="83d2a-115">The simplest case to deal with is when the client informs the server whether the entity is new or existing.</span></span> <span data-ttu-id="83d2a-116">例如，插入新實體的要求通常與更新現有實體的要求不同。</span><span class="sxs-lookup"><span data-stu-id="83d2a-116">For example, often the request to insert a new entity is different from the request to update an existing entity.</span></span>

<span data-ttu-id="83d2a-117">本節的其餘部分將涵蓋是否有必要以某種其他方式來判斷是要進行插入還是更新。</span><span class="sxs-lookup"><span data-stu-id="83d2a-117">The remainder of this section covers the cases where it necessary to determine in some other way whether to insert or update.</span></span>

### <a name="with-auto-generated-keys"></a><span data-ttu-id="83d2a-118">使用自動產生的索引鍵</span><span class="sxs-lookup"><span data-stu-id="83d2a-118">With auto-generated keys</span></span>

<span data-ttu-id="83d2a-119">自動產生索引鍵的值通常可用來判斷是否需要對實體進行插入或更新。</span><span class="sxs-lookup"><span data-stu-id="83d2a-119">The value of an automatically generated key can often be used to determine whether an entity needs to be inserted or updated.</span></span> <span data-ttu-id="83d2a-120">如果尚未設定索引鍵 (亦即，仍具有 CLR 預設值 Null、零等)，則該實體必定是新的而需要插入。</span><span class="sxs-lookup"><span data-stu-id="83d2a-120">If the key has not been set (that is, it still has the CLR default value of null, zero, etc.), then the entity must be new and needs inserting.</span></span> <span data-ttu-id="83d2a-121">另一方面，如果已設定索引鍵值，則表示實體先前必定已經儲存過，而現在是需要更新。</span><span class="sxs-lookup"><span data-stu-id="83d2a-121">On the other hand, if the key value has been set, then it must have already been previously saved and now needs updating.</span></span> <span data-ttu-id="83d2a-122">換句話說，如果索引鍵有值，即表示已對實體進行查詢、傳送給用戶端，而現在是返回來進行更新。</span><span class="sxs-lookup"><span data-stu-id="83d2a-122">In other words, if the key has a value, then the entity was queried, sent to the client, and has now come back to be updated.</span></span>

<span data-ttu-id="83d2a-123">在實體類型為已知的情況下，檢查是否有未設定的索引鍵相當簡單：</span><span class="sxs-lookup"><span data-stu-id="83d2a-123">It is easy to check for an unset key when the entity type is known:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewSimple)]

<span data-ttu-id="83d2a-124">不過，EF 針對所有實體類型和索引鍵類型也提供一個內建的方法來執行此操作：</span><span class="sxs-lookup"><span data-stu-id="83d2a-124">However, EF also has a built-in way to do this for any entity type and key type:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewGeneral)]

> [!TIP]  
> <span data-ttu-id="83d2a-125">只要內容一追蹤實體，就會立即設定索引鍵，即使該實體處於 Added 狀態時也一樣。</span><span class="sxs-lookup"><span data-stu-id="83d2a-125">Keys are set as soon as entities are tracked by the context, even if the entity is in the Added state.</span></span> <span data-ttu-id="83d2a-126">周遊實體圖表並判斷要針對每個實體執行什麼動作時 (例如使用 TrackGraph API 時)，這會很有幫助。</span><span class="sxs-lookup"><span data-stu-id="83d2a-126">This helps when traversing a graph of entities and deciding what to do with each, such as when using the TrackGraph API.</span></span> <span data-ttu-id="83d2a-127">使用索引鍵值時，應該只以這裡所示的方式在發出任何呼叫來追蹤實體「之前」__ 使用。</span><span class="sxs-lookup"><span data-stu-id="83d2a-127">The key value should only be used in the way shown here _before_ any call is made to track the entity.</span></span>

### <a name="with-other-keys"></a><span data-ttu-id="83d2a-128">搭配其他索引鍵</span><span class="sxs-lookup"><span data-stu-id="83d2a-128">With other keys</span></span>

<span data-ttu-id="83d2a-129">當索引鍵值不是以自動方式產生時，需要一些其他機制來識別新的實體。</span><span class="sxs-lookup"><span data-stu-id="83d2a-129">Some other mechanism is needed to identify new entities when key values are not generated automatically.</span></span> <span data-ttu-id="83d2a-130">這包括兩種一般做法：</span><span class="sxs-lookup"><span data-stu-id="83d2a-130">There are two general approaches to this:</span></span>

* <span data-ttu-id="83d2a-131">查詢實體</span><span class="sxs-lookup"><span data-stu-id="83d2a-131">Query for the entity</span></span>
* <span data-ttu-id="83d2a-132">從用戶端傳遞旗標</span><span class="sxs-lookup"><span data-stu-id="83d2a-132">Pass a flag from the client</span></span>

<span data-ttu-id="83d2a-133">若要查詢實體，請直接使用 Find 方法：</span><span class="sxs-lookup"><span data-stu-id="83d2a-133">To query for the entity, just use the Find method:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewQuery)]

<span data-ttu-id="83d2a-134">本文件的範圍不包含顯示從用戶端傳遞旗標的完整程式碼。</span><span class="sxs-lookup"><span data-stu-id="83d2a-134">It is beyond the scope of this document to show the full code for passing a flag from a client.</span></span> <span data-ttu-id="83d2a-135">在 Web 應用程式中，這通常意謂著針對不同的動作發出不同的要求，或是在要求中傳遞某種狀態，再於控制器中擷取該狀態。</span><span class="sxs-lookup"><span data-stu-id="83d2a-135">In a web app, it usually means making different requests for different actions, or passing some state in the request then extracting it in the controller.</span></span>

## <a name="saving-single-entities"></a><span data-ttu-id="83d2a-136">儲存單一實體</span><span class="sxs-lookup"><span data-stu-id="83d2a-136">Saving single entities</span></span>

<span data-ttu-id="83d2a-137">如果已知所需的是插入還是更新，便可適當地使用 Add 或 Update：</span><span class="sxs-lookup"><span data-stu-id="83d2a-137">If it is known whether or not an insert or update is needed, then either Add or Update can be used appropriately:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertAndUpdateSingleEntity)]

<span data-ttu-id="83d2a-138">不過，如果實體使用自動產生的索引鍵值，則 Update 方法對這兩種情況都適用：</span><span class="sxs-lookup"><span data-stu-id="83d2a-138">However, if the entity uses auto-generated key values, then the Update method can be used for both cases:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntity)]

<span data-ttu-id="83d2a-139">Update 方法通常會將實體標示為要進行更新，而不是插入。</span><span class="sxs-lookup"><span data-stu-id="83d2a-139">The Update method normally marks the entity for update, not insert.</span></span> <span data-ttu-id="83d2a-140">不過，如果實體有自動產生的索引鍵，且尚未設定任何索引鍵值，就會改為將實體標示為要進行插入。</span><span class="sxs-lookup"><span data-stu-id="83d2a-140">However, if the entity has a auto-generated key, and no key value has been set, then the entity is instead automatically marked for insert.</span></span>

<span data-ttu-id="83d2a-141">如果實體未使用自動產生的金鑰，應用程式就必須判斷是應該插入還是更新實體：例如：</span><span class="sxs-lookup"><span data-stu-id="83d2a-141">If the entity is not using auto-generated keys, then the application must decide whether the entity should be inserted or updated: For example:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntityWithFind)]

<span data-ttu-id="83d2a-142">步驟如下：</span><span class="sxs-lookup"><span data-stu-id="83d2a-142">The steps here are:</span></span>

* <span data-ttu-id="83d2a-143">如果 Find 傳回 Null，即表示資料庫尚未包含具有此識別碼的部落格，因此我們會呼叫 Add 來將它標示為要進行插入。</span><span class="sxs-lookup"><span data-stu-id="83d2a-143">If Find returns null, then the database doesn't already contain the blog with this ID, so we call Add mark it for insertion.</span></span>
* <span data-ttu-id="83d2a-144">如果 Find 傳回實體，則表示它存在於資料庫中，而內容現在正在追蹤該現有實體</span><span class="sxs-lookup"><span data-stu-id="83d2a-144">If Find returns an entity, then it exists in the database and the context is now tracking the existing entity</span></span>
  * <span data-ttu-id="83d2a-145">那麼，我們就會使用 SetValues 將此實體上所有屬性的值設定為那些來自用戶端的值。</span><span class="sxs-lookup"><span data-stu-id="83d2a-145">We then use SetValues to set the values for all properties on this entity to those that came from the client.</span></span>
  * <span data-ttu-id="83d2a-146">SetValues 呼叫會將實體標示為視需要進行更新。</span><span class="sxs-lookup"><span data-stu-id="83d2a-146">The SetValues call will mark the entity to be updated as needed.</span></span>

> [!TIP]  
> <span data-ttu-id="83d2a-147">SetValues 只會將值與所追蹤實體值不同的屬性標示為已修改。</span><span class="sxs-lookup"><span data-stu-id="83d2a-147">SetValues will only mark as modified the properties that have different values to those in the tracked entity.</span></span> <span data-ttu-id="83d2a-148">這意謂著傳送更新時，將只會更新已實際變更的資料行。</span><span class="sxs-lookup"><span data-stu-id="83d2a-148">This means that when the update is sent, only those columns that have actually changed will be updated.</span></span> <span data-ttu-id="83d2a-149">(如果沒有任何變更，則不會傳送任何更新)。</span><span class="sxs-lookup"><span data-stu-id="83d2a-149">(And if nothing has changed, then no update will be sent at all.)</span></span>

## <a name="working-with-graphs"></a><span data-ttu-id="83d2a-150">使用圖表</span><span class="sxs-lookup"><span data-stu-id="83d2a-150">Working with graphs</span></span>

### <a name="identity-resolution"></a><span data-ttu-id="83d2a-151">身分識別解析</span><span class="sxs-lookup"><span data-stu-id="83d2a-151">Identity resolution</span></span>

<span data-ttu-id="83d2a-152">如以上所述，EF Core 對於具有指定主索引鍵值的任何實體，只能追蹤其中一個執行個體。</span><span class="sxs-lookup"><span data-stu-id="83d2a-152">As noted above, EF Core can only track one instance of any entity with a given primary key value.</span></span> <span data-ttu-id="83d2a-153">使用圖表時，應該以理想的方式建立圖表來維持這個不變因素，而內容應該僅用於一個工作單位。</span><span class="sxs-lookup"><span data-stu-id="83d2a-153">When working with graphs the graph should ideally be created such that this invariant is maintained, and the context should be used for only one unit-of-work.</span></span> <span data-ttu-id="83d2a-154">如果圖表包含重複項，則必須在將圖表傳送給 EF 之前，先處理圖表以將多個執行個體合併成一個。</span><span class="sxs-lookup"><span data-stu-id="83d2a-154">If the graph does contain duplicates, then it will be necessary to process the graph before sending it to EF to consolidate multiple instances into one.</span></span> <span data-ttu-id="83d2a-155">如果執行個體具有衝突的值和關聯性，這可能會相當費工夫，因此應該在您的應用程式管線中儘快合併重複項，以避免需要解決衝突。</span><span class="sxs-lookup"><span data-stu-id="83d2a-155">This may not be trivial where instances have conflicting values and relationships, so consolidating duplicates should be done as soon as possible in your application pipeline to avoid conflict resolution.</span></span>

### <a name="all-newall-existing-entities"></a><span data-ttu-id="83d2a-156">所以新的/所有現有的實體</span><span class="sxs-lookup"><span data-stu-id="83d2a-156">All new/all existing entities</span></span>

<span data-ttu-id="83d2a-157">其中一個使用圖表的範例就是將部落格連同其相關文章集合一起進行插入或更新。</span><span class="sxs-lookup"><span data-stu-id="83d2a-157">An example of working with graphs is inserting or updating a blog together with its collection of associated posts.</span></span> <span data-ttu-id="83d2a-158">如果圖表中的所有實體都應該進行插入，或是全部都應該進行更新，則程序會與上述單一實體的程序相同。</span><span class="sxs-lookup"><span data-stu-id="83d2a-158">If all the entities in the graph should be inserted, or all should be updated, then the process is the same as described above for single entities.</span></span> <span data-ttu-id="83d2a-159">例如，部落格和文章中圖表的建立方式如果像這樣：</span><span class="sxs-lookup"><span data-stu-id="83d2a-159">For example, a graph of blogs and posts created like this:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#CreateBlogAndPosts)]

<span data-ttu-id="83d2a-160">則插入方式可以像這樣：</span><span class="sxs-lookup"><span data-stu-id="83d2a-160">can be inserted like this:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertGraph)]

<span data-ttu-id="83d2a-161">對 Add 的呼叫會將部落格及所有文章標示為要進行插入。</span><span class="sxs-lookup"><span data-stu-id="83d2a-161">The call to Add will mark the blog and all the posts to be inserted.</span></span>

<span data-ttu-id="83d2a-162">同樣地，如果圖表中的所有實體都需要進行更新，則可以使用 Update：</span><span class="sxs-lookup"><span data-stu-id="83d2a-162">Likewise, if all the entities in a graph need to be updated, then Update can be used:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#UpdateGraph)]

<span data-ttu-id="83d2a-163">部落格及其文章將會標示為要進行更新。</span><span class="sxs-lookup"><span data-stu-id="83d2a-163">The blog and all its posts will be marked to be updated.</span></span>

### <a name="mix-of-new-and-existing-entities"></a><span data-ttu-id="83d2a-164">新實體與現有實體的混合</span><span class="sxs-lookup"><span data-stu-id="83d2a-164">Mix of new and existing entities</span></span>

<span data-ttu-id="83d2a-165">使用自動產生的索引鍵時，可同樣使用 Update 來進行插入和更新，即使圖表包含需要插入與需要更新的混合實體時也一樣：</span><span class="sxs-lookup"><span data-stu-id="83d2a-165">With auto-generated keys, Update can again be used for both inserts and updates, even if the graph contains a mix of entities that require inserting and those that require updating:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateGraph)]

<span data-ttu-id="83d2a-166">Update 會將圖表中任何未設定索引鍵值的實體 (部落格或文章) 標示為要進行插入，所有其他實體則標示為要進行更新。</span><span class="sxs-lookup"><span data-stu-id="83d2a-166">Update will mark any entity in the graph, blog or post, for insertion if it does not have a key value set, while all other entities are marked for update.</span></span>

<span data-ttu-id="83d2a-167">與之前相同，未使用自動產生的索引鍵時，可以使用查詢和某些處理：</span><span class="sxs-lookup"><span data-stu-id="83d2a-167">As before, when not using auto-generated keys, a query and some processing can be used:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateGraphWithFind)]

## <a name="handling-deletes"></a><span data-ttu-id="83d2a-168">處理刪除</span><span class="sxs-lookup"><span data-stu-id="83d2a-168">Handling deletes</span></span>

<span data-ttu-id="83d2a-169">處理刪除可能相當棘手，因為當實體不存在時，常常意謂著應該將其刪除。</span><span class="sxs-lookup"><span data-stu-id="83d2a-169">Delete can be tricky to handle since often the absence of an entity means that it should be deleted.</span></span> <span data-ttu-id="83d2a-170">其中一個處理此情況的方式是使用「虛刪除」來將實體標示為已刪除，而不是實際進行刪除。</span><span class="sxs-lookup"><span data-stu-id="83d2a-170">One way to deal with this is to use "soft deletes" such that the entity is marked as deleted rather than actually being deleted.</span></span> <span data-ttu-id="83d2a-171">如此一來，刪除就變成與更新相同。</span><span class="sxs-lookup"><span data-stu-id="83d2a-171">Deletes then becomes the same as updates.</span></span> <span data-ttu-id="83d2a-172">您可以在使用[查詢篩選](xref:core/querying/filters)時實作虛刪除。</span><span class="sxs-lookup"><span data-stu-id="83d2a-172">Soft deletes can be implemented in using [query filters](xref:core/querying/filters).</span></span>

<span data-ttu-id="83d2a-173">針對真實的刪除，常見的模式是使用查詢模式的延伸來執行基本上是圖表差異的操作。</span><span class="sxs-lookup"><span data-stu-id="83d2a-173">For true deletes, a common pattern is to use an extension of the query pattern to perform what is essentially a graph diff.</span></span> <span data-ttu-id="83d2a-174">例如︰</span><span class="sxs-lookup"><span data-stu-id="83d2a-174">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertUpdateOrDeleteGraphWithFind)]

## <a name="trackgraph"></a><span data-ttu-id="83d2a-175">TrackGraph</span><span class="sxs-lookup"><span data-stu-id="83d2a-175">TrackGraph</span></span>

<span data-ttu-id="83d2a-176">就內部而言，Add、Attach 及 Update 會使用圖表周遊搭配針對每個實體所做的判斷，亦即應該將實體標示為 Added (要進行插入)、Modified (要進行更新)、Unchanged (不進行任何動作) 還是 Deleted (要進行刪除)。</span><span class="sxs-lookup"><span data-stu-id="83d2a-176">Internally, Add, Attach, and Update use graph-traversal with a determination made for each entity as to whether it should be marked as Added (to insert), Modified (to update), Unchanged (do nothing), or Deleted (to delete).</span></span> <span data-ttu-id="83d2a-177">這個機制會透過 TrackGraph API 公開。</span><span class="sxs-lookup"><span data-stu-id="83d2a-177">This mechanism is exposed via the TrackGraph API.</span></span> <span data-ttu-id="83d2a-178">例如，假設當用戶端傳回實體圖表時，會在每個實體上設定指出處理方式的旗標。</span><span class="sxs-lookup"><span data-stu-id="83d2a-178">For example, let's assume that when the client sends back a graph of entities it sets some flag on each entity indicating how it should be handled.</span></span> <span data-ttu-id="83d2a-179">接著，便可使用 TrackGraph 來處理此旗標：</span><span class="sxs-lookup"><span data-stu-id="83d2a-179">TrackGraph can then be used to process this flag:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#TrackGraph)]

<span data-ttu-id="83d2a-180">為了範例簡便起見，旗標僅顯示為實體的一部分。</span><span class="sxs-lookup"><span data-stu-id="83d2a-180">The flags are only shown as part of the entity for simplicity of the example.</span></span> <span data-ttu-id="83d2a-181">一般而言，旗標會是 DTO 或要求中所包含某個其他狀態的一部分。</span><span class="sxs-lookup"><span data-stu-id="83d2a-181">Typically the flags would be part of a DTO or some other state included in the request.</span></span>
