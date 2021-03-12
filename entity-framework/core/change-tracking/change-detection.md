---
title: 變更偵測和通知-EF Core
description: 使用 DetectChanges 或通知來偵測屬性和關聯性變更
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/change-detection
ms.openlocfilehash: fae8bdb1a89478531535b377f4ba8b02d1c848f4
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023181"
---
# <a name="change-detection-and-notifications"></a><span data-ttu-id="f3cc8-103">變更偵測和通知</span><span class="sxs-lookup"><span data-stu-id="f3cc8-103">Change Detection and Notifications</span></span>

<span data-ttu-id="f3cc8-104">每個 <xref:Microsoft.EntityFrameworkCore.DbContext> 實例都會追蹤對實體所做的變更。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-104">Each <xref:Microsoft.EntityFrameworkCore.DbContext> instance tracks changes made to entities.</span></span> <span data-ttu-id="f3cc8-105">接著，這些追蹤的實體會在呼叫時驅動資料庫的變更 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-105">These tracked entities in turn drive the changes to the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span> <span data-ttu-id="f3cc8-106">這涵蓋在 [Ef core 的變更追蹤](xref:core/change-tracking/index)中，本檔假設實體狀態和 Entity Framework CORE (ef Core) 變更追蹤的基本概念。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-106">This is covered in [Change Tracking in EF Core](xref:core/change-tracking/index), and this document assumes that entity states and the basics of Entity Framework Core (EF Core) change tracking are understood.</span></span>

<span data-ttu-id="f3cc8-107">追蹤屬性和關聯性變更需要 DbCoNtext 能夠偵測這些變更。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-107">Tracking property and relationship changes requires that the DbContext is able to detect these changes.</span></span> <span data-ttu-id="f3cc8-108">本檔涵蓋此偵測的發生方式，以及如何使用屬性通知或變更追蹤 proxy 來強制立即偵測變更。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-108">This document covers how this detection happens, as well as how to use property notifications or change-tracking proxies to force immediate detection of changes.</span></span>

> [!TIP]
> <span data-ttu-id="f3cc8-109">您可以 [從 GitHub 下載範例程式碼](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/ChangeDetectionAndNotifications)，以執行並偵測到本檔中的所有程式碼。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-109">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/ChangeDetectionAndNotifications).</span></span>

## <a name="snapshot-change-tracking"></a><span data-ttu-id="f3cc8-110">快照集變更追蹤</span><span class="sxs-lookup"><span data-stu-id="f3cc8-110">Snapshot change tracking</span></span>

<span data-ttu-id="f3cc8-111">根據預設，EF Core 會在 DbCoNtext 實例第一次追蹤時，建立每個實體屬性值的快照集。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-111">By default, EF Core creates a snapshot of every entity's property values when it is first tracked by a DbContext instance.</span></span> <span data-ttu-id="f3cc8-112">然後，此快照中儲存的值會與實體的目前值進行比較，以判斷哪些屬性值已變更。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-112">The values stored in this snapshot are then compared against the current values of the entity in order to determine which property values have changed.</span></span>

<span data-ttu-id="f3cc8-113">當呼叫 SaveChanges 以確保在將更新傳送至資料庫之前偵測到所有變更的值時，就會發生這項變更的偵測。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-113">This detection of changes happens when SaveChanges is called to ensure all changed values are detected before sending updates to the database.</span></span> <span data-ttu-id="f3cc8-114">不過，變更的偵測也會在其他時間發生，以確保應用程式能夠使用最新的追蹤資訊。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-114">However, the detection of changes also happens at other times to ensure the application is working with up-to-date tracking information.</span></span> <span data-ttu-id="f3cc8-115">您可以藉由呼叫來隨時強制偵測變更 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-115">Detection of changes can be forced at any time by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType>.</span></span>

### <a name="when-change-detection-is-needed"></a><span data-ttu-id="f3cc8-116">需要變更偵測時</span><span class="sxs-lookup"><span data-stu-id="f3cc8-116">When change detection is needed</span></span>

<span data-ttu-id="f3cc8-117">_若未使用 EF Core 來進行這項變更_，就需要在變更屬性或導覽時偵測變更。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-117">Detection of changes is needed when a property or navigation has been changed _without using EF Core to make this change_.</span></span> <span data-ttu-id="f3cc8-118">例如，請考慮載入 blog 和 post，然後對這些實體進行變更：</span><span class="sxs-lookup"><span data-stu-id="f3cc8-118">For example, consider loading blogs and posts and then making changes to these entities:</span></span>

<!--
        using var context = new BlogsContext();
        var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

        // Change a property value
        blog.Name = ".NET Blog (Updated!)";

        // Add a new entity to a navigation
        blog.Posts.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many..."
        });

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Snapshot_change_tracking_1](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=Snapshot_change_tracking_1)]

<span data-ttu-id="f3cc8-119">在呼叫之前查看 [變更追蹤器調試](xref:core/change-tracking/debug-views) 程式，會 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> 顯示尚未偵測到所做的變更，因此不會反映在實體狀態和已修改的屬性資料中：</span><span class="sxs-lookup"><span data-stu-id="f3cc8-119">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) before calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> shows that the changes made have not been detected and hence are not reflected in the entity states and modified property data:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, <not found>]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="f3cc8-120">具體而言，blog 專案的狀態仍然是 `Unchanged` ，而且新的貼文不會顯示為追蹤的實體。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-120">Specifically, the state of the blog entry is still `Unchanged`, and the new post does not appear as a tracked entity.</span></span> <span data-ttu-id="f3cc8-121"> (精明會注意到屬性會報告其新的值，即使 EF Core 尚未偵測到這些變更也是一樣。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-121">(The astute will notice properties report their new values, even though these changes have not yet been detected by EF Core.</span></span> <span data-ttu-id="f3cc8-122">這是因為 debug view 正在直接從實體實例讀取目前的值。 ) </span><span class="sxs-lookup"><span data-stu-id="f3cc8-122">This is because the debug view is reading current values directly from the entity instance.)</span></span>

<span data-ttu-id="f3cc8-123">在呼叫 DetectChanges 之後，與 debug view 對比：</span><span class="sxs-lookup"><span data-stu-id="f3cc8-123">Contrast this with the debug view after calling DetectChanges:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
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
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="f3cc8-124">現在，已正確地將 blog 標記為 `Modified` ，並偵測到新文章，並將其追蹤為 `Added` 。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-124">Now the blog is correctly marked as `Modified` and the new post has been detected and is tracked as `Added`.</span></span>

<span data-ttu-id="f3cc8-125">在本節一開始，我們指出不使用 _EF Core 進行變更_ 時，需要偵測變更。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-125">At the start of this section we stated that detecting changes is needed when not using _using EF Core to make the change_.</span></span> <span data-ttu-id="f3cc8-126">這是上述程式碼中發生的情況。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-126">This is what is happening in the code above.</span></span> <span data-ttu-id="f3cc8-127">也就是說，屬性和導覽的變更會 _直接在實體實例上_ 進行，而不是使用任何 EF Core 方法。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-127">That is, the changes to the property and navigation are made _directly on the entity instances_, and not by using any EF Core methods.</span></span>

<span data-ttu-id="f3cc8-128">將此與下列程式碼相比較，以相同方式修改實體，但這次使用 EF Core 方法：</span><span class="sxs-lookup"><span data-stu-id="f3cc8-128">Contrast this to the following code which modifies the entities in the same way, but this time using EF Core methods:</span></span>

<!--
        using var context = new BlogsContext();
        var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

        // Change a property value
        context.Entry(blog).Property(e => e.Name).CurrentValue = ".NET Blog (Updated!)";

        // Add a new entity to the DbContext
        context.Add(
            new Post
            {
                Blog = blog,
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Snapshot_change_tracking_2](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=Snapshot_change_tracking_2)]

<span data-ttu-id="f3cc8-129">在此情況下，變更追蹤器偵錯工具會顯示已知所有實體狀態和屬性修改，即使尚未進行變更的偵測也是一樣。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-129">In this case the change tracker debug view shows that all entity states and property modifications are known, even though detection of changes has not happened.</span></span> <span data-ttu-id="f3cc8-130">這是因為 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.CurrentValue?displayProperty=nameWithType> 是 Ef core 方法，這表示 Ef core 會立即知道這個方法所做的變更。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-130">This is because <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.CurrentValue?displayProperty=nameWithType> is an EF Core method, which means that EF Core immediately knows about the change made by this method.</span></span> <span data-ttu-id="f3cc8-131">同樣地，呼叫 <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> 可讓 EF Core 立即知道新的實體並適當地進行追蹤。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-131">Likewise, calling <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> allows EF Core to immediately know about the new entity and track it appropriately.</span></span>

> [!TIP]
> <span data-ttu-id="f3cc8-132">請勿嘗試使用 EF Core 方法來進行實體變更，以避免偵測變更。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-132">Don't attempt to avoid detecting changes by always using EF Core methods to make entity changes.</span></span> <span data-ttu-id="f3cc8-133">這麼做通常比較麻煩，而且執行的效能比一般方式變更實體還低。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-133">Doing so is often more cumbersome and performs less well than making changes to entities in the normal way.</span></span> <span data-ttu-id="f3cc8-134">本檔的目的是要在偵測到所需的變更時通知。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-134">The intention of this document is to inform as to when detecting changes is needed and when it is not.</span></span> <span data-ttu-id="f3cc8-135">目的不是要鼓勵避免變更偵測。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-135">The intention is not to encourage avoidance of change detection.</span></span>

### <a name="methods-that-automatically-detect-changes"></a><span data-ttu-id="f3cc8-136">自動偵測變更的方法</span><span class="sxs-lookup"><span data-stu-id="f3cc8-136">Methods that automatically detect changes</span></span>

<span data-ttu-id="f3cc8-137"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> 這可能會影響結果的方法會自動呼叫。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-137"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> is called automatically by methods where doing so is likely to impact the results.</span></span> <span data-ttu-id="f3cc8-138">方法如下：</span><span class="sxs-lookup"><span data-stu-id="f3cc8-138">These methods are:</span></span>

- <span data-ttu-id="f3cc8-139"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> 和 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A?displayProperty=nameWithType> ，確保在更新資料庫之前偵測到所有變更。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-139"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A?displayProperty=nameWithType>, to ensure that all changes are detected before updating the database.</span></span>
- <span data-ttu-id="f3cc8-140"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> 和 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> ，確保實體狀態和修改的屬性都是最新的。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-140"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType>, to ensure entity states and modified properties are up-to-date.</span></span>
- <span data-ttu-id="f3cc8-141"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType>，以確定結果正確無誤。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-141"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType>, to ensure that the result is accurate.</span></span>
- <span data-ttu-id="f3cc8-142"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>，可確保主體/父實體的正確實體狀態，再進行階層級。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-142"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>, to ensure correct entity states for principal/parent entities before cascading.</span></span>
- <span data-ttu-id="f3cc8-143"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType>，以確保追蹤的圖表是最新的。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-143"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType>, to ensure that the tracked graph is up-to-date.</span></span>

<span data-ttu-id="f3cc8-144">也有一些地方的偵測變更只會在單一實體實例上發生，而不是在整個追蹤實體的圖形上進行。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-144">There are also some places where detection of changes happens on only a single entity instance, rather than on the entire graph of tracked entities.</span></span> <span data-ttu-id="f3cc8-145">這些位置包括：</span><span class="sxs-lookup"><span data-stu-id="f3cc8-145">These places are:</span></span>

- <span data-ttu-id="f3cc8-146">使用時 <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> ，確保實體的狀態和已修改的屬性都是最新的。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-146">When using <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType>, to ensure that the entity's state and modified properties are up-to-date.</span></span>
- <span data-ttu-id="f3cc8-147">使用 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> 、或等方法 `Property` `Collection` `Reference` `Member` 來確保屬性的修改、目前的值等都是最新的。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-147">When using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> methods such as `Property`, `Collection`, `Reference` or `Member` to ensure property modifications, current values, etc. are up-to-date.</span></span>
- <span data-ttu-id="f3cc8-148">當相依/子系實體因為必要的關聯性被中斷而遭到刪除時。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-148">When a dependent/child entity is going to be deleted because a required relationship has been severed.</span></span> <span data-ttu-id="f3cc8-149">這會偵測實體不應刪除的時間，因為它已重新成為父代。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-149">This detects when an entity should not be deleted because it has been re-parented.</span></span>

<span data-ttu-id="f3cc8-150">您可以藉由呼叫來明確觸發單一實體之變更的本機偵測 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-150">Local detection of changes for a single entity can be triggered explicitly by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType>.</span></span>

> [!NOTE]
> <span data-ttu-id="f3cc8-151">本機偵測變更可能會遺失完整偵測會發現的一些變更。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-151">Local detect changes can miss some changes that a full detection would find.</span></span> <span data-ttu-id="f3cc8-152">當對其他實體未偵測到的變更所造成的串聯式動作影響到有問題的實體時，就會發生這種情況。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-152">This happens when cascading actions resulting from undetected changes to other entities have an impact on the entity in question.</span></span> <span data-ttu-id="f3cc8-153">在這種情況下，應用程式可能需要明確地呼叫來強制執行所有實體的完整掃描 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-153">In such situations the application may need to force a full scan of all entities by explicitly calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType>.</span></span>

### <a name="disabling-automatic-change-detection"></a><span data-ttu-id="f3cc8-154">停用自動變更偵測</span><span class="sxs-lookup"><span data-stu-id="f3cc8-154">Disabling automatic change detection</span></span>

<span data-ttu-id="f3cc8-155">偵測變更的效能不是大部分應用程式的瓶頸。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-155">The performance of detecting changes is not a bottleneck for most applications.</span></span> <span data-ttu-id="f3cc8-156">但是，偵測變更可能會成為追蹤上千個實體的部分應用程式的效能問題。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-156">However, detecting changes can become a performance problem for some applications that track thousands of entities.</span></span> <span data-ttu-id="f3cc8-157"> (確切的數位會視許多因素而定，例如實體中的屬性數目 ) 。基於這個原因，您可以使用來停用自動偵測變更 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.AutoDetectChangesEnabled?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-157">(The exact number will dependent on many things, such as the number of properties in the entity.) For this reason the automatic detection of changes can be disabled using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.AutoDetectChangesEnabled?displayProperty=nameWithType>.</span></span> <span data-ttu-id="f3cc8-158">例如，請考慮在多對多關聯性中處理與承載的聯結實體：</span><span class="sxs-lookup"><span data-stu-id="f3cc8-158">For example, consider processing join entities in a many-to-many relationship with payloads:</span></span>

<!--
        public override int SaveChanges()
        {
            foreach (var entityEntry in ChangeTracker.Entries<PostTag>()) // Detects changes automatically
            {
                if (entityEntry.State == EntityState.Added)
                {
                    entityEntry.Entity.TaggedBy = "ajcvickers";
                    entityEntry.Entity.TaggedOn = DateTime.Now;
                }
            }

            try
            {
                ChangeTracker.AutoDetectChangesEnabled = false;
                return base.SaveChanges(); // Avoid automatically detecting changes again here
            }
            finally
            {
                ChangeTracker.AutoDetectChangesEnabled = true;
            }
        }
-->
[!code-csharp[SaveChanges](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=SaveChanges)]

<span data-ttu-id="f3cc8-159">如我們在上一節中所知， <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> 和 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> 會自動偵測變更。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-159">As we know from the previous section, both <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> automatically detect changes.</span></span> <span data-ttu-id="f3cc8-160">不過，在呼叫專案之後，程式碼不會進行任何實體或屬性狀態變更。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-160">However, after calling Entries, the code does not then make any entity or property state changes.</span></span> <span data-ttu-id="f3cc8-161"> (在新增的實體上設定一般屬性值不會造成任何狀態變更。 ) 程式碼時，就會在向下呼叫基底 SaveChanges 方法時，停用不必要的自動變更偵測。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-161">(Setting normal property values on Added entities does not cause any state changes.) The code therefore disables unnecessary automatic change detection when calling down into the base SaveChanges method.</span></span> <span data-ttu-id="f3cc8-162">程式碼也會使用 try/finally 區塊，以確保即使 SaveChanges 失敗，也會還原預設設定。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-162">The code also makes use of a try/finally block to ensure that the default setting is restored even if SaveChanges fails.</span></span>

> [!TIP]
> <span data-ttu-id="f3cc8-163">請勿假設您的程式碼必須停用自動變更偵測才能正常執行。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-163">Do not assume that your code must disable automatic change detection to to perform well.</span></span> <span data-ttu-id="f3cc8-164">只有當程式碼剖析應用程式追蹤有許多實體指出變更偵測的效能是問題時，才需要這麼做。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-164">This is only needed when profiling an application tracking many entities indicates that performance of change detection is an issue.</span></span>

### <a name="detecting-changes-and-value-conversions"></a><span data-ttu-id="f3cc8-165">偵測變更和值轉換</span><span class="sxs-lookup"><span data-stu-id="f3cc8-165">Detecting changes and value conversions</span></span>

<span data-ttu-id="f3cc8-166">若要搭配實體類型使用快照集變更追蹤，EF Core 必須能夠：</span><span class="sxs-lookup"><span data-stu-id="f3cc8-166">To use snapshot change tracking with an entity type, EF Core must be able to:</span></span>

- <span data-ttu-id="f3cc8-167">追蹤實體時，建立每個屬性值的快照集</span><span class="sxs-lookup"><span data-stu-id="f3cc8-167">Make a snapshot of each property value when the entity is tracked</span></span>
- <span data-ttu-id="f3cc8-168">將此值與屬性的目前值進行比較</span><span class="sxs-lookup"><span data-stu-id="f3cc8-168">Compare this value to the current value of the property</span></span>
- <span data-ttu-id="f3cc8-169">產生值的雜湊碼</span><span class="sxs-lookup"><span data-stu-id="f3cc8-169">Generate a hash code for the value</span></span>

<span data-ttu-id="f3cc8-170">這會由 EF Core 針對可直接對應至資料庫的類型自動處理。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-170">This is handled automatically by EF Core for types that can be directly mapped to the database.</span></span> <span data-ttu-id="f3cc8-171">不過，當 [使用值轉換器來對應屬性](xref:core/modeling/value-conversions)時，該轉換器必須指定如何執行這些動作。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-171">However, when a [value converter is used to map a property](xref:core/modeling/value-conversions), then that converter must specify how to perform these actions.</span></span> <span data-ttu-id="f3cc8-172">這可透過值比較子來達成，並在 [值](xref:core/modeling/value-comparers) 比較子檔中詳細說明。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-172">This is achieved with a value comparer, and is described in detail in the [Value Comparers](xref:core/modeling/value-comparers) documentation.</span></span>

## <a name="notification-entities"></a><span data-ttu-id="f3cc8-173">通知實體</span><span class="sxs-lookup"><span data-stu-id="f3cc8-173">Notification entities</span></span>

<span data-ttu-id="f3cc8-174">建議針對大部分的應用程式使用快照集變更追蹤。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-174">Snapshot change tracking is recommended for most applications.</span></span> <span data-ttu-id="f3cc8-175">不過，追蹤許多實體及/或對這些實體進行許多變更的應用程式，可能會受益于執行實體，這些實體會在其屬性和導覽值變更時自動通知 EF Core。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-175">However, applications that track many entities and/or make many changes to those entities may benefit from implementing entities that automatically notify EF Core when their property and navigation values change.</span></span> <span data-ttu-id="f3cc8-176">這些稱為「通知實體」。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-176">These are known as "notification entities".</span></span>

### <a name="implementing-notification-entities"></a><span data-ttu-id="f3cc8-177">執行通知實體</span><span class="sxs-lookup"><span data-stu-id="f3cc8-177">Implementing notification entities</span></span>

<span data-ttu-id="f3cc8-178">通知實體 <xref:System.ComponentModel.INotifyPropertyChanging> 會使用和 <xref:System.ComponentModel.INotifyPropertyChanged> 介面，這些都是 .net 基類庫 (BCL) 的一部分。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-178">Notification entities make use of the <xref:System.ComponentModel.INotifyPropertyChanging> and <xref:System.ComponentModel.INotifyPropertyChanged> interfaces, which are part of the .NET base class library (BCL).</span></span> <span data-ttu-id="f3cc8-179">這些介面會定義在變更屬性值之前和之後必須引發的事件。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-179">These interfaces define events that must be fired before and after changing a property value.</span></span> <span data-ttu-id="f3cc8-180">例如：</span><span class="sxs-lookup"><span data-stu-id="f3cc8-180">For example:</span></span>

<!--
    public class Blog : INotifyPropertyChanging, INotifyPropertyChanged
    {
        public event PropertyChangingEventHandler PropertyChanging;
        public event PropertyChangedEventHandler PropertyChanged;

        private int _id;
        public int Id
        {
            get => _id;
            set
            {
                PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(nameof(Id)));
                _id = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Id)));
            }
        }

        private string _name;
        public string Name
        {
            get => _name;
            set
            {
                PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(nameof(Name)));
                _name = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Name)));
            }
        }

        public IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationEntitiesSamples.cs?name=Model)]

<span data-ttu-id="f3cc8-181">此外，任何集合導覽都必須執行 `INotifyCollectionChanged` ; 在上述範例中，您可以使用貼文來滿足這項工作 <xref:System.Collections.ObjectModel.ObservableCollection%601> 。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-181">In addition, any collection navigations must implement `INotifyCollectionChanged`; in the example above this satisfied by using an <xref:System.Collections.ObjectModel.ObservableCollection%601> of posts.</span></span> <span data-ttu-id="f3cc8-182">EF Core 也隨附具有 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ObservableHashSet%601> 更有效率查閱的實作為穩定順序的成本。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-182">EF Core also ships with an <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ObservableHashSet%601> implementation that has more efficient lookups at the expense of stable ordering.</span></span>

<span data-ttu-id="f3cc8-183">大部分的通知程式碼通常都會移到未對應的基類中。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-183">Most of this notification code is typically moved into an unmapped base class.</span></span> <span data-ttu-id="f3cc8-184">例如：</span><span class="sxs-lookup"><span data-stu-id="f3cc8-184">For example:</span></span>

<!--
    public class Blog : NotifyingEntity
    {
        private int _id;
        public int Id
        {
            get => _id;
            set => SetWithNotify(value, out _id);
        }

        private string _name;
        public string Name
        {
            get => _name;
            set => SetWithNotify(value, out _name);
        }

        public IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }

    public abstract class NotifyingEntity : INotifyPropertyChanging, INotifyPropertyChanged
    {
        protected void SetWithNotify<T>(T value, out T field, [CallerMemberName] string propertyName = "")
        {
            NotifyChanging(propertyName);
            field = value;
            NotifyChanged(propertyName);
        }

        public event PropertyChangingEventHandler PropertyChanging;
        public event PropertyChangedEventHandler PropertyChanged;

        private void NotifyChanged(string propertyName)
            => PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));

        private void NotifyChanging(string propertyName)
            => PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(propertyName));
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=Model)]

### <a name="configuring-notification-entities"></a><span data-ttu-id="f3cc8-185">設定通知實體</span><span class="sxs-lookup"><span data-stu-id="f3cc8-185">Configuring notification entities</span></span>

<span data-ttu-id="f3cc8-186">EF Core 無法驗證 `INotifyPropertyChanging` 或 `INotifyPropertyChanged` 完全實作為 ef core 使用。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-186">There is no way for EF Core to validate that `INotifyPropertyChanging` or `INotifyPropertyChanged` are fully implemented for use with EF Core.</span></span> <span data-ttu-id="f3cc8-187">尤其是，這些介面的某些用途只會在某些屬性上使用通知，而不是所有屬性 (包括 EF Core 所需的導覽) 。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-187">In particular, some uses of these interfaces do so with notifications only on certain properties, rather than on all properties (including navigations) as required by EF Core.</span></span> <span data-ttu-id="f3cc8-188">基於這個理由，EF Core 不會自動連接到這些事件。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-188">For this reason, EF Core does not automatically hook into these events.</span></span>

<span data-ttu-id="f3cc8-189">相反地，EF Core 必須設定為使用這些通知實體。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-189">Instead, EF Core must be configured to use these notification entities.</span></span> <span data-ttu-id="f3cc8-190">這通常是透過呼叫來針對所有實體類型進行 <xref:Microsoft.EntityFrameworkCore.ModelBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-190">This is usually done for all entity types by calling <xref:Microsoft.EntityFrameworkCore.ModelBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="f3cc8-191">例如：</span><span class="sxs-lookup"><span data-stu-id="f3cc8-191">For example:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.HasChangeTrackingStrategy(ChangeTrackingStrategy.ChangingAndChangedNotifications);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=OnModelCreating)]

<span data-ttu-id="f3cc8-192"> (也可以使用不同的實體類型來設定不同的策略 <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType> ，但這通常是這些的，因為那些不是通知實體的類型仍需要 DetectChanges ) 。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-192">(The strategy can also be set differently for different entity types using <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType>, but this is usually counterproductive since DetectChanges is still required for those types that are not notification entities.)</span></span>

<span data-ttu-id="f3cc8-193">完整通知變更追蹤需要同時 `INotifyPropertyChanging` 執行和 `INotifyPropertyChanged` 。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-193">Full notification change tracking requires that both `INotifyPropertyChanging` and `INotifyPropertyChanged` are implemented.</span></span> <span data-ttu-id="f3cc8-194">這可讓您在屬性值變更之前儲存原始值，避免 EF Core 在追蹤實體時建立快照集的需求。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-194">This allows original values to be saved just before the property value is changed, avoiding the need for EF Core to create a snapshot when tracking the entity.</span></span> <span data-ttu-id="f3cc8-195">實作為的實體類型 `INotifyPropertyChanged` 也可以搭配 EF Core 使用。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-195">Entity types that implement only `INotifyPropertyChanged` can also be used with EF Core.</span></span> <span data-ttu-id="f3cc8-196">在這種情況下，當追蹤實體以追蹤原始值時，EF 仍會建立快照集，但接著會使用通知立即偵測變更，而不需要呼叫 DetectChanges。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-196">In this case, EF still creates a snapshot when tracking an entity to keep track of original values, but then uses the notifications to detect changes immediately, rather than needing DetectChanges to be called.</span></span>

<span data-ttu-id="f3cc8-197"><xref:Microsoft.EntityFrameworkCore.ChangeTrackingStrategy>下表摘要說明不同的值。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-197">The different <xref:Microsoft.EntityFrameworkCore.ChangeTrackingStrategy> values are summarized in the the following table.</span></span>

| <span data-ttu-id="f3cc8-198">ChangeTrackingStrategy</span><span class="sxs-lookup"><span data-stu-id="f3cc8-198">ChangeTrackingStrategy</span></span>                              | <span data-ttu-id="f3cc8-199">需要的介面</span><span class="sxs-lookup"><span data-stu-id="f3cc8-199">Interfaces needed</span></span>                                      | <span data-ttu-id="f3cc8-200">需要 DetectChanges</span><span class="sxs-lookup"><span data-stu-id="f3cc8-200">Needs DetectChanges</span></span> | <span data-ttu-id="f3cc8-201">快照集原始值</span><span class="sxs-lookup"><span data-stu-id="f3cc8-201">Snapshots original values</span></span>
|:----------------------------------------------------|--------------------------------------------------------|---------------------|--------------------------
| <span data-ttu-id="f3cc8-202">快照式</span><span class="sxs-lookup"><span data-stu-id="f3cc8-202">Snapshot</span></span>                                            | <span data-ttu-id="f3cc8-203">無</span><span class="sxs-lookup"><span data-stu-id="f3cc8-203">None</span></span>                                                   | <span data-ttu-id="f3cc8-204">是</span><span class="sxs-lookup"><span data-stu-id="f3cc8-204">Yes</span></span>                 | <span data-ttu-id="f3cc8-205">是</span><span class="sxs-lookup"><span data-stu-id="f3cc8-205">Yes</span></span>
| <span data-ttu-id="f3cc8-206">ChangedNotifications</span><span class="sxs-lookup"><span data-stu-id="f3cc8-206">ChangedNotifications</span></span>                                | <span data-ttu-id="f3cc8-207">INotifyPropertyChanged</span><span class="sxs-lookup"><span data-stu-id="f3cc8-207">INotifyPropertyChanged</span></span>                                 | <span data-ttu-id="f3cc8-208">否</span><span class="sxs-lookup"><span data-stu-id="f3cc8-208">No</span></span>                  | <span data-ttu-id="f3cc8-209">是</span><span class="sxs-lookup"><span data-stu-id="f3cc8-209">Yes</span></span>
| <span data-ttu-id="f3cc8-210">ChangingAndChangedNotifications</span><span class="sxs-lookup"><span data-stu-id="f3cc8-210">ChangingAndChangedNotifications</span></span>                     | <span data-ttu-id="f3cc8-211">INotifyPropertyChanged 和 INotifyPropertyChanging</span><span class="sxs-lookup"><span data-stu-id="f3cc8-211">INotifyPropertyChanged and INotifyPropertyChanging</span></span>     | <span data-ttu-id="f3cc8-212">否</span><span class="sxs-lookup"><span data-stu-id="f3cc8-212">No</span></span>                  | <span data-ttu-id="f3cc8-213">否</span><span class="sxs-lookup"><span data-stu-id="f3cc8-213">No</span></span>
| <span data-ttu-id="f3cc8-214">ChangingAndChangedNotificationsWithOriginalValues</span><span class="sxs-lookup"><span data-stu-id="f3cc8-214">ChangingAndChangedNotificationsWithOriginalValues</span></span>   | <span data-ttu-id="f3cc8-215">INotifyPropertyChanged 和 INotifyPropertyChanging</span><span class="sxs-lookup"><span data-stu-id="f3cc8-215">INotifyPropertyChanged and INotifyPropertyChanging</span></span>     | <span data-ttu-id="f3cc8-216">否</span><span class="sxs-lookup"><span data-stu-id="f3cc8-216">No</span></span>                  | <span data-ttu-id="f3cc8-217">是</span><span class="sxs-lookup"><span data-stu-id="f3cc8-217">Yes</span></span>

### <a name="using-notification-entities"></a><span data-ttu-id="f3cc8-218">使用通知實體</span><span class="sxs-lookup"><span data-stu-id="f3cc8-218">Using notification entities</span></span>

<span data-ttu-id="f3cc8-219">通知實體的行為就像任何其他實體，但對實體實例所做的變更不需要呼叫來偵測 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> 這些變更。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-219">Notification entities behave like any other entities, except that making changes to the entity instances do not require a call to <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> to detect these changes.</span></span> <span data-ttu-id="f3cc8-220">例如：</span><span class="sxs-lookup"><span data-stu-id="f3cc8-220">For example:</span></span>

<!--
            using var context = new BlogsContext();
            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Change a property value
            blog.Name = ".NET Blog (Updated!)";

            // Add a new entity to a navigation
            blog.Posts.Add(new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Notification_entities_2](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=Notification_entities_2)]

<span data-ttu-id="f3cc8-221">使用一般實體時， [變更追蹤器調試](xref:core/change-tracking/debug-views) 程式會顯示在呼叫 DetectChanges 之前，不會偵測到這些變更。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-221">With normal entities, the [change tracker debug view](xref:core/change-tracking/debug-views) showed that these changes were not detected until DetectChanges was called.</span></span> <span data-ttu-id="f3cc8-222">使用通知實體時查看 [偵錯工具]，會顯示已立即偵測到這些變更：</span><span class="sxs-lookup"><span data-stu-id="f3cc8-222">Looking at the debug view when notification entities are used shows that these changes have been detected immediately:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
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
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

## <a name="change-tracking-proxies"></a><span data-ttu-id="f3cc8-223">變更追蹤 proxy</span><span class="sxs-lookup"><span data-stu-id="f3cc8-223">Change-tracking proxies</span></span>

> [!NOTE]
> <span data-ttu-id="f3cc8-224">EF Core 5.0 引進了變更追蹤 proxy。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-224">Change-tracking proxies were introduced in EF Core 5.0.</span></span>

<span data-ttu-id="f3cc8-225">EF Core 可以動態產生執行和的 proxy <xref:System.ComponentModel.INotifyPropertyChanging> 類型 <xref:System.ComponentModel.INotifyPropertyChanged> 。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-225">EF Core can dynamically generate proxy types that implement <xref:System.ComponentModel.INotifyPropertyChanging> and <xref:System.ComponentModel.INotifyPropertyChanged>.</span></span> <span data-ttu-id="f3cc8-226">這需要安裝 [Microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) NuGet 套件，並啟用變更追蹤 proxy， <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> 例如：</span><span class="sxs-lookup"><span data-stu-id="f3cc8-226">This requires installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) NuGet package, and enabling change-tracking proxies with <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.UseChangeTrackingProxies();
-->
[!code-csharp[OnConfiguring](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=OnConfiguring)]

<span data-ttu-id="f3cc8-227">建立動態 proxy 牽涉到使用 [Castle](https://www.nuget.org/packages/Castle.Core/) 來建立新的動態 .net 型別 () ，它繼承自實體型別，然後覆寫所有屬性 setter。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-227">Creating a dynamic proxy involves creating a new, dynamic .NET type (using the [Castle.Core](https://www.nuget.org/packages/Castle.Core/) proxies implementation), which inherits from the entity type and then overrides all property setters.</span></span> <span data-ttu-id="f3cc8-228">因此，proxy 的實體類型必須是可繼承的型別，而且必須具有可覆寫的屬性。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-228">Entity types for proxies must therefore be types that can be inherited from and must have properties that can be overridden.</span></span> <span data-ttu-id="f3cc8-229">此外，明確建立的集合導覽必須執行 <xref:System.Collections.Specialized.INotifyCollectionChanged> 下列範例：</span><span class="sxs-lookup"><span data-stu-id="f3cc8-229">Also, collection navigations created explicitly must implement <xref:System.Collections.Specialized.INotifyCollectionChanged> For example:</span></span>

<!--
    public class Blog
    {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }

        public virtual IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }

    public class Post
    {
        public virtual int Id { get; set; }
        public virtual string Title { get; set; }
        public virtual string Content { get; set; }

        public virtual int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=Model)]

<span data-ttu-id="f3cc8-230">變更追蹤 proxy 有一個重大的缺點，就是 EF Core 一律必須追蹤 proxy 的實例，而不是基礎實體類型的實例。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-230">One significant downside to change-tracking proxies is that EF Core must always track instances of the proxy, never instances of the underlying entity type.</span></span> <span data-ttu-id="f3cc8-231">這是因為基礎實體類型的實例不會產生通知，這表示將會遺漏對這些實體所做的變更。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-231">This is because instances of the underlying entity type will not generate notifications, which means changes made to these entities will be missed.</span></span>

<span data-ttu-id="f3cc8-232">EF Core 會在查詢資料庫時自動建立 proxy 實例，因此此缺點通常僅限於追蹤新的實體實例。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-232">EF Core creates proxy instances automatically when querying the database, so this downside is generally limited to tracking new entity instances.</span></span> <span data-ttu-id="f3cc8-233">這些實例必須使用 <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.CreateProxy%2A> 擴充方法建立，而 **不** 是使用一般的方式 `new` 。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-233">These instances must be created using the <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.CreateProxy%2A> extension methods, and **not** in the normal way using `new`.</span></span> <span data-ttu-id="f3cc8-234">這表示之前範例中的程式碼現在必須使用 `CreateProxy` ：</span><span class="sxs-lookup"><span data-stu-id="f3cc8-234">This means the code from the previous examples must now make use of `CreateProxy`:</span></span>

<!--
            using var context = new BlogsContext();
            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Change a property value
            blog.Name = ".NET Blog (Updated!)";

            // Add a new entity to a navigation
            blog.Posts.Add(
                context.CreateProxy<Post>(
                    p =>
                        {
                            p.Title = "What’s next for System.Text.Json?";
                            p.Content = ".NET 5.0 was released recently and has come with many...";
                        }));

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Change_tracking_proxies_1](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=Change_tracking_proxies_1)]

## <a name="change-tracking-events"></a><span data-ttu-id="f3cc8-235">變更追蹤事件</span><span class="sxs-lookup"><span data-stu-id="f3cc8-235">Change tracking events</span></span>

<span data-ttu-id="f3cc8-236">EF Core 會在 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType> 第一次追蹤實體時引發事件。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-236">EF Core fires the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType> event when an entity is tracked for the first time.</span></span> <span data-ttu-id="f3cc8-237">未來的實體狀態變更會產生 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> 事件。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-237">Future entity state changes result in <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> events.</span></span> <span data-ttu-id="f3cc8-238">如需詳細資訊，請參閱 [EF Core 中的 .Net 事件](xref:core/logging-events-diagnostics/events) 。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-238">See [.NET Events in EF Core](xref:core/logging-events-diagnostics/events) for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="f3cc8-239">`StateChanged`第一次追蹤實體時不會引發事件，即使狀態已從變更 `Detached` 為其中一個其他狀態也一樣。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-239">The `StateChanged` event is not fired when an entity is first tracked, even though the state has changed from `Detached` to one of the other states.</span></span> <span data-ttu-id="f3cc8-240">請務必接聽 `StateChanged` 和 `Tracked` 事件，以取得所有相關的通知。</span><span class="sxs-lookup"><span data-stu-id="f3cc8-240">Make sure to listen for both `StateChanged` and `Tracked` events to get all relevant notifications.</span></span>
