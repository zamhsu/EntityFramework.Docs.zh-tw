---
title: 身分識別解析-EF Core
description: 使用主鍵值將多個實體實例解析成單一實例
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/identity-resolution
ms.openlocfilehash: 24b2fbeea5f740dd2830676bfe8a49720c2b86a9
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024455"
---
# <a name="identity-resolution-in-ef-core"></a><span data-ttu-id="861de-103">EF Core 中的身分識別解析</span><span class="sxs-lookup"><span data-stu-id="861de-103">Identity Resolution in EF Core</span></span>

<span data-ttu-id="861de-104"><xref:Microsoft.EntityFrameworkCore.DbContext>只能追蹤一個具有任何指定主鍵值的實體實例。</span><span class="sxs-lookup"><span data-stu-id="861de-104">A <xref:Microsoft.EntityFrameworkCore.DbContext> can only track one entity instance with any given primary key value.</span></span> <span data-ttu-id="861de-105">這表示多個具有相同索引鍵值的實體實例，必須解析為單一實例。</span><span class="sxs-lookup"><span data-stu-id="861de-105">This means multiple instances of an entity with the same key value must be resolved to a single instance.</span></span> <span data-ttu-id="861de-106">這稱為「識別解析」。</span><span class="sxs-lookup"><span data-stu-id="861de-106">This is called "identity resolution".</span></span> <span data-ttu-id="861de-107">身分識別解析可確保 Entity Framework Core (EF Core) 正在追蹤一致的圖形，而實體的關聯性或屬性值沒有任何多義性。</span><span class="sxs-lookup"><span data-stu-id="861de-107">Identity resolution ensures Entity Framework Core (EF Core) is tracking a consistent graph with no ambiguities about the relationships or property values of the entities.</span></span>

> [!TIP]
> <span data-ttu-id="861de-108">本檔假設您已瞭解實體狀態和 EF Core 變更追蹤的基本概念。</span><span class="sxs-lookup"><span data-stu-id="861de-108">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="861de-109">如需有關這些主題的詳細資訊，請參閱 [EF Core 中的變更追蹤](xref:core/change-tracking/index) 。</span><span class="sxs-lookup"><span data-stu-id="861de-109">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="861de-110">您可以 [從 GitHub 下載範例程式碼](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/IdentityResolutionInEFCore)，以執行並偵測到本檔中的所有程式碼。</span><span class="sxs-lookup"><span data-stu-id="861de-110">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/IdentityResolutionInEFCore).</span></span>

## <a name="introduction"></a><span data-ttu-id="861de-111">簡介</span><span class="sxs-lookup"><span data-stu-id="861de-111">Introduction</span></span>

<span data-ttu-id="861de-112">下列程式碼會查詢實體，然後嘗試附加具有相同主鍵值的不同實例：</span><span class="sxs-lookup"><span data-stu-id="861de-112">The following code queries for an entity and then attempts to attach a different instance with the same primary key value:</span></span>

<!--
            using var context = new BlogsContext();

            var blogA = context.Blogs.Single(e => e.Id == 1);
            var blogB = new Blog { Id = 1, Name = ".NET Blog (All new!)" };

            try
            {
                context.Update(blogB); // This will throw
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
            }
-->
[!code-csharp[Identity_Resolution_in_EF_Core_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Identity_Resolution_in_EF_Core_1)]

<span data-ttu-id="861de-113">執行此程式碼會導致下列例外狀況：</span><span class="sxs-lookup"><span data-stu-id="861de-113">Running this code results in the following exception:</span></span>

> <span data-ttu-id="861de-114">InvalidOperationException：無法追蹤實體類型 ' Blog ' 的實例，因為已追蹤另一個具有索引鍵值 ' {Id： 1} ' 的實例。</span><span class="sxs-lookup"><span data-stu-id="861de-114">System.InvalidOperationException: The instance of entity type 'Blog' cannot be tracked because another instance with the key value '{Id: 1}' is already being tracked.</span></span> <span data-ttu-id="861de-115">附加現有的實體時，請確定只附加一個具有指定索引鍵值的實體實例。</span><span class="sxs-lookup"><span data-stu-id="861de-115">When attaching existing entities, ensure that only one entity instance with a given key value is attached.</span></span>

<span data-ttu-id="861de-116">EF Core 需要單一實例，因為：</span><span class="sxs-lookup"><span data-stu-id="861de-116">EF Core requires a single instance because:</span></span>

- <span data-ttu-id="861de-117">多個實例之間的屬性值可能不同。</span><span class="sxs-lookup"><span data-stu-id="861de-117">Property values may be different between multiple instances.</span></span> <span data-ttu-id="861de-118">更新資料庫時，EF Core 必須知道要使用哪些屬性值。</span><span class="sxs-lookup"><span data-stu-id="861de-118">When updating the database, EF Core needs to know which property values to use.</span></span>
- <span data-ttu-id="861de-119">在多個實例之間，與其他實體的關聯性可能會不同。</span><span class="sxs-lookup"><span data-stu-id="861de-119">Relationships with other entities may be different between multiple instances.</span></span> <span data-ttu-id="861de-120">例如，"blogA" 可能與不同于 "blogB" 的貼文集合相關。</span><span class="sxs-lookup"><span data-stu-id="861de-120">For example, "blogA" may be related to a different collection of posts than "blogB".</span></span>

<span data-ttu-id="861de-121">上述例外狀況通常會在下列情況中發生：</span><span class="sxs-lookup"><span data-stu-id="861de-121">The exception above is commonly encountered in these situations:</span></span>

- <span data-ttu-id="861de-122">嘗試更新實體時</span><span class="sxs-lookup"><span data-stu-id="861de-122">When attempting to update an entity</span></span>
- <span data-ttu-id="861de-123">嘗試追蹤實體的序列化圖形時</span><span class="sxs-lookup"><span data-stu-id="861de-123">When attempting to track a serialized graph of entities</span></span>
- <span data-ttu-id="861de-124">無法設定未自動產生的索引鍵值時</span><span class="sxs-lookup"><span data-stu-id="861de-124">When failing to set a key value that is not automatically generated</span></span>
- <span data-ttu-id="861de-125">針對多個工作單位重複使用 DbCoNtext 實例時</span><span class="sxs-lookup"><span data-stu-id="861de-125">When reusing a DbContext instance for multiple units-of-work</span></span>

<span data-ttu-id="861de-126">下列各節將討論這些情況。</span><span class="sxs-lookup"><span data-stu-id="861de-126">Each of these situations is discussed in the following sections.</span></span>

## <a name="updating-an-entity"></a><span data-ttu-id="861de-127">更新實體</span><span class="sxs-lookup"><span data-stu-id="861de-127">Updating an entity</span></span>

<span data-ttu-id="861de-128">有幾種不同的方法可以用新的值更新實體，如 [EF Core 中的變更追蹤](xref:core/change-tracking/index) 和 [明確追蹤實體](xref:core/change-tracking/explicit-tracking)中所述。</span><span class="sxs-lookup"><span data-stu-id="861de-128">There are several different approaches to update an entity with new values, as covered in [Change Tracking in EF Core](xref:core/change-tracking/index) and [Explicitly Tracking Entities](xref:core/change-tracking/explicit-tracking).</span></span> <span data-ttu-id="861de-129">這些方法在識別解析的內容中如下所述。</span><span class="sxs-lookup"><span data-stu-id="861de-129">These approaches are outlined below in the context of identity resolution.</span></span> <span data-ttu-id="861de-130">要注意的一個重點是，每個方法都使用查詢或呼叫其中一個或的呼叫 `Update` `Attach` ，但 **_永遠不會同時使用這兩_** 種方法。</span><span class="sxs-lookup"><span data-stu-id="861de-130">An important point to notice is that each of the approaches use either a query or a call to one of `Update` or `Attach`, but **_never both_**.</span></span>

### <a name="call-update"></a><span data-ttu-id="861de-131">呼叫更新</span><span class="sxs-lookup"><span data-stu-id="861de-131">Call Update</span></span>

<span data-ttu-id="861de-132">通常，要更新的實體不是來自我們將用於 SaveChanges 的 DbCoNtext 查詢。</span><span class="sxs-lookup"><span data-stu-id="861de-132">Often the entity to update does not come from a query on the DbContext that we are going to use for SaveChanges.</span></span> <span data-ttu-id="861de-133">例如，在 web 應用程式中，可以從 POST 要求中的資訊建立實體實例。</span><span class="sxs-lookup"><span data-stu-id="861de-133">For example, in a web application, an entity instance may be created from the information in a POST request.</span></span> <span data-ttu-id="861de-134">處理這項工作的最簡單方式是使用 <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> 或 <xref:Microsoft.EntityFrameworkCore.DbSet%601.Update%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="861de-134">The simplest way to handle this is to use <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> or <xref:Microsoft.EntityFrameworkCore.DbSet%601.Update%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="861de-135">例如：</span><span class="sxs-lookup"><span data-stu-id="861de-135">For example:</span></span>

<!--
    public static void UpdateFromHttpPost1(Blog blog)
    {
        using var context = new BlogsContext();

        context.Update(blog);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_1)]

<span data-ttu-id="861de-136">在此案例中：</span><span class="sxs-lookup"><span data-stu-id="861de-136">In this case:</span></span>

- <span data-ttu-id="861de-137">只會建立實體的單一實例。</span><span class="sxs-lookup"><span data-stu-id="861de-137">Only a single instance of the entity is created.</span></span>
- <span data-ttu-id="861de-138">在進行更新的過程中， **不** 會從資料庫查詢實體實例。</span><span class="sxs-lookup"><span data-stu-id="861de-138">The entity instance is **not** queried from the database as part of making the update.</span></span>
- <span data-ttu-id="861de-139">所有的屬性值都會在資料庫中更新，不論它們是否真的變更。</span><span class="sxs-lookup"><span data-stu-id="861de-139">All property values will be updated in the database, regardless of whether they have actually changed or not.</span></span>
- <span data-ttu-id="861de-140">進行一個資料庫來回行程。</span><span class="sxs-lookup"><span data-stu-id="861de-140">One database round-trip is made.</span></span>

### <a name="query-then-apply-changes"></a><span data-ttu-id="861de-141">查詢然後套用變更</span><span class="sxs-lookup"><span data-stu-id="861de-141">Query then apply changes</span></span>

<span data-ttu-id="861de-142">通常不知道當實體是從 POST 要求中的資訊建立時，哪些屬性值實際上已經變更或類似。</span><span class="sxs-lookup"><span data-stu-id="861de-142">Usually it is not known which property values have actually been changed when an entity is created from information in a POST request or similar.</span></span> <span data-ttu-id="861de-143">您通常只需要更新資料庫中的所有值，就像我們在上一個範例中所做的一樣。</span><span class="sxs-lookup"><span data-stu-id="861de-143">Often it is fine to just update all values in the database, as we did in the previous example.</span></span> <span data-ttu-id="861de-144">但是，如果應用程式正在處理許多實體，而且只有少數的實體有實際的變更，則限制傳送的更新可能會很有用。</span><span class="sxs-lookup"><span data-stu-id="861de-144">However, if the application is handling many entities and only a small number of those have actual changes, then it may be useful to limit the updates sent.</span></span> <span data-ttu-id="861de-145">這可以藉由執行查詢來追蹤目前存在於資料庫中的實體，然後將變更套用至這些追蹤的實體來達成。</span><span class="sxs-lookup"><span data-stu-id="861de-145">This can be achieved by executing a query to track the entities as they currently exist in the database, and then applying changes to these tracked entities.</span></span> <span data-ttu-id="861de-146">例如：</span><span class="sxs-lookup"><span data-stu-id="861de-146">For example:</span></span>

<!--
    public static void UpdateFromHttpPost2(Blog blog)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(blog.Id);

        trackedBlog.Name = blog.Name;
        trackedBlog.Summary = blog.Summary;

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_2](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_2)]

<span data-ttu-id="861de-147">在此案例中：</span><span class="sxs-lookup"><span data-stu-id="861de-147">In this case:</span></span>

- <span data-ttu-id="861de-148">只會追蹤實體的單一實例;查詢從資料庫傳回的 `Find` 。</span><span class="sxs-lookup"><span data-stu-id="861de-148">Only a single instance of the entity is tracked; the one that is returned from the database by the `Find` query.</span></span>
- <span data-ttu-id="861de-149">`Update``Attach`**不** 使用、等等。</span><span class="sxs-lookup"><span data-stu-id="861de-149">`Update`, `Attach`, etc. are **not** used.</span></span>
- <span data-ttu-id="861de-150">資料庫中只會更新已實際變更的屬性值。</span><span class="sxs-lookup"><span data-stu-id="861de-150">Only property values that have actually changed are updated in the database.</span></span>
- <span data-ttu-id="861de-151">進行兩次資料庫來回行程。</span><span class="sxs-lookup"><span data-stu-id="861de-151">Two database round-trips are made.</span></span>

<span data-ttu-id="861de-152">EF Core 有一些協助程式來傳送屬性值（如下所示）。</span><span class="sxs-lookup"><span data-stu-id="861de-152">EF Core has some helpers for transferring property values like this.</span></span> <span data-ttu-id="861de-153">例如， <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> 將會從指定的物件複製所有值，然後在追蹤的物件上設定這些值：</span><span class="sxs-lookup"><span data-stu-id="861de-153">For example, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> will copy all the values from the given object and set them on the tracked object:</span></span>

<!--
    public static void UpdateFromHttpPost3(Blog blog)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(blog.Id);

        context.Entry(trackedBlog).CurrentValues.SetValues(blog);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_3](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_3)]

<span data-ttu-id="861de-154">`SetValues` 接受各種物件類型，包括資料傳輸物件 (Dto) 屬性名稱符合實體類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="861de-154">`SetValues` accepts various object types, including data transfer objects (DTOs) with property names that match the properties of the entity type.</span></span> <span data-ttu-id="861de-155">例如：</span><span class="sxs-lookup"><span data-stu-id="861de-155">For example:</span></span>

<!--
    public static void UpdateFromHttpPost4(BlogDto dto)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(dto.Id);

        context.Entry(trackedBlog).CurrentValues.SetValues(dto);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_4](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_4)]

<span data-ttu-id="861de-156">或具有屬性值之名稱/值專案的字典：</span><span class="sxs-lookup"><span data-stu-id="861de-156">Or a dictionary with name/value entries for the property values:</span></span>

<!--
    public static void UpdateFromHttpPost5(Dictionary<string, object> propertyValues)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(propertyValues["Id"]);

        context.Entry(trackedBlog).CurrentValues.SetValues(propertyValues);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_5](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_5)]

<span data-ttu-id="861de-157">如需使用屬性值的詳細資訊，請參閱 [存取追蹤的實體](xref:core/change-tracking/entity-entries) 。</span><span class="sxs-lookup"><span data-stu-id="861de-157">See [Accessing tracked entities](xref:core/change-tracking/entity-entries) for more information on working with property values like this.</span></span>

### <a name="use-original-values"></a><span data-ttu-id="861de-158">使用原始值</span><span class="sxs-lookup"><span data-stu-id="861de-158">Use original values</span></span>

<span data-ttu-id="861de-159">到目前為止，每個方法在進行更新之前都已執行查詢，或更新所有屬性值，而不論它們是否已變更。</span><span class="sxs-lookup"><span data-stu-id="861de-159">So far each approach has either executed a query before making the update, or updated all property values regardless of whether or not they have changed.</span></span> <span data-ttu-id="861de-160">若只要更新已變更但未查詢為更新一部分的值，則需要已變更哪些屬性值的特定資訊。</span><span class="sxs-lookup"><span data-stu-id="861de-160">To update only values that have changed without querying as part of the update requires specific information about which property values have changed.</span></span> <span data-ttu-id="861de-161">取得這項資訊的常見方式是傳回 HTTP Post 或類似的目前值和原始值。</span><span class="sxs-lookup"><span data-stu-id="861de-161">A common way to get this information is to send back both the current and original values in the HTTP Post or similar.</span></span> <span data-ttu-id="861de-162">例如：</span><span class="sxs-lookup"><span data-stu-id="861de-162">For example:</span></span>

<!--
    public static void UpdateFromHttpPost6(Blog blog, Dictionary<string, object> originalValues)
    {
        using var context = new BlogsContext();

        context.Attach(blog);
        context.Entry(blog).OriginalValues.SetValues(originalValues);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_6](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_6)]

<span data-ttu-id="861de-163">在此程式碼中，會先附加具有已修改值的實體。</span><span class="sxs-lookup"><span data-stu-id="861de-163">In this code the entity with modified values is first attached.</span></span> <span data-ttu-id="861de-164">這會導致 EF Core 追蹤狀態中的實體， `Unchanged` 也就是沒有任何屬性值標示為已修改。</span><span class="sxs-lookup"><span data-stu-id="861de-164">This causes EF Core to track the entity in the `Unchanged` state; that is, with no property values marked as modified.</span></span> <span data-ttu-id="861de-165">然後，會將原始值的字典套用至此追蹤的實體。</span><span class="sxs-lookup"><span data-stu-id="861de-165">The dictionary of original values is then applied to this tracked entity.</span></span> <span data-ttu-id="861de-166">這會將不同的目前值和原始值標示為已修改的屬性。</span><span class="sxs-lookup"><span data-stu-id="861de-166">This will mark as modified properties with different current and original values.</span></span> <span data-ttu-id="861de-167">具有相同目前值和原始值的屬性將不會標示為已修改。</span><span class="sxs-lookup"><span data-stu-id="861de-167">Properties that have the same current and original values will not be marked as modified.</span></span>

<span data-ttu-id="861de-168">在此案例中：</span><span class="sxs-lookup"><span data-stu-id="861de-168">In this case:</span></span>

- <span data-ttu-id="861de-169">使用 Attach 只追蹤實體的單一實例。</span><span class="sxs-lookup"><span data-stu-id="861de-169">Only a single instance of the entity is tracked, using Attach.</span></span>
- <span data-ttu-id="861de-170">在進行更新的過程中， **不** 會從資料庫查詢實體實例。</span><span class="sxs-lookup"><span data-stu-id="861de-170">The entity instance is **not** queried from the database as part of making the update.</span></span>
- <span data-ttu-id="861de-171">套用原始值可確保資料庫中只會更新實際變更的屬性值。</span><span class="sxs-lookup"><span data-stu-id="861de-171">Applying the original values ensures that only property values that have actually changed are updated in the database.</span></span>
- <span data-ttu-id="861de-172">進行一個資料庫來回行程。</span><span class="sxs-lookup"><span data-stu-id="861de-172">One database round-trip is made.</span></span>

<span data-ttu-id="861de-173">如同上一節中的範例，原始值不需要以字典的形式傳遞;實體實例或 DTO 也可以運作。</span><span class="sxs-lookup"><span data-stu-id="861de-173">As with the examples in the previous section, the original values do not have to passed as a dictionary; an entity instance or DTO will also work.</span></span>

> [!TIP]
> <span data-ttu-id="861de-174">雖然這個方法有吸引人的特性，但它確實需要在 web 用戶端之間傳送實體的原始值。</span><span class="sxs-lookup"><span data-stu-id="861de-174">While this approach has appealing characteristics, it does require sending the entity's original values to and from the web client.</span></span> <span data-ttu-id="861de-175">請仔細考慮這項額外的複雜性是否值得優勢;對於許多應用程式來說，其中一種較簡單的方法是更實用的方法。</span><span class="sxs-lookup"><span data-stu-id="861de-175">Carefully consider whether this extra complexity is worth the benefits; for many applications one of the simpler approaches is more pragmatic.</span></span>

## <a name="attaching-a-serialized-graph"></a><span data-ttu-id="861de-176">附加序列化圖形</span><span class="sxs-lookup"><span data-stu-id="861de-176">Attaching a serialized graph</span></span>

<span data-ttu-id="861de-177">EF Core 適用于透過外鍵和導覽屬性連接的實體圖形，如 [變更外鍵和](xref:core/change-tracking/relationship-changes)導覽所述。</span><span class="sxs-lookup"><span data-stu-id="861de-177">EF Core works with graphs of entities connected via foreign keys and navigation properties, as described in [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes).</span></span> <span data-ttu-id="861de-178">如果這些圖形是在 EF Core 之外建立，例如，從 JSON 檔案，則它們可以有相同實體的多個實例。</span><span class="sxs-lookup"><span data-stu-id="861de-178">If these graphs are created outside of EF Core using, for example, from a JSON file, then they can have multiple instances of the same entity.</span></span> <span data-ttu-id="861de-179">這些重複專案必須先解析成單一實例，然後才能追蹤圖形。</span><span class="sxs-lookup"><span data-stu-id="861de-179">These duplicates need to be resolved into single instances before the graph can be tracked.</span></span>

### <a name="graphs-with-no-duplicates"></a><span data-ttu-id="861de-180">沒有重複專案的圖表</span><span class="sxs-lookup"><span data-stu-id="861de-180">Graphs with no duplicates</span></span>

<span data-ttu-id="861de-181">繼續進行之前，請務必先辨識：</span><span class="sxs-lookup"><span data-stu-id="861de-181">Before going any further it is important to recognize that:</span></span>

- <span data-ttu-id="861de-182">序列化程式通常具有在圖形中處理迴圈和重複實例的選項。</span><span class="sxs-lookup"><span data-stu-id="861de-182">Serializers often have options for handling loops and duplicate instances in the graph.</span></span>
- <span data-ttu-id="861de-183">當做圖形根使用的物件選擇通常有助於減少或移除重複專案。</span><span class="sxs-lookup"><span data-stu-id="861de-183">The choice of object used as the graph root can often help reduce or remove duplicates.</span></span>

<span data-ttu-id="861de-184">可能的話，請使用序列化選項，並選擇不會產生重複專案的根。</span><span class="sxs-lookup"><span data-stu-id="861de-184">If possible, use serialization options and choose roots that do not result in duplicates.</span></span> <span data-ttu-id="861de-185">例如，下列程式碼會使用 [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/) ，將 blog 清單與其相關文章進行序列化：</span><span class="sxs-lookup"><span data-stu-id="861de-185">For example, the following code uses [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/) to serialize a list of blogs each with its associated posts:</span></span>

<!--
            using var context = new BlogsContext();

            var blogs = context.Blogs.Include(e => e.Posts).ToList();

            var serialized = JsonConvert.SerializeObject(
                blogs,
                new JsonSerializerSettings
                {
                    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
                    Formatting = Formatting.Indented
                });

            Console.WriteLine(serialized);
-->
[!code-csharp[Attaching_a_serialized_graph_1a](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_1a)]

<span data-ttu-id="861de-186">此程式碼所產生的 JSON 為：</span><span class="sxs-lookup"><span data-stu-id="861de-186">The JSON generated from this code is:</span></span>

```json
[
  {
    "Id": 1,
    "Name": ".NET Blog",
    "Summary": "Posts about .NET",
    "Posts": [
      {
        "Id": 1,
        "Title": "Announcing the Release of EF Core 5.0",
        "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
        "BlogId": 1
      },
      {
        "Id": 2,
        "Title": "Announcing F# 5",
        "Content": "F# 5 is the latest version of F#, the functional programming language...",
        "BlogId": 1
      }
    ]
  },
  {
    "Id": 2,
    "Name": "Visual Studio Blog",
    "Summary": "Posts about Visual Studio",
    "Posts": [
      {
        "Id": 3,
        "Title": "Disassembly improvements for optimized managed debugging",
        "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
        "BlogId": 2
      },
      {
        "Id": 4,
        "Title": "Database Profiling with Visual Studio",
        "Content": "Examine when database queries were executed and measure how long the take using...",
        "BlogId": 2
      }
    ]
  }
]
```

<span data-ttu-id="861de-187">請注意，JSON 中沒有重複的 blog 或文章。</span><span class="sxs-lookup"><span data-stu-id="861de-187">Notice that there are no duplicate blogs or posts in the JSON.</span></span> <span data-ttu-id="861de-188">這表示簡單的呼叫 `Update` 將會運作，以更新資料庫中的這些實體：</span><span class="sxs-lookup"><span data-stu-id="861de-188">This means that simple calls to `Update` will work to update these entities in the database:</span></span>

<!--
        public static void UpdateBlogsFromJson(string json)
        {
            using var context = new BlogsContext();

            var blogs = JsonConvert.DeserializeObject<List<Blog>>(json);

            foreach (var blog in blogs)
            {
                context.Update(blog);
            }

            context.SaveChanges();
        }
-->
[!code-csharp[Attaching_a_serialized_graph_1b](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_1b)]

### <a name="handling-duplicates"></a><span data-ttu-id="861de-189">處理重複專案</span><span class="sxs-lookup"><span data-stu-id="861de-189">Handling duplicates</span></span>

<span data-ttu-id="861de-190">上述範例中的程式碼會將每個 blog 序列化為其相關文章。</span><span class="sxs-lookup"><span data-stu-id="861de-190">The code in the previous example serialized each blog with its associated posts.</span></span> <span data-ttu-id="861de-191">如果變更為使用其相關聯的 blog 來將每個貼文序列化，則會將重複項導入序列化的 JSON 中。</span><span class="sxs-lookup"><span data-stu-id="861de-191">If this is changed to serialize each post with its associated blog, then duplicates are introduced into the serialized JSON.</span></span> <span data-ttu-id="861de-192">例如：</span><span class="sxs-lookup"><span data-stu-id="861de-192">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var posts = context.Posts.Include(e => e.Blog).ToList();

            var serialized = JsonConvert.SerializeObject(
                posts,
                new JsonSerializerSettings
                {
                    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
                    Formatting = Formatting.Indented
                });

            Console.WriteLine(serialized);
-->
[!code-csharp[Attaching_a_serialized_graph_2](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_2)]

<span data-ttu-id="861de-193">序列化的 JSON 現在看起來像這樣：</span><span class="sxs-lookup"><span data-stu-id="861de-193">The serialized JSON now looks like this:</span></span>

```json
[
  {
    "Id": 1,
    "Title": "Announcing the Release of EF Core 5.0",
    "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
    "BlogId": 1,
    "Blog": {
      "Id": 1,
      "Name": ".NET Blog",
      "Summary": "Posts about .NET",
      "Posts": [
        {
          "Id": 2,
          "Title": "Announcing F# 5",
          "Content": "F# 5 is the latest version of F#, the functional programming language...",
          "BlogId": 1
        }
      ]
    }
  },
  {
    "Id": 2,
    "Title": "Announcing F# 5",
    "Content": "F# 5 is the latest version of F#, the functional programming language...",
    "BlogId": 1,
    "Blog": {
      "Id": 1,
      "Name": ".NET Blog",
      "Summary": "Posts about .NET",
      "Posts": [
        {
          "Id": 1,
          "Title": "Announcing the Release of EF Core 5.0",
          "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
          "BlogId": 1
        }
      ]
    }
  },
  {
    "Id": 3,
    "Title": "Disassembly improvements for optimized managed debugging",
    "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
    "BlogId": 2,
    "Blog": {
      "Id": 2,
      "Name": "Visual Studio Blog",
      "Summary": "Posts about Visual Studio",
      "Posts": [
        {
          "Id": 4,
          "Title": "Database Profiling with Visual Studio",
          "Content": "Examine when database queries were executed and measure how long the take using...",
          "BlogId": 2
        }
      ]
    }
  },
  {
    "Id": 4,
    "Title": "Database Profiling with Visual Studio",
    "Content": "Examine when database queries were executed and measure how long the take using...",
    "BlogId": 2,
    "Blog": {
      "Id": 2,
      "Name": "Visual Studio Blog",
      "Summary": "Posts about Visual Studio",
      "Posts": [
        {
          "Id": 3,
          "Title": "Disassembly improvements for optimized managed debugging",
          "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
          "BlogId": 2
        }
      ]
    }
  }
]
```

<span data-ttu-id="861de-194">請注意，圖形現在包含多個具有相同金鑰值的 Blog 實例，以及多個具有相同索引鍵值的 Post 實例。</span><span class="sxs-lookup"><span data-stu-id="861de-194">Notice that the graph now includes multiple Blog instances with the same key value, as well as multiple Post instance with the same key value.</span></span> <span data-ttu-id="861de-195">如同我們在前一個範例中所做的一樣，嘗試追蹤此圖形將會擲回：</span><span class="sxs-lookup"><span data-stu-id="861de-195">Attempting to track this graph like we did in the previous example will throw:</span></span>

> <span data-ttu-id="861de-196">InvalidOperationException：無法追蹤實體類型 ' Post ' 的實例，因為已追蹤另一個具有索引鍵值 ' {Id： 2} ' 的實例。</span><span class="sxs-lookup"><span data-stu-id="861de-196">System.InvalidOperationException: The instance of entity type 'Post' cannot be tracked because another instance with the key value '{Id: 2}' is already being tracked.</span></span> <span data-ttu-id="861de-197">附加現有的實體時，請確定只附加一個具有指定索引鍵值的實體實例。</span><span class="sxs-lookup"><span data-stu-id="861de-197">When attaching existing entities, ensure that only one entity instance with a given key value is attached.</span></span>

<span data-ttu-id="861de-198">我們可以透過兩種方式來修正此問題：</span><span class="sxs-lookup"><span data-stu-id="861de-198">We can fix this in two ways:</span></span>

- <span data-ttu-id="861de-199">使用保留參考的 JSON 序列化選項</span><span class="sxs-lookup"><span data-stu-id="861de-199">Use JSON serialization options that preserve references</span></span>
- <span data-ttu-id="861de-200">在追蹤圖形時執行身分識別解析</span><span class="sxs-lookup"><span data-stu-id="861de-200">Perform identity resolution while the graph is being tracked</span></span>

#### <a name="preserve-references"></a><span data-ttu-id="861de-201">保留參考</span><span class="sxs-lookup"><span data-stu-id="861de-201">Preserve references</span></span>

<span data-ttu-id="861de-202">Json.NET 提供 `PreserveReferencesHandling` 處理此的選項。</span><span class="sxs-lookup"><span data-stu-id="861de-202">Json.NET provides the `PreserveReferencesHandling` option to handle this.</span></span> <span data-ttu-id="861de-203">例如：</span><span class="sxs-lookup"><span data-stu-id="861de-203">For example:</span></span>

<!--
            var serialized = JsonConvert.SerializeObject(
                posts,
                new JsonSerializerSettings
                {
                    PreserveReferencesHandling = PreserveReferencesHandling.All,
                    Formatting = Formatting.Indented
                });
-->
[!code-csharp[Attaching_a_serialized_graph_3](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_3)]

<span data-ttu-id="861de-204">產生的 JSON 現在看起來像這樣：</span><span class="sxs-lookup"><span data-stu-id="861de-204">The resulting JSON now looks like this:</span></span>

```json
{
  "$id": "1",
  "$values": [
    {
      "$id": "2",
      "Id": 1,
      "Title": "Announcing the Release of EF Core 5.0",
      "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
      "BlogId": 1,
      "Blog": {
        "$id": "3",
        "Id": 1,
        "Name": ".NET Blog",
        "Summary": "Posts about .NET",
        "Posts": [
          {
            "$ref": "2"
          },
          {
            "$id": "4",
            "Id": 2,
            "Title": "Announcing F# 5",
            "Content": "F# 5 is the latest version of F#, the functional programming language...",
            "BlogId": 1,
            "Blog": {
              "$ref": "3"
            }
          }
        ]
      }
    },
    {
      "$ref": "4"
    },
    {
      "$id": "5",
      "Id": 3,
      "Title": "Disassembly improvements for optimized managed debugging",
      "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
      "BlogId": 2,
      "Blog": {
        "$id": "6",
        "Id": 2,
        "Name": "Visual Studio Blog",
        "Summary": "Posts about Visual Studio",
        "Posts": [
          {
            "$ref": "5"
          },
          {
            "$id": "7",
            "Id": 4,
            "Title": "Database Profiling with Visual Studio",
            "Content": "Examine when database queries were executed and measure how long the take using...",
            "BlogId": 2,
            "Blog": {
              "$ref": "6"
            }
          }
        ]
      }
    },
    {
      "$ref": "7"
    }
  ]
}
```

<span data-ttu-id="861de-205">請注意，此 JSON 已將重複專案取代為參考 `"$ref": "5"` ，例如，參考圖形中已存在的實例。</span><span class="sxs-lookup"><span data-stu-id="861de-205">Notice that this JSON has replaced duplicates with references like `"$ref": "5"` that refer to the already existing instance in the graph.</span></span> <span data-ttu-id="861de-206">您可以再次使用的簡單呼叫來追蹤此圖形 `Update` ，如上所示。</span><span class="sxs-lookup"><span data-stu-id="861de-206">This graph can again be tracked using the simple calls to `Update`, as shown above.</span></span>

<span data-ttu-id="861de-207"><xref:System.Text.Json>.Net 基類程式庫 (BCL) 的支援具有類似的選項，因此會產生相同的結果。</span><span class="sxs-lookup"><span data-stu-id="861de-207">The <xref:System.Text.Json> support in the .NET base class libraries (BCL) has a similar option which produces the same result.</span></span> <span data-ttu-id="861de-208">例如：</span><span class="sxs-lookup"><span data-stu-id="861de-208">For example:</span></span>

<!--
            var serialized = System.Text.Json.JsonSerializer.Serialize(posts, new System.Text.Json.JsonSerializerOptions
            {
                ReferenceHandler = ReferenceHandler.Preserve,
                WriteIndented = true
            });
-->
[!code-csharp[Attaching_a_serialized_graph_4](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_4)]

#### <a name="resolve-duplicates"></a><span data-ttu-id="861de-209">解決重複專案</span><span class="sxs-lookup"><span data-stu-id="861de-209">Resolve duplicates</span></span>

<span data-ttu-id="861de-210">如果無法在序列化程式中消除重複專案，則會 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> 提供方法來處理此情況。</span><span class="sxs-lookup"><span data-stu-id="861de-210">If it is not possible to eliminate duplicates in the serialization process, then <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> provides a way to handle this.</span></span> <span data-ttu-id="861de-211">TrackGraph 的運作方式類似 `Add` ，但 `Attach` `Update` 它會在追蹤之前為每個實體實例產生回呼。</span><span class="sxs-lookup"><span data-stu-id="861de-211">TrackGraph works like `Add`, `Attach` and `Update` except that it generates a callback for every entity instance before tracking it.</span></span> <span data-ttu-id="861de-212">此回呼可以用來追蹤或忽略實體。</span><span class="sxs-lookup"><span data-stu-id="861de-212">This callback can be used to either track the entity or ignore it.</span></span> <span data-ttu-id="861de-213">例如：</span><span class="sxs-lookup"><span data-stu-id="861de-213">For example:</span></span>

<!--
        public static void UpdatePostsFromJsonWithIdentityResolution(string json)
        {
            using var context = new BlogsContext();

            var posts = JsonConvert.DeserializeObject<List<Post>>(json);

            foreach (var post in posts)
            {
                context.ChangeTracker.TrackGraph(
                    post, node =>
                        {
                            var keyValue = node.Entry.Property("Id").CurrentValue;
                            var entityType = node.Entry.Metadata;

                            var existingEntity = node.Entry.Context.ChangeTracker.Entries()
                                .FirstOrDefault(
                                    e => Equals(e.Metadata, entityType)
                                         && Equals(e.Property("Id").CurrentValue, keyValue));

                            if (existingEntity == null)
                            {
                                Console.WriteLine($"Tracking {entityType} entity with key value {keyValue}");

                                node.Entry.State = EntityState.Modified;
                            }
                            else
                            {
                                Console.WriteLine($"Discarding duplicate {entityType} entity with key value {keyValue}");
                            }
                        });
            }

            context.SaveChanges();
        }
-->
[!code-csharp[Attaching_a_serialized_graph_5](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_5)]

<span data-ttu-id="861de-214">針對圖形中的每個實體，此程式碼將會：</span><span class="sxs-lookup"><span data-stu-id="861de-214">For each entity in the graph, this code will:</span></span>

- <span data-ttu-id="861de-215">尋找實體的實體類型和金鑰值</span><span class="sxs-lookup"><span data-stu-id="861de-215">Find the entity type and key value of the entity</span></span>
- <span data-ttu-id="861de-216">在變更追蹤程式中使用此索引鍵查閱實體</span><span class="sxs-lookup"><span data-stu-id="861de-216">Lookup the entity with this key in the change tracker</span></span>
  - <span data-ttu-id="861de-217">如果找到實體，則不會採取進一步的動作，因為實體重複</span><span class="sxs-lookup"><span data-stu-id="861de-217">If the entity is found, then no further action is taken as the entity is a duplicate</span></span>
  - <span data-ttu-id="861de-218">如果找不到實體，則會將狀態設定為，以追蹤該實體。 `Modified`</span><span class="sxs-lookup"><span data-stu-id="861de-218">If the entity is not found, then it is tracked by setting the state to `Modified`</span></span>

<span data-ttu-id="861de-219">執行此程式碼的輸出為：</span><span class="sxs-lookup"><span data-stu-id="861de-219">The output from running this code is:</span></span>

```output
Tracking EntityType: Post entity with key value 1
Tracking EntityType: Blog entity with key value 1
Tracking EntityType: Post entity with key value 2
Discarding duplicate EntityType: Post entity with key value 2
Tracking EntityType: Post entity with key value 3
Tracking EntityType: Blog entity with key value 2
Tracking EntityType: Post entity with key value 4
Discarding duplicate EntityType: Post entity with key value 4
```

> [!IMPORTANT]
> <span data-ttu-id="861de-220">這段 **程式碼假設所有的重複專案都完全相同**。</span><span class="sxs-lookup"><span data-stu-id="861de-220">This code **assumes that all duplicates are identical**.</span></span> <span data-ttu-id="861de-221">這可讓您安全地選擇其中一個重複專案，以在捨棄其他重複專案時進行追蹤。</span><span class="sxs-lookup"><span data-stu-id="861de-221">This makes it safe to arbitrarily choose one of the duplicates to track while discarding the others.</span></span> <span data-ttu-id="861de-222">如果重複專案可能不同，則程式碼將需要決定要使用哪一個，以及如何將屬性和導覽值結合在一起。</span><span class="sxs-lookup"><span data-stu-id="861de-222">If the duplicates can differ, then the code will need to decide how to determine which one to use, and how to combine property and navigation values together.</span></span>

> [!NOTE]
> <span data-ttu-id="861de-223">為了簡單起見，此程式碼假設每個實體都有一個稱為的 primary key 屬性 `Id` 。</span><span class="sxs-lookup"><span data-stu-id="861de-223">For simplicity, this code assumes each entity has a primary key property called `Id`.</span></span> <span data-ttu-id="861de-224">這可能會編寫至抽象基類或介面。</span><span class="sxs-lookup"><span data-stu-id="861de-224">This could be codified into an abstract base class or interface.</span></span> <span data-ttu-id="861de-225">或者，您可以從中繼資料取得 primary key 屬性或屬性， <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> 讓此程式碼能夠使用任何類型的實體。</span><span class="sxs-lookup"><span data-stu-id="861de-225">Alternately, the primary key property or properties could be obtained from the <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadata such that this code would work with any type of entity.</span></span>

## <a name="failing-to-set-key-values"></a><span data-ttu-id="861de-226">無法設定索引鍵值</span><span class="sxs-lookup"><span data-stu-id="861de-226">Failing to set key values</span></span>

<span data-ttu-id="861de-227">實體類型通常會設定為使用 [自動產生](xref:core/modeling/generated-properties)的索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="861de-227">Entity types are often configured to use [automatically generated key values](xref:core/modeling/generated-properties).</span></span> <span data-ttu-id="861de-228">這是非複合索引鍵的整數和 GUID 屬性的預設值。</span><span class="sxs-lookup"><span data-stu-id="861de-228">This is the default for integer and GUID properties of non-composite keys.</span></span> <span data-ttu-id="861de-229">但是，如果實體類型未設定為使用自動產生的索引鍵值，則必須在追蹤實體之前設定明確的索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="861de-229">However, if the entity type is not configured to use automatically generated key values, then an explicit key value must be set before tracking the entity.</span></span> <span data-ttu-id="861de-230">例如，使用下列實體類型：</span><span class="sxs-lookup"><span data-stu-id="861de-230">For example, using the following entity type:</span></span>

<!--
    public class Pet
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Name { get; set; }
    }
-->
[!code-csharp[Pet](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Pet)]

<span data-ttu-id="861de-231">請考慮嘗試追蹤兩個新實體實例的程式碼，而不設定索引鍵值：</span><span class="sxs-lookup"><span data-stu-id="861de-231">Consider code that attempts to tracker two new entity instances without setting key values:</span></span>

<!--
            using var context = new BlogsContext();

            context.Add(new Pet { Name = "Smokey" });

            try
            {
                context.Add(new Pet { Name = "Clippy" }); // This will throw
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
            }
-->
[!code-csharp[Failing_to_set_key_values_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Failing_to_set_key_values_1)]

<span data-ttu-id="861de-232">這段程式碼將會擲回：</span><span class="sxs-lookup"><span data-stu-id="861de-232">This code will throw:</span></span>

> <span data-ttu-id="861de-233">InvalidOperationException：無法追蹤實體類型 ' 寵物 ' 的實例，因為已追蹤另一個具有索引鍵值 ' {Id： 0} ' 的實例。</span><span class="sxs-lookup"><span data-stu-id="861de-233">System.InvalidOperationException: The instance of entity type 'Pet' cannot be tracked because another instance with the key value '{Id: 0}' is already being tracked.</span></span> <span data-ttu-id="861de-234">附加現有的實體時，請確定只附加一個具有指定索引鍵值的實體實例。</span><span class="sxs-lookup"><span data-stu-id="861de-234">When attaching existing entities, ensure that only one entity instance with a given key value is attached.</span></span>

<span data-ttu-id="861de-235">修正此問題的方法是明確地設定索引鍵值，或將索引鍵屬性設定為使用產生的索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="861de-235">The fix for this is to either to set key values explicitly or configure the key property to use generated key values.</span></span> <span data-ttu-id="861de-236">如需詳細資訊，請參閱 [產生的值](xref:core/modeling/generated-properties) 。</span><span class="sxs-lookup"><span data-stu-id="861de-236">See [Generated Values](xref:core/modeling/generated-properties) for more information.</span></span>

## <a name="overusing-a-single-dbcontext-instance"></a><span data-ttu-id="861de-237">過度使用單一 DbCoNtext 實例</span><span class="sxs-lookup"><span data-stu-id="861de-237">Overusing a single DbContext instance</span></span>

<span data-ttu-id="861de-238"><xref:Microsoft.EntityFrameworkCore.DbContext> 的設計目的是代表短期的工作單位（如 [DbCoNtext 初始化和](xref:core/dbcontext-configuration/index)設定中所述），以及 [EF Core 變更追蹤](xref:core/change-tracking/index)中的詳細說明。</span><span class="sxs-lookup"><span data-stu-id="861de-238"><xref:Microsoft.EntityFrameworkCore.DbContext> is designed to represent a short-lived unit-of-work, as described in [DbContext Initialization and Configuration](xref:core/dbcontext-configuration/index), and elaborated on in [Change Tracking in EF Core](xref:core/change-tracking/index).</span></span> <span data-ttu-id="861de-239">未遵循此指引可讓您輕鬆地遇到嘗試追蹤相同實體的多個實例的情況。</span><span class="sxs-lookup"><span data-stu-id="861de-239">Not following this guidance makes it is easy to run into situations where an attempt is made to track multiple instances of the same entity.</span></span> <span data-ttu-id="861de-240">常見範例包括：</span><span class="sxs-lookup"><span data-stu-id="861de-240">Common examples are:</span></span>

- <span data-ttu-id="861de-241">使用相同的 DbCoNtext 實例來設定測試狀態，然後執行測試。</span><span class="sxs-lookup"><span data-stu-id="861de-241">Using the same DbContext instance to both set up test state and then execute the test.</span></span> <span data-ttu-id="861de-242">這通常會導致 DbCoNtext 仍在測試設定中追蹤一個實體實例，然後嘗試在適當的測試中附加新的實例。</span><span class="sxs-lookup"><span data-stu-id="861de-242">This often results in the DbContext still tracking one entity instance from test setup, while then attempting to attach a new instance in the test proper.</span></span> <span data-ttu-id="861de-243">相反地，請使用不同的 DbCoNtext 實例來設定測試狀態和適當的測試程式碼。</span><span class="sxs-lookup"><span data-stu-id="861de-243">Instead, use a different DbContext instance for setting up test state and the test code proper.</span></span>
- <span data-ttu-id="861de-244">使用存放庫中的共用 DbCoNtext 實例或類似的程式碼。</span><span class="sxs-lookup"><span data-stu-id="861de-244">Using a shared DbContext instance in a repository or similar code.</span></span> <span data-ttu-id="861de-245">相反地，請確定您的存放庫針對每個工作單位使用單一 DbCoNtext 實例。</span><span class="sxs-lookup"><span data-stu-id="861de-245">Instead, make sure your repository uses a single DbContext instance for each unit-of-work.</span></span>

## <a name="identity-resolution-and-queries"></a><span data-ttu-id="861de-246">身分識別解析和查詢</span><span class="sxs-lookup"><span data-stu-id="861de-246">Identity resolution and queries</span></span>

<span data-ttu-id="861de-247">當從查詢追蹤實體時，會自動進行識別解析。</span><span class="sxs-lookup"><span data-stu-id="861de-247">Identity resolution happens automatically when entities are tracked from a query.</span></span> <span data-ttu-id="861de-248">這表示，如果已經追蹤具有指定之索引鍵值的實體實例，則會使用這個現有的追蹤實例，而不是建立新的實例。</span><span class="sxs-lookup"><span data-stu-id="861de-248">This means that if an entity instance with a given key value is already tracked, then this existing tracked instance is used instead of creating a new instance.</span></span> <span data-ttu-id="861de-249">這有一個重要的結果：如果資料庫中的資料已變更，則不會反映在查詢的結果中。</span><span class="sxs-lookup"><span data-stu-id="861de-249">This has an important consequence: if the data has changed in the database, then this will not be reflected in the results of the query.</span></span> <span data-ttu-id="861de-250">這是將新的 DbCoNtext 實例用於每個工作單位（如 [DbCoNtext 初始化和](xref:core/dbcontext-configuration/index)設定，以及 [EF Core 中的變更追蹤](xref:core/change-tracking/index)中的詳細說明）的好理由。</span><span class="sxs-lookup"><span data-stu-id="861de-250">This is a good reason to use a new DbContext instance for each unit-of-work, as described in [DbContext Initialization and Configuration](xref:core/dbcontext-configuration/index), and elaborated on in [Change Tracking in EF Core](xref:core/change-tracking/index).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="861de-251">請務必瞭解 EF Core 一律會針對資料庫在 DbSet 上執行 LINQ 查詢，而且只會根據資料庫中的內容傳回結果。</span><span class="sxs-lookup"><span data-stu-id="861de-251">It is important to understand that EF Core always executes a LINQ query on a DbSet against the database and only returns results based on what is in the database.</span></span> <span data-ttu-id="861de-252">不過，對於追蹤查詢，如果傳回的實體已被追蹤，則會使用追蹤的實例，而不是從資料庫中的資料建立實例。</span><span class="sxs-lookup"><span data-stu-id="861de-252">However, for a tracking query, if the entities returned are already tracked, then the tracked instances are used instead of creating a instances from the data in the database.</span></span>

<span data-ttu-id="861de-253"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload> 或者， <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> 當追蹤的實體需要以資料庫中的最新資料重新整理時，也可以使用。</span><span class="sxs-lookup"><span data-stu-id="861de-253"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload> or <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> can be used when tracked entities need to be refreshed with the latest data from the database.</span></span> <span data-ttu-id="861de-254">如需詳細資訊，請參閱 [存取追蹤的實體](xref:core/change-tracking/entity-entries) 。</span><span class="sxs-lookup"><span data-stu-id="861de-254">See [Accessing Tracked Entities](xref:core/change-tracking/entity-entries) for more information.</span></span>

<span data-ttu-id="861de-255">相較于追蹤查詢，無追蹤查詢不會執行識別解析。</span><span class="sxs-lookup"><span data-stu-id="861de-255">In contrast to tracking queries, no-tracking queries do not perform identity resolution.</span></span> <span data-ttu-id="861de-256">這表示，在先前所述的 JSON 序列化案例中，無追蹤查詢可能會傳回重複的專案。</span><span class="sxs-lookup"><span data-stu-id="861de-256">This means that no-tracking queries can return duplicates just like in the JSON serialization case described earlier.</span></span> <span data-ttu-id="861de-257">如果查詢結果將會序列化並傳送至用戶端，這通常不會有問題。</span><span class="sxs-lookup"><span data-stu-id="861de-257">This is usually not an issue if the query results are going to be serialized and sent to the client.</span></span>

> [!TIP]
> <span data-ttu-id="861de-258">請勿定期執行無追蹤查詢，然後將傳回的實體附加至相同的內容。</span><span class="sxs-lookup"><span data-stu-id="861de-258">Do not routinely perform a no-tracking query and then attach the returned entities to the same context.</span></span> <span data-ttu-id="861de-259">這會比使用追蹤查詢更慢且更難。</span><span class="sxs-lookup"><span data-stu-id="861de-259">This will be both slower and harder to get right than using a tracking query.</span></span>

<span data-ttu-id="861de-260">無追蹤查詢不會執行身分識別解析，因為這樣會影響從查詢串流處理大量實體的效能。</span><span class="sxs-lookup"><span data-stu-id="861de-260">No-tracking queries do not perform identity resolution because doing so impacts the performance of streaming a large number of entities from a query.</span></span> <span data-ttu-id="861de-261">這是因為身分識別解析需要追蹤每個傳回的實例，如此就可以使用它，而不是稍後建立重複的實例。</span><span class="sxs-lookup"><span data-stu-id="861de-261">This is because identity resolution requires keeping track of each instance returned so that it can be used instead of later creating a duplicate.</span></span>

<span data-ttu-id="861de-262">從 EF Core 5.0 開始，可以強制執行不追蹤查詢，以使用來執行識別解析 <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.AsNoTrackingWithIdentityResolution%60%601(System.Linq.IQueryable{%60%600})> 。</span><span class="sxs-lookup"><span data-stu-id="861de-262">Starting with EF Core 5.0, no-tracking queries can be forced to perform identity resolution by using <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.AsNoTrackingWithIdentityResolution%60%601(System.Linq.IQueryable{%60%600})>.</span></span> <span data-ttu-id="861de-263">然後，查詢會追蹤傳回的實例 (而不是以正常方式進行追蹤) 並確保查詢結果中不會建立重複的專案。</span><span class="sxs-lookup"><span data-stu-id="861de-263">The query will then keep track of returned instances (without tracking them in the normal way) and ensure no duplicates are created in the query results.</span></span>

## <a name="overriding-object-equality"></a><span data-ttu-id="861de-264">覆寫物件相等</span><span class="sxs-lookup"><span data-stu-id="861de-264">Overriding object equality</span></span>

<span data-ttu-id="861de-265">比較實體實例時，EF Core 會使用 [參考相等](/dotnet/csharp/programming-guide/statements-expressions-operators/equality-comparisons) 。</span><span class="sxs-lookup"><span data-stu-id="861de-265">EF Core uses [reference equality](/dotnet/csharp/programming-guide/statements-expressions-operators/equality-comparisons) when comparing entity instances.</span></span> <span data-ttu-id="861de-266">即使實體類型覆寫 <xref:System.Object.Equals(System.Object)?displayProperty=nameWithType> 或變更物件相等，還是會發生這種情況。</span><span class="sxs-lookup"><span data-stu-id="861de-266">This is the case even if the entity types override <xref:System.Object.Equals(System.Object)?displayProperty=nameWithType> or otherwise change object equality.</span></span> <span data-ttu-id="861de-267">不過，有一個地方覆寫相等可能會影響 EF Core 行為：當集合導覽使用覆寫的相等，而不是參考相等時，則會將多個實例報告為相同。</span><span class="sxs-lookup"><span data-stu-id="861de-267">However, there is one place where overriding equality can impact EF Core behavior: when collection navigations use the overridden equality instead of reference equality, and hence report multiple instances as the same.</span></span>

<span data-ttu-id="861de-268">因此，建議您避免覆寫實體相等。</span><span class="sxs-lookup"><span data-stu-id="861de-268">Because of this it is recommended that overriding entity equality should be avoided.</span></span> <span data-ttu-id="861de-269">如果使用，請務必建立強制參考相等的集合導覽。</span><span class="sxs-lookup"><span data-stu-id="861de-269">If it is used, then make sure to create collection navigations that force reference equality.</span></span> <span data-ttu-id="861de-270">例如，建立使用參考相等的相等比較子：</span><span class="sxs-lookup"><span data-stu-id="861de-270">For example, create an equality comparer that uses reference equality:</span></span>

<!--
    public sealed class ReferenceEqualityComparer : IEqualityComparer<object>
    {
        private ReferenceEqualityComparer()
        {
        }

        public static ReferenceEqualityComparer Instance { get; } = new ReferenceEqualityComparer();

        bool IEqualityComparer<object>.Equals(object x, object y) => x == y;

        int IEqualityComparer<object>.GetHashCode(object obj) => RuntimeHelpers.GetHashCode(obj);
    }
-->
[!code-csharp[ReferenceEqualityComparer](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/ReferenceEqualityComparer.cs?name=ReferenceEqualityComparer)]

<span data-ttu-id="861de-271">從 .NET 5 開始 (，這會以的形式包含在 BCL 中 <xref:System.Collections.Generic.ReferenceEqualityComparer> 。 ) </span><span class="sxs-lookup"><span data-stu-id="861de-271">(Starting with .NET 5, this is included in the BCL as <xref:System.Collections.Generic.ReferenceEqualityComparer>.)</span></span>

<span data-ttu-id="861de-272">然後，您可以在建立集合導覽時使用這個比較子。</span><span class="sxs-lookup"><span data-stu-id="861de-272">This comparer can then be used when creating collection navigations.</span></span> <span data-ttu-id="861de-273">例如：</span><span class="sxs-lookup"><span data-stu-id="861de-273">For example:</span></span>

<!--
        public ICollection<Order> Orders { get; set; }
            = new HashSet<Order>(ReferenceEqualityComparer.Instance);
-->
[!code-csharp[OrdersCollection](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=OrdersCollection)]

### <a name="comparing-key-properties"></a><span data-ttu-id="861de-274">比較索引鍵屬性</span><span class="sxs-lookup"><span data-stu-id="861de-274">Comparing key properties</span></span>

<span data-ttu-id="861de-275">除了相等比較之外，也需要排序索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="861de-275">In addition to equality comparisons, key values also need to be ordered.</span></span> <span data-ttu-id="861de-276">在對 SaveChanges 的單一呼叫中更新多個實體時，請務必避免鎖死。</span><span class="sxs-lookup"><span data-stu-id="861de-276">This is important for avoiding deadlocks when updating multiple entities in a single call to SaveChanges.</span></span> <span data-ttu-id="861de-277">用於主要、替代或外鍵屬性的所有型別，以及用於唯一索引的型別都必須實 <xref:System.IComparable%601> 和 <xref:System.IEquatable%601> 。</span><span class="sxs-lookup"><span data-stu-id="861de-277">All types used for primary, alternate, or foreign key properties, as well as those used for unique indexes, must implement <xref:System.IComparable%601> and <xref:System.IEquatable%601>.</span></span> <span data-ttu-id="861de-278">通常用來做為索引鍵的類型 (int、Guid、字串等 ) 已經支援這些介面。</span><span class="sxs-lookup"><span data-stu-id="861de-278">Types normally used as keys (int, Guid, string, etc.) already support these interfaces.</span></span> <span data-ttu-id="861de-279">自訂金鑰類型可能會新增這些介面。</span><span class="sxs-lookup"><span data-stu-id="861de-279">Custom key types may to add these interfaces.</span></span>
