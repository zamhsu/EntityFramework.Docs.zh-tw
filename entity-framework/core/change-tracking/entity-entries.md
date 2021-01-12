---
title: 存取追蹤的實體-EF Core
description: 使用 Entityentry.state、DbCoNtext 和 DbSet 存取追蹤的實體
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/entity-entries
ms.openlocfilehash: f385016aba61535f33e34c622dd43ce6dc823fc5
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129683"
---
# <a name="accessing-tracked-entities"></a><span data-ttu-id="e19df-103">存取追蹤的實體</span><span class="sxs-lookup"><span data-stu-id="e19df-103">Accessing Tracked Entities</span></span>

<span data-ttu-id="e19df-104">有四個主要的 Api 可存取所追蹤的實體 <xref:Microsoft.EntityFrameworkCore.DbContext> ：</span><span class="sxs-lookup"><span data-stu-id="e19df-104">There are four main APIs for accessing entities tracked by a <xref:Microsoft.EntityFrameworkCore.DbContext>:</span></span>

- <span data-ttu-id="e19df-105"><xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601>傳回指定之實體實例的實例。</span><span class="sxs-lookup"><span data-stu-id="e19df-105"><xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> returns an <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instance for a given entity instance.</span></span>
- <span data-ttu-id="e19df-106"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%2A?displayProperty=nameWithType><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601>傳回所有追蹤實體的實例，或針對指定類型的所有已追蹤實體。</span><span class="sxs-lookup"><span data-stu-id="e19df-106"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%2A?displayProperty=nameWithType> returns <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instances for all tracked entities, or for all tracked entities of a given type.</span></span>
- <span data-ttu-id="e19df-107"><xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>、 <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType> 、 <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> 和 <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> 依主鍵尋找單一實體，先查看追蹤的實體，然後視需要查詢資料庫。</span><span class="sxs-lookup"><span data-stu-id="e19df-107"><xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType>, and <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> find a single entity by primary key, first looking in tracked entities, and then querying the database if needed.</span></span>
- <span data-ttu-id="e19df-108"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> 針對 DbSet 所代表之實體類型的實體，傳回 (非 Entityentry.state) 實例的實際實體。</span><span class="sxs-lookup"><span data-stu-id="e19df-108"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> returns actual entities (not EntityEntry instances) for entities of the entity type represented by the DbSet.</span></span>

<span data-ttu-id="e19df-109">以下各節將更詳細地說明每一個。</span><span class="sxs-lookup"><span data-stu-id="e19df-109">Each of these is described in more detail in the sections below.</span></span>

> [!TIP]
> <span data-ttu-id="e19df-110">本檔假設您已瞭解實體狀態以及 EF Core 變更追蹤的基本概念。</span><span class="sxs-lookup"><span data-stu-id="e19df-110">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="e19df-111">如需有關這些主題的詳細資訊，請參閱 [EF Core 中的變更追蹤](xref:core/change-tracking/index) 。</span><span class="sxs-lookup"><span data-stu-id="e19df-111">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="e19df-112">您可以 [從 GitHub 下載範例程式碼](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/AccessingTrackedEntities)，以執行並偵測到本檔中的所有程式碼。</span><span class="sxs-lookup"><span data-stu-id="e19df-112">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/AccessingTrackedEntities).</span></span>

## <a name="using-dbcontextentry-and-entityentry-instances"></a><span data-ttu-id="e19df-113">使用 DbCoNtext 和 Entityentry.state 實例</span><span class="sxs-lookup"><span data-stu-id="e19df-113">Using DbContext.Entry and EntityEntry instances</span></span>

<span data-ttu-id="e19df-114">針對每個追蹤的實體，Entity Framework Core (EF Core) 追蹤：</span><span class="sxs-lookup"><span data-stu-id="e19df-114">For each tracked entity, Entity Framework Core (EF Core) keeps track of:</span></span>

- <span data-ttu-id="e19df-115">實體的整體狀態。</span><span class="sxs-lookup"><span data-stu-id="e19df-115">The overall state of the entity.</span></span> <span data-ttu-id="e19df-116">這是、、 `Unchanged` 或中的其中一個 `Modified` `Added` `Deleted` ; 如需詳細資訊，請參閱 [EF Core 中的變更追蹤](xref:core/change-tracking/index) 。</span><span class="sxs-lookup"><span data-stu-id="e19df-116">This is one of `Unchanged`, `Modified`, `Added`, or `Deleted`; see [Change Tracking in EF Core](xref:core/change-tracking/index) for more information.</span></span>
- <span data-ttu-id="e19df-117">追蹤實體之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="e19df-117">The relationships between tracked entities.</span></span> <span data-ttu-id="e19df-118">例如，貼文所屬的 blog。</span><span class="sxs-lookup"><span data-stu-id="e19df-118">For example, the blog to which a post belongs.</span></span>
- <span data-ttu-id="e19df-119">屬性的「目前值」。</span><span class="sxs-lookup"><span data-stu-id="e19df-119">The "current values" of properties.</span></span>
- <span data-ttu-id="e19df-120">當此資訊可用時，屬性的「原始值」。</span><span class="sxs-lookup"><span data-stu-id="e19df-120">The "original values" of properties, when this information is available.</span></span> <span data-ttu-id="e19df-121">原始值是在從資料庫查詢實體時所存在的屬性值。</span><span class="sxs-lookup"><span data-stu-id="e19df-121">Original values are the property values that existed when entity was queried from the database.</span></span>
- <span data-ttu-id="e19df-122">自查詢之後，哪些屬性值已經過修改。</span><span class="sxs-lookup"><span data-stu-id="e19df-122">Which property values have been modified since they were queried.</span></span>
- <span data-ttu-id="e19df-123">屬性值的其他相關資訊，例如值是否為 [暫時性](xref:core/change-tracking/miscellaneous#temporary-values)。</span><span class="sxs-lookup"><span data-stu-id="e19df-123">Other information about property values, such as whether or not the value is [temporary](xref:core/change-tracking/miscellaneous#temporary-values).</span></span>

<span data-ttu-id="e19df-124">傳遞實體實例，以 <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> 提供對指定實體之這項資訊的存取權。</span><span class="sxs-lookup"><span data-stu-id="e19df-124">Passing an entity instance to <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> results in an <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> providing access to this information for the given entity.</span></span> <span data-ttu-id="e19df-125">例如：</span><span class="sxs-lookup"><span data-stu-id="e19df-125">For example:</span></span>

<!--
        using var context = new BlogsContext();

        var blog = context.Blogs.Single(e => e.Id == 1);
        var entityEntry = context.Entry(blog);

-->
[!code-csharp[Using_DbContext_Entry_and_EntityEntry_instances_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbContext_Entry_and_EntityEntry_instances_1)]

<span data-ttu-id="e19df-126">下列各節顯示如何使用 Entityentry.state 來存取和操作實體狀態，以及實體屬性和導覽的狀態。</span><span class="sxs-lookup"><span data-stu-id="e19df-126">The following sections show how to use an EntityEntry to access and manipulate entity state, as well as the state of the entity's properties and navigations.</span></span>

### <a name="working-with-the-entity"></a><span data-ttu-id="e19df-127">使用實體</span><span class="sxs-lookup"><span data-stu-id="e19df-127">Working with the entity</span></span>

<span data-ttu-id="e19df-128">最常見的用法 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> 是存取實體的目前 <xref:Microsoft.EntityFrameworkCore.EntityState> 。</span><span class="sxs-lookup"><span data-stu-id="e19df-128">The most common use of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> is to access the current <xref:Microsoft.EntityFrameworkCore.EntityState> of an entity.</span></span> <span data-ttu-id="e19df-129">例如：</span><span class="sxs-lookup"><span data-stu-id="e19df-129">For example:</span></span>

<!--
        var currentState = context.Entry(blog).State;
        if (currentState == EntityState.Unchanged)
        {
            context.Entry(blog).State = EntityState.Modified;
        }
-->
[!code-csharp[Work_with_the_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_the_entity_1)]

<span data-ttu-id="e19df-130">Entry 方法也可以用於尚未追蹤的實體。</span><span class="sxs-lookup"><span data-stu-id="e19df-130">The Entry method can also be used on entities that are not yet tracked.</span></span> <span data-ttu-id="e19df-131">這不 _會開始追蹤實體_;實體的狀態仍是 `Detatched` 。</span><span class="sxs-lookup"><span data-stu-id="e19df-131">This _does not start tracking the entity_; the state of the entity is still `Detatched`.</span></span> <span data-ttu-id="e19df-132">不過，傳回的 Entityentry.state 可接著用來變更實體狀態，此時實體將會在給定狀態中追蹤。</span><span class="sxs-lookup"><span data-stu-id="e19df-132">However, the returned EntityEntry can then be used to change the entity state, at which point the entity will become tracked in the given state.</span></span> <span data-ttu-id="e19df-133">例如，下列程式碼將會開始追蹤 Blog 實例，如下所示 `Added` ：</span><span class="sxs-lookup"><span data-stu-id="e19df-133">For example, the following code will start tracking a Blog instance as `Added`:</span></span>

<!--
        var newBlog = new Blog();
        Debug.Assert(context.Entry(newBlog).State == EntityState.Detached);

        context.Entry(newBlog).State = EntityState.Added;
        Debug.Assert(context.Entry(newBlog).State == EntityState.Added);
-->
[!code-csharp[Work_with_the_entity_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_the_entity_2)]

> [!TIP]
> <span data-ttu-id="e19df-134">不同于 EF6，設定個別實體的狀態並不會造成所有連接的實體被追蹤。</span><span class="sxs-lookup"><span data-stu-id="e19df-134">Unlike in EF6, setting the state of an individual entity will not cause all connected entities to be tracked.</span></span> <span data-ttu-id="e19df-135">這會讓以這種方式將狀態設定為較低層級的作業，而不是呼叫 `Add` 、 `Attach` 或，這會 `Update` 在實體的整個圖形上操作。</span><span class="sxs-lookup"><span data-stu-id="e19df-135">This makes setting the state this way a lower-level operation than calling `Add`, `Attach`, or `Update`, which operate on an entire graph of entities.</span></span>

<span data-ttu-id="e19df-136">下表摘要說明使用 Entityentry.state 來處理整個實體的方式：</span><span class="sxs-lookup"><span data-stu-id="e19df-136">The following table summarizes ways to use an EntityEntry to work with an entire entity:</span></span>

| <span data-ttu-id="e19df-137">Entityentry.state 成員</span><span class="sxs-lookup"><span data-stu-id="e19df-137">EntityEntry member</span></span>                                                                                         | <span data-ttu-id="e19df-138">描述</span><span class="sxs-lookup"><span data-stu-id="e19df-138">Description</span></span>
|:-----------------------------------------------------------------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.State?displayProperty=nameWithType>         | <span data-ttu-id="e19df-139">取得和設定 <xref:Microsoft.EntityFrameworkCore.EntityState> 實體的。</span><span class="sxs-lookup"><span data-stu-id="e19df-139">Gets and sets the <xref:Microsoft.EntityFrameworkCore.EntityState> of the entity.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Entity?displayProperty=nameWithType>        | <span data-ttu-id="e19df-140">取得實體實例。</span><span class="sxs-lookup"><span data-stu-id="e19df-140">Gets the entity instance.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Context?displayProperty=nameWithType>       | <span data-ttu-id="e19df-141"><xref:Microsoft.EntityFrameworkCore.DbContext>正在追蹤此實體的。</span><span class="sxs-lookup"><span data-stu-id="e19df-141">The <xref:Microsoft.EntityFrameworkCore.DbContext> that is tracking this entity.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Metadata?displayProperty=nameWithType>      | <span data-ttu-id="e19df-142"><xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> 實體類型的中繼資料。</span><span class="sxs-lookup"><span data-stu-id="e19df-142"><xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadata for the type of entity.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.IsKeySet?displayProperty=nameWithType>      | <span data-ttu-id="e19df-143">是否已設定實體的索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="e19df-143">Whether or not the entity has had its key value set.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload?displayProperty=nameWithType>        | <span data-ttu-id="e19df-144">使用從資料庫讀取的值來覆寫屬性值。</span><span class="sxs-lookup"><span data-stu-id="e19df-144">Overwrites property values with values read from the database.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType> | <span data-ttu-id="e19df-145">只強制偵測此實體的變更;請參閱 [變更偵測和通知](xref:core/change-tracking/change-detection)。</span><span class="sxs-lookup"><span data-stu-id="e19df-145">Forces detection of changes for this entity only; see [Change Detection and Notifications](xref:core/change-tracking/change-detection).</span></span>

### <a name="working-with-a-single-property"></a><span data-ttu-id="e19df-146">使用單一屬性</span><span class="sxs-lookup"><span data-stu-id="e19df-146">Working with a single property</span></span>

<span data-ttu-id="e19df-147">的數個多載 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Property%2A?displayProperty=nameWithType> 允許存取實體之個別屬性的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="e19df-147">Several overloads of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Property%2A?displayProperty=nameWithType> allow access to information about an individual property of an entity.</span></span> <span data-ttu-id="e19df-148">例如，使用強型別、流暢的 API：</span><span class="sxs-lookup"><span data-stu-id="e19df-148">For example, using a strongly-typed, fluent-like API:</span></span>

<!--
            PropertyEntry<Blog, string> propertyEntry = context.Entry(blog).Property(e => e.Name);
-->
[!code-csharp[Work_with_a_single_property_1a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1a)]

<span data-ttu-id="e19df-149">屬性名稱可以改以字串的形式傳遞。</span><span class="sxs-lookup"><span data-stu-id="e19df-149">The property name can instead be passed as a string.</span></span> <span data-ttu-id="e19df-150">例如：</span><span class="sxs-lookup"><span data-stu-id="e19df-150">For example:</span></span>

<!--
            PropertyEntry<Blog, string> propertyEntry = context.Entry(blog).Property<string>("Name");
-->
[!code-csharp[Work_with_a_single_property_1b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1b)]

<span data-ttu-id="e19df-151">然後， <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> 會使用傳回的來存取屬性的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="e19df-151">The returned <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> can then be used to access information about the property.</span></span> <span data-ttu-id="e19df-152">例如，它可以用來取得及設定此實體上屬性的目前值：</span><span class="sxs-lookup"><span data-stu-id="e19df-152">For example, it can be used to get and set the current value of the property on this entity:</span></span>

<!--
            string currentValue = context.Entry(blog).Property(e => e.Name).CurrentValue;
            context.Entry(blog).Property(e => e.Name).CurrentValue = "1unicorn2";
-->
[!code-csharp[Work_with_a_single_property_1d](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1d)]

<span data-ttu-id="e19df-153">以上所使用的兩個屬性方法都會傳回強型別泛型 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> 實例。</span><span class="sxs-lookup"><span data-stu-id="e19df-153">Both of the Property methods used above return a strongly-typed generic <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> instance.</span></span> <span data-ttu-id="e19df-154">偏好使用此泛型型別是因為它允許存取屬性值，而不需要對實值型別進行 [裝箱](/dotnet/csharp/programming-guide/types/boxing-and-unboxing)。</span><span class="sxs-lookup"><span data-stu-id="e19df-154">Using this generic type is preferred because it allows access to property values without [boxing value types](/dotnet/csharp/programming-guide/types/boxing-and-unboxing).</span></span> <span data-ttu-id="e19df-155">但是，如果在編譯時期不知道實體或屬性的類型，則 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> 可以改為取得非泛型：</span><span class="sxs-lookup"><span data-stu-id="e19df-155">However, if the type of entity or property is not known at compile-time, then a non-generic <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> can be obtained instead:</span></span>

<!--
            var propertyEntry = context.Entry(blog).Property("Name");
-->
[!code-csharp[Work_with_a_single_property_1c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1c)]

<span data-ttu-id="e19df-156">這可讓您存取任何屬性的屬性資訊（不論其型別為何），但會犧牲實值型別。</span><span class="sxs-lookup"><span data-stu-id="e19df-156">This allows access to property information for any property regardless of its type, at the expense of boxing value types.</span></span> <span data-ttu-id="e19df-157">例如：</span><span class="sxs-lookup"><span data-stu-id="e19df-157">For example:</span></span>

<!--
            object blog = context.Blogs.Single(e => e.Id == 1);

            object currentValue = context.Entry(blog).Property("Name").CurrentValue;
            context.Entry(blog).Property("Name").CurrentValue = "1unicorn2";
-->
[!code-csharp[Work_with_a_single_property_1e](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1e)]

<span data-ttu-id="e19df-158">下表摘要說明 PropertyEntry 所公開的屬性資訊：</span><span class="sxs-lookup"><span data-stu-id="e19df-158">The following table summarizes property information exposed by PropertyEntry:</span></span>

| <span data-ttu-id="e19df-159">PropertyEntry 成員</span><span class="sxs-lookup"><span data-stu-id="e19df-159">PropertyEntry member</span></span>                               | <span data-ttu-id="e19df-160">描述</span><span class="sxs-lookup"><span data-stu-id="e19df-160">Description</span></span>
|:-------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.CurrentValue?displayProperty=nameWithType>  | <span data-ttu-id="e19df-161">取得和設定屬性的目前值。</span><span class="sxs-lookup"><span data-stu-id="e19df-161">Gets and sets the current value of the property.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.OriginalValue?displayProperty=nameWithType> | <span data-ttu-id="e19df-162">取得和設定屬性的原始值（如果有的話）。</span><span class="sxs-lookup"><span data-stu-id="e19df-162">Gets and sets the original value of the property, if available.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.EntityEntry?displayProperty=nameWithType>   | <span data-ttu-id="e19df-163">實體的反向參考 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> 。</span><span class="sxs-lookup"><span data-stu-id="e19df-163">A back reference to the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> for the entity.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.Metadata?displayProperty=nameWithType>          | <span data-ttu-id="e19df-164"><xref:Microsoft.EntityFrameworkCore.Metadata.IProperty> 屬性的中繼資料。</span><span class="sxs-lookup"><span data-stu-id="e19df-164"><xref:Microsoft.EntityFrameworkCore.Metadata.IProperty> metadata for the property.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified?displayProperty=nameWithType>        | <span data-ttu-id="e19df-165">指出此屬性是否標示為已修改，並允許變更此狀態。</span><span class="sxs-lookup"><span data-stu-id="e19df-165">Indicates whether this property is marked as modified, and allows this state to be changed.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary?displayProperty=nameWithType>       | <span data-ttu-id="e19df-166">指出此屬性是否標記為 [暫存](xref:core/change-tracking/miscellaneous#temporary-values#temporary-values)，並允許變更此狀態。</span><span class="sxs-lookup"><span data-stu-id="e19df-166">Indicates whether this property is marked as [temporary](xref:core/change-tracking/miscellaneous#temporary-values#temporary-values), and allows this state to be changed.</span></span>

<span data-ttu-id="e19df-167">注意：</span><span class="sxs-lookup"><span data-stu-id="e19df-167">Notes:</span></span>

- <span data-ttu-id="e19df-168">屬性的原始值是在從資料庫查詢實體時，該屬性所擁有的值。</span><span class="sxs-lookup"><span data-stu-id="e19df-168">The original value of a property is the value that the property had when the entity was queried from the database.</span></span> <span data-ttu-id="e19df-169">但是，如果實體已中斷連接，然後明確附加至另一個 DbCoNtext （例如，使用或），則無法使用原始值 `Attach` `Update` 。</span><span class="sxs-lookup"><span data-stu-id="e19df-169">However, original values are not available if the entity was disconnected and then explicitly attached to another DbContext, for example with `Attach` or `Update`.</span></span> <span data-ttu-id="e19df-170">在此情況下，傳回的原始值將會與目前的值相同。</span><span class="sxs-lookup"><span data-stu-id="e19df-170">In this case, the original value returned will be the same as the current value.</span></span>
- <span data-ttu-id="e19df-171"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 只會更新標示為已修改的屬性。</span><span class="sxs-lookup"><span data-stu-id="e19df-171"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> will only update properties marked as modified.</span></span> <span data-ttu-id="e19df-172">設定 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified> 為 true 以強制 EF Core 更新指定的屬性值，或將它設定為 false，以防止 EF Core 更新屬性值。</span><span class="sxs-lookup"><span data-stu-id="e19df-172">Set <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified> to true to force EF Core to update a given property value, or set it to false to prevent EF Core from updating the property value.</span></span>
- <span data-ttu-id="e19df-173">[暫存值](xref:core/change-tracking/miscellaneous) 通常是由 EF Core [值](xref:core/modeling/generated-properties)產生器所產生。</span><span class="sxs-lookup"><span data-stu-id="e19df-173">[Temporary values](xref:core/change-tracking/miscellaneous) are typically generated by EF Core [value generators](xref:core/modeling/generated-properties).</span></span> <span data-ttu-id="e19df-174">設定屬性的目前值會將暫存值取代為指定的值，並將該屬性標示為不是暫時性的。</span><span class="sxs-lookup"><span data-stu-id="e19df-174">Setting the current value of a property will replace the temporary value with the given value and mark the property as not temporary.</span></span> <span data-ttu-id="e19df-175"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary>如果設定為 true，則即使在明確設定值之後，也會強制將值設為暫時性。</span><span class="sxs-lookup"><span data-stu-id="e19df-175">Set <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary> to true to force a value to be temporary even after it has been explicitly set.</span></span>

### <a name="working-with-a-single-navigation"></a><span data-ttu-id="e19df-176">使用單一導覽</span><span class="sxs-lookup"><span data-stu-id="e19df-176">Working with a single navigation</span></span>

<span data-ttu-id="e19df-177">、和的數個多載 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> 可讓您存取個別流覽的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="e19df-177">Several overloads of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A?displayProperty=nameWithType>, and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> allow access to information about an individual navigation.</span></span>

<span data-ttu-id="e19df-178">透過方法存取單一相關實體的參考導覽 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A> 。</span><span class="sxs-lookup"><span data-stu-id="e19df-178">Reference navigations to a single related entity are accessed through the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A> methods.</span></span> <span data-ttu-id="e19df-179">參考導覽指向一對多關聯性的「一」側邊，以及一對一關聯性的兩端。</span><span class="sxs-lookup"><span data-stu-id="e19df-179">Reference navigations point to the "one" sides of one-to-many relationships, and both sides of one-to-one relationships.</span></span> <span data-ttu-id="e19df-180">例如：</span><span class="sxs-lookup"><span data-stu-id="e19df-180">For example:</span></span>

<!--
        ReferenceEntry<Post, Blog> referenceEntry1 = context.Entry(post).Reference(e => e.Blog);
        ReferenceEntry<Post, Blog> referenceEntry2 = context.Entry(post).Reference<Blog>("Blog");
        ReferenceEntry referenceEntry3 = context.Entry(post).Reference("Blog");
-->
[!code-csharp[Work_with_a_single_navigation_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_1)]

<span data-ttu-id="e19df-181">當針對一對多和多對多關聯性的「多」端使用時，導覽也可以是相關實體的集合。</span><span class="sxs-lookup"><span data-stu-id="e19df-181">Navigations can also be collections of related entities when used for the "many" sides of one-to-many and many-to-many relationships.</span></span> <span data-ttu-id="e19df-182">這些 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A> 方法可用來存取集合導覽。</span><span class="sxs-lookup"><span data-stu-id="e19df-182">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A> methods are used to access collection navigations.</span></span> <span data-ttu-id="e19df-183">例如：</span><span class="sxs-lookup"><span data-stu-id="e19df-183">For example:</span></span>

<!--
        CollectionEntry<Blog, Post> collectionEntry1 = context.Entry(blog).Collection(e => e.Posts);
        CollectionEntry<Blog, Post> collectionEntry2 = context.Entry(blog).Collection<Post>("Posts");
        CollectionEntry collectionEntry3 = context.Entry(blog).Collection("Posts");
-->
[!code-csharp[Work_with_a_single_navigation_2a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_2a)]

<span data-ttu-id="e19df-184">某些作業對所有導覽都很常見。</span><span class="sxs-lookup"><span data-stu-id="e19df-184">Some operations are common for all navigations.</span></span> <span data-ttu-id="e19df-185">您可以使用方法來存取參考和集合導覽 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="e19df-185">These can be accessed for both reference and collection navigations using the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> method.</span></span> <span data-ttu-id="e19df-186">請注意，同時存取所有導覽時，只會提供非一般存取。</span><span class="sxs-lookup"><span data-stu-id="e19df-186">Note that only non-generic access is available when accessing all navigations together.</span></span> <span data-ttu-id="e19df-187">例如：</span><span class="sxs-lookup"><span data-stu-id="e19df-187">For example:</span></span>

<!--
        NavigationEntry navigationEntry = context.Entry(blog).Navigation("Posts");
-->
[!code-csharp[Work_with_a_single_navigation_2b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_2b)]

<span data-ttu-id="e19df-188">下表摘要說明使用 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ReferenceEntry%602> 、和的方式 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.CollectionEntry%602> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> ：</span><span class="sxs-lookup"><span data-stu-id="e19df-188">The following table summarizes ways to use <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ReferenceEntry%602>, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.CollectionEntry%602>, and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry>:</span></span>

| <span data-ttu-id="e19df-189">NavigationEntry 成員</span><span class="sxs-lookup"><span data-stu-id="e19df-189">NavigationEntry member</span></span>                                                                                    | <span data-ttu-id="e19df-190">描述</span><span class="sxs-lookup"><span data-stu-id="e19df-190">Description</span></span>
|:----------------------------------------------------------------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.MemberEntry.CurrentValue?displayProperty=nameWithType> | <span data-ttu-id="e19df-191">取得和設定導覽的目前值。</span><span class="sxs-lookup"><span data-stu-id="e19df-191">Gets and sets the current value of the navigation.</span></span> <span data-ttu-id="e19df-192">這是集合導覽的整個集合。</span><span class="sxs-lookup"><span data-stu-id="e19df-192">This is the entire collection for collection navigations.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Metadata?displayProperty=nameWithType> | <span data-ttu-id="e19df-193"><xref:Microsoft.EntityFrameworkCore.Metadata.INavigationBase> 導覽的中繼資料。</span><span class="sxs-lookup"><span data-stu-id="e19df-193"><xref:Microsoft.EntityFrameworkCore.Metadata.INavigationBase> metadata for the navigation.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.IsLoaded?displayProperty=nameWithType> | <span data-ttu-id="e19df-194">取得或設定值，這個值表示是否已從資料庫完全載入相關實體或集合。</span><span class="sxs-lookup"><span data-stu-id="e19df-194">Gets or sets a value indicating whether the related entity or collection has been fully loaded from the database.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Load?displayProperty=nameWithType>     | <span data-ttu-id="e19df-195">從資料庫載入相關的實體或集合;請參閱 [明確載入相關資料](xref:core/querying/related-data/explicit)。</span><span class="sxs-lookup"><span data-stu-id="e19df-195">Loads the related entity or collection from the database; see [Explicit Loading of Related Data](xref:core/querying/related-data/explicit).</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Query?displayProperty=nameWithType>    | <span data-ttu-id="e19df-196">查詢 EF Core 會使用將此導覽載入為 `IQueryable` 可進一步組成的，請參閱 [明確載入相關資料](xref:core/querying/related-data/explicit)。</span><span class="sxs-lookup"><span data-stu-id="e19df-196">The query EF Core would use to load this navigation as an `IQueryable` that can be further composed; see [Explicit Loading of Related Data](xref:core/querying/related-data/explicit).</span></span>

### <a name="working-with-all-properties-of-an-entity"></a><span data-ttu-id="e19df-197">使用實體的所有屬性</span><span class="sxs-lookup"><span data-stu-id="e19df-197">Working with all properties of an entity</span></span>

<span data-ttu-id="e19df-198"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Properties?displayProperty=nameWithType><xref:System.Collections.Generic.IEnumerable%601> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> 針對實體的每個屬性，傳回的。</span><span class="sxs-lookup"><span data-stu-id="e19df-198"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Properties?displayProperty=nameWithType> returns an <xref:System.Collections.Generic.IEnumerable%601> of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> for every property of the entity.</span></span> <span data-ttu-id="e19df-199">這可以用來對實體的每個屬性執行動作。</span><span class="sxs-lookup"><span data-stu-id="e19df-199">This can be used to perform an action for every property of the entity.</span></span> <span data-ttu-id="e19df-200">例如，若要將任何 DateTime 屬性設定為 `DateTime.Now` ：</span><span class="sxs-lookup"><span data-stu-id="e19df-200">For example, to set any DateTime property to `DateTime.Now`:</span></span>

<!--
        foreach (var propertyEntry in context.Entry(blog).Properties)
        {
            if (propertyEntry.Metadata.ClrType == typeof(DateTime))
            {
                propertyEntry.CurrentValue = DateTime.Now;
            }
        }
-->
[!code-csharp[Work_with_all_properties_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_1)]

<span data-ttu-id="e19df-201">此外，Entityentry.state 還包含數個方法，可同時取得和設定所有屬性值。</span><span class="sxs-lookup"><span data-stu-id="e19df-201">In addition, EntityEntry contains several methods to get and set all property values at the same time.</span></span> <span data-ttu-id="e19df-202">這些方法會使用 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues> 類別，此類別代表屬性及其值的集合。</span><span class="sxs-lookup"><span data-stu-id="e19df-202">These methods use the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues> class, which represents a collection of properties and their values.</span></span> <span data-ttu-id="e19df-203">您可以取得目前或原始值的 PropertyValues，或目前儲存在資料庫中的值。</span><span class="sxs-lookup"><span data-stu-id="e19df-203">PropertyValues can be obtained for current or original values, or for the values as currently stored in the database.</span></span> <span data-ttu-id="e19df-204">例如：</span><span class="sxs-lookup"><span data-stu-id="e19df-204">For example:</span></span>

<!--
        var currentValues = context.Entry(blog).CurrentValues;
        var originalValues = context.Entry(blog).OriginalValues;
        var databaseValues = context.Entry(blog).GetDatabaseValues();
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2a)]

<span data-ttu-id="e19df-205">這些 PropertyValues 物件並不太實用。</span><span class="sxs-lookup"><span data-stu-id="e19df-205">These PropertyValues objects are not very useful on their own.</span></span> <span data-ttu-id="e19df-206">不過，它們可以合併以執行操作實體時所需的一般作業。</span><span class="sxs-lookup"><span data-stu-id="e19df-206">However, they can be combined to perform common operations needed when manipulating entities.</span></span> <span data-ttu-id="e19df-207">當使用資料傳輸物件以及解決 [開放式平行存取衝突](xref:core/saving/concurrency)時，這非常有用。</span><span class="sxs-lookup"><span data-stu-id="e19df-207">This is useful when working with data transfer objects and when resolving [optimistic concurrency conflicts](xref:core/saving/concurrency).</span></span> <span data-ttu-id="e19df-208">下列各節顯示一些範例。</span><span class="sxs-lookup"><span data-stu-id="e19df-208">The following sections show some examples.</span></span>

#### <a name="setting-current-or-original-values-from-an-entity-or-dto"></a><span data-ttu-id="e19df-209">從實體或 DTO 設定目前或原始的值</span><span class="sxs-lookup"><span data-stu-id="e19df-209">Setting current or original values from an entity or DTO</span></span>

<span data-ttu-id="e19df-210">您可以從另一個物件複製值，藉以更新實體的目前或原始值。</span><span class="sxs-lookup"><span data-stu-id="e19df-210">The current or original values of an entity can be updated by copying values from another object.</span></span> <span data-ttu-id="e19df-211">例如，假設有一個 `BlogDto` 資料傳輸物件 (DTO) 具有與實體類型相同的屬性：</span><span class="sxs-lookup"><span data-stu-id="e19df-211">For example, consider a `BlogDto` data transfer object (DTO) with the same properties as the entity type:</span></span>

<!--
public class BlogDto
{
    public int Id { get; set; }
    public string Name { get; set; }
}
-->
[!code-csharp[BlogDto](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=BlogDto)]

<span data-ttu-id="e19df-212">這可以用來設定追蹤實體目前的值 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> ：</span><span class="sxs-lookup"><span data-stu-id="e19df-212">This can be used to set the current values of a tracked entity using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType>:</span></span>

<!--
        var blogDto = new BlogDto { Id = 1, Name = "1unicorn2" };

        context.Entry(blog).CurrentValues.SetValues(blogDto);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2b)]

<span data-ttu-id="e19df-213">使用從服務呼叫取得的值或多層式應用程式中的用戶端來更新實體時，有時會使用這項技術。</span><span class="sxs-lookup"><span data-stu-id="e19df-213">This technique is sometimes used when updating an entity with values obtained from a service call or a client in an n-tier application.</span></span> <span data-ttu-id="e19df-214">請注意，使用的物件不一定要與實體具有相同的類型，只要它的屬性名稱符合實體的名稱即可。</span><span class="sxs-lookup"><span data-stu-id="e19df-214">Note that the object used does not have to be of the same type as the entity so long as it has properties whose names match those of the entity.</span></span> <span data-ttu-id="e19df-215">在上述範例中，會使用 DTO 的實例 `BlogDto` 來設定追蹤實體的目前值 `Blog` 。</span><span class="sxs-lookup"><span data-stu-id="e19df-215">In the example above, an instance of the DTO `BlogDto` is used to set the current values of a tracked `Blog` entity.</span></span>

<span data-ttu-id="e19df-216">請注意，如果設定的值與目前的值不同，屬性就會標示為已修改。</span><span class="sxs-lookup"><span data-stu-id="e19df-216">Note that properties will only be marked as modified if the value set differs from the current value.</span></span>

#### <a name="setting-current-or-original-values-from-a-dictionary"></a><span data-ttu-id="e19df-217">從字典設定目前或原始的值</span><span class="sxs-lookup"><span data-stu-id="e19df-217">Setting current or original values from a dictionary</span></span>

<span data-ttu-id="e19df-218">先前的範例會從實體或 DTO 實例設定值。</span><span class="sxs-lookup"><span data-stu-id="e19df-218">The previous example set values from an entity or DTO instance.</span></span> <span data-ttu-id="e19df-219">當屬性值在字典中儲存為名稱/值組時，就可以使用相同的行為。</span><span class="sxs-lookup"><span data-stu-id="e19df-219">The same behavior is available when property values are stored as name/value pairs in a dictionary.</span></span> <span data-ttu-id="e19df-220">例如：</span><span class="sxs-lookup"><span data-stu-id="e19df-220">For example:</span></span>

<!--
        var blogDictionary = new Dictionary<string, object>
        {
            ["Id"] = 1,
            ["Name"] = "1unicorn2"
        };

        context.Entry(blog).CurrentValues.SetValues(blogDictionary);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2d](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2d)]

#### <a name="setting-current-or-original-values-from-the-database"></a><span data-ttu-id="e19df-221">從資料庫設定目前或原始的值</span><span class="sxs-lookup"><span data-stu-id="e19df-221">Setting current or original values from the database</span></span>

<span data-ttu-id="e19df-222">您可以藉由呼叫 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> 或 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValuesAsync%2A> 並使用傳回的物件來設定目前或原始值，或同時使用傳回的物件，從資料庫的最新值更新實體的目前或原始值。</span><span class="sxs-lookup"><span data-stu-id="e19df-222">The current or original values of an entity can be updated with the latest values from the database by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> or <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValuesAsync%2A> and using the returned object to set current or original values, or both.</span></span> <span data-ttu-id="e19df-223">例如：</span><span class="sxs-lookup"><span data-stu-id="e19df-223">For example:</span></span>

<!--
        var databaseValues = context.Entry(blog).GetDatabaseValues();
        context.Entry(blog).CurrentValues.SetValues(databaseValues);
        context.Entry(blog).OriginalValues.SetValues(databaseValues);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2c)]

#### <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a><span data-ttu-id="e19df-224">建立包含目前、原始或資料庫值的複製物件</span><span class="sxs-lookup"><span data-stu-id="e19df-224">Creating a cloned object containing current, original, or database values</span></span>

<span data-ttu-id="e19df-225">從 CurrentValues、OriginalValues 或 GetDatabaseValues 傳回的 PropertyValues 物件，可以用來建立使用之實體的複製 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.ToObject?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="e19df-225">The PropertyValues object returned from CurrentValues, OriginalValues, or GetDatabaseValues can be used to create a clone of the entity using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.ToObject?displayProperty=nameWithType>.</span></span> <span data-ttu-id="e19df-226">例如：</span><span class="sxs-lookup"><span data-stu-id="e19df-226">For example:</span></span>

<!--
var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2e](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2e)]

<span data-ttu-id="e19df-227">請注意， `ToObject` 會傳回 DbCoNtext 不會追蹤的新實例。</span><span class="sxs-lookup"><span data-stu-id="e19df-227">Note that `ToObject` returns a new instance that is not tracked by the DbContext.</span></span> <span data-ttu-id="e19df-228">傳回的物件也沒有任何設定為其他實體的關聯性。</span><span class="sxs-lookup"><span data-stu-id="e19df-228">The returned object also does not have any relationships set to other entities.</span></span>

<span data-ttu-id="e19df-229">複製的物件有助於解決與資料庫並行更新相關的問題，尤其是當資料系結至特定類型的物件時。</span><span class="sxs-lookup"><span data-stu-id="e19df-229">The cloned object can be useful for resolving issues related to concurrent updates to the database, especially when data binding to objects of a certain type.</span></span> <span data-ttu-id="e19df-230">如需詳細資訊，請參閱 [開放式並行](xref:core/saving/concurrency) 存取。</span><span class="sxs-lookup"><span data-stu-id="e19df-230">See [optimistic concurrency](xref:core/saving/concurrency) for more information.</span></span>

### <a name="working-with-all-navigations-of-an-entity"></a><span data-ttu-id="e19df-231">使用實體的所有導覽</span><span class="sxs-lookup"><span data-stu-id="e19df-231">Working with all navigations of an entity</span></span>

<span data-ttu-id="e19df-232"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigations?displayProperty=nameWithType><xref:System.Collections.Generic.IEnumerable%601> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> 針對每個實體導覽，傳回的。</span><span class="sxs-lookup"><span data-stu-id="e19df-232"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigations?displayProperty=nameWithType> returns an <xref:System.Collections.Generic.IEnumerable%601> of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> for every navigation of the entity.</span></span> <span data-ttu-id="e19df-233"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.References?displayProperty=nameWithType> 並 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Collections?displayProperty=nameWithType> 進行相同的動作，但僅限於參考或集合導覽。</span><span class="sxs-lookup"><span data-stu-id="e19df-233"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.References?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Collections?displayProperty=nameWithType> do the same thing, but restricted to reference or collection navigations respectively.</span></span> <span data-ttu-id="e19df-234">這可以用來對實體的每個導覽執行動作。</span><span class="sxs-lookup"><span data-stu-id="e19df-234">This can be used to perform an action for every navigation of the entity.</span></span> <span data-ttu-id="e19df-235">例如，若要強制載入所有相關實體：</span><span class="sxs-lookup"><span data-stu-id="e19df-235">For example, to force loading of all related entities:</span></span>

<!--
        foreach (var navigationEntry in context.Entry(blog).Navigations)
        {
            navigationEntry.Load();
        }
-->
[!code-csharp[Work_with_all_navigations_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_navigations_of_an_entity_1)]

### <a name="working-with-all-members-of-an-entity"></a><span data-ttu-id="e19df-236">使用實體的所有成員</span><span class="sxs-lookup"><span data-stu-id="e19df-236">Working with all members of an entity</span></span>

<span data-ttu-id="e19df-237">一般屬性和導覽屬性有不同的狀態和行為。</span><span class="sxs-lookup"><span data-stu-id="e19df-237">Regular properties and navigation properties have different state and behavior.</span></span> <span data-ttu-id="e19df-238">因此，通常會分開處理導覽和非導覽，如上述各節所示。</span><span class="sxs-lookup"><span data-stu-id="e19df-238">It is therefore common to process navigations and non-navigations separately, as shown in the sections above.</span></span> <span data-ttu-id="e19df-239">但是，無論實體的成員是一般屬性或導覽，有時也會很有用。</span><span class="sxs-lookup"><span data-stu-id="e19df-239">However, sometimes it can be useful to do something with any member of the entity, regardless of whether it is a regular property or navigation.</span></span> <span data-ttu-id="e19df-240"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Member%2A?displayProperty=nameWithType><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Members?displayProperty=nameWithType>提供此用途。</span><span class="sxs-lookup"><span data-stu-id="e19df-240"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Member%2A?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Members?displayProperty=nameWithType> are provided for this purpose.</span></span> <span data-ttu-id="e19df-241">例如：</span><span class="sxs-lookup"><span data-stu-id="e19df-241">For example:</span></span>

<!--
        foreach (var memberEntry in context.Entry(blog).Members)
        {
            Console.WriteLine(
                $"Member {memberEntry.Metadata.Name} is of type {memberEntry.Metadata.ClrType.ShortDisplayName()} and has value {memberEntry.CurrentValue}");
        }
-->
[!code-csharp[Work_with_all_members_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_members_of_an_entity_1)]

<span data-ttu-id="e19df-242">在範例的 blog 上執行此程式碼會產生下列輸出：</span><span class="sxs-lookup"><span data-stu-id="e19df-242">Running this code on a blog from the sample generates the following output:</span></span>

```output
Member Id is of type int and has value 1
Member Name is of type string and has value .NET Blog
Member Posts is of type IList<Post> and has value System.Collections.Generic.List`1[Post]
```

> [!TIP]
> <span data-ttu-id="e19df-243">[ [變更追蹤](xref:core/change-tracking/debug-views) 器] 偵錯工具會顯示如下的資訊。</span><span class="sxs-lookup"><span data-stu-id="e19df-243">The [change tracker debug view](xref:core/change-tracking/debug-views) shows information like this.</span></span> <span data-ttu-id="e19df-244">整個變更追蹤器的偵錯工具是從 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DebugView?displayProperty=nameWithType> 每個追蹤實體的個體產生的。</span><span class="sxs-lookup"><span data-stu-id="e19df-244">The debug view for the entire change tracker is generated from the individual <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DebugView?displayProperty=nameWithType> of each tracked entity.</span></span>

## <a name="find-and-findasync"></a><span data-ttu-id="e19df-245">尋找和 FindAsync</span><span class="sxs-lookup"><span data-stu-id="e19df-245">Find and FindAsync</span></span>

<span data-ttu-id="e19df-246"><xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>、 <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType> 、 <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> 和的 <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> 設計目的，是為了在已知單一實體的主要索引鍵時有效率地查閱。</span><span class="sxs-lookup"><span data-stu-id="e19df-246"><xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType>, and <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> are designed for efficient lookup of a single entity when its primary key is known.</span></span> <span data-ttu-id="e19df-247">Find 會先檢查是否已追蹤實體，如果是，則會立即傳回該實體。</span><span class="sxs-lookup"><span data-stu-id="e19df-247">Find first checks if the entity is already tracked, and if so returns the entity immediately.</span></span> <span data-ttu-id="e19df-248">只有在未在本機追蹤實體時，才會進行資料庫查詢。</span><span class="sxs-lookup"><span data-stu-id="e19df-248">A database query is only made if the entity is not tracked locally.</span></span> <span data-ttu-id="e19df-249">例如，請考慮下列程式碼，此程式碼會針對相同的實體呼叫兩次尋找：</span><span class="sxs-lookup"><span data-stu-id="e19df-249">For example, consider this code that calls Find twice for the same entity:</span></span>

<!--
        using var context = new BlogsContext();

        Console.WriteLine("First call to Find...");
        var blog1 = context.Blogs.Find(1);

        Console.WriteLine($"...found blog {blog1.Name}");

        Console.WriteLine();
        Console.WriteLine("Second call to Find...");
        var blog2 = context.Blogs.Find(1);
        Debug.Assert(blog1 == blog2);

        Console.WriteLine("...returned the same instance without executing a query.");
-->
[!code-csharp[Find_and_FindAsync_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Find_and_FindAsync_1)]

<span data-ttu-id="e19df-250">使用 SQLite 時，此程式碼 (包括 EF Core 記錄) 的輸出為：</span><span class="sxs-lookup"><span data-stu-id="e19df-250">The output from this code (including EF Core logging) when using SQLite is:</span></span>

```output
First call to Find...
info: 12/29/2020 07:45:53.682 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (1ms) [Parameters=[@__p_0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
      SELECT "b"."Id", "b"."Name"
      FROM "Blogs" AS "b"
      WHERE "b"."Id" = @__p_0
      LIMIT 1
...found blog .NET Blog

Second call to Find...
...returned the same instance without executing a query.
```

<span data-ttu-id="e19df-251">請注意，第一次呼叫在本機找不到實體，因此會執行資料庫查詢。</span><span class="sxs-lookup"><span data-stu-id="e19df-251">Notice that the first call does not find the entity locally and so executes a database query.</span></span> <span data-ttu-id="e19df-252">相反地，第二個呼叫會傳回相同的實例，而不會查詢資料庫，因為它已經被追蹤。</span><span class="sxs-lookup"><span data-stu-id="e19df-252">Conversely, the second call returns the same instance without querying the database because it is already being tracked.</span></span>

<span data-ttu-id="e19df-253">如果未在本機追蹤具有指定索引鍵的實體，而且不存在於資料庫中，則 Find 會傳回 null。</span><span class="sxs-lookup"><span data-stu-id="e19df-253">Find returns null if an entity with the given key is not tracked locally and does not exist in the database.</span></span>

### <a name="composite-keys"></a><span data-ttu-id="e19df-254">複合索引鍵</span><span class="sxs-lookup"><span data-stu-id="e19df-254">Composite keys</span></span>

<span data-ttu-id="e19df-255">Find 也可以搭配複合索引鍵使用。</span><span class="sxs-lookup"><span data-stu-id="e19df-255">Find can also be used with composite keys.</span></span> <span data-ttu-id="e19df-256">例如，假設有一個 `OrderLine` 具有由訂單識別碼和產品識別碼組成之複合索引鍵的實體：</span><span class="sxs-lookup"><span data-stu-id="e19df-256">For example, consider an `OrderLine` entity with a composite key consisting of the order ID and the product ID:</span></span>

<!--
public class OrderLine
{
    public int OrderId { get; set; }
    public int ProductId { get; set; }

    //...
}
-->
[!code-csharp[OrderLine](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=OrderLine)]

<span data-ttu-id="e19df-257">您必須在中設定複合索引鍵， <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> 以定義主要部分 _及其順序_。</span><span class="sxs-lookup"><span data-stu-id="e19df-257">The composite key must be configured in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> to define the key parts _and their order_.</span></span> <span data-ttu-id="e19df-258">例如：</span><span class="sxs-lookup"><span data-stu-id="e19df-258">For example:</span></span>

<!--
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder
            .Entity<OrderLine>()
            .HasKey(e => new { e.OrderId, e.ProductId });
    }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=OnModelCreating)]

<span data-ttu-id="e19df-259">請注意， `OrderId` 是金鑰的第一個部分，而 `ProductId` 是索引鍵的第二個部分。</span><span class="sxs-lookup"><span data-stu-id="e19df-259">Notice that `OrderId` is the first part of the key and `ProductId` is the second part of the key.</span></span> <span data-ttu-id="e19df-260">傳遞要尋找的索引鍵值時，必須使用此順序。</span><span class="sxs-lookup"><span data-stu-id="e19df-260">This order must be used when passing key values to Find.</span></span> <span data-ttu-id="e19df-261">例如：</span><span class="sxs-lookup"><span data-stu-id="e19df-261">For example:</span></span>

<!--
        var orderline = context.OrderLines.Find(orderId, productId);
-->
[!code-csharp[Find_and_FindAsync_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Find_and_FindAsync_2)]

## <a name="using-changetrackerentries-to-access-all-tracked-entities"></a><span data-ttu-id="e19df-262">使用 ChangeTracker 存取所有已追蹤的實體</span><span class="sxs-lookup"><span data-stu-id="e19df-262">Using ChangeTracker.Entries to access all tracked entities</span></span>

<span data-ttu-id="e19df-263">到目前為止，我們一次只存取了一個 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> 。</span><span class="sxs-lookup"><span data-stu-id="e19df-263">So far we have accessed only a single <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> at a time.</span></span> <span data-ttu-id="e19df-264"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> 傳回 DbCoNtext 目前追蹤之每個實體的 Entityentry.state。</span><span class="sxs-lookup"><span data-stu-id="e19df-264"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> returns an EntityEntry for every entity currently tracked by the DbContext.</span></span> <span data-ttu-id="e19df-265">例如：</span><span class="sxs-lookup"><span data-stu-id="e19df-265">For example:</span></span>

<!--
        using var context = new BlogsContext();
        var blogs = context.Blogs.Include(e => e.Posts).ToList();

        foreach (var entityEntry in context.ChangeTracker.Entries())
        {
            Console.WriteLine($"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property("Id").CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1a)]

<span data-ttu-id="e19df-266">這會程式碼產生下列輸出：</span><span class="sxs-lookup"><span data-stu-id="e19df-266">This code generates the following output:</span></span>

```output
Found Blog entity with ID 1
Found Post entity with ID 1
Found Post entity with ID 2
```

<span data-ttu-id="e19df-267">請注意，系統會傳回 blog 和 post 的專案。</span><span class="sxs-lookup"><span data-stu-id="e19df-267">Notice that entries for both blogs and posts are returned.</span></span> <span data-ttu-id="e19df-268">您可以改用泛型多載，將結果篩選成特定的實體類型 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> ：</span><span class="sxs-lookup"><span data-stu-id="e19df-268">The results can instead be filtered to a specific entity type using the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> generic overload:</span></span>

<!--
        foreach (var entityEntry in context.ChangeTracker.Entries<Post>())
        {
            Console.WriteLine(
                $"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property(e => e.Id).CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1b)]

<span data-ttu-id="e19df-269">這段程式碼的輸出顯示只會傳回文章：</span><span class="sxs-lookup"><span data-stu-id="e19df-269">The output from this code shows that only posts are returned:</span></span>

```output
Found Post entity with ID 1
Found Post entity with ID 2
```

<span data-ttu-id="e19df-270">此外，使用泛型多載會傳回泛型 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> 實例。</span><span class="sxs-lookup"><span data-stu-id="e19df-270">Also, using the generic overload returns generic <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instances.</span></span> <span data-ttu-id="e19df-271">這可讓您 `Id` 在此範例中，對屬性進行類似的存取。</span><span class="sxs-lookup"><span data-stu-id="e19df-271">This is what allows that fluent-like access to the `Id` property in this example.</span></span>

<span data-ttu-id="e19df-272">用於篩選的泛型型別不需要是對應的實體類型;您可以改為使用未對應的基底類型或介面。</span><span class="sxs-lookup"><span data-stu-id="e19df-272">The generic type used for filtering does not have to be a mapped entity type; an unmapped base type or interface can be used instead.</span></span> <span data-ttu-id="e19df-273">例如，如果模型中的所有實體類型都執行定義其索引鍵屬性的介面：</span><span class="sxs-lookup"><span data-stu-id="e19df-273">For example, if all the entity types in the model implement an interface defining their key property:</span></span>

<!--
public interface IEntityWithKey
{
    int Id { get; set; }
}
-->
[!code-csharp[IEntityWithKey](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=IEntityWithKey)]

<span data-ttu-id="e19df-274">然後，您可以使用這個介面，以強型別方式來處理任何追蹤實體的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="e19df-274">Then this interface can be used to work with the key of any tracked entity in a strongly-typed manner.</span></span> <span data-ttu-id="e19df-275">例如：</span><span class="sxs-lookup"><span data-stu-id="e19df-275">For example:</span></span>

<!--
        foreach (var entityEntry in context.ChangeTracker.Entries<IEntityWithKey>())
        {
            Console.WriteLine(
                $"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property(e => e.Id).CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1c)]

## <a name="using-dbsetlocal-to-query-tracked-entities"></a><span data-ttu-id="e19df-276">使用 DbSet 來查詢追蹤的實體</span><span class="sxs-lookup"><span data-stu-id="e19df-276">Using DbSet.Local to query tracked entities</span></span>

<span data-ttu-id="e19df-277">EF Core 查詢一律會在資料庫上執行，而且只會傳回已儲存至資料庫的實體。</span><span class="sxs-lookup"><span data-stu-id="e19df-277">EF Core queries are always executed on the database, and only return entities that have been saved to the database.</span></span> <span data-ttu-id="e19df-278"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> 提供一種機制，可查詢本機、追蹤實體的 DbCoNtext。</span><span class="sxs-lookup"><span data-stu-id="e19df-278"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> provides a mechanism to query the DbContext for local, tracked entities.</span></span>

<span data-ttu-id="e19df-279">因為 `DbSet.Local` 是用來查詢追蹤的實體，所以通常會將實體載入 DbCoNtext，然後使用這些載入的實體。</span><span class="sxs-lookup"><span data-stu-id="e19df-279">Since `DbSet.Local` is used to query tracked entities, it is typical to load entities into the DbContext and then work with those loaded entities.</span></span> <span data-ttu-id="e19df-280">這特別適用于資料系結，但在其他情況下也會很有用。</span><span class="sxs-lookup"><span data-stu-id="e19df-280">This is especially true for data binding, but can also be useful in other situations.</span></span> <span data-ttu-id="e19df-281">例如，在下列程式碼中，會先針對所有的 blog 和 post 查詢資料庫。</span><span class="sxs-lookup"><span data-stu-id="e19df-281">For example, in the following code the database is first queried for all blogs and posts.</span></span> <span data-ttu-id="e19df-282"><xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.Load%2A>擴充方法是用來執行此查詢，其中包含內容所追蹤的結果，而不會直接傳回給應用程式。</span><span class="sxs-lookup"><span data-stu-id="e19df-282">The <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.Load%2A> extension method is used to execute this query with the results tracked by the context without being returned directly to the application.</span></span> <span data-ttu-id="e19df-283"> (使用 `ToList` 或類似的效果相同，但有建立傳回清單的額外負荷，此處不需要。 ) 此範例會使用 `DbSet.Local` 來存取本機追蹤的實體：</span><span class="sxs-lookup"><span data-stu-id="e19df-283">(Using `ToList` or similar has the same effect but with the overhead of creating the returned list, which is not needed here.) The example then uses `DbSet.Local` to access the locally tracked entities:</span></span>

<!--
        using var context = new BlogsContext();

        context.Blogs.Include(e => e.Posts).Load();

        foreach (var blog in context.Blogs.Local)
        {
            Console.WriteLine($"Blog: {blog.Name}");
        }

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_1)]

<span data-ttu-id="e19df-284">請注意，不同的是 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> ，會 `DbSet.Local` 直接傳回實體實例。</span><span class="sxs-lookup"><span data-stu-id="e19df-284">Notice that, unlike <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType>, `DbSet.Local` returns entity instances directly.</span></span> <span data-ttu-id="e19df-285">當然，Entityentry.state 可以藉由呼叫來取得所傳回之實體的永遠。 <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="e19df-285">An EntityEntry can, of course, always be obtained for the returned entity by calling <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType>.</span></span>

### <a name="the-local-view"></a><span data-ttu-id="e19df-286">本機視圖</span><span class="sxs-lookup"><span data-stu-id="e19df-286">The local view</span></span>

<span data-ttu-id="e19df-287"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> 傳回可反映這些實體目前的本機追蹤實體的視圖 <xref:Microsoft.EntityFrameworkCore.EntityState> 。</span><span class="sxs-lookup"><span data-stu-id="e19df-287"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> returns a view of locally tracked entities that reflects the current <xref:Microsoft.EntityFrameworkCore.EntityState> of those entities.</span></span> <span data-ttu-id="e19df-288">具體來說，這表示：</span><span class="sxs-lookup"><span data-stu-id="e19df-288">Specifically, this means that:</span></span>

- <span data-ttu-id="e19df-289">`Added` 包括實體。</span><span class="sxs-lookup"><span data-stu-id="e19df-289">`Added` entities are included.</span></span> <span data-ttu-id="e19df-290">請注意，這不是一般 EF Core 查詢的情況，因為 `Added` 實體尚未存在於資料庫中，因此不會由資料庫查詢傳回。</span><span class="sxs-lookup"><span data-stu-id="e19df-290">Note that this is not the case for normal EF Core queries, since `Added` entities do not yet exist in the database and so are therefore never returned by a database query.</span></span>
- <span data-ttu-id="e19df-291">`Deleted` 排除實體。</span><span class="sxs-lookup"><span data-stu-id="e19df-291">`Deleted` entities are excluded.</span></span> <span data-ttu-id="e19df-292">請注意，這並不是一般 EF Core 查詢的情況，因為 `Deleted` 實體仍然存在於資料庫中，因此 _會_ 由資料庫查詢傳回。</span><span class="sxs-lookup"><span data-stu-id="e19df-292">Note that this is again not the case for normal EF Core queries, since `Deleted` entities still exist in the database and so _are_ returned by database queries.</span></span>

<span data-ttu-id="e19df-293">這所有的方法都 `DbSet.Local` 是在反映實體圖形目前概念狀態的資料上查看，其中 `Added` 包含包含的實體和 `Deleted` 排除的實體。</span><span class="sxs-lookup"><span data-stu-id="e19df-293">All of this means that `DbSet.Local` is view over the data that reflects the current conceptual state of the entity graph, with `Added` entities included and `Deleted` entities excluded.</span></span> <span data-ttu-id="e19df-294">這會比對在呼叫 SaveChanges 之後預期的資料庫狀態。</span><span class="sxs-lookup"><span data-stu-id="e19df-294">This matches what database state is expected to be after SaveChanges is called.</span></span>

<span data-ttu-id="e19df-295">這通常是資料系結的理想觀點，因為它會根據應用程式所做的變更，向使用者呈現其瞭解的資料。</span><span class="sxs-lookup"><span data-stu-id="e19df-295">This is typically the ideal view for data binding, since it presents to the user the data as they understand it based on the changes made by the application.</span></span>

<span data-ttu-id="e19df-296">下列程式碼示範我將一篇貼文標示為 `Deleted` ，然後新增貼文，將其標示為 `Added` ：</span><span class="sxs-lookup"><span data-stu-id="e19df-296">The following code demonstrates this my marking one post as `Deleted` and then adding a new post, marking it as `Added`:</span></span>

<!--
        using var context = new BlogsContext();

        var posts = context.Posts.Include(e => e.Blog).ToList();

        Console.WriteLine("Local view after loading posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }

        context.Remove(posts[1]);

        context.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many...",
            Blog = posts[0].Blog
        });

        Console.WriteLine("Local view after adding and deleting posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_2)]

<span data-ttu-id="e19df-297">此程式碼的輸出為：</span><span class="sxs-lookup"><span data-stu-id="e19df-297">The output from this code is:</span></span>

```output
Local view after loading posts:
  Post: Announcing the Release of EF Core 5.0
  Post: Announcing F# 5
  Post: Announcing .NET 5.0
Local view after adding and deleting posts:
  Post: What’s next for System.Text.Json?
  Post: Announcing the Release of EF Core 5.0
  Post: Announcing .NET 5.0
```

<span data-ttu-id="e19df-298">請注意，已刪除的文章會從本機視圖中移除，並包含新增的貼文。</span><span class="sxs-lookup"><span data-stu-id="e19df-298">Notice that the deleted post is removed from the local view, and the added post is included.</span></span>

### <a name="using-local-to-add-and-remove-entities"></a><span data-ttu-id="e19df-299">使用 Local 來新增和移除實體</span><span class="sxs-lookup"><span data-stu-id="e19df-299">Using Local to add and remove entities</span></span>

<span data-ttu-id="e19df-300"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> 會傳回 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601> 的執行個體。</span><span class="sxs-lookup"><span data-stu-id="e19df-300"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> returns an instance of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601>.</span></span> <span data-ttu-id="e19df-301">這是的實 <xref:System.Collections.Generic.ICollection%601> ，會在從集合中加入和移除實體時，產生並回應通知。</span><span class="sxs-lookup"><span data-stu-id="e19df-301">This is an implementation of <xref:System.Collections.Generic.ICollection%601> that generates and responds to notifications when entities are added and removed from the collection.</span></span> <span data-ttu-id="e19df-302"> (此概念與相同 <xref:System.Collections.ObjectModel.ObservableCollection%601> ，但實作為現有 EF Core 變更追蹤專案的投影，而不是獨立集合。 ) </span><span class="sxs-lookup"><span data-stu-id="e19df-302">(This is the same concept as <xref:System.Collections.ObjectModel.ObservableCollection%601>, but implemented as a projection over existing EF Core change tracking entries, rather than as an independent collection.)</span></span>

<span data-ttu-id="e19df-303">本機視圖的通知會連結至 DbCoNtext 變更追蹤，讓本機視圖與 DbCoNtext 保持同步。</span><span class="sxs-lookup"><span data-stu-id="e19df-303">The local view's notifications are hooked into DbContext change tracking such that the local view stays in sync with the DbContext.</span></span> <span data-ttu-id="e19df-304">具體來說：</span><span class="sxs-lookup"><span data-stu-id="e19df-304">Specifically:</span></span>

- <span data-ttu-id="e19df-305">新增實體可 `DbSet.Local` 讓 DbCoNtext 追蹤，通常會處於 `Added` 狀態。</span><span class="sxs-lookup"><span data-stu-id="e19df-305">Adding a new entity to `DbSet.Local` causes it to be tracked by the DbContext, typically in the `Added` state.</span></span> <span data-ttu-id="e19df-306"> (如果實體已經有產生的索引鍵值，則會改為追蹤 `Unchanged` 。 ) </span><span class="sxs-lookup"><span data-stu-id="e19df-306">(If the entity already has a generated key value, then it is tracked as `Unchanged` instead.)</span></span>
- <span data-ttu-id="e19df-307">從移除實體 `DbSet.Local` 會導致它標示為 `Deleted` 。</span><span class="sxs-lookup"><span data-stu-id="e19df-307">Removing an entity from `DbSet.Local` causes it to be marked as `Deleted`.</span></span>
- <span data-ttu-id="e19df-308">DbCoNtext 所追蹤的實體會自動出現在 `DbSet.Local` 集合中。</span><span class="sxs-lookup"><span data-stu-id="e19df-308">An entity that becomes tracked by the DbContext will automatically appear in the `DbSet.Local` collection.</span></span> <span data-ttu-id="e19df-309">例如，執行查詢來導入多個實體時，會自動導致本機視圖更新。</span><span class="sxs-lookup"><span data-stu-id="e19df-309">For example, executing a query to bring in more entities automatically causes the local view to be updated.</span></span>
- <span data-ttu-id="e19df-310">標示為的實體 `Deleted` 會自動從本機集合中移除。</span><span class="sxs-lookup"><span data-stu-id="e19df-310">An entity that is marked as `Deleted` will be removed from the local collection automatically.</span></span>

<span data-ttu-id="e19df-311">這表示，您只要在集合中加入和移除，就可以使用本機 view 來操作追蹤的實體。</span><span class="sxs-lookup"><span data-stu-id="e19df-311">This means the local view can be used to manipulate tracked entities simply by adding and removing from the collection.</span></span> <span data-ttu-id="e19df-312">例如，讓我們修改先前的範例程式碼，以新增和移除本機集合中的貼文：</span><span class="sxs-lookup"><span data-stu-id="e19df-312">For example, lets modify the previous example code to add and remove posts from the local collection:</span></span>

<!--
        using var context = new BlogsContext();

        var posts = context.Posts.Include(e => e.Blog).ToList();

        Console.WriteLine("Local view after loading posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }

        context.Posts.Local.Remove(posts[1]);

        context.Posts.Local.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many...",
            Blog = posts[0].Blog
        });

        Console.WriteLine("Local view after adding and deleting posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_3](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_3)]

<span data-ttu-id="e19df-313">輸出會與上一個範例保持不變，因為對本機視圖所做的變更會與 DbCoNtext 同步。</span><span class="sxs-lookup"><span data-stu-id="e19df-313">The output remains unchanged from the previous example because changes made to the local view are synced with the DbContext.</span></span>

### <a name="using-the-local-view-for-windows-forms-or-wpf-data-binding"></a><span data-ttu-id="e19df-314">使用 Windows Forms 或 WPF 資料系結的本機視圖</span><span class="sxs-lookup"><span data-stu-id="e19df-314">Using the local view for Windows Forms or WPF data binding</span></span>

<span data-ttu-id="e19df-315"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> 形成資料系結至 EF Core 實體的基礎。</span><span class="sxs-lookup"><span data-stu-id="e19df-315"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> forms the basis for data binding to EF Core entities.</span></span> <span data-ttu-id="e19df-316">但是，當使用 Windows Forms 和 WPF 時，最好使用與預期的特定類型的通知集合搭配使用。</span><span class="sxs-lookup"><span data-stu-id="e19df-316">However, both Windows Forms and WPF work best when used with the specific type of notifying collection that they expect.</span></span> <span data-ttu-id="e19df-317">本機視圖支援建立這些特定的集合類型：</span><span class="sxs-lookup"><span data-stu-id="e19df-317">The local view supports creating these specific collection types:</span></span>

- <span data-ttu-id="e19df-318"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToObservableCollection?displayProperty=nameWithType> 傳回 <xref:System.Collections.ObjectModel.ObservableCollection%601> WPF 資料系結的。</span><span class="sxs-lookup"><span data-stu-id="e19df-318"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToObservableCollection?displayProperty=nameWithType> returns an <xref:System.Collections.ObjectModel.ObservableCollection%601> for WPF data binding.</span></span>
- <span data-ttu-id="e19df-319"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToBindingList?displayProperty=nameWithType> 傳回 Windows Forms 資料系結的 <xref:System.ComponentModel.BindingList%601> 。</span><span class="sxs-lookup"><span data-stu-id="e19df-319"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToBindingList?displayProperty=nameWithType> returns a <xref:System.ComponentModel.BindingList%601> for Windows Forms data binding.</span></span>

<span data-ttu-id="e19df-320">例如：</span><span class="sxs-lookup"><span data-stu-id="e19df-320">For example:</span></span>

<!--
        ObservableCollection<Post> observableCollection = context.Posts.Local.ToObservableCollection();
        BindingList<Post> bindingList = context.Posts.Local.ToBindingList();
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_4](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_4)]

<span data-ttu-id="e19df-321">如需有關使用 EF Core 的 WPF 資料系結的詳細資訊，請參閱 [使用 wpf 開始](xref:core/get-started/wpf) 。</span><span class="sxs-lookup"><span data-stu-id="e19df-321">See [Get Started with WPF](xref:core/get-started/wpf) for more information on WPF data binding with EF Core.</span></span>

> [!TIP]
> <span data-ttu-id="e19df-322">給定 DbSet 實例的本機視圖會在第一次存取時延遲建立，然後再進行快取。</span><span class="sxs-lookup"><span data-stu-id="e19df-322">The local view for a given DbSet instance is created lazily when first accessed and then cached.</span></span> <span data-ttu-id="e19df-323">LocalView 本身的建立速度很快，而且不會使用大量的記憶體。</span><span class="sxs-lookup"><span data-stu-id="e19df-323">LocalView creation itself is fast and it does not use significant memory.</span></span> <span data-ttu-id="e19df-324">不過，它會呼叫 [DetectChanges](xref:core/change-tracking/change-detection)，這對大量實體而言可能很慢。</span><span class="sxs-lookup"><span data-stu-id="e19df-324">However, it does call [DetectChanges](xref:core/change-tracking/change-detection), which can be slow for large numbers of entities.</span></span> <span data-ttu-id="e19df-325">和建立的集合 `ToObservableCollection` `ToBindingList` 也會延遲建立，然後再進行快取。</span><span class="sxs-lookup"><span data-stu-id="e19df-325">The collections created by `ToObservableCollection` and `ToBindingList` are also created lazily and and then cached.</span></span> <span data-ttu-id="e19df-326">這兩種方法都會建立新的集合，在涉及上千個實體時，可能會很慢，而且會使用大量的記憶體。</span><span class="sxs-lookup"><span data-stu-id="e19df-326">Both of these methods create new collections, which can be slow and use a lot of memory when thousands of entities are involved.</span></span>
