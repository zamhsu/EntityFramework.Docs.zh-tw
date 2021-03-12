---
title: 變更追蹤程式偵錯工具-EF Core
description: 使用 ChangeTracker DebugView 和記錄訊息來進行 EF Core 變更追蹤的調試
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/debug-views
ms.openlocfilehash: cda81728beb5ffbc8604c191e84b3553a7343ec0
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023168"
---
# <a name="change-tracker-debugging"></a><span data-ttu-id="14c24-103">變更追蹤程式偵錯工具</span><span class="sxs-lookup"><span data-stu-id="14c24-103">Change Tracker Debugging</span></span>

<span data-ttu-id="14c24-104">Entity Framework Core (EF Core) 變更追蹤器會產生兩種輸出，以協助進行偵錯工具：</span><span class="sxs-lookup"><span data-stu-id="14c24-104">The Entity Framework Core (EF Core) change tracker generates two kinds of output to help with debugging:</span></span>

- <span data-ttu-id="14c24-105"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType>提供所有正在追蹤之實體的人們看得懂的觀點</span><span class="sxs-lookup"><span data-stu-id="14c24-105">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType> provides a human-readable view of all entities being tracked</span></span>
- <span data-ttu-id="14c24-106">當變更追蹤器偵測到狀態並修正關聯性時，會產生 Debug 層級記錄訊息</span><span class="sxs-lookup"><span data-stu-id="14c24-106">Debug-level log messages are generated when the change tracker detects state and fixes up relationships</span></span>

> [!TIP]
> <span data-ttu-id="14c24-107">本檔假設您已瞭解實體狀態和 EF Core 變更追蹤的基本概念。</span><span class="sxs-lookup"><span data-stu-id="14c24-107">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="14c24-108">如需有關這些主題的詳細資訊，請參閱 [EF Core 中的變更追蹤](xref:core/change-tracking/index) 。</span><span class="sxs-lookup"><span data-stu-id="14c24-108">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="14c24-109">您可以 [從 GitHub 下載範例程式碼](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/ChangeTrackerDebugging)，以執行並偵測到本檔中的所有程式碼。</span><span class="sxs-lookup"><span data-stu-id="14c24-109">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/ChangeTrackerDebugging).</span></span>

## <a name="change-tracker-debug-view"></a><span data-ttu-id="14c24-110">變更追蹤器的偵錯工具</span><span class="sxs-lookup"><span data-stu-id="14c24-110">Change tracker debug view</span></span>

<span data-ttu-id="14c24-111">您可以在 IDE 的偵錯工具中存取變更追蹤器的偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="14c24-111">The change tracker debug view can be accessed in the debugger of your IDE.</span></span> <span data-ttu-id="14c24-112">例如，使用 Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="14c24-112">For example, with Visual Studio:</span></span>

![從 Visual Studio 偵錯工具存取變更追蹤器的偵錯工具](_static/debug-view.png)

<span data-ttu-id="14c24-114">它也可以直接從程式碼存取，例如，將偵錯工具傳送至主控台：</span><span class="sxs-lookup"><span data-stu-id="14c24-114">It can also be accessed directly from code, for example to send the debug view to the console:</span></span>

<!--
        Console.WriteLine(context.ChangeTracker.DebugView.ShortView);
-->
[!code-csharp[Change_tracker_debug_view_1b](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Change_tracker_debug_view_1b)]

<span data-ttu-id="14c24-115">Debug view 具有簡短的形式和長格式的表單。</span><span class="sxs-lookup"><span data-stu-id="14c24-115">The debug view has a short form and a long form.</span></span> <span data-ttu-id="14c24-116">簡短形式會顯示追蹤的實體、其狀態和索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="14c24-116">The short form shows tracked entities, their state, and key values.</span></span> <span data-ttu-id="14c24-117">完整形式也包含所有屬性和導覽值和狀態。</span><span class="sxs-lookup"><span data-stu-id="14c24-117">The long form also includes all property and navigation values and state.</span></span>

### <a name="the-short-view"></a><span data-ttu-id="14c24-118">簡短視圖</span><span class="sxs-lookup"><span data-stu-id="14c24-118">The short view</span></span>

<span data-ttu-id="14c24-119">讓我們看看使用本檔結尾顯示之模型的 debug view 範例。</span><span class="sxs-lookup"><span data-stu-id="14c24-119">Let's look at a debug view example using the model shown at the end of this document.</span></span> <span data-ttu-id="14c24-120">首先，我們會追蹤一些實體，並將它們放在一些不同的狀態，如此一來，我們就可以看到良好的變更追蹤資料：</span><span class="sxs-lookup"><span data-stu-id="14c24-120">First, we will track some entities and put them in some different states, just so we have good change tracking data to view:</span></span>

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs
            .Include(e => e.Posts).ThenInclude(e => e.Tags)
            .Include(e => e.Assets)
            .ToList();

        // Mark something Added
        blogs[0].Posts.Add(
            new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many new features and..."
            });

        // Mark something Deleted
        blogs[1].Posts.Remove(blogs[1].Posts[1]);

        // Make something Modified
        blogs[0].Name = ".NET Blog (All new!)";

        context.ChangeTracker.DetectChanges();
-->
[!code-csharp[Change_tracker_debug_view_1a](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Change_tracker_debug_view_1a)]

<span data-ttu-id="14c24-121">在此時列印簡短視圖（如上所示）會產生下列輸出：</span><span class="sxs-lookup"><span data-stu-id="14c24-121">Printing the short view at this point, as shown above, results in the following output:</span></span>

```output
Blog {Id: 1} Modified AK {AssetsId: ed727978-1ffe-4709-baee-73913e8e44a0}
Blog {Id: 2} Unchanged AK {AssetsId: 3a54b880-2b9d-486b-9403-dc2e52d36d65}
BlogAssets {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65} Unchanged FK {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65}
BlogAssets {Id: ed727978-1ffe-4709-baee-73913e8e44a0} Unchanged FK {Id: ed727978-1ffe-4709-baee-73913e8e44a0}
Post {Id: -2147482643} Added FK {BlogId: 1}
Post {Id: 1} Unchanged FK {BlogId: 1}
Post {Id: 2} Unchanged FK {BlogId: 1}
Post {Id: 3} Unchanged FK {BlogId: 2}
Post {Id: 4} Deleted FK {BlogId: 2}
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 1} Unchanged FK {PostsId: 1} FK {TagsId: 1}
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 3} Unchanged FK {PostsId: 1} FK {TagsId: 3}
PostTag (Dictionary<string, object>) {PostsId: 2, TagsId: 1} Unchanged FK {PostsId: 2} FK {TagsId: 1}
PostTag (Dictionary<string, object>) {PostsId: 3, TagsId: 2} Unchanged FK {PostsId: 3} FK {TagsId: 2}
PostTag (Dictionary<string, object>) {PostsId: 4, TagsId: 2} Deleted FK {PostsId: 4} FK {TagsId: 2}
Tag {Id: 1} Unchanged
Tag {Id: 2} Unchanged
Tag {Id: 3} Unchanged
```

<span data-ttu-id="14c24-122">注意：</span><span class="sxs-lookup"><span data-stu-id="14c24-122">Notice:</span></span>

- <span data-ttu-id="14c24-123">每個追蹤的實體都會以其主鍵 (PK) 值來列出。</span><span class="sxs-lookup"><span data-stu-id="14c24-123">Each tracked entity is listed with its primary key (PK) value.</span></span> <span data-ttu-id="14c24-124">例如： `Blog {Id: 1}` 。</span><span class="sxs-lookup"><span data-stu-id="14c24-124">For example, `Blog {Id: 1}`.</span></span>
- <span data-ttu-id="14c24-125">如果實體是共用型別實體型別，則也會顯示它的 CLR 型別。</span><span class="sxs-lookup"><span data-stu-id="14c24-125">If the entity is a shared-type entity type, then it's CLR type is also shown.</span></span> <span data-ttu-id="14c24-126">例如： `PostTag (Dictionary<string, object>)` 。</span><span class="sxs-lookup"><span data-stu-id="14c24-126">For example, `PostTag (Dictionary<string, object>)`.</span></span>
- <span data-ttu-id="14c24-127"><xref:Microsoft.EntityFrameworkCore.EntityState>接下來會顯示。</span><span class="sxs-lookup"><span data-stu-id="14c24-127">The <xref:Microsoft.EntityFrameworkCore.EntityState> is shown next.</span></span> <span data-ttu-id="14c24-128">這會是、、或的其中一個 `Unchanged` `Added` `Modified` `Deleted` 。</span><span class="sxs-lookup"><span data-stu-id="14c24-128">This will be one of `Unchanged`, `Added`, `Modified`, or `Deleted`.</span></span>
- <span data-ttu-id="14c24-129">接下來會顯示 (AKs) 之任何替代索引鍵的值。</span><span class="sxs-lookup"><span data-stu-id="14c24-129">Values for any alternate keys (AKs) are shown next.</span></span> <span data-ttu-id="14c24-130">例如： `AK {AssetsId: ed727978-1ffe-4709-baee-73913e8e44a0}` 。</span><span class="sxs-lookup"><span data-stu-id="14c24-130">For example, `AK {AssetsId: ed727978-1ffe-4709-baee-73913e8e44a0}`.</span></span>
- <span data-ttu-id="14c24-131">最後，會顯示 (Fk) 之任何外鍵的值。</span><span class="sxs-lookup"><span data-stu-id="14c24-131">Finally, values for any foreign keys (FKs) are shown.</span></span> <span data-ttu-id="14c24-132">例如： `FK {PostsId: 4} FK {TagsId: 2}` 。</span><span class="sxs-lookup"><span data-stu-id="14c24-132">For example, `FK {PostsId: 4} FK {TagsId: 2}`.</span></span>

### <a name="the-long-view"></a><span data-ttu-id="14c24-133">冗長的觀點</span><span class="sxs-lookup"><span data-stu-id="14c24-133">The long view</span></span>

<span data-ttu-id="14c24-134">您可以透過與簡短視圖相同的方式，將冗長的視圖傳送到主控台：</span><span class="sxs-lookup"><span data-stu-id="14c24-134">The long view can be sent to the console in the same way as the short view:</span></span>

<!--
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Change_tracker_debug_view_1c](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Change_tracker_debug_view_1c)]

<span data-ttu-id="14c24-135">與上述簡短視圖相同狀態的輸出為：</span><span class="sxs-lookup"><span data-stu-id="14c24-135">The output for the same state as the short view above is:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  AssetsId: 'ed727978-1ffe-4709-baee-73913e8e44a0' AK
  Name: '.NET Blog (All new!)' Modified Originally '.NET Blog'
  Assets: {Id: ed727978-1ffe-4709-baee-73913e8e44a0}
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  AssetsId: '3a54b880-2b9d-486b-9403-dc2e52d36d65' AK
  Name: 'Visual Studio Blog'
  Assets: {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65}
  Posts: [{Id: 3}]
BlogAssets {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65} Unchanged
  Id: '3a54b880-2b9d-486b-9403-dc2e52d36d65' PK FK
  Banner: <null>
  Blog: {Id: 2}
BlogAssets {Id: ed727978-1ffe-4709-baee-73913e8e44a0} Unchanged
  Id: 'ed727978-1ffe-4709-baee-73913e8e44a0' PK FK
  Banner: <null>
  Blog: {Id: 1}
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many new fe...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
  Tags: []
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: [{Id: 1}, {Id: 3}]
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: [{Id: 1}]
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: [{Id: 2}]
Post {Id: 4} Deleted
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: <null>
  Tags: [{Id: 2}]
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 1} Unchanged
  PostsId: 1 PK FK
  TagsId: 1 PK FK
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 3} Unchanged
  PostsId: 1 PK FK
  TagsId: 3 PK FK
PostTag (Dictionary<string, object>) {PostsId: 2, TagsId: 1} Unchanged
  PostsId: 2 PK FK
  TagsId: 1 PK FK
PostTag (Dictionary<string, object>) {PostsId: 3, TagsId: 2} Unchanged
  PostsId: 3 PK FK
  TagsId: 2 PK FK
PostTag (Dictionary<string, object>) {PostsId: 4, TagsId: 2} Deleted
  PostsId: 4 PK FK
  TagsId: 2 PK FK
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  Posts: [{Id: 1}, {Id: 2}]
Tag {Id: 2} Unchanged
  Id: 2 PK
  Text: 'Visual Studio'
  Posts: [{Id: 3}, {Id: 4}]
Tag {Id: 3} Unchanged
  Id: 3 PK
  Text: 'EF Core'
  Posts: [{Id: 1}]
```

<span data-ttu-id="14c24-136">每個追蹤的實體及其狀態會顯示為先前的狀態。</span><span class="sxs-lookup"><span data-stu-id="14c24-136">Each tracked entity and its state is shown as before.</span></span> <span data-ttu-id="14c24-137">但是，冗長的視圖也會顯示內容和導覽值。</span><span class="sxs-lookup"><span data-stu-id="14c24-137">However, the long view also shows property and navigation values.</span></span>

#### <a name="property-values"></a><span data-ttu-id="14c24-138">屬性值</span><span class="sxs-lookup"><span data-stu-id="14c24-138">Property values</span></span>

<span data-ttu-id="14c24-139">針對每個屬性，長時間會顯示內容是否為主鍵的一部分 (PK) 、替代索引鍵 (AK) ，或外鍵 (FK) 。</span><span class="sxs-lookup"><span data-stu-id="14c24-139">For each property, the long view shows whether or not the property is part of a primary key (PK), alternate key (AK), or foreign key (FK).</span></span> <span data-ttu-id="14c24-140">例如：</span><span class="sxs-lookup"><span data-stu-id="14c24-140">For example:</span></span>

- <span data-ttu-id="14c24-141">`Blog.Id` 是主要索引鍵屬性： `Id: 1 PK`</span><span class="sxs-lookup"><span data-stu-id="14c24-141">`Blog.Id` is a primary key property: `Id: 1 PK`</span></span>
- <span data-ttu-id="14c24-142">`Blog.AssetsId` 是替代索引鍵屬性： `AssetsId: 'ed727978-1ffe-4709-baee-73913e8e44a0' AK`</span><span class="sxs-lookup"><span data-stu-id="14c24-142">`Blog.AssetsId` is an alternate key property: `AssetsId: 'ed727978-1ffe-4709-baee-73913e8e44a0' AK`</span></span>
- <span data-ttu-id="14c24-143">`Post.BlogId` 為外鍵屬性： `BlogId: 2 FK`</span><span class="sxs-lookup"><span data-stu-id="14c24-143">`Post.BlogId` is a foreign key property: `BlogId: 2 FK`</span></span>
- <span data-ttu-id="14c24-144">`BlogAssets.Id` 同時為主要索引鍵和外鍵屬性： `Id: '3a54b880-2b9d-486b-9403-dc2e52d36d65' PK FK`</span><span class="sxs-lookup"><span data-stu-id="14c24-144">`BlogAssets.Id` is both a primary key and a foreign key property: `Id: '3a54b880-2b9d-486b-9403-dc2e52d36d65' PK FK`</span></span>

<span data-ttu-id="14c24-145">已修改的屬性值會標示為如此，而且也會顯示內容的原始值。</span><span class="sxs-lookup"><span data-stu-id="14c24-145">Property values that have been modified are marked as such, and the original value of the property is also shown.</span></span> <span data-ttu-id="14c24-146">例如： `Name: '.NET Blog (All new!)' Modified Originally '.NET Blog'` 。</span><span class="sxs-lookup"><span data-stu-id="14c24-146">For example, `Name: '.NET Blog (All new!)' Modified Originally '.NET Blog'`.</span></span>

<span data-ttu-id="14c24-147">最後， `Added` 具有暫存索引鍵值的實體表示值是暫時性的。</span><span class="sxs-lookup"><span data-stu-id="14c24-147">Finally, `Added` entities with temporary key values indicate that the value is temporary.</span></span> <span data-ttu-id="14c24-148">例如： `Id: -2147482643 PK Temporary` 。</span><span class="sxs-lookup"><span data-stu-id="14c24-148">For example, `Id: -2147482643 PK Temporary`.</span></span>

#### <a name="navigation-values"></a><span data-ttu-id="14c24-149">導覽值</span><span class="sxs-lookup"><span data-stu-id="14c24-149">Navigation values</span></span>

<span data-ttu-id="14c24-150">導覽值會使用導覽參考之實體的主鍵值來顯示。</span><span class="sxs-lookup"><span data-stu-id="14c24-150">Navigation values are displayed using the primary key values of the entities that the navigations reference.</span></span> <span data-ttu-id="14c24-151">例如，在上面的輸出中，post 3 與 blog 2 相關。</span><span class="sxs-lookup"><span data-stu-id="14c24-151">For example, in the output above, post 3 is related to blog 2.</span></span> <span data-ttu-id="14c24-152">這表示 `Post.Blog` 導覽指向識別碼為2的 `Blog` 實例。</span><span class="sxs-lookup"><span data-stu-id="14c24-152">This means that the `Post.Blog` navigation points to the `Blog` instance with ID 2.</span></span> <span data-ttu-id="14c24-153">這會顯示為 `Blog: {Id: 2}` 。</span><span class="sxs-lookup"><span data-stu-id="14c24-153">This is shown as `Blog: {Id: 2}`.</span></span>

<span data-ttu-id="14c24-154">集合導覽也會發生相同的情況，但在此情況下，可能會有多個相關的實體。</span><span class="sxs-lookup"><span data-stu-id="14c24-154">The same thing happens for collection navigations, except that in this case there can be multiple related entities.</span></span> <span data-ttu-id="14c24-155">例如，集合導覽 `Blog.Posts` 包含三個實體，分別有索引鍵值1、2和-2147482643。</span><span class="sxs-lookup"><span data-stu-id="14c24-155">For example, the collection navigation `Blog.Posts` contains three entities, with key values 1, 2, and -2147482643 respectively.</span></span> <span data-ttu-id="14c24-156">這會顯示為 `[{Id: 1}, {Id: 2}, {Id: -2147482643}]` 。</span><span class="sxs-lookup"><span data-stu-id="14c24-156">This is shown as `[{Id: 1}, {Id: 2}, {Id: -2147482643}]`.</span></span>

## <a name="change-tracker-logging"></a><span data-ttu-id="14c24-157">變更追蹤程式記錄</span><span class="sxs-lookup"><span data-stu-id="14c24-157">Change tracker logging</span></span>

<span data-ttu-id="14c24-158">`Debug` <xref:Microsoft.Extensions.Logging.LogLevel> 每當[偵測到屬性或流覽變更](xref:core/change-tracking/debug-views)時，變更追蹤程式就會記錄訊息。</span><span class="sxs-lookup"><span data-stu-id="14c24-158">The change tracker logs messages at the `Debug` <xref:Microsoft.Extensions.Logging.LogLevel> whenever it [detects property or navigation changes](xref:core/change-tracking/debug-views).</span></span> <span data-ttu-id="14c24-159">例如，當您 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> 在本檔頂端的程式碼中呼叫，且 [啟用了 debug 記錄](xref:core/logging-events-diagnostics/index)時，會產生下列記錄：</span><span class="sxs-lookup"><span data-stu-id="14c24-159">For example, when <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> is called in the code at the top of this document and [debug logging is enabled](xref:core/logging-events-diagnostics/index), then the following logs are generated:</span></span>

```output
dbug: 12/30/2020 13:52:44.815 CoreEventId.DetectChangesStarting[10800] (Microsoft.EntityFrameworkCore.ChangeTracking)
      DetectChanges starting for 'BlogsContext'.
dbug: 12/30/2020 13:52:44.818 CoreEventId.PropertyChangeDetected[10802] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The unchanged property 'Blog.Name' was detected as changed from '.NET Blog' to '.NET Blog (All new!)' and will be marked as modified for entity with key '{Id: 1}'.
dbug: 12/30/2020 13:52:44.820 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'Blog' entity with key '{Id: 1}' tracked by 'BlogsContext' changed state from 'Unchanged' to 'Modified'.
dbug: 12/30/2020 13:52:44.821 CoreEventId.CollectionChangeDetected[10804] (Microsoft.EntityFrameworkCore.ChangeTracking)
      1 entities were added and 0 entities were removed from navigation 'Blog.Posts' on entity with key '{Id: 1}'.
dbug: 12/30/2020 13:52:44.822 CoreEventId.ValueGenerated[10808] (Microsoft.EntityFrameworkCore.ChangeTracking)
      'BlogsContext' generated temporary value '-2147482638' for the property 'Id.Post'.
dbug: 12/30/2020 13:52:44.822 CoreEventId.StartedTracking[10806] (Microsoft.EntityFrameworkCore.ChangeTracking)
      Context 'BlogsContext' started tracking 'Post' entity with key '{Id: -2147482638}'.
dbug: 12/30/2020 13:52:44.827 CoreEventId.CollectionChangeDetected[10804] (Microsoft.EntityFrameworkCore.ChangeTracking)
      0 entities were added and 1 entities were removed from navigation 'Blog.Posts' on entity with key '{Id: 2}'.
dbug: 12/30/2020 13:52:44.827 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'Post' entity with key '{Id: 4}' tracked by 'BlogsContext' changed state from 'Unchanged' to 'Modified'.
dbug: 12/30/2020 13:52:44.829 CoreEventId.CascadeDeleteOrphan[10003] (Microsoft.EntityFrameworkCore.Update)
      An entity of type 'Post' with key '{Id: 4}' changed to 'Deleted' state due to severed required relationship to its parent entity of type 'Blog'.
dbug: 12/30/2020 13:52:44.829 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'Post' entity with key '{Id: 4}' tracked by 'BlogsContext' changed state from 'Modified' to 'Deleted'.
dbug: 12/30/2020 13:52:44.829 CoreEventId.CollectionChangeDetected[10804] (Microsoft.EntityFrameworkCore.ChangeTracking)
      0 entities were added and 1 entities were removed from navigation 'Blog.Posts' on entity with key '{Id: 2}'.
dbug: 12/30/2020 13:52:44.831 CoreEventId.CascadeDelete[10002] (Microsoft.EntityFrameworkCore.Update)
      A cascade state change of an entity of type 'PostTag' with key '{PostsId: 4, TagsId: 2}' to 'Deleted' occurred due to the deletion of its parent entity of type 'Post' with key '{Id: 4}'.
dbug: 12/30/2020 13:52:44.831 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'PostTag' entity with key '{PostsId: 4, TagsId: 2}' tracked by 'BlogsContext' changed state from 'Unchanged' to 'Deleted'.
dbug: 12/30/2020 13:52:44.831 CoreEventId.DetectChangesCompleted[10801] (Microsoft.EntityFrameworkCore.ChangeTracking)
      DetectChanges completed for 'BlogsContext'.
```

<span data-ttu-id="14c24-160">下表摘要說明變更追蹤程式記錄訊息：</span><span class="sxs-lookup"><span data-stu-id="14c24-160">The following table summaries the change tracker logging messages:</span></span>

| <span data-ttu-id="14c24-161">事件識別碼</span><span class="sxs-lookup"><span data-stu-id="14c24-161">Event ID</span></span>                                                                                                               | <span data-ttu-id="14c24-162">Description</span><span class="sxs-lookup"><span data-stu-id="14c24-162">Description</span></span>
|:-----------------------------------------------------------------------------------------------------------------------|----
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.DetectChangesStarting?displayProperty=nameWithType>        | <span data-ttu-id="14c24-163"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> 正在啟動</span><span class="sxs-lookup"><span data-stu-id="14c24-163"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> is starting</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.DetectChangesCompleted?displayProperty=nameWithType>       | <span data-ttu-id="14c24-164"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> 已完成</span><span class="sxs-lookup"><span data-stu-id="14c24-164"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> has completed</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.PropertyChangeDetected?displayProperty=nameWithType>       | <span data-ttu-id="14c24-165">一般的屬性值已變更</span><span class="sxs-lookup"><span data-stu-id="14c24-165">A normal property value has changed</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ForeignKeyChangeDetected?displayProperty=nameWithType>     | <span data-ttu-id="14c24-166">外鍵屬性值已變更</span><span class="sxs-lookup"><span data-stu-id="14c24-166">A foreign key property value has changed</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.CollectionChangeDetected?displayProperty=nameWithType>     | <span data-ttu-id="14c24-167">非略過集合導覽已新增或移除相關的實體。</span><span class="sxs-lookup"><span data-stu-id="14c24-167">A non-skip collection navigation has had related entities added or removed.</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ReferenceChangeDetected?displayProperty=nameWithType>      | <span data-ttu-id="14c24-168">參考導覽已變更為指向另一個實體，或設定為 null</span><span class="sxs-lookup"><span data-stu-id="14c24-168">A reference navigation has been changed to point to another entity, or set to null</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.StartedTracking?displayProperty=nameWithType>              | <span data-ttu-id="14c24-169">EF Core 已開始追蹤實體</span><span class="sxs-lookup"><span data-stu-id="14c24-169">EF Core started tracking an entity</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.StateChanged?displayProperty=nameWithType>                 | <span data-ttu-id="14c24-170"><xref:Microsoft.EntityFrameworkCore.EntityState>實體的已變更</span><span class="sxs-lookup"><span data-stu-id="14c24-170">The <xref:Microsoft.EntityFrameworkCore.EntityState> of an entity has changed</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ValueGenerated?displayProperty=nameWithType>               | <span data-ttu-id="14c24-171">已為屬性產生值</span><span class="sxs-lookup"><span data-stu-id="14c24-171">A value was generated for a property</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.SkipCollectionChangeDetected?displayProperty=nameWithType> | <span data-ttu-id="14c24-172">略過集合導覽已新增或移除相關實體</span><span class="sxs-lookup"><span data-stu-id="14c24-172">A skip collection navigation has had related entities added or removed</span></span>

## <a name="the-model"></a><span data-ttu-id="14c24-173">模型</span><span class="sxs-lookup"><span data-stu-id="14c24-173">The model</span></span>

<span data-ttu-id="14c24-174">上述範例所使用的模型包含下列實體類型：</span><span class="sxs-lookup"><span data-stu-id="14c24-174">The model used for the examples above contains the following entity types:</span></span>

<!--
public class Blog
{
    public int Id { get; set; } // Primary key
    public Guid AssetsId { get; set; } // Alternate key
    public string Name { get; set; }

    public IList<Post> Posts { get; } = new List<Post>(); // Collection navigation
    public BlogAssets Assets { get; set; } // Reference navigation
}

public class BlogAssets
{
    public Guid Id { get; set; } // Primary key and foreign key
    public byte[] Banner { get; set; }

    public Blog Blog { get; set; } // Reference navigation
}

public class Post
{
    public int Id { get; set; } // Primary key
    public string Title { get; set; }
    public string Content { get; set; }

    public int BlogId { get; set; } // Foreign key
    public Blog Blog { get; set; } // Reference navigation

    public IList<Tag> Tags { get; } = new List<Tag>(); // Skip collection navigation
}

public class Tag
{
    public int Id { get; set; } // Primary key
    public string Text { get; set; }

    public IList<Post> Posts { get; } = new List<Post>(); // Skip collection navigation
}
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Model)]

<span data-ttu-id="14c24-175">模型大多是依照慣例設定的，在 OnModelCreating 中只有幾行：</span><span class="sxs-lookup"><span data-stu-id="14c24-175">The model is mostly configured by convention, with just a few lines in OnModelCreating:</span></span>

<!--
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder
            .Entity<Blog>()
            .Property(e => e.AssetsId)
            .ValueGeneratedOnAdd();

        modelBuilder
            .Entity<BlogAssets>()
            .HasOne(e => e.Blog)
            .WithOne(e => e.Assets)
            .HasForeignKey<BlogAssets>(e => e.Id)
            .HasPrincipalKey<Blog>(e => e.AssetsId);
    }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=OnModelCreating)]
