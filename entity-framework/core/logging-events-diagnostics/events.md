---
title: .NET 事件-EF Core
description: EF Core 所定義的 .NET 事件
author: ajcvickers
ms.date: 10/15/2020
uid: core/logging-events-diagnostics/events
ms.openlocfilehash: 0888009af0bedfb63690e72c4a0e08979a9e9cf3
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129248"
---
# <a name="net-events-in-ef-core"></a><span data-ttu-id="19c17-103">EF Core 中的 .NET 事件</span><span class="sxs-lookup"><span data-stu-id="19c17-103">.NET Events in EF Core</span></span>

> [!TIP]
> <span data-ttu-id="19c17-104">您可以從 GitHub [下載事件範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Events) 。</span><span class="sxs-lookup"><span data-stu-id="19c17-104">You can [download the events sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Events) from GitHub.</span></span>

<span data-ttu-id="19c17-105">Entity Framework Core (EF Core) 會公開 [.net 事件](/dotnet/standard/events/) ，以在 EF Core 程式碼中發生特定情況時做為回呼。</span><span class="sxs-lookup"><span data-stu-id="19c17-105">Entity Framework Core (EF Core) exposes [.NET events](/dotnet/standard/events/) to act as callbacks when certain things happen in the EF Core code.</span></span> <span data-ttu-id="19c17-106">事件比 [攔截](xref:core/logging-events-diagnostics/interceptors) 器更簡單，並允許更具彈性的註冊。</span><span class="sxs-lookup"><span data-stu-id="19c17-106">Events are simpler than [interceptors](xref:core/logging-events-diagnostics/interceptors) and allow more flexible registration.</span></span> <span data-ttu-id="19c17-107">不過，它們只會進行同步處理，因此無法執行非封鎖的非同步 i/o。</span><span class="sxs-lookup"><span data-stu-id="19c17-107">However, they are sync only and so cannot perform non-blocking async I/O.</span></span>

<span data-ttu-id="19c17-108">每個實例都會註冊事件 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="19c17-108">Events are registered per `DbContext` instance.</span></span> <span data-ttu-id="19c17-109">您可以使用 [診斷](xref:core/logging-events-diagnostics/diagnostic-listeners) 接聽程式來取得相同資訊，但會針對進程中的所有 DbCoNtext 實例取得相同的資訊。</span><span class="sxs-lookup"><span data-stu-id="19c17-109">Use a [diagnostic listener](xref:core/logging-events-diagnostics/diagnostic-listeners) to get the same information but for all DbContext instances in the process.</span></span>

## <a name="events-raised-by-ef-core"></a><span data-ttu-id="19c17-110">EF Core 所引發的事件</span><span class="sxs-lookup"><span data-stu-id="19c17-110">Events raised by EF Core</span></span>

<span data-ttu-id="19c17-111">EF Core 會引發下列事件：</span><span class="sxs-lookup"><span data-stu-id="19c17-111">The following events are raised by EF Core:</span></span>

| <span data-ttu-id="19c17-112">事件</span><span class="sxs-lookup"><span data-stu-id="19c17-112">Event</span></span> | <span data-ttu-id="19c17-113">引進的版本</span><span class="sxs-lookup"><span data-stu-id="19c17-113">Version introduced</span></span> | <span data-ttu-id="19c17-114">引發時</span><span class="sxs-lookup"><span data-stu-id="19c17-114">When raised</span></span>
|:------|--------------------|-------
| <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> | <span data-ttu-id="19c17-115">5.0</span><span class="sxs-lookup"><span data-stu-id="19c17-115">5.0</span></span> | <span data-ttu-id="19c17-116">在 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 或開頭 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span><span class="sxs-lookup"><span data-stu-id="19c17-116">At the start of <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span></span>
| <xref:Microsoft.EntityFrameworkCore.DbContext.SavedChanges?displayProperty=nameWithType> | <span data-ttu-id="19c17-117">5.0</span><span class="sxs-lookup"><span data-stu-id="19c17-117">5.0</span></span> | <span data-ttu-id="19c17-118">在成功 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 或之後 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span><span class="sxs-lookup"><span data-stu-id="19c17-118">At the end of a successful <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span></span>
| <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesFailed?displayProperty=nameWithType> | <span data-ttu-id="19c17-119">5.0</span><span class="sxs-lookup"><span data-stu-id="19c17-119">5.0</span></span> | <span data-ttu-id="19c17-120">在失敗的結尾 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 或 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span><span class="sxs-lookup"><span data-stu-id="19c17-120">At the end of a failed <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType> | <span data-ttu-id="19c17-121">2.1</span><span class="sxs-lookup"><span data-stu-id="19c17-121">2.1</span></span> | <span data-ttu-id="19c17-122">當實體由內容追蹤時</span><span class="sxs-lookup"><span data-stu-id="19c17-122">When an entity is tracked by the context</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> | <span data-ttu-id="19c17-123">2.1</span><span class="sxs-lookup"><span data-stu-id="19c17-123">2.1</span></span> | <span data-ttu-id="19c17-124">當追蹤的實體變更其狀態時</span><span class="sxs-lookup"><span data-stu-id="19c17-124">When a tracked entity changes its state</span></span>

### <a name="example-timestamp-state-changes"></a><span data-ttu-id="19c17-125">範例：時間戳記狀態變更</span><span class="sxs-lookup"><span data-stu-id="19c17-125">Example: Timestamp state changes</span></span>

<span data-ttu-id="19c17-126">DbCoNtext 追蹤的每個實體都有 <xref:Microsoft.EntityFrameworkCore.EntityState> 。</span><span class="sxs-lookup"><span data-stu-id="19c17-126">Each entity tracked by a DbContext has an <xref:Microsoft.EntityFrameworkCore.EntityState>.</span></span> <span data-ttu-id="19c17-127">例如， `Added` 狀態表示實體將會插入至資料庫。</span><span class="sxs-lookup"><span data-stu-id="19c17-127">For example, the `Added` state indicates that the entity will be inserted into the database.</span></span>

<span data-ttu-id="19c17-128">這個範例會使用 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked> 和 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged> 事件來偵測實體何時變更狀態。</span><span class="sxs-lookup"><span data-stu-id="19c17-128">This example uses the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged> events to detect when an entity changes state.</span></span> <span data-ttu-id="19c17-129">接著，它會使用目前的時間為實體加上戳記，指出此變更發生的時間。</span><span class="sxs-lookup"><span data-stu-id="19c17-129">It then stamps the entity with the current time indicating when this change happened.</span></span> <span data-ttu-id="19c17-130">這會產生時間戳記，以指出實體插入、刪除及/或上次更新的時間。</span><span class="sxs-lookup"><span data-stu-id="19c17-130">This results in timestamps indicating when the entity was inserted, deleted, and/or last updated.</span></span>

<span data-ttu-id="19c17-131">此範例中的實體類型會執行定義時間戳記屬性的介面：</span><span class="sxs-lookup"><span data-stu-id="19c17-131">The entity types in this example implement an interface that defines the timestamp properties:</span></span>

<!--
public interface IHasTimestamps
{
    DateTime? Added { get; set; }
    DateTime? Deleted { get; set; }
    DateTime? Modified { get; set; }
}
-->
[!code-csharp[IHasTimestamps](../../../samples/core/Miscellaneous/Events/Program.cs?name=IHasTimestamps)]

<span data-ttu-id="19c17-132">然後，應用程式 DbCoNtext 上的方法可以設定任何實作為此介面之實體的時間戳記：</span><span class="sxs-lookup"><span data-stu-id="19c17-132">A method on the application's DbContext can then set timestamps for any entity that implements this interface:</span></span>

<!--
    private static void UpdateTimestamps(object sender, EntityEntryEventArgs e)
    {
        if (e.Entry.Entity is IHasTimestamps entityWithTimestamps)
        {
            switch (e.Entry.State)
            {
                case EntityState.Deleted:
                    entityWithTimestamps.Deleted = DateTime.UtcNow;
                    Console.WriteLine($"Stamped for delete: {e.Entry.Entity}");
                    break;
                case EntityState.Modified:
                    entityWithTimestamps.Modified = DateTime.UtcNow;
                    Console.WriteLine($"Stamped for update: {e.Entry.Entity}");
                    break;
                case EntityState.Added:
                    entityWithTimestamps.Added = DateTime.UtcNow;
                    Console.WriteLine($"Stamped for insert: {e.Entry.Entity}");
                    break;
            }
        }
    }
-->
[!code-csharp[UpdateTimestamps](../../../samples/core/Miscellaneous/Events/Program.cs?name=UpdateTimestamps)]

<span data-ttu-id="19c17-133">這個方法具有適當的簽章，可用來做為和事件的事件處理常式 `Tracked` `StateChanged` 。</span><span class="sxs-lookup"><span data-stu-id="19c17-133">This method has the appropriate signature to use as an event handler for both the `Tracked` and `StateChanged` events.</span></span> <span data-ttu-id="19c17-134">在 DbCoNtext 的函式中，會為這兩個事件註冊處理常式。</span><span class="sxs-lookup"><span data-stu-id="19c17-134">The handler is registered for both events in the DbContext constructor.</span></span> <span data-ttu-id="19c17-135">請注意，您可以在任何時間將事件附加至 DbCoNtext;在內容的函式中，這種情況並不是必要的。</span><span class="sxs-lookup"><span data-stu-id="19c17-135">Note that events can be attached to a DbContext at any time; it is not required that this happen in the context constructor.</span></span>

<!--
    public BlogsContext()
    {
        ChangeTracker.StateChanged += UpdateTimestamps;
        ChangeTracker.Tracked += UpdateTimestamps;
    }
-->
[!code-csharp[ContextConstructor](../../../samples/core/Miscellaneous/Events/Program.cs?name=ContextConstructor)]

<span data-ttu-id="19c17-136">這兩個事件都是必要的，因為新 `Tracked` 的實體會在第一次追蹤時引發事件。</span><span class="sxs-lookup"><span data-stu-id="19c17-136">Both events are needed because new entities fire `Tracked` events when they are first tracked.</span></span> <span data-ttu-id="19c17-137">`StateChanged` 只有當實體 _已_ 在追蹤時變更狀態時，才會引發事件。</span><span class="sxs-lookup"><span data-stu-id="19c17-137">`StateChanged` events are only fired for entities that change state while they are _already_ being tracked.</span></span>

<span data-ttu-id="19c17-138">此範例的 [範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Events) 包含一個簡單的主控台應用程式，可對 [日誌] 資料庫進行變更：</span><span class="sxs-lookup"><span data-stu-id="19c17-138">The [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Events) for this example contains a simple console application that makes changes to the blogging database:</span></span>

<!--
        using (var context = new BlogsContext())
        {
            context.Database.EnsureDeleted();
            context.Database.EnsureCreated();

            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = "EF Blog",
                    Posts =
                    {
                        new Post { Id = 1, Title = "EF Core 3.1!" },
                        new Post { Id = 2, Title = "EF Core 5.0!" }
                    }
                });

            context.SaveChanges();
        }

        using (var context = new BlogsContext())
        {
            var blog = context.Blogs.Include(e => e.Posts).Single();

            blog.Name = "EF Core Blog";
            context.Remove(blog.Posts.First());
            blog.Posts.Add(new Post { Id = 3, Title = "EF Core 6.0!" });

            context.SaveChanges();
        }
-->
[!code-csharp[Demonstration](../../../samples/core/Miscellaneous/Events/Program.cs?name=Demonstration)]

<span data-ttu-id="19c17-139">這段程式碼的輸出會顯示發生的狀態變更和套用的時間戳記：</span><span class="sxs-lookup"><span data-stu-id="19c17-139">The output from this code shows the state changes happening and the timestamps being applied:</span></span>

```output
Stamped for insert: Blog 1 Added on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 1 Added on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 2 Added on: 10/15/2020 11:01:26 PM
Stamped for delete: Post 1 Added on: 10/15/2020 11:01:26 PM Deleted on: 10/15/2020 11:01:26 PM
Stamped for update: Blog 1 Added on: 10/15/2020 11:01:26 PM Modified on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 3 Added on: 10/15/2020 11:01:26 PM
```
