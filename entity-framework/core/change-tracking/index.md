---
title: 變更追蹤-EF Core
description: EF Core 的變更追蹤總覽
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/index
ms.openlocfilehash: ccd14947cf53e3f8a38d2e0ddf2601b29e845775
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024442"
---
# <a name="change-tracking-in-ef-core"></a><span data-ttu-id="a8352-103">EF Core 中的變更追蹤</span><span class="sxs-lookup"><span data-stu-id="a8352-103">Change Tracking in EF Core</span></span>

<span data-ttu-id="a8352-104">每個 <xref:Microsoft.EntityFrameworkCore.DbContext> 實例都會追蹤對實體所做的變更。</span><span class="sxs-lookup"><span data-stu-id="a8352-104">Each <xref:Microsoft.EntityFrameworkCore.DbContext> instance tracks changes made to entities.</span></span> <span data-ttu-id="a8352-105">接著，這些追蹤的實體會在呼叫時驅動資料庫的變更 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 。</span><span class="sxs-lookup"><span data-stu-id="a8352-105">These tracked entities in turn drive the changes to the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span>

<span data-ttu-id="a8352-106">本檔概述 Entity Framework Core (EF Core) 變更追蹤，以及它與查詢和更新之間的關聯。</span><span class="sxs-lookup"><span data-stu-id="a8352-106">This document presents an overview of Entity Framework Core (EF Core) change tracking and how it relates to queries and updates.</span></span>

> [!TIP]
> <span data-ttu-id="a8352-107">您可以 [從 GitHub 下載範例程式碼](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/ChangeTrackingInEFCore)，以執行並偵測到本檔中的所有程式碼。</span><span class="sxs-lookup"><span data-stu-id="a8352-107">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/ChangeTrackingInEFCore).</span></span>

> [!TIP]
> <span data-ttu-id="a8352-108">為了簡單起見，本檔會使用和參考同步方法（例如）， <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 而不是其非同步對應專案（例如） <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="a8352-108">For simplicity, this document uses and references synchronous methods such as <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> rather than their async equivalents such as <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>.</span></span> <span data-ttu-id="a8352-109">除非另有說明，否則呼叫和等候 async 方法可以替代。</span><span class="sxs-lookup"><span data-stu-id="a8352-109">Calling and awaiting the async method can be substituted unless otherwise noted.</span></span>

## <a name="how-to-track-entities"></a><span data-ttu-id="a8352-110">如何追蹤實體</span><span class="sxs-lookup"><span data-stu-id="a8352-110">How to track entities</span></span>

<span data-ttu-id="a8352-111">當實體實例為時，就會進行追蹤：</span><span class="sxs-lookup"><span data-stu-id="a8352-111">Entity instances become tracked when they are:</span></span>

- <span data-ttu-id="a8352-112">從針對資料庫執行的查詢傳回</span><span class="sxs-lookup"><span data-stu-id="a8352-112">Returned from a query executed against the database</span></span>
- <span data-ttu-id="a8352-113">由 `Add` 、 `Attach` 、 `Update` 或類似方法明確附加至 DbCoNtext</span><span class="sxs-lookup"><span data-stu-id="a8352-113">Explicitly attached to the DbContext by `Add`, `Attach`, `Update`, or similar methods</span></span>
- <span data-ttu-id="a8352-114">偵測到連接到現有追蹤實體的新實體</span><span class="sxs-lookup"><span data-stu-id="a8352-114">Detected as new entities connected to existing tracked entities</span></span>

<span data-ttu-id="a8352-115">當下列情況時，不會再追蹤實體實例：</span><span class="sxs-lookup"><span data-stu-id="a8352-115">Entity instances are no longer tracked when:</span></span>

- <span data-ttu-id="a8352-116">已處置 DbCoNtext</span><span class="sxs-lookup"><span data-stu-id="a8352-116">The DbContext is disposed</span></span>
- <span data-ttu-id="a8352-117"> (EF Core 5.0 和更新版本時，會清除變更追蹤器) </span><span class="sxs-lookup"><span data-stu-id="a8352-117">The change tracker is cleared (EF Core 5.0 and later)</span></span>
- <span data-ttu-id="a8352-118">實體已明確卸離</span><span class="sxs-lookup"><span data-stu-id="a8352-118">The entities are explicitly detached</span></span>

<span data-ttu-id="a8352-119">DbCoNtext 的設計目的是代表短期的工作單位，如 [DbCoNtext 初始化和](xref:core/dbcontext-configuration/index)設定中所述。</span><span class="sxs-lookup"><span data-stu-id="a8352-119">DbContext is designed to represent a short-lived unit-of-work, as described in [DbContext Initialization and Configuration](xref:core/dbcontext-configuration/index).</span></span> <span data-ttu-id="a8352-120">這表示處置 DbCoNtext 是停止追蹤實體 _的正常方式_ 。</span><span class="sxs-lookup"><span data-stu-id="a8352-120">This means that disposing the DbContext is _the normal way_ to stop tracking entities.</span></span> <span data-ttu-id="a8352-121">換句話說，DbCoNtext 的存留期應該是：</span><span class="sxs-lookup"><span data-stu-id="a8352-121">In other words, the lifetime of a DbContext should be:</span></span>

1. <span data-ttu-id="a8352-122">建立 DbCoNtext 實例</span><span class="sxs-lookup"><span data-stu-id="a8352-122">Create the DbContext instance</span></span>
2. <span data-ttu-id="a8352-123">追蹤某些實體</span><span class="sxs-lookup"><span data-stu-id="a8352-123">Track some entities</span></span>
3. <span data-ttu-id="a8352-124">對實體進行一些變更</span><span class="sxs-lookup"><span data-stu-id="a8352-124">Make some changes to the entities</span></span>
4. <span data-ttu-id="a8352-125">呼叫 SaveChanges 以更新資料庫</span><span class="sxs-lookup"><span data-stu-id="a8352-125">Call SaveChanges to update the database</span></span>
5. <span data-ttu-id="a8352-126">處置 DbCoNtext 實例</span><span class="sxs-lookup"><span data-stu-id="a8352-126">Dispose the DbContext instance</span></span>

> [!TIP]
> <span data-ttu-id="a8352-127">採用這種方法時，不需要清除變更追蹤器或明確卸離實體實例。</span><span class="sxs-lookup"><span data-stu-id="a8352-127">It is not necessary to clear the change tracker or explicitly detach entity instances when taking this approach.</span></span> <span data-ttu-id="a8352-128">但是，如果您需要卸離實體，則呼叫 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType> 會比將實體逐一卸離的效率更高。</span><span class="sxs-lookup"><span data-stu-id="a8352-128">However, if you do need to detach entities, then calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType> is more efficient than detaching entities one-by-one.</span></span>

## <a name="entity-states"></a><span data-ttu-id="a8352-129">實體狀態</span><span class="sxs-lookup"><span data-stu-id="a8352-129">Entity states</span></span>

<span data-ttu-id="a8352-130">每個實體都與指定的相關聯 <xref:Microsoft.EntityFrameworkCore.EntityState> ：</span><span class="sxs-lookup"><span data-stu-id="a8352-130">Every entity is associated with a given <xref:Microsoft.EntityFrameworkCore.EntityState>:</span></span>

- <span data-ttu-id="a8352-131">`Detached` 未追蹤實體 <xref:Microsoft.EntityFrameworkCore.DbContext> 。</span><span class="sxs-lookup"><span data-stu-id="a8352-131">`Detached` entities are not being tracked by the <xref:Microsoft.EntityFrameworkCore.DbContext>.</span></span>
- <span data-ttu-id="a8352-132">`Added` 實體是新的，而且尚未插入資料庫中。</span><span class="sxs-lookup"><span data-stu-id="a8352-132">`Added` entities are new and have not yet been inserted into the database.</span></span> <span data-ttu-id="a8352-133">這表示在呼叫時將會插入它們 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 。</span><span class="sxs-lookup"><span data-stu-id="a8352-133">This means they will be inserted when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span>
- <span data-ttu-id="a8352-134">`Unchanged` 實體從資料庫查詢 _以來尚未變更_ 。</span><span class="sxs-lookup"><span data-stu-id="a8352-134">`Unchanged` entities have _not_ been changed since they were queried from the database.</span></span> <span data-ttu-id="a8352-135">從查詢傳回的所有實體一開始都會處於此狀態。</span><span class="sxs-lookup"><span data-stu-id="a8352-135">All entities returned from queries are initially in this state.</span></span>
- <span data-ttu-id="a8352-136">`Modified` 實體自資料庫查詢之後已經變更。</span><span class="sxs-lookup"><span data-stu-id="a8352-136">`Modified` entities have been changed since they were queried from the database.</span></span> <span data-ttu-id="a8352-137">這表示在呼叫 SaveChanges 時將會更新它們。</span><span class="sxs-lookup"><span data-stu-id="a8352-137">This means they will be updated when SaveChanges is called.</span></span>
- <span data-ttu-id="a8352-138">`Deleted` 實體存在於資料庫中，但在呼叫 SaveChanges 時標示為已刪除。</span><span class="sxs-lookup"><span data-stu-id="a8352-138">`Deleted` entities exist in the database, but are marked to be deleted when SaveChanges is called.</span></span>

<span data-ttu-id="a8352-139">EF Core 會追蹤屬性層級的變更。</span><span class="sxs-lookup"><span data-stu-id="a8352-139">EF Core tracks changes at the property level.</span></span> <span data-ttu-id="a8352-140">例如，如果只修改了單一屬性值，資料庫更新將只會變更該值。</span><span class="sxs-lookup"><span data-stu-id="a8352-140">For example, if only a single property value is modified, then a database update will change only that value.</span></span> <span data-ttu-id="a8352-141">不過，只有當實體本身處於修改狀態時，才能將屬性標示為已修改。</span><span class="sxs-lookup"><span data-stu-id="a8352-141">However, properties can only be marked as modified when the entity itself is in the Modified state.</span></span> <span data-ttu-id="a8352-142"> (或從替代的觀點來看，修改的狀態表示至少有一個屬性值標示為已修改。 ) </span><span class="sxs-lookup"><span data-stu-id="a8352-142">(Or, from an alternate perspective, the Modified state means that at least one property value has been marked as modified.)</span></span>

<span data-ttu-id="a8352-143">下表摘要說明不同的狀態：</span><span class="sxs-lookup"><span data-stu-id="a8352-143">The following table summarizes the different states:</span></span>

| <span data-ttu-id="a8352-144">實體狀態</span><span class="sxs-lookup"><span data-stu-id="a8352-144">Entity state</span></span>     | <span data-ttu-id="a8352-145">DbCoNtext 追蹤</span><span class="sxs-lookup"><span data-stu-id="a8352-145">Tracked by DbContext</span></span> | <span data-ttu-id="a8352-146">存在於資料庫中</span><span class="sxs-lookup"><span data-stu-id="a8352-146">Exists in database</span></span> | <span data-ttu-id="a8352-147">修改的屬性</span><span class="sxs-lookup"><span data-stu-id="a8352-147">Properties modified</span></span> | <span data-ttu-id="a8352-148">SaveChanges 上的動作</span><span class="sxs-lookup"><span data-stu-id="a8352-148">Action on SaveChanges</span></span>
|:-----------------|----------------------|--------------------|---------------------|-----------------------
| `Detached`       | <span data-ttu-id="a8352-149">否</span><span class="sxs-lookup"><span data-stu-id="a8352-149">No</span></span>                   | -                  | -                   | -
| `Added`          | <span data-ttu-id="a8352-150">是</span><span class="sxs-lookup"><span data-stu-id="a8352-150">Yes</span></span>                  | <span data-ttu-id="a8352-151">否</span><span class="sxs-lookup"><span data-stu-id="a8352-151">No</span></span>                 | -                   | <span data-ttu-id="a8352-152">插入</span><span class="sxs-lookup"><span data-stu-id="a8352-152">Insert</span></span>
| `Unchanged`      | <span data-ttu-id="a8352-153">是</span><span class="sxs-lookup"><span data-stu-id="a8352-153">Yes</span></span>                  | <span data-ttu-id="a8352-154">是</span><span class="sxs-lookup"><span data-stu-id="a8352-154">Yes</span></span>                | <span data-ttu-id="a8352-155">否</span><span class="sxs-lookup"><span data-stu-id="a8352-155">No</span></span>                  | -
| `Modified`       | <span data-ttu-id="a8352-156">是</span><span class="sxs-lookup"><span data-stu-id="a8352-156">Yes</span></span>                  | <span data-ttu-id="a8352-157">是</span><span class="sxs-lookup"><span data-stu-id="a8352-157">Yes</span></span>                | <span data-ttu-id="a8352-158">是</span><span class="sxs-lookup"><span data-stu-id="a8352-158">Yes</span></span>                 | <span data-ttu-id="a8352-159">更新</span><span class="sxs-lookup"><span data-stu-id="a8352-159">Update</span></span>
| `Deleted`        | <span data-ttu-id="a8352-160">是</span><span class="sxs-lookup"><span data-stu-id="a8352-160">Yes</span></span>                  | <span data-ttu-id="a8352-161">是</span><span class="sxs-lookup"><span data-stu-id="a8352-161">Yes</span></span>                | -                   | <span data-ttu-id="a8352-162">刪除</span><span class="sxs-lookup"><span data-stu-id="a8352-162">Delete</span></span>

> [!NOTE]
> <span data-ttu-id="a8352-163">為了清楚起見，此文字使用關係資料庫詞彙。</span><span class="sxs-lookup"><span data-stu-id="a8352-163">This text uses relational database terms for clarity.</span></span> <span data-ttu-id="a8352-164">NoSQL 資料庫通常會支援類似的作業，但可能會有不同的名稱。</span><span class="sxs-lookup"><span data-stu-id="a8352-164">NoSQL databases typically support similar operations but possibly with different names.</span></span> <span data-ttu-id="a8352-165">如需詳細資訊，請參閱您的資料庫提供者檔。</span><span class="sxs-lookup"><span data-stu-id="a8352-165">Consult your database provider documentation for more information.</span></span>

## <a name="tracking-from-queries"></a><span data-ttu-id="a8352-166">從查詢追蹤</span><span class="sxs-lookup"><span data-stu-id="a8352-166">Tracking from queries</span></span>

<span data-ttu-id="a8352-167">當相同的 <xref:Microsoft.EntityFrameworkCore.DbContext> 實例用於查詢實體，並藉由呼叫來更新它們時，EF Core 變更追蹤的效果最佳。 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A></span><span class="sxs-lookup"><span data-stu-id="a8352-167">EF Core change tracking works best when the same <xref:Microsoft.EntityFrameworkCore.DbContext> instance is used to both query for entities and update them by calling <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>.</span></span> <span data-ttu-id="a8352-168">這是因為 EF Core 會自動追蹤查詢實體的狀態，然後在呼叫 SaveChanges 時，偵測對這些實體所做的任何變更。</span><span class="sxs-lookup"><span data-stu-id="a8352-168">This is because EF Core automatically tracks the state of queried entities and then detects any changes made to these entities when SaveChanges is called.</span></span>

<span data-ttu-id="a8352-169">這種方法有幾個優點，而不是 [明確追蹤實體實例](xref:core/change-tracking/explicit-tracking)：</span><span class="sxs-lookup"><span data-stu-id="a8352-169">This approach has several advantages over [explicitly tracking entity instances](xref:core/change-tracking/explicit-tracking):</span></span>

- <span data-ttu-id="a8352-170">它為簡單式。</span><span class="sxs-lookup"><span data-stu-id="a8352-170">It is simple.</span></span> <span data-ttu-id="a8352-171">實體狀態很少需要明確操作--EF Core 會負責狀態變更。</span><span class="sxs-lookup"><span data-stu-id="a8352-171">Entity states rarely need to be manipulated explicitly--EF Core takes care of state changes.</span></span>
- <span data-ttu-id="a8352-172">更新僅限於實際變更的值。</span><span class="sxs-lookup"><span data-stu-id="a8352-172">Updates are limited to only those values that have actually changed.</span></span>
- <span data-ttu-id="a8352-173">系統會保留 [陰影屬性](xref:core/modeling/shadow-properties) 的值，並視需要加以使用。</span><span class="sxs-lookup"><span data-stu-id="a8352-173">The values of [shadow properties](xref:core/modeling/shadow-properties) are preserved and used as needed.</span></span> <span data-ttu-id="a8352-174">當外鍵儲存在陰影狀態時，這會特別相關。</span><span class="sxs-lookup"><span data-stu-id="a8352-174">This is especially relevant when foreign keys are stored in shadow state.</span></span>
- <span data-ttu-id="a8352-175">系統會自動保留屬性的原始值，並將其用於有效率的更新。</span><span class="sxs-lookup"><span data-stu-id="a8352-175">The original values of properties are preserved automatically and used for efficient updates.</span></span>

## <a name="simple-query-and-update"></a><span data-ttu-id="a8352-176">簡單查詢和更新</span><span class="sxs-lookup"><span data-stu-id="a8352-176">Simple query and update</span></span>

<span data-ttu-id="a8352-177">例如，假設有一個簡單的 blog/post 模型：</span><span class="sxs-lookup"><span data-stu-id="a8352-177">For example, consider a simple blog/posts model:</span></span>

<!--
public class Blog
{
    public int Id { get; set; }

    public string Name { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; }
    public Blog Blog { get; set; }
}
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Model)]

<span data-ttu-id="a8352-178">我們可以使用此模型來查詢 blog 和文章，然後對資料庫進行一些更新：</span><span class="sxs-lookup"><span data-stu-id="a8352-178">We can use this model to query for blogs and posts and then make some updates to the database:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            blog.Name = ".NET Blog (Updated!)";

            foreach (var post in blog.Posts.Where(e => !e.Title.Contains("5.0")))
            {
                post.Title = post.Title.Replace("5", "5.0");
            }

            context.SaveChanges();
-->
[!code-csharp[Simple_query_and_update_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Simple_query_and_update_1)]

<span data-ttu-id="a8352-179">呼叫 SaveChanges 會導致下列資料庫更新，並使用 SQLite 作為範例資料庫：</span><span class="sxs-lookup"><span data-stu-id="a8352-179">Calling SaveChanges results in the following database updates, using SQLite as an example database:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog (Updated!)' (Size = 20)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='2' (DbType = String), @p0='Announcing F# 5.0' (Size = 17)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "Title" = @p0
WHERE "Id" = @p1;
SELECT changes();
```

<span data-ttu-id="a8352-180">[變更追蹤](xref:core/change-tracking/debug-views)器的偵錯工具可讓您以視覺化方式呈現正在追蹤的實體及其狀態。</span><span class="sxs-lookup"><span data-stu-id="a8352-180">The [change tracker debug view](xref:core/change-tracking/debug-views) is a great way visualize which entities are being tracked and what their states are.</span></span> <span data-ttu-id="a8352-181">例如，在呼叫 SaveChanges 之前，將下列程式碼插入至上述範例：</span><span class="sxs-lookup"><span data-stu-id="a8352-181">For example, inserting the following code into the sample above before calling SaveChanges:</span></span>

<!--
                context.ChangeTracker.DetectChanges();
                Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Simple_query_and_update_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Simple_query_and_update_2)]

<span data-ttu-id="a8352-182">產生下列輸出：</span><span class="sxs-lookup"><span data-stu-id="a8352-182">Generates the following output:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="a8352-183">請特別注意：</span><span class="sxs-lookup"><span data-stu-id="a8352-183">Notice specifically:</span></span>

- <span data-ttu-id="a8352-184">`Blog.Name`屬性會標示為修改的 (`Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'`) ，而這會導致 blog 處於 `Modified` 狀態。</span><span class="sxs-lookup"><span data-stu-id="a8352-184">The `Blog.Name` property is marked as modified (`Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'`), and this results in the blog being in the `Modified` state.</span></span>
- <span data-ttu-id="a8352-185">`Post.Title`Post 2 的屬性會標示為修改的 (`Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'`) ，這會導致此文章處於 `Modified` 狀態。</span><span class="sxs-lookup"><span data-stu-id="a8352-185">The `Post.Title` property of post 2 is marked as modified (`Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'`), and this results in this post being in the `Modified` state.</span></span>
- <span data-ttu-id="a8352-186">第2篇的其他屬性值並未變更，因此未標示為已修改。</span><span class="sxs-lookup"><span data-stu-id="a8352-186">The other property values of post 2 have not changed and are therefore not marked as modified.</span></span> <span data-ttu-id="a8352-187">這就是為什麼這些值不會包含在資料庫更新中的原因。</span><span class="sxs-lookup"><span data-stu-id="a8352-187">This is why these values are not included in the database update.</span></span>
- <span data-ttu-id="a8352-188">另一篇文章未以任何方式修改。</span><span class="sxs-lookup"><span data-stu-id="a8352-188">The other post was not modified in any way.</span></span> <span data-ttu-id="a8352-189">這就是為什麼它仍處於 `Unchanged` 狀態，而且不包含在資料庫更新中。</span><span class="sxs-lookup"><span data-stu-id="a8352-189">This is why it is still in the `Unchanged` state and is not included in the database update.</span></span>

## <a name="query-then-insert-update-and-delete"></a><span data-ttu-id="a8352-190">查詢、插入、更新和刪除</span><span class="sxs-lookup"><span data-stu-id="a8352-190">Query then insert, update, and delete</span></span>

<span data-ttu-id="a8352-191">如同上述範例中的更新，可以與相同工作單位中的插入和刪除結合。</span><span class="sxs-lookup"><span data-stu-id="a8352-191">Updates like those in the previous example can be combined with inserts and deletes in the same unit-of-work.</span></span> <span data-ttu-id="a8352-192">例如：</span><span class="sxs-lookup"><span data-stu-id="a8352-192">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Modify property values
            blog.Name = ".NET Blog (Updated!)";

            // Insert a new Post
            blog.Posts.Add(new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

            // Mark an existing Post as Deleted
            var postToDelete = blog.Posts.Single(e => e.Title == "Announcing F# 5");
            context.Remove(postToDelete);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();
-->
[!code-csharp[Query_then_insert_update_and_delete_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Query_then_insert_update_and_delete_1)]

<span data-ttu-id="a8352-193">在此範例中：</span><span class="sxs-lookup"><span data-stu-id="a8352-193">In this example:</span></span>

- <span data-ttu-id="a8352-194">從資料庫查詢和追蹤的 blog 和相關文章</span><span class="sxs-lookup"><span data-stu-id="a8352-194">A blog and related posts are queried from the database and tracked</span></span>
- <span data-ttu-id="a8352-195">`Blog.Name`屬性已變更</span><span class="sxs-lookup"><span data-stu-id="a8352-195">The `Blog.Name` property is changed</span></span>
- <span data-ttu-id="a8352-196">新貼文會新增至 blog 的現有文章集合</span><span class="sxs-lookup"><span data-stu-id="a8352-196">A new post is added to the collection of existing posts for the blog</span></span>
- <span data-ttu-id="a8352-197">藉由呼叫來標記現有的貼文以進行刪除 <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="a8352-197">An existing post is marked for deletion by calling <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType></span></span>

<span data-ttu-id="a8352-198">在呼叫 SaveChanges 之前再次查看 [變更追蹤](xref:core/change-tracking/debug-views) 器的偵錯工具，會顯示 EF Core 如何追蹤這些變更：</span><span class="sxs-lookup"><span data-stu-id="a8352-198">Looking again at the [change tracker debug view](xref:core/change-tracking/debug-views) before calling SaveChanges shows how EF Core is tracking these changes:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}, {Id: -2147482638}]
Post {Id: -2147482638} Added
  Id: -2147482638 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="a8352-199">請注意：</span><span class="sxs-lookup"><span data-stu-id="a8352-199">Notice that:</span></span>

- <span data-ttu-id="a8352-200">此 blog 會標示為 `Modified` 。</span><span class="sxs-lookup"><span data-stu-id="a8352-200">The blog is marked as `Modified`.</span></span> <span data-ttu-id="a8352-201">這會產生資料庫更新。</span><span class="sxs-lookup"><span data-stu-id="a8352-201">This will generate a database update.</span></span>
- <span data-ttu-id="a8352-202">Post 2 標示為 `Deleted` 。</span><span class="sxs-lookup"><span data-stu-id="a8352-202">Post 2 is marked as `Deleted`.</span></span> <span data-ttu-id="a8352-203">這會產生資料庫刪除。</span><span class="sxs-lookup"><span data-stu-id="a8352-203">This will generate a database delete.</span></span>
- <span data-ttu-id="a8352-204">具有暫存識別碼的新貼文會與 blog 1 相關聯，並標示為 `Added` 。</span><span class="sxs-lookup"><span data-stu-id="a8352-204">A new post with a temporary ID is associated with blog 1 and is marked as `Added`.</span></span> <span data-ttu-id="a8352-205">這會產生資料庫插入。</span><span class="sxs-lookup"><span data-stu-id="a8352-205">This will generate a database insert.</span></span>

<span data-ttu-id="a8352-206">這會導致下列資料庫命令 (在呼叫 SaveChanges 時使用 SQLite) ：</span><span class="sxs-lookup"><span data-stu-id="a8352-206">This results in the following database commands (using SQLite) when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog (Updated!)' (Size = 20)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 was released recently and has come with many...' (Size = 56), @p2='What's next for System.Text.Json?' (Size = 33)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

<span data-ttu-id="a8352-207">如需有關插入和刪除實體的詳細資訊，請參閱 [明確的追蹤實體](xref:core/change-tracking/explicit-tracking) 。</span><span class="sxs-lookup"><span data-stu-id="a8352-207">See [Explicitly Tracking Entities](xref:core/change-tracking/explicit-tracking) for more information on inserting and deleting entities.</span></span> <span data-ttu-id="a8352-208">如需 EF Core 如何自動偵測這類變更的詳細資訊，請參閱 [變更偵測和通知](xref:core/change-tracking/change-detection) 。</span><span class="sxs-lookup"><span data-stu-id="a8352-208">See [Change Detection and Notifications](xref:core/change-tracking/change-detection) for more information on how EF Core automatically detects changes like this.</span></span>

> [!TIP]
> <span data-ttu-id="a8352-209">呼叫 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType> 以判斷是否有任何變更會導致 SaveChanges 進行資料庫的更新。</span><span class="sxs-lookup"><span data-stu-id="a8352-209">Call <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType> to determine whether any changes have been made that will cause SaveChanges to make updates to the database.</span></span> <span data-ttu-id="a8352-210">如果 HasChanges 傳回 false，則 SaveChanges 將為無作業。</span><span class="sxs-lookup"><span data-stu-id="a8352-210">If HasChanges return false, then SaveChanges will be a no-op.</span></span>
