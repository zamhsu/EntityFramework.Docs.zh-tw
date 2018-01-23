---
title: "串聯刪除-EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: ee8e14ec-2158-4c9c-96b5-118715e2ed9e
ms.technology: entity-framework-core
uid: core/saving/cascade-delete
ms.openlocfilehash: e1cb194d7c7472af59eb44fe2a084fa16c40c186
ms.sourcegitcommit: 3b21a7fdeddc7b3c70d9b7777b72bef61f59216c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2018
---
# <a name="cascade-delete"></a><span data-ttu-id="36e7c-102">串聯刪除</span><span class="sxs-lookup"><span data-stu-id="36e7c-102">Cascade Delete</span></span>

<span data-ttu-id="36e7c-103">串聯刪除常用於資料庫詞彙來描述可讓刪除的資料列會自動觸發相關的資料列的刪除作業特性。</span><span class="sxs-lookup"><span data-stu-id="36e7c-103">Cascade delete is commonly used in database terminology to describe a characteristic that allows the deletion of a row to automatically trigger the deletion of related rows.</span></span> <span data-ttu-id="36e7c-104">密切相關的概念也涵蓋在 EF 核心刪除行為是子實體的父系的關聯性時自動刪除已執行過-通常稱為 「 刪除的遺棄項目 」 這個 i。</span><span class="sxs-lookup"><span data-stu-id="36e7c-104">A closely related concept also covered by EF Core delete behaviors is the automatic deletion of a child entity when it's relationship to a parent has been severed--this i commonly known as "deleting orphans".</span></span>

<span data-ttu-id="36e7c-105">EF 核心實作數個不同的刪除行為，並可讓個別的關聯性的刪除行為的組態。</span><span class="sxs-lookup"><span data-stu-id="36e7c-105">EF Core implements several different delete behaviors and allows for the configuration of the delete behaviors of individual relationships.</span></span> <span data-ttu-id="36e7c-106">EF 核心也會實作自動設定 [關聯性的 requiredness] 為基礎的每個關聯性的實用的預設刪除行為的慣例 (../modeling/relationships.md#required-and-optional-relationships)。</span><span class="sxs-lookup"><span data-stu-id="36e7c-106">EF Core also implements conventions that automatically configure useful default delete behaviors for each relationship based on the [requiredness of the relationship] (../modeling/relationships.md#required-and-optional-relationships).</span></span>

## <a name="delete-behaviors"></a><span data-ttu-id="36e7c-107">刪除行為</span><span class="sxs-lookup"><span data-stu-id="36e7c-107">Delete behaviors</span></span>
<span data-ttu-id="36e7c-108">刪除中所定義的行為*DeleteBehavior*列舉值類型，而且可以傳遞至*OnDelete* fluent API 來控制是否刪除主體/父實體或的英國相依性/子實體的關聯性應該有副作用的相依性/子實體上。</span><span class="sxs-lookup"><span data-stu-id="36e7c-108">Delete behaviors are defined in the *DeleteBehavior* enumerator type and can be passed to the *OnDelete* fluent API to control whether the deletion of a principal/parent entity or the severing of the relationship to dependent/child entities should have a side effect on the dependent/child entities.</span></span>

<span data-ttu-id="36e7c-109">有三個 EF 主體/父實體被刪除或切斷子系關聯性時，可以採取的動作：</span><span class="sxs-lookup"><span data-stu-id="36e7c-109">There are three actions EF can take when a principal/parent entity is deleted or the relationship to the child is severed:</span></span>
* <span data-ttu-id="36e7c-110">您可以刪除子/相依性</span><span class="sxs-lookup"><span data-stu-id="36e7c-110">The child/dependent can be deleted</span></span>
* <span data-ttu-id="36e7c-111">您可以將子系的外部索引鍵值為 null</span><span class="sxs-lookup"><span data-stu-id="36e7c-111">The child's foreign key values can be set to null</span></span>
* <span data-ttu-id="36e7c-112">子系會保持不變</span><span class="sxs-lookup"><span data-stu-id="36e7c-112">The child remains unchanged</span></span>

> [!NOTE]  
> <span data-ttu-id="36e7c-113">當主要實體會刪除使用 EF 核心，而且相依的實體不會載入記憶體中 （也就是追蹤相依性），只會套用在 EF 核心模式中設定刪除行為。</span><span class="sxs-lookup"><span data-stu-id="36e7c-113">The delete behavior configured in the EF Core model is only applied when the principal entity is deleted using EF Core and the dependent entities are loaded in memory (i.e. for tracked dependents).</span></span> <span data-ttu-id="36e7c-114">對應的重疊顯示行為必須能夠確保資料不受到內容追蹤資料庫中的安裝程式已套用的必要動作。</span><span class="sxs-lookup"><span data-stu-id="36e7c-114">A corresponding cascade behavior needs to be setup in the database to ensure data that is not being tracked by the context has the necessary action applied.</span></span> <span data-ttu-id="36e7c-115">如果您使用 EF 核心來建立資料庫時，此重疊顯示行為會為您的安裝程式。</span><span class="sxs-lookup"><span data-stu-id="36e7c-115">If you use EF Core to create the database, this cascade behavior will be setup for you.</span></span>

<span data-ttu-id="36e7c-116">上述第二個動作，將外部索引鍵的值設定為 null 不是有效，如果外部索引鍵不是可為 null。</span><span class="sxs-lookup"><span data-stu-id="36e7c-116">For the second action above, setting a foreign key value to null is not valid if foreign key is not nullable.</span></span> <span data-ttu-id="36e7c-117">（非可為 null 的外部索引鍵就相當於必要的關聯性）。在這些情況下，EF 核心追蹤，外部索引鍵屬性已被標示為 null 呼叫 SaveChanges，因為變更無法保存至資料庫，發生例外狀況在這之前。</span><span class="sxs-lookup"><span data-stu-id="36e7c-117">(A non-nullable foreign key is equivalent to a required relationship.) In these cases, EF Core tracks that the foreign key property has been marked as null until SaveChanges is called, at which time an exception is thrown because the change cannot be persisted to the database.</span></span> <span data-ttu-id="36e7c-118">這是類似於從資料庫取得條件約束違規。</span><span class="sxs-lookup"><span data-stu-id="36e7c-118">This is similar to getting a constraint violation from the database.</span></span>

<span data-ttu-id="36e7c-119">有四個 delete 行為，如下列表格中所列。</span><span class="sxs-lookup"><span data-stu-id="36e7c-119">There are four delete behaviors, as listed in the tables below.</span></span> <span data-ttu-id="36e7c-120">選擇性的關聯性 （可為 null 的外部索引鍵） 它_是_能夠儲存的 null 外部索引鍵值，而造成下列影響：</span><span class="sxs-lookup"><span data-stu-id="36e7c-120">For optional relationships (nullable foreign key) it _is_ possible to save a null foreign key value, which results in the following effects:</span></span>

| <span data-ttu-id="36e7c-121">行為名稱</span><span class="sxs-lookup"><span data-stu-id="36e7c-121">Behavior Name</span></span> | <span data-ttu-id="36e7c-122">在記憶體中的相依性/子系上的效果</span><span class="sxs-lookup"><span data-stu-id="36e7c-122">Effect on dependent/child in memory</span></span> | <span data-ttu-id="36e7c-123">在資料庫中的相依性/子系上的效果</span><span class="sxs-lookup"><span data-stu-id="36e7c-123">Effect on dependent/child in database</span></span>
|-|-|-
| <span data-ttu-id="36e7c-124">**Cascade**</span><span class="sxs-lookup"><span data-stu-id="36e7c-124">**Cascade**</span></span> | <span data-ttu-id="36e7c-125">刪除實體</span><span class="sxs-lookup"><span data-stu-id="36e7c-125">Entities are deleted</span></span> | <span data-ttu-id="36e7c-126">刪除實體</span><span class="sxs-lookup"><span data-stu-id="36e7c-126">Entities are deleted</span></span>
| <span data-ttu-id="36e7c-127">**ClientSetNull** （預設值）</span><span class="sxs-lookup"><span data-stu-id="36e7c-127">**ClientSetNull** (Default)</span></span> | <span data-ttu-id="36e7c-128">外部索引鍵屬性會設定為 null</span><span class="sxs-lookup"><span data-stu-id="36e7c-128">Foreign key properties are set to null</span></span> | <span data-ttu-id="36e7c-129">無</span><span class="sxs-lookup"><span data-stu-id="36e7c-129">None</span></span>
| <span data-ttu-id="36e7c-130">**SetNull**</span><span class="sxs-lookup"><span data-stu-id="36e7c-130">**SetNull**</span></span> | <span data-ttu-id="36e7c-131">外部索引鍵屬性會設定為 null</span><span class="sxs-lookup"><span data-stu-id="36e7c-131">Foreign key properties are set to null</span></span> | <span data-ttu-id="36e7c-132">外部索引鍵屬性會設定為 null</span><span class="sxs-lookup"><span data-stu-id="36e7c-132">Foreign key properties are set to null</span></span>
| <span data-ttu-id="36e7c-133">**Restrict**</span><span class="sxs-lookup"><span data-stu-id="36e7c-133">**Restrict**</span></span> | <span data-ttu-id="36e7c-134">無</span><span class="sxs-lookup"><span data-stu-id="36e7c-134">None</span></span> | <span data-ttu-id="36e7c-135">無</span><span class="sxs-lookup"><span data-stu-id="36e7c-135">None</span></span>

<span data-ttu-id="36e7c-136">它是必要的關聯性 （非可為 null 的外部索引鍵）_不_能夠儲存的 null 外部索引鍵值，而造成下列影響：</span><span class="sxs-lookup"><span data-stu-id="36e7c-136">For required relationships (non-nullable foreign key) it is _not_ possible to save a null foreign key value, which results in the following effects:</span></span>

| <span data-ttu-id="36e7c-137">行為名稱</span><span class="sxs-lookup"><span data-stu-id="36e7c-137">Behavior Name</span></span> | <span data-ttu-id="36e7c-138">在記憶體中的相依性/子系上的效果</span><span class="sxs-lookup"><span data-stu-id="36e7c-138">Effect on dependent/child in memory</span></span> | <span data-ttu-id="36e7c-139">在資料庫中的相依性/子系上的效果</span><span class="sxs-lookup"><span data-stu-id="36e7c-139">Effect on dependent/child in database</span></span>
|-|-|-
| <span data-ttu-id="36e7c-140">**Cascade** （預設值）</span><span class="sxs-lookup"><span data-stu-id="36e7c-140">**Cascade** (Default)</span></span> | <span data-ttu-id="36e7c-141">刪除實體</span><span class="sxs-lookup"><span data-stu-id="36e7c-141">Entities are deleted</span></span> | <span data-ttu-id="36e7c-142">刪除實體</span><span class="sxs-lookup"><span data-stu-id="36e7c-142">Entities are deleted</span></span>
| <span data-ttu-id="36e7c-143">**ClientSetNull**</span><span class="sxs-lookup"><span data-stu-id="36e7c-143">**ClientSetNull**</span></span> | <span data-ttu-id="36e7c-144">SaveChanges 擲回</span><span class="sxs-lookup"><span data-stu-id="36e7c-144">SaveChanges throws</span></span> | <span data-ttu-id="36e7c-145">無</span><span class="sxs-lookup"><span data-stu-id="36e7c-145">None</span></span>
| <span data-ttu-id="36e7c-146">**SetNull**</span><span class="sxs-lookup"><span data-stu-id="36e7c-146">**SetNull**</span></span> | <span data-ttu-id="36e7c-147">SaveChanges 擲回</span><span class="sxs-lookup"><span data-stu-id="36e7c-147">SaveChanges throws</span></span> | <span data-ttu-id="36e7c-148">SaveChanges 擲回</span><span class="sxs-lookup"><span data-stu-id="36e7c-148">SaveChanges throws</span></span>
| <span data-ttu-id="36e7c-149">**Restrict**</span><span class="sxs-lookup"><span data-stu-id="36e7c-149">**Restrict**</span></span> | <span data-ttu-id="36e7c-150">無</span><span class="sxs-lookup"><span data-stu-id="36e7c-150">None</span></span> | <span data-ttu-id="36e7c-151">無</span><span class="sxs-lookup"><span data-stu-id="36e7c-151">None</span></span>

<span data-ttu-id="36e7c-152">在上述資料表中*無*可能會導致條件約束違規。</span><span class="sxs-lookup"><span data-stu-id="36e7c-152">In the tables above, *None* can result in a constraint violation.</span></span> <span data-ttu-id="36e7c-153">比方說，如果主體/子實體已刪除，但若要變更相依/子系的外部索引鍵會採取任何動作，然後資料庫將可能擲回 SaveChanges 上因為外部索引條件約束違規。</span><span class="sxs-lookup"><span data-stu-id="36e7c-153">For example, if a principal/child entity is deleted but no action is taken to change the foreign key of a dependent/child, then the database will likely throw on SaveChanges due to a foreign constraint violation.</span></span>

<span data-ttu-id="36e7c-154">在高的層級：</span><span class="sxs-lookup"><span data-stu-id="36e7c-154">At a high level:</span></span>
* <span data-ttu-id="36e7c-155">如果您有沒有父代，就無法存在的實體，而且您想要小心，自動刪除子系的 EF，然後使用*Cascade*。</span><span class="sxs-lookup"><span data-stu-id="36e7c-155">If you have entities that cannot exist without a parent, and you want EF to take care for deleting the children automatically, then use *Cascade*.</span></span>
  * <span data-ttu-id="36e7c-156">沒有父代通常使不能存在的實體，使用必要的關聯性， *Cascade*是預設值。</span><span class="sxs-lookup"><span data-stu-id="36e7c-156">Entities that cannot exist without a parent usually make use of required relationships, for which *Cascade* is the default.</span></span>
* <span data-ttu-id="36e7c-157">如果您有可能會或可能沒有父代，實體，而且您想 EF 需要小心的 null 外部索引鍵，然後使用*ClientSetNull*</span><span class="sxs-lookup"><span data-stu-id="36e7c-157">If you have entities that may or may not have a parent, and you want EF to take care of nulling out the foreign key for you, then use *ClientSetNull*</span></span>
  * <span data-ttu-id="36e7c-158">實體可以存在沒有父代通常使用於選擇性的關聯性，其中*ClientSetNull*是預設值。</span><span class="sxs-lookup"><span data-stu-id="36e7c-158">Entities that can exist without a parent usually make use of optional relationships, for which *ClientSetNull* is the default.</span></span>
  * <span data-ttu-id="36e7c-159">如果您想要同時嘗試甚至傳播到子外部索引鍵的 null 值的資料庫時的子實體未載入，然後使用*SetNull*。</span><span class="sxs-lookup"><span data-stu-id="36e7c-159">If you want the database to also try to propagate null values to child foreign keys even when the child entity is not loaded, then use *SetNull*.</span></span> <span data-ttu-id="36e7c-160">不過，請注意，資料庫必須支援此功能，設定資料庫，就像這樣可能會導致其他限制，這樣實際上會使得這個選項並不實用。</span><span class="sxs-lookup"><span data-stu-id="36e7c-160">However, note that the database must support this, and configuring the database like this can result in other restrictions, which in practice often makes this option impractical.</span></span> <span data-ttu-id="36e7c-161">這就是為什麼*SetNull*不是預設值。</span><span class="sxs-lookup"><span data-stu-id="36e7c-161">This is why *SetNull* is not the default.</span></span>
* <span data-ttu-id="36e7c-162">如果您不想永遠自動刪除實體或 null 外部索引鍵時自動執行，然後使用 EF 核心*限制*。</span><span class="sxs-lookup"><span data-stu-id="36e7c-162">If you don't want EF Core to ever delete an entity automatically or null out the foreign key automatically, then use *Restrict*.</span></span> <span data-ttu-id="36e7c-163">請注意，這需要，您的程式碼保持子實體外部索引鍵值同步和手動否則條件約束將會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="36e7c-163">Note that this requires that your code keep child entities and their foreign key values in sync manually otherwise constraint exceptions will be thrown.</span></span>

> [!NOTE]
> <span data-ttu-id="36e7c-164">在 EF 核心，不同於 EF6，串聯效果不會發生立即，而是只呼叫 SaveChanges 是。</span><span class="sxs-lookup"><span data-stu-id="36e7c-164">In EF Core, unlike EF6, cascading effects do not happen immediately, but instead only when SaveChanges is called.</span></span>

> [!NOTE]  
> <span data-ttu-id="36e7c-165">**在 EF 核心 2.0 中的變更：**在舊版中，*限制*會導致追蹤的相依實體設為選擇性的外部索引鍵屬性為 null，且預設刪除選擇性的關聯性的行為。</span><span class="sxs-lookup"><span data-stu-id="36e7c-165">**Changes in EF Core 2.0:** In previous releases, *Restrict* would cause optional foreign key properties in tracked dependent entities to be set to null, and was the default delete behavior for optional relationships.</span></span> <span data-ttu-id="36e7c-166">在 EF 核心 2.0 中， *ClientSetNull*已引入來代表該行為，並成為選擇性的關聯性的預設值。</span><span class="sxs-lookup"><span data-stu-id="36e7c-166">In EF Core 2.0, the *ClientSetNull* was introduced to represent that behavior and became the default for optional relationships.</span></span> <span data-ttu-id="36e7c-167">行為*限制*已調整為永遠不會相依的實體上有任何副作用。</span><span class="sxs-lookup"><span data-stu-id="36e7c-167">The behavior of *Restrict* was adjusted to never have any side effects on dependent entities.</span></span>

## <a name="entity-deletion-examples"></a><span data-ttu-id="36e7c-168">實體刪除範例</span><span class="sxs-lookup"><span data-stu-id="36e7c-168">Entity deletion examples</span></span>

<span data-ttu-id="36e7c-169">下列程式碼是一部分[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/CascadeDelete/)，可以下載並執行。</span><span class="sxs-lookup"><span data-stu-id="36e7c-169">The code below is part of a [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/CascadeDelete/) that can be downloaded and run.</span></span> <span data-ttu-id="36e7c-170">此範例會示範當刪除父實體時，每個刪除行為選擇性及必要的關聯性會發生什麼事。</span><span class="sxs-lookup"><span data-stu-id="36e7c-170">The sample shows what happens for each delete behavior for both optional and required relationships when a parent entity is deleted.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/CascadeDelete/Sample.cs#DeleteBehaviorVariations)]

<span data-ttu-id="36e7c-171">讓我們逐步解說來了解這為什麼每個變化。</span><span class="sxs-lookup"><span data-stu-id="36e7c-171">Let's walk through each variation to understand what is happening.</span></span>

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a><span data-ttu-id="36e7c-172">DeleteBehavior.Cascade 必要或選用的關聯性</span><span class="sxs-lookup"><span data-stu-id="36e7c-172">DeleteBehavior.Cascade with required or optional relationship</span></span>

```
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
    DELETE FROM [Posts] WHERE [PostId] = 1
    DELETE FROM [Posts] WHERE [PostId] = 2
    DELETE FROM [Blogs] WHERE [BlogId] = 1

  After SaveChanges:
    Blog '1' is in state Detached with 2 posts referenced.
      Post '1' is in state Detached with FK '1' and no reference to a blog.
      Post '1' is in state Detached with FK '1' and no reference to a blog.
```

* <span data-ttu-id="36e7c-173">部落格會標示為已刪除</span><span class="sxs-lookup"><span data-stu-id="36e7c-173">Blog is marked as Deleted</span></span>
* <span data-ttu-id="36e7c-174">文章一開始保持未變更，因為串聯，聯集不會發生直到 SaveChanges</span><span class="sxs-lookup"><span data-stu-id="36e7c-174">Posts initially remain Unchanged since cascades do not happen until SaveChanges</span></span>
* <span data-ttu-id="36e7c-175">SaveChanges 傳送刪除相依項目/子系 （文章） 和然後主體/父 （部落格）</span><span class="sxs-lookup"><span data-stu-id="36e7c-175">SaveChanges sends deletes for both dependents/children (posts) and then the principal/parent (blog)</span></span>
* <span data-ttu-id="36e7c-176">儲存之後，所有實體已卸都離，因為它們現在已從資料庫刪除</span><span class="sxs-lookup"><span data-stu-id="36e7c-176">After saving, all entities are detached since they have now been deleted from the database</span></span>

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a><span data-ttu-id="36e7c-177">DeleteBehavior.ClientSetNull 或 DeleteBehavior.SetNull 與必要的關聯性</span><span class="sxs-lookup"><span data-stu-id="36e7c-177">DeleteBehavior.ClientSetNull or DeleteBehavior.SetNull with required relationship</span></span>

```
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1

  SaveChanges threw DbUpdateException: Cannot insert the value NULL into column 'BlogId', table 'EFSaving.CascadeDelete.dbo.Posts'; column does not allow nulls. UPDATE fails. The statement has been terminated.
```

* <span data-ttu-id="36e7c-178">部落格會標示為已刪除</span><span class="sxs-lookup"><span data-stu-id="36e7c-178">Blog is marked as Deleted</span></span>
* <span data-ttu-id="36e7c-179">文章一開始保持未變更，因為串聯，聯集不會發生直到 SaveChanges</span><span class="sxs-lookup"><span data-stu-id="36e7c-179">Posts initially remain Unchanged since cascades do not happen until SaveChanges</span></span>
* <span data-ttu-id="36e7c-180">SaveChanges 嘗試將 post FK 設定為 null，但這會失敗，因為 FK 不是可為 null</span><span class="sxs-lookup"><span data-stu-id="36e7c-180">SaveChanges attempts to set the post FK to null, but this fails because the FK is not nullable</span></span>

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a><span data-ttu-id="36e7c-181">DeleteBehavior.ClientSetNull 或 DeleteBehavior.SetNull 與選擇性的關聯性</span><span class="sxs-lookup"><span data-stu-id="36e7c-181">DeleteBehavior.ClientSetNull or DeleteBehavior.SetNull with optional relationship</span></span>

```
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 2
    DELETE FROM [Blogs] WHERE [BlogId] = 1

  After SaveChanges:
    Blog '1' is in state Detached with 2 posts referenced.
      Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
      Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
```

* <span data-ttu-id="36e7c-182">部落格會標示為已刪除</span><span class="sxs-lookup"><span data-stu-id="36e7c-182">Blog is marked as Deleted</span></span>
* <span data-ttu-id="36e7c-183">文章一開始保持未變更，因為串聯，聯集不會發生直到 SaveChanges</span><span class="sxs-lookup"><span data-stu-id="36e7c-183">Posts initially remain Unchanged since cascades do not happen until SaveChanges</span></span>
* <span data-ttu-id="36e7c-184">SaveChanges 嘗試設定為 null 的兩個相依性/子系 （文章） FK，然後再刪除主體/父 （部落格）</span><span class="sxs-lookup"><span data-stu-id="36e7c-184">SaveChanges attempts sets the FK of both dependents/children (posts) to null before deleting the principal/parent (blog)</span></span>
* <span data-ttu-id="36e7c-185">儲存之後，主體/父系 （部落格） 被刪除，但仍然會追蹤相依性/子系 （文章）</span><span class="sxs-lookup"><span data-stu-id="36e7c-185">After saving, the principal/parent (blog) is deleted, but the dependents/children (posts) are still tracked</span></span>
* <span data-ttu-id="36e7c-186">追蹤相依性/子系 （文章） 現在有 null FK 值，並已移除其參考已刪除主體/父 （部落格）</span><span class="sxs-lookup"><span data-stu-id="36e7c-186">The tracked dependents/children (posts) now have null FK values and their reference to the deleted principal/parent (blog) has been removed</span></span>

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a><span data-ttu-id="36e7c-187">DeleteBehavior.Restrict 必要或選用的關聯性</span><span class="sxs-lookup"><span data-stu-id="36e7c-187">DeleteBehavior.Restrict with required or optional relationship</span></span>

```
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
  SaveChanges threw InvalidOperationException: The association between entity types 'Blog' and 'Post' has been severed but the foreign key for this relationship cannot be set to null. If the dependent entity should be deleted, then setup the relationship to use cascade deletes.
```

* <span data-ttu-id="36e7c-188">部落格會標示為已刪除</span><span class="sxs-lookup"><span data-stu-id="36e7c-188">Blog is marked as Deleted</span></span>
* <span data-ttu-id="36e7c-189">文章一開始保持未變更，因為串聯，聯集不會發生直到 SaveChanges</span><span class="sxs-lookup"><span data-stu-id="36e7c-189">Posts initially remain Unchanged since cascades do not happen until SaveChanges</span></span>
* <span data-ttu-id="36e7c-190">因為*限制*告訴 EF 不會自動將 FK 設定為 null，則它仍然非 null，而不儲存的 SaveChanges 擲回</span><span class="sxs-lookup"><span data-stu-id="36e7c-190">Since *Restrict* tells EF to not automatically set the FK to null, it remains non-null and SaveChanges throws without saving</span></span>

## <a name="delete-orphans-examples"></a><span data-ttu-id="36e7c-191">刪除孤立的範例</span><span class="sxs-lookup"><span data-stu-id="36e7c-191">Delete orphans examples</span></span>

<span data-ttu-id="36e7c-192">下列程式碼是一部分[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/CascadeDelete/)可以下載可執行的。</span><span class="sxs-lookup"><span data-stu-id="36e7c-192">The code below is part of a [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/CascadeDelete/) that can be downloaded an run.</span></span> <span data-ttu-id="36e7c-193">此範例會示範切斷父/主體及其子系/相依性之間的關聯性時，每個刪除行為選擇性及必要的關聯性會發生什麼事。</span><span class="sxs-lookup"><span data-stu-id="36e7c-193">The sample shows what happens for each delete behavior for both optional and required relationships when the relationship between a parent/principal and its children/dependents is severed.</span></span> <span data-ttu-id="36e7c-194">在此範例中，關聯性切斷移除主體/父系 （部落格） 上的集合導覽屬性的相依性/子系 （文章）。</span><span class="sxs-lookup"><span data-stu-id="36e7c-194">In this example, the relationship is severed by removing the dependents/children (posts) from the collection navigation property on the principal/parent (blog).</span></span> <span data-ttu-id="36e7c-195">不過，行為會是相同，如果主體/父代參考從相依式改為 nulled 出。</span><span class="sxs-lookup"><span data-stu-id="36e7c-195">However, the behavior is the same if the reference from dependent/child to principal/parent is instead nulled out.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/CascadeDelete/Sample.cs#DeleteOrphansVariations)]

<span data-ttu-id="36e7c-196">讓我們逐步解說來了解這為什麼每個變化。</span><span class="sxs-lookup"><span data-stu-id="36e7c-196">Let's walk through each variation to understand what is happening.</span></span>

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a><span data-ttu-id="36e7c-197">DeleteBehavior.Cascade 必要或選用的關聯性</span><span class="sxs-lookup"><span data-stu-id="36e7c-197">DeleteBehavior.Cascade with required or optional relationship</span></span>

```
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK '1' and no reference to a blog.
      Post '1' is in state Modified with FK '1' and no reference to a blog.

  Saving changes:
    DELETE FROM [Posts] WHERE [PostId] = 1
    DELETE FROM [Posts] WHERE [PostId] = 2

  After SaveChanges:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Detached with FK '1' and no reference to a blog.
      Post '1' is in state Detached with FK '1' and no reference to a blog.
```

* <span data-ttu-id="36e7c-198">因為英國關聯性標示為 null FK 文章標示為已修改</span><span class="sxs-lookup"><span data-stu-id="36e7c-198">Posts are marked as Modified because severing the relationship caused the FK to be marked as null</span></span>
  * <span data-ttu-id="36e7c-199">如果 FK 不是可為 null，然後實際的值不會變更即使標示為 null</span><span class="sxs-lookup"><span data-stu-id="36e7c-199">If the FK is not nullable, then the actual value will not change even though it is marked as null</span></span>
* <span data-ttu-id="36e7c-200">SaveChanges 傳送刪除相依項目/子系 （文章）</span><span class="sxs-lookup"><span data-stu-id="36e7c-200">SaveChanges sends deletes for dependents/children (posts)</span></span>
* <span data-ttu-id="36e7c-201">儲存之後，相依性/子系 （文章） 會卸離，因為它們現在已從資料庫刪除</span><span class="sxs-lookup"><span data-stu-id="36e7c-201">After saving, the dependents/children (posts) are detached since they have now been deleted from the database</span></span>

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a><span data-ttu-id="36e7c-202">DeleteBehavior.ClientSetNull 或 DeleteBehavior.SetNull 與必要的關聯性</span><span class="sxs-lookup"><span data-stu-id="36e7c-202">DeleteBehavior.ClientSetNull or DeleteBehavior.SetNull with required relationship</span></span>

```
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK 'null' and no reference to a blog.
      Post '1' is in state Modified with FK 'null' and no reference to a blog.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1

  SaveChanges threw DbUpdateException: Cannot insert the value NULL into column 'BlogId', table 'EFSaving.CascadeDelete.dbo.Posts'; column does not allow nulls. UPDATE fails. The statement has been terminated.
```

* <span data-ttu-id="36e7c-203">因為英國關聯性標示為 null FK 文章標示為已修改</span><span class="sxs-lookup"><span data-stu-id="36e7c-203">Posts are marked as Modified because severing the relationship caused the FK to be marked as null</span></span>
  * <span data-ttu-id="36e7c-204">如果 FK 不是可為 null，然後實際的值不會變更即使標示為 null</span><span class="sxs-lookup"><span data-stu-id="36e7c-204">If the FK is not nullable, then the actual value will not change even though it is marked as null</span></span>
* <span data-ttu-id="36e7c-205">SaveChanges 嘗試將 post FK 設定為 null，但這會失敗，因為 FK 不是可為 null</span><span class="sxs-lookup"><span data-stu-id="36e7c-205">SaveChanges attempts to set the post FK to null, but this fails because the FK is not nullable</span></span>

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a><span data-ttu-id="36e7c-206">DeleteBehavior.ClientSetNull 或 DeleteBehavior.SetNull 與選擇性的關聯性</span><span class="sxs-lookup"><span data-stu-id="36e7c-206">DeleteBehavior.ClientSetNull or DeleteBehavior.SetNull with optional relationship</span></span>

```
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK 'null' and no reference to a blog.
      Post '1' is in state Modified with FK 'null' and no reference to a blog.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 2

  After SaveChanges:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
      Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
```

* <span data-ttu-id="36e7c-207">因為英國關聯性標示為 null FK 文章標示為已修改</span><span class="sxs-lookup"><span data-stu-id="36e7c-207">Posts are marked as Modified because severing the relationship caused the FK to be marked as null</span></span>
  * <span data-ttu-id="36e7c-208">如果 FK 不是可為 null，然後實際的值不會變更即使標示為 null</span><span class="sxs-lookup"><span data-stu-id="36e7c-208">If the FK is not nullable, then the actual value will not change even though it is marked as null</span></span>
* <span data-ttu-id="36e7c-209">SaveChanges 設定這兩個相依性/子系 （文章） 的 FK 設為 null</span><span class="sxs-lookup"><span data-stu-id="36e7c-209">SaveChanges sets the FK of both dependents/children (posts) to null</span></span>
* <span data-ttu-id="36e7c-210">儲存之後，現在有相依性/子系 （文章） 的 null FK 值且已移除其參考已刪除主體/父 （部落格）</span><span class="sxs-lookup"><span data-stu-id="36e7c-210">After saving, the dependents/children (posts) now have null FK values and their reference to the deleted principal/parent (blog) has been removed</span></span>

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a><span data-ttu-id="36e7c-211">DeleteBehavior.Restrict 必要或選用的關聯性</span><span class="sxs-lookup"><span data-stu-id="36e7c-211">DeleteBehavior.Restrict with required or optional relationship</span></span>

```
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK '1' and no reference to a blog.
      Post '1' is in state Modified with FK '1' and no reference to a blog.

  Saving changes:
  SaveChanges threw InvalidOperationException: The association between entity types 'Blog' and 'Post' has been severed but the foreign key for this relationship cannot be set to null. If the dependent entity should be deleted, then setup the relationship to use cascade deletes.
```

* <span data-ttu-id="36e7c-212">因為英國關聯性標示為 null FK 文章標示為已修改</span><span class="sxs-lookup"><span data-stu-id="36e7c-212">Posts are marked as Modified because severing the relationship caused the FK to be marked as null</span></span>
  * <span data-ttu-id="36e7c-213">如果 FK 不是可為 null，然後實際的值不會變更即使標示為 null</span><span class="sxs-lookup"><span data-stu-id="36e7c-213">If the FK is not nullable, then the actual value will not change even though it is marked as null</span></span>
* <span data-ttu-id="36e7c-214">因為*限制*告訴 EF 不會自動將 FK 設定為 null，則它仍然非 null，而不儲存的 SaveChanges 擲回</span><span class="sxs-lookup"><span data-stu-id="36e7c-214">Since *Restrict* tells EF to not automatically set the FK to null, it remains non-null and SaveChanges throws without saving</span></span>

## <a name="cascading-to-untracked-entities"></a><span data-ttu-id="36e7c-215">階層式未被追蹤的實體</span><span class="sxs-lookup"><span data-stu-id="36e7c-215">Cascading to untracked entities</span></span>

<span data-ttu-id="36e7c-216">當您呼叫*SaveChanges*，串聯刪除規則將套用到內容正在追蹤的任何實體。</span><span class="sxs-lookup"><span data-stu-id="36e7c-216">When you call *SaveChanges*, the cascade delete rules will be applied to any entities that are being tracked by the context.</span></span> <span data-ttu-id="36e7c-217">這是在所有情況的範例中，如上所示，這就是為什麼已產生 sql 陳述式來刪除主體/父 （部落格） 和所有相依項目/子系 （文章）：</span><span class="sxs-lookup"><span data-stu-id="36e7c-217">This is the situation in all the examples shown above, which is why SQL was generated to delete both the principal/parent (blog) and all the dependents/children (posts):</span></span>

```sql
    DELETE FROM [Posts] WHERE [PostId] = 1
    DELETE FROM [Posts] WHERE [PostId] = 2
    DELETE FROM [Blogs] WHERE [BlogId] = 1
```

<span data-ttu-id="36e7c-218">如果只有主體已載入-例如，查詢進行時不含部落格`Include(b => b.Posts)`成也包括文章-然後 SaveChanges 只會產生 SQL 來刪除主體/父系：</span><span class="sxs-lookup"><span data-stu-id="36e7c-218">If only the principal is loaded--for example, when a query is made for a blog without an `Include(b => b.Posts)` to also include posts--then SaveChanges will only generate SQL to delete the principal/parent:</span></span>

```sql
    DELETE FROM [Blogs] WHERE [BlogId] = 1
```

<span data-ttu-id="36e7c-219">如果資料庫有對應的重疊顯示行為設定，只會刪除相依項目/子系 （文章）。</span><span class="sxs-lookup"><span data-stu-id="36e7c-219">The dependents/children (posts) will only be deleted if the database has a corresponding cascade behavior configured.</span></span> <span data-ttu-id="36e7c-220">如果您使用 EF 來建立資料庫時，此重疊顯示行為會為您的安裝程式。</span><span class="sxs-lookup"><span data-stu-id="36e7c-220">If you use EF to create the database, this cascade behavior will be setup for you.</span></span>
