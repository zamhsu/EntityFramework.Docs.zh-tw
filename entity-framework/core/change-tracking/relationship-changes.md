---
title: 變更外鍵和導覽-EF Core
description: 如何藉由操作外鍵和導覽來變更實體之間的關聯性
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/relationship-changes
ms.openlocfilehash: 5d3a68a40ed8bd112c19bdaed466071b03e30a66
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024403"
---
# <a name="changing-foreign-keys-and-navigations"></a><span data-ttu-id="0c179-103">變更外鍵和導覽</span><span class="sxs-lookup"><span data-stu-id="0c179-103">Changing Foreign Keys and Navigations</span></span>

## <a name="overview-of-foreign-keys-and-navigations"></a><span data-ttu-id="0c179-104">外鍵和導覽的總覽</span><span class="sxs-lookup"><span data-stu-id="0c179-104">Overview of foreign keys and navigations</span></span>

<span data-ttu-id="0c179-105">Entity Framework Core (EF Core) 模型中的關聯性是使用 (Fk) 的外鍵來表示。</span><span class="sxs-lookup"><span data-stu-id="0c179-105">Relationships in an Entity Framework Core (EF Core) model are represented using foreign keys (FKs).</span></span> <span data-ttu-id="0c179-106">FK 是由關聯性中的相依或子實體上的一或多個屬性所組成。</span><span class="sxs-lookup"><span data-stu-id="0c179-106">An FK consists of one or more properties on the dependent or child entity in the relationship.</span></span> <span data-ttu-id="0c179-107">當相依/子系上的外鍵屬性值符合 (PK) 主體/父系上的屬性時，此相依/子系實體會與指定的主體/父實體相關聯。</span><span class="sxs-lookup"><span data-stu-id="0c179-107">This dependent/child entity is associated with a given principal/parent entity when the values of the foreign key properties on the dependent/child match the values of the alternate or primary key (PK) properties on the principal/parent.</span></span>

<span data-ttu-id="0c179-108">外鍵是在資料庫中儲存和操作關聯性的好方法，但是在應用程式程式碼中使用多個相關實體時，並不太容易。</span><span class="sxs-lookup"><span data-stu-id="0c179-108">Foreign keys are a good way to store and manipulate relationships in the database, but are not very friendly when working with multiple related entities in application code.</span></span> <span data-ttu-id="0c179-109">因此，大部分的 EF Core 模型也會在 FK 表示上分層「導覽」。</span><span class="sxs-lookup"><span data-stu-id="0c179-109">Therefore, most EF Core models also layer "navigations" over the FK representation.</span></span> <span data-ttu-id="0c179-110">導覽表單 c #/.NET 實體實例之間的參考，這些實體實例可反映將外鍵值與主要或替代索引鍵值相符的關聯。</span><span class="sxs-lookup"><span data-stu-id="0c179-110">Navigations form C#/.NET references between entity instances that reflect the associations found by matching foreign key values to primary or alternate key values.</span></span>

<span data-ttu-id="0c179-111">導覽可以在關聯性的兩端使用，或完全不使用，只留下 FK 屬性。</span><span class="sxs-lookup"><span data-stu-id="0c179-111">Navigations can be used on both sides of the relationship, on one side only, or not at all, leaving only the FK property.</span></span> <span data-ttu-id="0c179-112">FK 屬性可以藉由將其設為 [陰影屬性](xref:core/modeling/shadow-properties)來隱藏。</span><span class="sxs-lookup"><span data-stu-id="0c179-112">The FK property can be hidden by making it a [shadow property](xref:core/modeling/shadow-properties).</span></span> <span data-ttu-id="0c179-113">如需模型關聯性的詳細資訊，請參閱 [關聯](xref:core/modeling/relationships) 性。</span><span class="sxs-lookup"><span data-stu-id="0c179-113">See [Relationships](xref:core/modeling/relationships) for more information on modelling relationships.</span></span>

> [!TIP]
> <span data-ttu-id="0c179-114">本檔假設您已瞭解實體狀態和 EF Core 變更追蹤的基本概念。</span><span class="sxs-lookup"><span data-stu-id="0c179-114">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="0c179-115">如需有關這些主題的詳細資訊，請參閱 [EF Core 中的變更追蹤](xref:core/change-tracking/index) 。</span><span class="sxs-lookup"><span data-stu-id="0c179-115">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="0c179-116">您可以 [從 GitHub 下載範例程式碼](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/ChangingFKsAndNavigations)，以執行並偵測到本檔中的所有程式碼。</span><span class="sxs-lookup"><span data-stu-id="0c179-116">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/ChangingFKsAndNavigations).</span></span>

### <a name="example-model"></a><span data-ttu-id="0c179-117">範例模型</span><span class="sxs-lookup"><span data-stu-id="0c179-117">Example model</span></span>

<span data-ttu-id="0c179-118">下列模型包含四個實體類型及其之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="0c179-118">The following model contains four entity types with relationships between them.</span></span> <span data-ttu-id="0c179-119">程式碼中的批註會指出哪些屬性是外鍵、主鍵和導覽。</span><span class="sxs-lookup"><span data-stu-id="0c179-119">The comments in the code indicate which properties are foreign keys, primary keys, and navigations.</span></span>

<!--
public class Blog
{
    public int Id { get; set; } // Primary key
    public string Name { get; set; }

    public IList<Post> Posts { get; } = new List<Post>(); // Collection navigation
    public BlogAssets Assets { get; set; } // Reference navigation
}

public class BlogAssets
{
    public int Id { get; set; } // Primary key
    public byte[] Banner { get; set; }

    public int BlogId { get; set; } // Foreign key
    public Blog Blog { get; set; } // Reference navigation
}

public class Post
{
    public int Id { get; set; } // Primary key
    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; } // Foreign key
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
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Model)]

<span data-ttu-id="0c179-120">此模型中的三個關聯性為：</span><span class="sxs-lookup"><span data-stu-id="0c179-120">The three relationships in this model are:</span></span>

- <span data-ttu-id="0c179-121">每個 blog 都可以有多個張貼 (一對多) ：</span><span class="sxs-lookup"><span data-stu-id="0c179-121">Each blog can have many posts (one-to-many):</span></span>
  - <span data-ttu-id="0c179-122">`Blog` 為主體/父系。</span><span class="sxs-lookup"><span data-stu-id="0c179-122">`Blog` is the principal/parent.</span></span>
  - <span data-ttu-id="0c179-123">`Post` 這是相依/子系。</span><span class="sxs-lookup"><span data-stu-id="0c179-123">`Post` is the dependent/child.</span></span> <span data-ttu-id="0c179-124">它包含 FK 屬性 `Post.BlogId` ，其值必須符合 `Blog.Id` 相關 blog 的 PK 值。</span><span class="sxs-lookup"><span data-stu-id="0c179-124">It contains the FK property `Post.BlogId`, the value of which must match the `Blog.Id` PK value of the related blog.</span></span>
  - <span data-ttu-id="0c179-125">`Post.Blog` 是從 post 到相關的 blog 的參考導覽。</span><span class="sxs-lookup"><span data-stu-id="0c179-125">`Post.Blog` is a reference navigation from a post to the associated blog.</span></span> <span data-ttu-id="0c179-126">`Post.Blog` 是的反向導覽 `Blog.Posts` 。</span><span class="sxs-lookup"><span data-stu-id="0c179-126">`Post.Blog` is the inverse navigation for `Blog.Posts`.</span></span>
  - <span data-ttu-id="0c179-127">`Blog.Posts` 是從 blog 到所有相關貼文的集合導覽。</span><span class="sxs-lookup"><span data-stu-id="0c179-127">`Blog.Posts` is a collection navigation from a blog to all the associated posts.</span></span> <span data-ttu-id="0c179-128">`Blog.Posts` 是的反向導覽 `Post.Blog` 。</span><span class="sxs-lookup"><span data-stu-id="0c179-128">`Blog.Posts` is the inverse navigation for `Post.Blog`.</span></span>
- <span data-ttu-id="0c179-129">每個 blog 都可以有一個資產 (一對一的) ：</span><span class="sxs-lookup"><span data-stu-id="0c179-129">Each blog can have one assets (one-to-one):</span></span>
  - <span data-ttu-id="0c179-130">`Blog` 為主體/父系。</span><span class="sxs-lookup"><span data-stu-id="0c179-130">`Blog` is the principal/parent.</span></span>
  - <span data-ttu-id="0c179-131">`BlogAssets` 這是相依/子系。</span><span class="sxs-lookup"><span data-stu-id="0c179-131">`BlogAssets` is the dependent/child.</span></span> <span data-ttu-id="0c179-132">它包含 FK 屬性 `BlogAssets.BlogId` ，其值必須符合 `Blog.Id` 相關 blog 的 PK 值。</span><span class="sxs-lookup"><span data-stu-id="0c179-132">It contains the FK property `BlogAssets.BlogId`, the value of which must match the `Blog.Id` PK value of the related blog.</span></span>
  - <span data-ttu-id="0c179-133">`BlogAssets.Blog` 是從資產到相關的 blog 的參考導覽。</span><span class="sxs-lookup"><span data-stu-id="0c179-133">`BlogAssets.Blog` is a reference navigation from the assets to the associated blog.</span></span> <span data-ttu-id="0c179-134">`BlogAssets.Blog` 是的反向導覽 `Blog.Assets` 。</span><span class="sxs-lookup"><span data-stu-id="0c179-134">`BlogAssets.Blog` is the inverse navigation for `Blog.Assets`.</span></span>
  - <span data-ttu-id="0c179-135">`Blog.Assets` 是從 blog 到相關聯資產的參考導覽。</span><span class="sxs-lookup"><span data-stu-id="0c179-135">`Blog.Assets` is a reference navigation from the blog to the associated assets.</span></span> <span data-ttu-id="0c179-136">`Blog.Assets` 是的反向導覽 `BlogAssets.Blog` 。</span><span class="sxs-lookup"><span data-stu-id="0c179-136">`Blog.Assets` is the inverse navigation for `BlogAssets.Blog`.</span></span>
- <span data-ttu-id="0c179-137">每一篇文章都可以有多個標記，而且每個標籤都可以有許多 (多對多) 的貼文：</span><span class="sxs-lookup"><span data-stu-id="0c179-137">Each post can have many tags and each tag can have many posts (many-to-many):</span></span>
  - <span data-ttu-id="0c179-138">多對多關聯性是一層以上的 2 1 對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="0c179-138">Many-to-many relationships are a further layer over two one-to-many relationships.</span></span> <span data-ttu-id="0c179-139">本檔稍後會討論多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="0c179-139">Many-to-many relationships are covered later in this document.</span></span>
  - <span data-ttu-id="0c179-140">`Post.Tags` 是從 post 到所有相關標記的集合導覽。</span><span class="sxs-lookup"><span data-stu-id="0c179-140">`Post.Tags` is a collection navigation from a post to all the associated tags.</span></span> <span data-ttu-id="0c179-141">`Post.Tags` 是的反向導覽 `Tag.Posts` 。</span><span class="sxs-lookup"><span data-stu-id="0c179-141">`Post.Tags` is the inverse navigation for `Tag.Posts`.</span></span>
  - <span data-ttu-id="0c179-142">`Tag.Posts` 是從標記到所有相關貼文的集合導覽。</span><span class="sxs-lookup"><span data-stu-id="0c179-142">`Tag.Posts` is a collection navigation from a tag to all the associated posts.</span></span> <span data-ttu-id="0c179-143">`Tag.Posts` 是的反向導覽 `Post.Tags` 。</span><span class="sxs-lookup"><span data-stu-id="0c179-143">`Tag.Posts` is the inverse navigation for `Post.Tags`.</span></span>

<span data-ttu-id="0c179-144">如需有關如何建立關聯性模型和設定的詳細資訊，請參閱 [關聯](xref:core/modeling/relationships) 性。</span><span class="sxs-lookup"><span data-stu-id="0c179-144">See [Relationships](xref:core/modeling/relationships) for more information on how to model and configure relationships.</span></span>

## <a name="relationship-fixup"></a><span data-ttu-id="0c179-145">關聯性修正</span><span class="sxs-lookup"><span data-stu-id="0c179-145">Relationship fixup</span></span>

<span data-ttu-id="0c179-146">EF Core 會讓導覽與外鍵值保持一致，反之亦然。</span><span class="sxs-lookup"><span data-stu-id="0c179-146">EF Core keeps navigations in alignment with foreign key values and vice versa.</span></span> <span data-ttu-id="0c179-147">也就是說，如果外鍵值變更，使其現在參考不同的主體/父實體，則會更新導覽以反映這項變更。</span><span class="sxs-lookup"><span data-stu-id="0c179-147">That is, if a foreign key value changes such that it now refers to a different principal/parent entity, then the navigations are updated to reflect this change.</span></span> <span data-ttu-id="0c179-148">同樣地，如果變更導覽，則會更新相關實體的外鍵值，以反映這項變更。</span><span class="sxs-lookup"><span data-stu-id="0c179-148">Likewise, if a navigation is changed, then the foreign key values of the entities involved are updated to reflect this change.</span></span> <span data-ttu-id="0c179-149">這稱為「關聯性修正」。</span><span class="sxs-lookup"><span data-stu-id="0c179-149">This is called "relationship fixup".</span></span>

### <a name="fixup-by-query"></a><span data-ttu-id="0c179-150">修復（依查詢）</span><span class="sxs-lookup"><span data-stu-id="0c179-150">Fixup by query</span></span>

<span data-ttu-id="0c179-151">當從資料庫查詢實體時，會先發生修復問題。</span><span class="sxs-lookup"><span data-stu-id="0c179-151">Fixup first occurs when entities are queried from the database.</span></span> <span data-ttu-id="0c179-152">資料庫只有外鍵值，因此當 EF Core 從資料庫建立實體實例時，會使用外鍵值來設定參考導覽，並適當地將實體新增至集合導覽。</span><span class="sxs-lookup"><span data-stu-id="0c179-152">The database has only foreign key values, so when EF Core creates an entity instance from the database it uses the foreign key values to set reference navigations and add entities to collection navigations as appropriate.</span></span> <span data-ttu-id="0c179-153">例如，請考慮對 blog 和其相關文章和資產的查詢：</span><span class="sxs-lookup"><span data-stu-id="0c179-153">For example, consider a query for blogs and its associated posts and assets:</span></span>

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs
            .Include(e => e.Posts)
            .Include(e => e.Assets)
            .ToList();

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Relationship_fixup_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Relationship_fixup_1)]

<span data-ttu-id="0c179-154">針對每個 blog，EF Core 會先建立 `Blog` 實例。</span><span class="sxs-lookup"><span data-stu-id="0c179-154">For each blog, EF Core will first create a `Blog` instance.</span></span> <span data-ttu-id="0c179-155">然後，當每個貼文都從資料庫載入時，就 `Post.Blog` 會將參考導覽設定為指向相關的 blog。</span><span class="sxs-lookup"><span data-stu-id="0c179-155">Then, as each post is loaded from the database its `Post.Blog` reference navigation is set to point to the associated blog.</span></span> <span data-ttu-id="0c179-156">同樣地，post 也會新增至 `Blog.Posts` 集合導覽。</span><span class="sxs-lookup"><span data-stu-id="0c179-156">Likewise, the post is added to the `Blog.Posts` collection navigation.</span></span> <span data-ttu-id="0c179-157">也會發生相同的問題 `BlogAssets` ，但在此情況下，這兩個導覽都是參考。</span><span class="sxs-lookup"><span data-stu-id="0c179-157">The same thing happens with `BlogAssets`, except in this case both navigations are references.</span></span> <span data-ttu-id="0c179-158">`Blog.Assets`導覽設定為指向 [資產] 實例，而 `BlogAsserts.Blog` 導覽設定為指向 [blog] 實例。</span><span class="sxs-lookup"><span data-stu-id="0c179-158">The `Blog.Assets` navigation is set to point to the assets instance, and the `BlogAsserts.Blog` navigation is set to point to the blog instance.</span></span>

<span data-ttu-id="0c179-159">查看此查詢之後的 [變更追蹤器調試](xref:core/change-tracking/debug-views) 程式，會顯示兩個 blog，每個都有一個資產和兩個貼文：</span><span class="sxs-lookup"><span data-stu-id="0c179-159">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) after this query shows two blogs, each with one assets and two posts being tracked:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: 1}
  Posts: [{Id: 1}, {Id: 2}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 1} Unchanged
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 2} Unchanged
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: []
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
Post {Id: 4} Unchanged
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

<span data-ttu-id="0c179-160">[調試] 視圖會顯示索引鍵值和導覽。</span><span class="sxs-lookup"><span data-stu-id="0c179-160">The debug view shows both key values and navigations.</span></span> <span data-ttu-id="0c179-161">導覽會使用相關實體的主鍵值來顯示。</span><span class="sxs-lookup"><span data-stu-id="0c179-161">Navigations are shown using the primary key values of the related entities.</span></span> <span data-ttu-id="0c179-162">例如， `Posts: [{Id: 1}, {Id: 2}]` 在上面的輸出中，表示 `Blog.Posts` 集合導覽會分別包含兩個與主鍵1和2相關的貼文。</span><span class="sxs-lookup"><span data-stu-id="0c179-162">For example, `Posts: [{Id: 1}, {Id: 2}]` in the output above indicates that the `Blog.Posts` collection navigation contains two related posts with primary keys 1 and 2 respectively.</span></span> <span data-ttu-id="0c179-163">同樣地，對於每個與第一個 blog 相關聯的貼文， `Blog: {Id: 1}` 這一行指出 `Post.Blog` 導覽參考了主要金鑰1的 blog。</span><span class="sxs-lookup"><span data-stu-id="0c179-163">Similarly, for each post associated with the first blog, the `Blog: {Id: 1}` line indicates that the `Post.Blog` navigation references the Blog with primary key 1.</span></span>

### <a name="fixup-to-locally-tracked-entities"></a><span data-ttu-id="0c179-164">修復本機追蹤的實體</span><span class="sxs-lookup"><span data-stu-id="0c179-164">Fixup to locally tracked entities</span></span>

<span data-ttu-id="0c179-165">關聯性修正程式也會發生在從追蹤查詢傳回的實體與 DbCoNtext 已追蹤的實體之間。</span><span class="sxs-lookup"><span data-stu-id="0c179-165">Relationship fixup also happens between entities returned from a tracking query and entities already tracked by the DbContext.</span></span> <span data-ttu-id="0c179-166">例如，請考慮針對 blog、文章和資產執行三個不同的查詢：</span><span class="sxs-lookup"><span data-stu-id="0c179-166">For example, consider executing three separate queries for blogs, posts, and assets:</span></span>

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var assets = context.Assets.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var posts = context.Posts.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Relationship_fixup_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Relationship_fixup_2)]
<span data-ttu-id="0c179-167">在第一次查詢時，在第一次查詢時，只會追蹤兩個 blog：</span><span class="sxs-lookup"><span data-stu-id="0c179-167">Looking again at the debug views, after the first query only the two blogs are tracked:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: []
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: <null>
  Posts: []
```

<span data-ttu-id="0c179-168">`Blog.Assets`參考導覽為 null，而且集合導覽 `Blog.Posts` 是空的，因為內容目前未追蹤任何相關聯的實體。</span><span class="sxs-lookup"><span data-stu-id="0c179-168">The `Blog.Assets` reference navigations are null, and the `Blog.Posts` collection navigations are empty because no associated entities are currently being tracked by the context.</span></span>

<span data-ttu-id="0c179-169">在第二個查詢之後， `Blogs.Assets` 已修正參考導覽以指向新追蹤的 `BlogAsset` 實例。</span><span class="sxs-lookup"><span data-stu-id="0c179-169">After the second query, the `Blogs.Assets` reference navigations have been fixed up to point to the newly tracked `BlogAsset` instances.</span></span> <span data-ttu-id="0c179-170">同樣地， `BlogAssets.Blog` 參考導覽也會設定為指向適當的已追蹤 `Blog` 實例。</span><span class="sxs-lookup"><span data-stu-id="0c179-170">Likewise, the `BlogAssets.Blog` reference navigations are set to point to the appropriate already tracked `Blog` instance.</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: 1}
  Posts: []
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: []
BlogAssets {Id: 1} Unchanged
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 2} Unchanged
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
```

<span data-ttu-id="0c179-171">最後，在第三個查詢之後， `Blog.Posts` 集合導覽現在會包含所有相關的貼文，而這些 `Post.Blog` 參考會指向適當的 `Blog` 實例：</span><span class="sxs-lookup"><span data-stu-id="0c179-171">Finally, after the third query, the `Blog.Posts` collection navigations now contain all related posts, and the `Post.Blog` references point to the appropriate `Blog` instance:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: 1}
  Posts: [{Id: 1}, {Id: 2}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 1} Unchanged
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 2} Unchanged
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: []
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
Post {Id: 4} Unchanged
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

<span data-ttu-id="0c179-172">這與原始的單一查詢相同，因為 EF Core 已在追蹤實體的情況下，將導覽視為實體進行修正，即使來自多個不同的查詢也一樣。</span><span class="sxs-lookup"><span data-stu-id="0c179-172">This is the same end-state as was achieved with the original single query, since EF Core fixed up navigations as entities were tracked, even when coming from multiple different queries.</span></span>

> [!NOTE]
> <span data-ttu-id="0c179-173">修復永遠不會導致從資料庫傳回更多資料。</span><span class="sxs-lookup"><span data-stu-id="0c179-173">Fixup never causes more data to be returned from the database.</span></span> <span data-ttu-id="0c179-174">它只會連接查詢所傳回或已由 DbCoNtext 追蹤的實體。</span><span class="sxs-lookup"><span data-stu-id="0c179-174">It only connects entities that are already returned by the query or already tracked by the DbContext.</span></span> <span data-ttu-id="0c179-175">如需在序列化實體時處理重複項的詳細資訊，請參閱 [EF Core 中的識別解析](xref:core/change-tracking/identity-resolution) 。</span><span class="sxs-lookup"><span data-stu-id="0c179-175">See [Identity Resolution in EF Core](xref:core/change-tracking/identity-resolution) for information about handling duplicates when serializing entities.</span></span>

## <a name="changing-relationships-using-navigations"></a><span data-ttu-id="0c179-176">使用導覽變更關聯性</span><span class="sxs-lookup"><span data-stu-id="0c179-176">Changing relationships using navigations</span></span>

<span data-ttu-id="0c179-177">若要變更兩個實體之間的關聯性，最簡單的方式是藉由操作導覽，並讓 EF Core 適當地修復反向導覽和 FK 值。</span><span class="sxs-lookup"><span data-stu-id="0c179-177">The easiest way to change the relationship between two entities is by manipulating a navigation, while leaving EF Core to fixup the inverse navigation and FK values appropriately.</span></span> <span data-ttu-id="0c179-178">可以下列方式進行：</span><span class="sxs-lookup"><span data-stu-id="0c179-178">This can be done by:</span></span>

- <span data-ttu-id="0c179-179">新增或移除集合導覽中的實體。</span><span class="sxs-lookup"><span data-stu-id="0c179-179">Adding or removing an entity from a collection navigation.</span></span>
- <span data-ttu-id="0c179-180">變更參考導覽以指向不同的實體，或將它設定為 null。</span><span class="sxs-lookup"><span data-stu-id="0c179-180">Changing a reference navigation to point to a different entity, or setting it to null.</span></span>

### <a name="adding-or-removing-from-collection-navigations"></a><span data-ttu-id="0c179-181">從集合導覽新增或移除</span><span class="sxs-lookup"><span data-stu-id="0c179-181">Adding or removing from collection navigations</span></span>

<span data-ttu-id="0c179-182">例如，讓我們將 Visual Studio blog 的其中一篇文章移至 .NET blog。</span><span class="sxs-lookup"><span data-stu-id="0c179-182">For example, let's move one of the posts from the Visual Studio blog to the .NET blog.</span></span> <span data-ttu-id="0c179-183">這需要先載入 blog 和貼文，然後再從一個 blog 上的導覽集合將 post 移至其他 blog 上的導覽集合：</span><span class="sxs-lookup"><span data-stu-id="0c179-183">This requires first loading the blogs and posts, and then moving the post from the navigation collection on one blog to the navigation collection on the other blog:</span></span>

<!--
        using var context = new BlogsContext();

        var dotNetBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == ".NET Blog");
        var vsBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == "Visual Studio Blog");

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);
        dotNetBlog.Posts.Add(post);

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Changing_relationships_using_navigations_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_navigations_1)]

> [!TIP]
> <span data-ttu-id="0c179-184"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType>這裡需要呼叫，因為存取偵錯工具並不會[自動偵測變更](xref:core/change-tracking/change-detection)。</span><span class="sxs-lookup"><span data-stu-id="0c179-184">A call to <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> is needed here because accessing the debug view does not cause [automatic detection of changes](xref:core/change-tracking/change-detection).</span></span>

<span data-ttu-id="0c179-185">這是在執行上述程式碼之後列印的 debug view：</span><span class="sxs-lookup"><span data-stu-id="0c179-185">This is the debug view printed after running the code above:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: <null>
  Posts: [{Id: 4}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: []
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: 1 FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 1}
  Tags: []
Post {Id: 4} Unchanged
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

<span data-ttu-id="0c179-186">`Blog.Posts`.Net Blog 上的導覽現在有三個貼文 (`Posts: [{Id: 1}, {Id: 2}, {Id: 3}]`) 。</span><span class="sxs-lookup"><span data-stu-id="0c179-186">The `Blog.Posts` navigation on the .NET Blog now has three posts (`Posts: [{Id: 1}, {Id: 2}, {Id: 3}]`).</span></span> <span data-ttu-id="0c179-187">同樣地， `Blog.Posts` Visual Studio blog 上的導覽只會有一個 post (`Posts: [{Id: 4}]`) 。</span><span class="sxs-lookup"><span data-stu-id="0c179-187">Likewise, the `Blog.Posts` navigation on the Visual Studio blog only has one post (`Posts: [{Id: 4}]`).</span></span> <span data-ttu-id="0c179-188">這是預期的，因為程式碼會明確變更這些集合。</span><span class="sxs-lookup"><span data-stu-id="0c179-188">This is to be expected since the code explicitly changed these collections.</span></span>

<span data-ttu-id="0c179-189">更有趣的是，即使程式碼未明確變更 `Post.Blog` 導覽，還是已修正為指向 Visual Studio blog (`Blog: {Id: 1}`) 。</span><span class="sxs-lookup"><span data-stu-id="0c179-189">More interestingly, even though the code did not explicitly change the `Post.Blog` navigation, it has been fixed-up to point to the Visual Studio blog (`Blog: {Id: 1}`).</span></span> <span data-ttu-id="0c179-190">此外， `Post.BlogId` 外鍵值也已更新，以符合 .net blog 的主鍵值。</span><span class="sxs-lookup"><span data-stu-id="0c179-190">Also, the `Post.BlogId` foreign key value has been updated to match the primary key value of the .NET blog.</span></span> <span data-ttu-id="0c179-191">這項變更會變更中的 FK 值，然後在呼叫 SaveChanges 時保存到資料庫中：</span><span class="sxs-lookup"><span data-stu-id="0c179-191">This change to the FK value in then persisted to the database when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='3' (DbType = String), @p0='1' (Nullable = true) (DbType = String)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();
```

### <a name="changing-reference-navigations"></a><span data-ttu-id="0c179-192">變更參考導覽</span><span class="sxs-lookup"><span data-stu-id="0c179-192">Changing reference navigations</span></span>

<span data-ttu-id="0c179-193">在先前的範例中，會在每個 blog 上操作貼文的集合流覽，以將貼文從某個 blog 移至另一個。</span><span class="sxs-lookup"><span data-stu-id="0c179-193">In the previous example, a post was moved from one blog to another by manipulating the collection navigation of posts on each blog.</span></span> <span data-ttu-id="0c179-194">您可以改為將 `Post.Blog` 參考導覽變更為指向新的 blog，以達成相同的目的。</span><span class="sxs-lookup"><span data-stu-id="0c179-194">The same thing can be achieved by instead changing the `Post.Blog` reference navigation to point to the new blog.</span></span> <span data-ttu-id="0c179-195">例如：</span><span class="sxs-lookup"><span data-stu-id="0c179-195">For example:</span></span>

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        post.Blog = dotNetBlog;
-->
[!code-csharp[Changing_relationships_using_navigations_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_navigations_2)]

<span data-ttu-id="0c179-196">這項變更之後的偵錯工具看起來與之前的範例 _完全相同_ 。</span><span class="sxs-lookup"><span data-stu-id="0c179-196">The debug view after this change is _exactly the same_ as it was in the previous example.</span></span> <span data-ttu-id="0c179-197">這是因為 EF Core 偵測到參考導覽變更，然後將集合導覽和 FK 值修正為相符。</span><span class="sxs-lookup"><span data-stu-id="0c179-197">This because EF Core detected the reference navigation change and then fixed up the collection navigations and FK value to match.</span></span>

## <a name="changing-relationships-using-foreign-key-values"></a><span data-ttu-id="0c179-198">使用外鍵值變更關聯性</span><span class="sxs-lookup"><span data-stu-id="0c179-198">Changing relationships using foreign key values</span></span>

<span data-ttu-id="0c179-199">在上一節中，關聯性是由導航操作，讓外鍵值自動更新。</span><span class="sxs-lookup"><span data-stu-id="0c179-199">In the previous section, relationships were manipulated by navigations leaving foreign key values to be updated automatically.</span></span> <span data-ttu-id="0c179-200">這是在 EF Core 中操作關聯性的建議方式。</span><span class="sxs-lookup"><span data-stu-id="0c179-200">This is the recommended way to manipulate relationships in EF Core.</span></span> <span data-ttu-id="0c179-201">不過，您也可以直接操作 FK 值。</span><span class="sxs-lookup"><span data-stu-id="0c179-201">However, it is also possible to manipulate FK values directly.</span></span> <span data-ttu-id="0c179-202">例如，我們可以變更外鍵值，將貼文從某個 blog 移至另一個 `Post.BlogId` ：</span><span class="sxs-lookup"><span data-stu-id="0c179-202">For example, we can move a post from one blog to another by changing the `Post.BlogId` foreign key value:</span></span>

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        post.BlogId = dotNetBlog.Id;
-->
[!code-csharp[Changing_relationships_using_foreign_key_values_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_foreign_key_values_1)]

<span data-ttu-id="0c179-203">請注意，這與變更參考導覽的方式非常類似，如先前的範例所示。</span><span class="sxs-lookup"><span data-stu-id="0c179-203">Notice how this is very similar to changing the reference navigation, as shown in the previous example.</span></span>

<span data-ttu-id="0c179-204">這項變更之後的偵錯工具會再次與先前兩個範例的案例 _完全相同_ 。</span><span class="sxs-lookup"><span data-stu-id="0c179-204">The debug view after this change is again _exactly the same_ as was the case for the previous two examples.</span></span> <span data-ttu-id="0c179-205">這是因為 EF Core 偵測到 FK 值變更，然後將參考和集合導覽全部修正為相符。</span><span class="sxs-lookup"><span data-stu-id="0c179-205">This because EF Core detected the FK value change and then fixed up both the reference and collection navigations to match.</span></span>

> [!TIP]
> <span data-ttu-id="0c179-206">當關聯性變更時，請勿撰寫程式碼來操作所有導覽和 FK 值。</span><span class="sxs-lookup"><span data-stu-id="0c179-206">Do not write code to manipulate all navigations and FK values each time a relationship changes.</span></span> <span data-ttu-id="0c179-207">這類程式碼更複雜，而且必須確保每個案例中的外鍵和導覽一致變更。</span><span class="sxs-lookup"><span data-stu-id="0c179-207">Such code is more complicated and must ensure consistent changes to foreign keys and navigations in every case.</span></span> <span data-ttu-id="0c179-208">可能的話，只要操作單一導覽，或可能兩種導覽。</span><span class="sxs-lookup"><span data-stu-id="0c179-208">If possible, just manipulate a single navigation, or maybe both navigations.</span></span> <span data-ttu-id="0c179-209">如有需要，只要操作 FK 值即可。</span><span class="sxs-lookup"><span data-stu-id="0c179-209">If needed, just manipulate FK values.</span></span> <span data-ttu-id="0c179-210">避免同時操作導覽和 FK 值。</span><span class="sxs-lookup"><span data-stu-id="0c179-210">Avoid manipulating both navigations and FK values.</span></span>

## <a name="fixup-for-added-or-deleted-entities"></a><span data-ttu-id="0c179-211">新增或刪除之實體的修復</span><span class="sxs-lookup"><span data-stu-id="0c179-211">Fixup for added or deleted entities</span></span>

### <a name="adding-to-a-collection-navigation"></a><span data-ttu-id="0c179-212">加入至集合導覽</span><span class="sxs-lookup"><span data-stu-id="0c179-212">Adding to a collection navigation</span></span>

<span data-ttu-id="0c179-213">EF Core 會在 [偵測](xref:core/change-tracking/change-detection) 到新的相依/子實體已加入至集合導覽時執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="0c179-213">EF Core performs the following actions when it [detects](xref:core/change-tracking/change-detection) that a new dependent/child entity has been added to a collection navigation:</span></span>

- <span data-ttu-id="0c179-214">如果未追蹤實體，則會進行追蹤。</span><span class="sxs-lookup"><span data-stu-id="0c179-214">If the entity is not tracked, then it is tracked.</span></span> <span data-ttu-id="0c179-215"> (實體通常會處於 `Added` 狀態。</span><span class="sxs-lookup"><span data-stu-id="0c179-215">(The entity will usually be in the `Added` state.</span></span> <span data-ttu-id="0c179-216">但是，如果實體類型設定為使用產生的索引鍵，且已設定主鍵值，則會以狀態追蹤實體 `Unchanged` 。 ) </span><span class="sxs-lookup"><span data-stu-id="0c179-216">However, if the entity type is configured to use generated keys and the primary key value is set, then the entity is tracked in the `Unchanged` state.)</span></span>
- <span data-ttu-id="0c179-217">如果實體與不同的主體/父系相關聯，則會切斷該關聯性。</span><span class="sxs-lookup"><span data-stu-id="0c179-217">If the entity is associated with a different principal/parent, then that relationship is severed.</span></span>
- <span data-ttu-id="0c179-218">實體會與擁有集合導覽的主體/父系產生關聯。</span><span class="sxs-lookup"><span data-stu-id="0c179-218">The entity becomes associated with the principal/parent that owns the collection navigation.</span></span>
- <span data-ttu-id="0c179-219">所有相關實體都會修正導覽和外鍵值。</span><span class="sxs-lookup"><span data-stu-id="0c179-219">Navigations and foreign key values are fixed up for all entities involved.</span></span>

<span data-ttu-id="0c179-220">根據這一點，我們可以看到，將貼文從某個 blog 移至另一個，其實不需要將它從舊的集合導覽中移除，再將它新增至新的。</span><span class="sxs-lookup"><span data-stu-id="0c179-220">Based on this we can see that to move a post from one blog to another we don't actually need to remove it from the old collection navigation before adding it to the new.</span></span> <span data-ttu-id="0c179-221">因此，您可以從上述範例中的程式碼進行變更：</span><span class="sxs-lookup"><span data-stu-id="0c179-221">So the code from the example above can be changed from:</span></span>

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);
        dotNetBlog.Posts.Add(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_1)]

<span data-ttu-id="0c179-222">變更為：</span><span class="sxs-lookup"><span data-stu-id="0c179-222">To:</span></span>

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        dotNetBlog.Posts.Add(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_2)]

<span data-ttu-id="0c179-223">EF Core 會看到貼文已新增至新的 blog，並會自動從第一個 blog 的集合中移除。</span><span class="sxs-lookup"><span data-stu-id="0c179-223">EF Core sees that the post has been added to a new blog and automatically removes it from the collection on the first blog.</span></span>

### <a name="removing-from-a-collection-navigation"></a><span data-ttu-id="0c179-224">從集合導覽中移除</span><span class="sxs-lookup"><span data-stu-id="0c179-224">Removing from a collection navigation</span></span>

<span data-ttu-id="0c179-225">從主體/父系的集合導覽中移除相依/子系實體，會導致切斷與該主體/父系的關聯性。</span><span class="sxs-lookup"><span data-stu-id="0c179-225">Removing a dependent/child entity from the collection navigation of the principal/parent causes severing of the relationship to that principal/parent.</span></span> <span data-ttu-id="0c179-226">接下來會發生什麼事，取決於關聯性是選擇性或必要。</span><span class="sxs-lookup"><span data-stu-id="0c179-226">What happens next depends on whether the relationship is optional or required.</span></span>

#### <a name="optional-relationships"></a><span data-ttu-id="0c179-227">組織關係</span><span class="sxs-lookup"><span data-stu-id="0c179-227">Optional relationships</span></span>

<span data-ttu-id="0c179-228">根據預設，選擇性的關聯性會將外鍵值設定為 null。</span><span class="sxs-lookup"><span data-stu-id="0c179-228">By default for optional relationships, the foreign key value is set to null.</span></span> <span data-ttu-id="0c179-229">這表示相依/子系不再與 _任何_ 主體/父系相關聯。</span><span class="sxs-lookup"><span data-stu-id="0c179-229">This means that the dependent/child is no longer associated with _any_ principal/parent.</span></span> <span data-ttu-id="0c179-230">例如，讓我們載入一個 blog 和文章，然後從集合導覽中移除其中一篇文章 `Blog.Posts` ：</span><span class="sxs-lookup"><span data-stu-id="0c179-230">For example, let's load a blog and posts and then remove one of the posts from the `Blog.Posts` collection navigation:</span></span>

<!--
        var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
        dotNetBlog.Posts.Remove(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_3](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_3)]

<span data-ttu-id="0c179-231">在此變更之後查看 [變更追蹤調試](xref:core/change-tracking/debug-views) 程式，會顯示：</span><span class="sxs-lookup"><span data-stu-id="0c179-231">Looking at the [change tracking debug view](xref:core/change-tracking/debug-views) after this change shows that:</span></span>

- <span data-ttu-id="0c179-232">`Post.BlogId`FK 已設定為 null (`BlogId: <null> FK Modified Originally 1`) </span><span class="sxs-lookup"><span data-stu-id="0c179-232">The `Post.BlogId` FK has been set to null (`BlogId: <null> FK Modified Originally 1`)</span></span>
- <span data-ttu-id="0c179-233">`Post.Blog`參考導覽已設定為 null (`Blog: <null>`) </span><span class="sxs-lookup"><span data-stu-id="0c179-233">The `Post.Blog` reference navigation has been set to null (`Blog: <null>`)</span></span>
- <span data-ttu-id="0c179-234">貼文已從 `Blog.Posts` 集合導覽 (移除 `Posts: [{Id: 1}]`) </span><span class="sxs-lookup"><span data-stu-id="0c179-234">The post has been removed from `Blog.Posts` collection navigation (`Posts: [{Id: 1}]`)</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
  Tags: []
```

<span data-ttu-id="0c179-235">請注意，貼文 _未標示_ 為 `Deleted` 。</span><span class="sxs-lookup"><span data-stu-id="0c179-235">Notice that the post is _not_ marked as `Deleted`.</span></span> <span data-ttu-id="0c179-236">它會標示為， `Modified` 以便在呼叫 SaveChanges 時，將資料庫中的 FK 值設定為 null。</span><span class="sxs-lookup"><span data-stu-id="0c179-236">It is marked as `Modified` so that the FK value in the database will be set to null when SaveChanges is called.</span></span>

#### <a name="required-relationships"></a><span data-ttu-id="0c179-237">必要關係</span><span class="sxs-lookup"><span data-stu-id="0c179-237">Required relationships</span></span>

<span data-ttu-id="0c179-238"> (不允許將 FK 值設定為 null，而且通常不可能) 需要的關聯性。</span><span class="sxs-lookup"><span data-stu-id="0c179-238">Setting the FK value to null is not allowed (and is usually not possible) for required relationships.</span></span> <span data-ttu-id="0c179-239">因此，切斷所需的關聯性，表示相依/子系實體必須重設為新主體/父系的父代，或在呼叫 SaveChanges 以避免參考條件約束違規時，從資料庫中移除。</span><span class="sxs-lookup"><span data-stu-id="0c179-239">Therefore, severing a required relationship means that the dependent/child entity must be either re-parented to a new principal/parent, or removed from the database when SaveChanges is called to avoid a referential constraint violation.</span></span> <span data-ttu-id="0c179-240">這稱為「刪除孤立」，在 EF Core 中，是必要關聯性的預設行為。</span><span class="sxs-lookup"><span data-stu-id="0c179-240">This is known as "deleting orphans", and is the default behavior in EF Core for required relationships.</span></span>

<span data-ttu-id="0c179-241">例如，讓我們變更需要的 blog 和 post 之間的關聯性，然後執行與上述範例相同的程式碼：</span><span class="sxs-lookup"><span data-stu-id="0c179-241">For example, let's change the relationship between blog and posts to be required and then run the same code as in the previous example:</span></span>

<!--
        var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
        dotNetBlog.Posts.Remove(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_4](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_4)]

<span data-ttu-id="0c179-242">在此變更之後查看 [偵錯工具] 視圖，顯示：</span><span class="sxs-lookup"><span data-stu-id="0c179-242">Looking at the debug view after this change shows that:</span></span>

- <span data-ttu-id="0c179-243">貼文已標示為 `Deleted` 在呼叫 SaveChanges 時，從資料庫中將它刪除。</span><span class="sxs-lookup"><span data-stu-id="0c179-243">The post has been marked as `Deleted` such that it will be deleted from the database when SaveChanges is called.</span></span>
- <span data-ttu-id="0c179-244">`Post.Blog`參考導覽已設定為 null (`Blog: <null>`) 。</span><span class="sxs-lookup"><span data-stu-id="0c179-244">The `Post.Blog` reference navigation has been set to null (`Blog: <null>`).</span></span>
- <span data-ttu-id="0c179-245">已從集合導覽移除 post `Blog.Posts` (`Posts: [{Id: 1}]`) 。</span><span class="sxs-lookup"><span data-stu-id="0c179-245">The post has been removed from `Blog.Posts` collection navigation (`Posts: [{Id: 1}]`).</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
  Tags: []
```

<span data-ttu-id="0c179-246">請注意， `Post.BlogId` 因為必要的關聯性，會維持不變，不能設定為 null。</span><span class="sxs-lookup"><span data-stu-id="0c179-246">Notice that the `Post.BlogId` remains unchanged since for a required relationship it cannot be set to null.</span></span>

<span data-ttu-id="0c179-247">呼叫 SaveChanges 會導致正在刪除孤立的 post：</span><span class="sxs-lookup"><span data-stu-id="0c179-247">Calling SaveChanges results in the orphaned post being deleted:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

#### <a name="delete-orphans-timing-and-re-parenting"></a><span data-ttu-id="0c179-248">刪除孤立的計時和重新父代</span><span class="sxs-lookup"><span data-stu-id="0c179-248">Delete orphans timing and re-parenting</span></span>

<span data-ttu-id="0c179-249">依預設， `Deleted` 在偵測 [到](xref:core/change-tracking/change-detection)關聯性變更時，就會立即標記孤立。</span><span class="sxs-lookup"><span data-stu-id="0c179-249">By default, marking orphans as `Deleted` happens as soon as the relationship change is [detected](xref:core/change-tracking/change-detection).</span></span> <span data-ttu-id="0c179-250">不過，在實際呼叫 SaveChanges 之前，此進程可能會延遲。</span><span class="sxs-lookup"><span data-stu-id="0c179-250">However, this process can be delayed until SaveChanges is actually called.</span></span> <span data-ttu-id="0c179-251">這有助於避免產生已從一個主體/父系移除的實體孤立專案，但是在呼叫 SaveChanges 之前，將會以新的主體/父系重新建立父代。</span><span class="sxs-lookup"><span data-stu-id="0c179-251">This can be useful to avoid making orphans of entities that have been removed from one principal/parent, but will be re-parented with a new principal/parent before SaveChanges is called.</span></span> <span data-ttu-id="0c179-252"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> 用來設定此時間。</span><span class="sxs-lookup"><span data-stu-id="0c179-252"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> is used to set this timing.</span></span> <span data-ttu-id="0c179-253">例如：</span><span class="sxs-lookup"><span data-stu-id="0c179-253">For example:</span></span>

<!--
        context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;

        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        dotNetBlog.Posts.Add(post);

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_5](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_5)]

<span data-ttu-id="0c179-254">從第一個集合中移除 post 之後，物件未標示為 `Deleted` 先前範例中的。</span><span class="sxs-lookup"><span data-stu-id="0c179-254">After removing the post from the first collection the object is not marked as `Deleted` as it was in the previous example.</span></span> <span data-ttu-id="0c179-255">相反地，EF Core 會追蹤此關聯性， _即使這是必要的關聯_ 性也是一樣。</span><span class="sxs-lookup"><span data-stu-id="0c179-255">Instead, EF Core is tracking that the relationship is severed _even though this is a required relationship_.</span></span> <span data-ttu-id="0c179-256"> (FK 值會被 EF Core 視為 null，即使該型別不可為 null 也是一樣。</span><span class="sxs-lookup"><span data-stu-id="0c179-256">(The FK value is considered null by EF Core even though it cannot really be null because the type is not nullable.</span></span> <span data-ttu-id="0c179-257">這就是所謂的「概念 null」。 ) </span><span class="sxs-lookup"><span data-stu-id="0c179-257">This is known as a "conceptual null".)</span></span>

```output
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: []
```

<span data-ttu-id="0c179-258">此時呼叫 SaveChanges 會導致刪除孤立的貼文。</span><span class="sxs-lookup"><span data-stu-id="0c179-258">Calling SaveChanges at this time would result in the orphaned post being deleted.</span></span> <span data-ttu-id="0c179-259">不過，如上述範例所示，在呼叫 SaveChanges 之前，post 會與新的 blog 相關聯，然後它會適當地修正至新的 blog，而且不再被視為孤立：</span><span class="sxs-lookup"><span data-stu-id="0c179-259">However, if as in the example above, post is associated with a new blog before SaveChanges is called, then it will be fixed up appropriately to that new blog and is no longer considered an orphan:</span></span>

```output
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: 1 FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 1}
  Tags: []
```

<span data-ttu-id="0c179-260">此時呼叫的 SaveChanges 將會更新資料庫中的貼文，而不是刪除它。</span><span class="sxs-lookup"><span data-stu-id="0c179-260">SaveChanges called at this point will update the post in the database rather than deleting it.</span></span>

<span data-ttu-id="0c179-261">您也可以關閉自動刪除孤立的功能。</span><span class="sxs-lookup"><span data-stu-id="0c179-261">It is also possible to turn off automatic deletion of orphans.</span></span> <span data-ttu-id="0c179-262">如果在追蹤孤立的同時呼叫 SaveChanges，這將會導致例外狀況。</span><span class="sxs-lookup"><span data-stu-id="0c179-262">This will result in an exception if SaveChanges is called while an orphan is being tracked.</span></span> <span data-ttu-id="0c179-263">例如，下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="0c179-263">For example, this code:</span></span>

<!--
                var dotNetBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == ".NET Blog");

                context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.Never;

                var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
                dotNetBlog.Posts.Remove(post);

                context.SaveChanges(); // Throws
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_6](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_6)]

<span data-ttu-id="0c179-264">將擲回這個例外狀況：</span><span class="sxs-lookup"><span data-stu-id="0c179-264">Will throw this exception:</span></span>

> <span data-ttu-id="0c179-265">InvalidOperationException：實體 ' Blog ' 和 ' Post ' 之間的關聯（具有索引鍵值 ' {BlogId： 1} '）已遭到中斷，但關聯性標示為必要或隱含需要，因為外鍵不可為 null。</span><span class="sxs-lookup"><span data-stu-id="0c179-265">System.InvalidOperationException: The association between entities 'Blog' and 'Post' with the key value '{BlogId: 1}' has been severed, but the relationship is either marked as required or is implicitly required because the foreign key is not nullable.</span></span> <span data-ttu-id="0c179-266">如果相依/子系實體在必要的關聯性中斷時應該刪除，請將關聯性設定為使用串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="0c179-266">If the dependent/child entity should be deleted when a required relationship is severed, configure the relationship to use cascade deletes.</span></span>

<span data-ttu-id="0c179-267">您可以藉由呼叫來隨時強制刪除遺孤以及串聯刪除 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="0c179-267">Deletion of orphans, as well as cascade deletes, can be forced at any time by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>.</span></span> <span data-ttu-id="0c179-268">將此項與設定的 [刪除孤立時間] 結合，可 `Never` 確保永遠不會刪除孤立的，除非 EF Core 明確指示您這樣做。</span><span class="sxs-lookup"><span data-stu-id="0c179-268">Combining this with setting the delete orphan timing to `Never` will ensure orphans are never deleted unless EF Core is explicitly instructed to do so.</span></span>

### <a name="changing-a-reference-navigation"></a><span data-ttu-id="0c179-269">變更參考導覽</span><span class="sxs-lookup"><span data-stu-id="0c179-269">Changing a reference navigation</span></span>

<span data-ttu-id="0c179-270">變更一對多關聯性的參考導覽與變更關聯性另一端的集合導覽具有相同的效果。</span><span class="sxs-lookup"><span data-stu-id="0c179-270">Changing the reference navigation of a one-to-many relationship has the same effect as changing the collection navigation on the other end of the relationship.</span></span> <span data-ttu-id="0c179-271">將相依/子系的參考導覽設定為 null，相當於從主體/父系的集合導覽中移除實體。</span><span class="sxs-lookup"><span data-stu-id="0c179-271">Setting the reference navigation of dependent/child to null is equivalent to removing the entity from the collection navigation of the principal/parent.</span></span> <span data-ttu-id="0c179-272">所有的修復和資料庫變更都會依照上一節所述進行，包括在需要關聯性的情況下將實體設為孤立。</span><span class="sxs-lookup"><span data-stu-id="0c179-272">All fixup and database changes happen as described in the previous section, including making the entity an orphan if the relationship is required.</span></span>

#### <a name="optional-one-to-one-relationships"></a><span data-ttu-id="0c179-273">選擇性的一對一關聯性</span><span class="sxs-lookup"><span data-stu-id="0c179-273">Optional one-to-one relationships</span></span>

<span data-ttu-id="0c179-274">若為一對一關聯性，變更參考導覽會導致任何先前的關聯性被中斷。</span><span class="sxs-lookup"><span data-stu-id="0c179-274">For one-to-one relationships, changing a reference navigation causes any previous relationship to be severed.</span></span> <span data-ttu-id="0c179-275">對於選擇性的關聯性，這表示先前相關的相依/子系上的 FK 值設定為 null。</span><span class="sxs-lookup"><span data-stu-id="0c179-275">For optional relationships, this means that the FK value on the previously related dependent/child is set to null.</span></span> <span data-ttu-id="0c179-276">例如：</span><span class="sxs-lookup"><span data-stu-id="0c179-276">For example:</span></span>

<!--
        using var context = new BlogsContext();

        var dotNetBlog = context.Blogs.Include(e => e.Assets).Single(e => e.Name == ".NET Blog");
        dotNetBlog.Assets = new BlogAssets();

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_7](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_7)]

<span data-ttu-id="0c179-277">在呼叫 SaveChanges 之前，偵錯工具會顯示新的資產已取代現有的資產，現在已標示為 `Modified` 具有 null `BlogAssets.BlogId` FK 值：</span><span class="sxs-lookup"><span data-stu-id="0c179-277">The debug view before calling SaveChanges shows that the new assets has replaced the existing assets, which is now marked as `Modified` with a null `BlogAssets.BlogId` FK value:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: -2147482629}
  Posts: []
BlogAssets {Id: -2147482629} Added
  Id: -2147482629 PK Temporary
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 1} Modified
  Id: 1 PK
  Banner: <null>
  BlogId: <null> FK Modified Originally 1
  Blog: <null>
```

<span data-ttu-id="0c179-278">這會導致在呼叫 SaveChanges 時產生更新和插入：</span><span class="sxs-lookup"><span data-stu-id="0c179-278">This results in an update and an insert when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Assets" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p2=NULL, @p3='1' (Nullable = true) (DbType = String)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Assets" ("Banner", "BlogId")
VALUES (@p2, @p3);
SELECT "Id"
FROM "Assets"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

#### <a name="required-one-to-one-relationships"></a><span data-ttu-id="0c179-279">需要一對一關聯性</span><span class="sxs-lookup"><span data-stu-id="0c179-279">Required one-to-one relationships</span></span>

<span data-ttu-id="0c179-280">執行與上述範例相同的程式碼，但這次使用必要的一對一關聯性，顯示先前相關聯的 `BlogAssets` 已標示為 `Deleted` ，因為當新的位置為新的時，它會變成孤立的狀態 `BlogAssets` ：</span><span class="sxs-lookup"><span data-stu-id="0c179-280">Running the same code as in the previous example, but this time with a required one-to-one relationship, shows that the previously associated `BlogAssets` is now marked as `Deleted`, since it becomes an orphan when the new `BlogAssets` takes its place:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: -2147482639}
  Posts: []
BlogAssets {Id: -2147482639} Added
  Id: -2147482639 PK Temporary
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 1} Deleted
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: <null>
```

<span data-ttu-id="0c179-281">然後，這會在呼叫 SaveChanges 時產生 delete 和 insert：</span><span class="sxs-lookup"><span data-stu-id="0c179-281">This then results in an delete an and insert when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Assets"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1=NULL, @p2='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Assets" ("Banner", "BlogId")
VALUES (@p1, @p2);
SELECT "Id"
FROM "Assets"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

<span data-ttu-id="0c179-282">將孤立的標記標記為已刪除的時間，可以像針對集合導覽所顯示的相同方式變更，而且具有相同的效果。</span><span class="sxs-lookup"><span data-stu-id="0c179-282">The timing of marking orphans as deleted can be changed in the same way as shown for collection navigations and has the same effects.</span></span>

### <a name="deleting-an-entity"></a><span data-ttu-id="0c179-283">刪除實體</span><span class="sxs-lookup"><span data-stu-id="0c179-283">Deleting an entity</span></span>

#### <a name="optional-relationships"></a><span data-ttu-id="0c179-284">組織關係</span><span class="sxs-lookup"><span data-stu-id="0c179-284">Optional relationships</span></span>

<span data-ttu-id="0c179-285">當實體標示為 `Deleted` （例如藉由呼叫）時 <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> ，已刪除之實體的參考會從其他實體的導覽中移除。</span><span class="sxs-lookup"><span data-stu-id="0c179-285">When an entity is marked as `Deleted`, for example by calling <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType>, then references to the deleted entity are removed from the navigations of other entities.</span></span> <span data-ttu-id="0c179-286">若為選擇性的關聯性，則會將相依實體中的 FK 值設定為 null。</span><span class="sxs-lookup"><span data-stu-id="0c179-286">For optional relationships, the FK values in dependent entities are set to null.</span></span>

<span data-ttu-id="0c179-287">例如，讓我們將 Visual Studio 的 blog 標示為 `Deleted` ：</span><span class="sxs-lookup"><span data-stu-id="0c179-287">For example, let's mark the Visual Studio blog as `Deleted`:</span></span>

<!--
        using var context = new BlogsContext();

        var vsBlog = context.Blogs
            .Include(e => e.Posts)
            .Include(e => e.Assets)
            .Single(e => e.Name == "Visual Studio Blog");

        context.Remove(vsBlog);

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Deleting_an_entity_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Deleting_an_entity_1)]

<span data-ttu-id="0c179-288">在呼叫 SaveChanges 之前，查看 [變更追蹤](xref:core/change-tracking/debug-views) 器的偵錯工具顯示：</span><span class="sxs-lookup"><span data-stu-id="0c179-288">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) before calling SaveChanges shows:</span></span>

```output
Blog {Id: 2} Deleted
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 2} Modified
  Id: 2 PK
  Banner: <null>
  BlogId: <null> FK Modified Originally 2
  Blog: <null>
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: []
Post {Id: 4} Modified
  Id: 4 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: <null>
  Tags: []
```

<span data-ttu-id="0c179-289">請注意：</span><span class="sxs-lookup"><span data-stu-id="0c179-289">Notice that:</span></span>

- <span data-ttu-id="0c179-290">此 blog 會標示為 `Deleted` 。</span><span class="sxs-lookup"><span data-stu-id="0c179-290">The blog is marked as `Deleted`.</span></span>
- <span data-ttu-id="0c179-291">與已刪除的 blog 相關的資產具有 null FK 值 (`BlogId: <null> FK Modified Originally 2`) 和 null 參考流覽 (`Blog: <null>`) </span><span class="sxs-lookup"><span data-stu-id="0c179-291">The assets related to the deleted blog has a null FK value (`BlogId: <null> FK Modified Originally 2`) and a null reference navigation (`Blog: <null>`)</span></span>
- <span data-ttu-id="0c179-292">與已刪除的 blog 相關的每個貼文都有 null FK 值 (`BlogId: <null> FK Modified Originally 2`) 和 null 參考流覽 (`Blog: <null>`) </span><span class="sxs-lookup"><span data-stu-id="0c179-292">Each post related to the deleted blog has a null FK value (`BlogId: <null> FK Modified Originally 2`) and a null reference navigation (`Blog: <null>`)</span></span>

#### <a name="required-relationships"></a><span data-ttu-id="0c179-293">必要關係</span><span class="sxs-lookup"><span data-stu-id="0c179-293">Required relationships</span></span>

<span data-ttu-id="0c179-294">必要關聯性的修復行為與選用的關聯性相同，不同之處在于相依/子系實體會標示為， `Deleted` 因為它們不能在沒有主體/父系的情況下存在，而且必須在呼叫 SaveChanges 時從資料庫中移除，以避免參考條件約束例外狀況。</span><span class="sxs-lookup"><span data-stu-id="0c179-294">The fixup behavior for required relationships is the same as for optional relationships except that the dependent/child entities are marked as `Deleted` since they cannot exist without a principal/parent and must be removed from the database when SaveChanges is called to avoid a referential constraint exception.</span></span> <span data-ttu-id="0c179-295">這稱為「串聯刪除」，在 EF Core 中，是必要關聯性的預設行為。</span><span class="sxs-lookup"><span data-stu-id="0c179-295">This is known as "cascade delete", and is the default behavior in EF Core for required relationships.</span></span> <span data-ttu-id="0c179-296">例如，執行與上一個範例相同的程式碼，但使用必要的關聯性會導致在呼叫 SaveChanges 之前的下列偵錯工具視圖：</span><span class="sxs-lookup"><span data-stu-id="0c179-296">For example, running the same code as in the previous example but with a required relationship results in the following debug view before SaveChanges is called:</span></span>

```output
Blog {Id: 2} Deleted
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 2} Deleted
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
Post {Id: 3} Deleted
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
Post {Id: 4} Deleted
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

<span data-ttu-id="0c179-297">如預期般，相依項/子系現在標示為 `Deleted` 。</span><span class="sxs-lookup"><span data-stu-id="0c179-297">As expected, the dependents/children are now marked as `Deleted`.</span></span> <span data-ttu-id="0c179-298">不過請注意，已刪除實體上的導覽尚未 _變更。_</span><span class="sxs-lookup"><span data-stu-id="0c179-298">However, notice that the navigations on the deleted entities have _not_ changed.</span></span> <span data-ttu-id="0c179-299">這看起來似乎很奇怪，但它會藉由清除所有導覽來避免將已刪除的實體圖表完全解除切。</span><span class="sxs-lookup"><span data-stu-id="0c179-299">This may seem strange, but it avoids completely shredding a deleted graph of entities by clearing all navigations.</span></span> <span data-ttu-id="0c179-300">也就是，即使在刪除後，blog、資產和貼文仍會形成實體的圖形。</span><span class="sxs-lookup"><span data-stu-id="0c179-300">That is, the blog, asset, and posts still form a graph of entities even after having been deleted.</span></span> <span data-ttu-id="0c179-301">這可讓您更輕鬆地刪除實體的圖形，而不是在 EF6 中切割圖形的情況。</span><span class="sxs-lookup"><span data-stu-id="0c179-301">This makes it much easier to un-delete a graph of entities than was the case in EF6 where the graph was shredded.</span></span>

#### <a name="cascade-delete-timing-and-re-parenting"></a><span data-ttu-id="0c179-302">串聯刪除計時和重新父代</span><span class="sxs-lookup"><span data-stu-id="0c179-302">Cascade delete timing and re-parenting</span></span>

<span data-ttu-id="0c179-303">依預設，當父/主體標示為時，就會立即進行串聯刪除 `Deleted` 。</span><span class="sxs-lookup"><span data-stu-id="0c179-303">By default, cascade delete happens as soon as the parent/principal is marked as `Deleted`.</span></span> <span data-ttu-id="0c179-304">這與刪除孤立的相同，如先前所述。</span><span class="sxs-lookup"><span data-stu-id="0c179-304">This is the same as for deleting orphans, as described previously.</span></span> <span data-ttu-id="0c179-305">就像刪除孤立的一樣，在呼叫 SaveChanges 之前（甚至完全停用），您可以藉由適當設定來延遲此進程 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="0c179-305">As with deleting orphans, this process can be delayed until SaveChanges is called, or even disabled entirely, by setting <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> appropriately.</span></span> <span data-ttu-id="0c179-306">這與刪除孤立的方式相同，包括刪除主體/父系之後，重新父代/相依項。</span><span class="sxs-lookup"><span data-stu-id="0c179-306">This is useful in the same way as it is for deleting orphans, including for re-parenting children/dependents after deletion of a principal/parent.</span></span>

<span data-ttu-id="0c179-307">您可以藉由呼叫來強制執行串聯刪除，以及刪除孤立的孤立部分 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="0c179-307">Cascade deletes, as well as deleting orphans, can be forced at any time by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>.</span></span> <span data-ttu-id="0c179-308">將此項與設定串聯刪除時間結合，可確保永遠不會發生串聯刪除， `Never` 除非明確指示 EF Core 執行此動作。</span><span class="sxs-lookup"><span data-stu-id="0c179-308">Combining this with setting the cascade delete timing to `Never` will ensure cascade deletes never happen unless EF Core is explicitly instructed to do so.</span></span>

> [!TIP]
> <span data-ttu-id="0c179-309">串聯刪除和刪除孤立的緊密相關。</span><span class="sxs-lookup"><span data-stu-id="0c179-309">Cascade delete and deleting orphans are closely related.</span></span> <span data-ttu-id="0c179-310">這兩種方式都會在中斷與所需主體/父系的關聯性時，刪除相依/子系實體。</span><span class="sxs-lookup"><span data-stu-id="0c179-310">Both result in deleting dependent/child entities when the relationship to their required principal/parent is severed.</span></span> <span data-ttu-id="0c179-311">若是串聯刪除，則會發生此切斷，是因為主體/父系本身已被刪除。</span><span class="sxs-lookup"><span data-stu-id="0c179-311">For cascade delete, this severing happens because the principal/parent is itself deleted.</span></span> <span data-ttu-id="0c179-312">若為孤立，主體/父實體仍存在，但不再與相依/子系實體相關。</span><span class="sxs-lookup"><span data-stu-id="0c179-312">For orphans, the principal/parent entity still exists, but is no longer related to the dependent/child entities.</span></span>

## <a name="many-to-many-relationships"></a><span data-ttu-id="0c179-313">多對多關聯性</span><span class="sxs-lookup"><span data-stu-id="0c179-313">Many-to-many relationships</span></span>

<span data-ttu-id="0c179-314">EF Core 中的多對多關聯性是使用聯結實體來執行。</span><span class="sxs-lookup"><span data-stu-id="0c179-314">Many-to-many relationships in EF Core are implemented using a join entity.</span></span> <span data-ttu-id="0c179-315">多對多關聯性的每一端都與具有一對多關聯性的這個聯結實體相關。</span><span class="sxs-lookup"><span data-stu-id="0c179-315">Each side the many-to-many relationship is related to this join entity with a one-to-many relationship.</span></span> <span data-ttu-id="0c179-316">在 EF Core 5.0 之前，此聯結實體必須明確定義和對應。</span><span class="sxs-lookup"><span data-stu-id="0c179-316">Before EF Core 5.0, this join entity had to explicitly defined and mapped.</span></span> <span data-ttu-id="0c179-317">從 EF Core 5.0 開始，可以隱含地建立並隱藏。</span><span class="sxs-lookup"><span data-stu-id="0c179-317">Starting with EF Core 5.0, it can be created implicitly and hidden.</span></span> <span data-ttu-id="0c179-318">不過，在這兩種情況下，基礎行為都相同。</span><span class="sxs-lookup"><span data-stu-id="0c179-318">However, in both cases the underlying behavior is the same.</span></span> <span data-ttu-id="0c179-319">我們將先探討這個基礎行為，以瞭解多對多關聯性的追蹤運作方式。</span><span class="sxs-lookup"><span data-stu-id="0c179-319">We will look at this underlying behavior first to understand how tracking of many-to-many relationships works.</span></span>

### <a name="how-many-to-many-relationships-work"></a><span data-ttu-id="0c179-320">多對多關聯性的運作方式</span><span class="sxs-lookup"><span data-stu-id="0c179-320">How many-to-many relationships work</span></span>

<span data-ttu-id="0c179-321">請考慮使用明確定義的聯結實體類型，在貼文和標記之間建立多對多關聯性的 EF Core 模型：</span><span class="sxs-lookup"><span data-stu-id="0c179-321">Consider this EF Core model that creates a many-to-many relationship between posts and tags using an explicitly defined join entity type:</span></span>

<!--
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }

        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class Tag
    {
        public int Id { get; set; }
        public string Text { get; set; }

        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class PostTag
    {
        public int PostId { get; set; } // First part of composite PK; FK to Post
        public int TagId { get; set; } // Second part of composite PK; FK to Tag

        public Post Post { get; set; } // Reference navigation
        public Tag Tag { get; set; } // Reference navigation
    }
    -->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Model)]

<span data-ttu-id="0c179-322">請注意， `PostTag` 聯結實體類型包含兩個外鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="0c179-322">Notice that the `PostTag` join entity type contains two foreign key properties.</span></span> <span data-ttu-id="0c179-323">在此模型中，針對要與標記相關的貼文，必須有一個 PostTag 聯結實體，其中 `PostTag.PostId` 外鍵值符合 `Post.Id` 主鍵值，以及 `PostTag.TagId` 外鍵值與 `Tag.Id` 主鍵值相符的值。</span><span class="sxs-lookup"><span data-stu-id="0c179-323">In this model, for a post to be related to a tag, there must be a PostTag join entity where the `PostTag.PostId` foreign key value matches the `Post.Id` primary key value, and where the `PostTag.TagId` foreign key value matches the `Tag.Id` primary key value.</span></span> <span data-ttu-id="0c179-324">例如：</span><span class="sxs-lookup"><span data-stu-id="0c179-324">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            context.Add(new PostTag { PostId = post.Id, TagId = tag.Id });

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Many_to_many_relationships_1)]

<span data-ttu-id="0c179-325">在執行此程式碼之後查看 [變更追蹤器調試](xref:core/change-tracking/debug-views) 程式，會顯示 post 和標記是由新的 `PostTag` 聯結實體所關聯：</span><span class="sxs-lookup"><span data-stu-id="0c179-325">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) after running this code shows that the post and tag are related by the new `PostTag` join entity:</span></span>

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  PostTags: [{PostId: 3, TagId: 1}]
PostTag {PostId: 3, TagId: 1} Added
  PostId: 3 PK FK
  TagId: 1 PK FK
  Post: {Id: 3}
  Tag: {Id: 1}
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  PostTags: [{PostId: 3, TagId: 1}]
```

<span data-ttu-id="0c179-326">請注意，和上的集合導覽已 `Post` `Tag` 修正，如同上的參考導覽 `PostTag` 。</span><span class="sxs-lookup"><span data-stu-id="0c179-326">Notice that the collection navigations on `Post` and `Tag` have been fixed up, as have the reference navigations on `PostTag`.</span></span> <span data-ttu-id="0c179-327">這些關聯性可以由導覽操作，而不是 FK 值，就如同上述範例中所述。</span><span class="sxs-lookup"><span data-stu-id="0c179-327">These relationships can be manipulated by navigations instead of FK values, just as in all the preceding examples.</span></span> <span data-ttu-id="0c179-328">例如，您可以藉由在聯結實體上設定參考導覽來修改上面的程式碼，以加入關聯性：</span><span class="sxs-lookup"><span data-stu-id="0c179-328">For example, the code above can be modified to add the relationship by setting the reference navigations on the join entity:</span></span>

<!--
            context.Add(new PostTag { Post = post, Tag = tag });
-->
[!code-csharp[Many_to_many_relationships_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Many_to_many_relationships_2)]

<span data-ttu-id="0c179-329">這會產生與上述範例中的 Fk 和導覽完全相同的變更。</span><span class="sxs-lookup"><span data-stu-id="0c179-329">This results in exactly the same change to FKs and navigations as in the previous example.</span></span>

### <a name="skip-navigations"></a><span data-ttu-id="0c179-330">略過導覽</span><span class="sxs-lookup"><span data-stu-id="0c179-330">Skip navigations</span></span>

> [!NOTE]
> <span data-ttu-id="0c179-331">略過導覽是在 EF Core 5.0 中引進。</span><span class="sxs-lookup"><span data-stu-id="0c179-331">Skip navigations were introduced in EF Core 5.0.</span></span>

<span data-ttu-id="0c179-332">手動操作聯結資料表可能很麻煩。</span><span class="sxs-lookup"><span data-stu-id="0c179-332">Manipulating the join table manually can be cumbersome.</span></span> <span data-ttu-id="0c179-333">從 EF Core 5.0 開始，可以使用「略過」聯結實體的特殊集合導覽，直接操作多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="0c179-333">Starting with EF Core 5.0, many-to-many relationships can be manipulated directly using special collection navigations that "skip over" the join entity.</span></span> <span data-ttu-id="0c179-334">例如，您可以將兩個 skip 導覽新增至上述模型;其中一個是從 Post 至標記，另一個則是從標記到貼文：</span><span class="sxs-lookup"><span data-stu-id="0c179-334">For example, two skip navigations can be added to the model above; one from Post to Tags, and the other from Tag to Posts:</span></span>

<!--
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }

        public IList<Tag> Tags { get; } = new List<Tag>(); // Skip collection navigation
        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class Tag
    {
        public int Id { get; set; }
        public string Text { get; set; }

        public IList<Post> Posts { get; } = new List<Post>(); // Skip collection navigation
        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class PostTag
    {
        public int PostId { get; set; } // First part of composite PK; FK to Post
        public int TagId { get; set; } // Second part of composite PK; FK to Tag

        public Post Post { get; set; } // Reference navigation
        public Tag Tag { get; set; } // Reference navigation
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Model)]

<span data-ttu-id="0c179-335">此多對多關聯性需要下列設定，以確保略過導覽和一般導覽全都適用于相同的多對多關聯性：</span><span class="sxs-lookup"><span data-stu-id="0c179-335">This many-to-many relationship requires the following configuration to ensure the skip navigations and normal navigations are all used for the same many-to-many relationship:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<PostTag>(
                    j => j.HasOne(t => t.Tag).WithMany(p => p.PostTags),
                    j => j.HasOne(t => t.Post).WithMany(p => p.PostTags));
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=OnModelCreating)]

<span data-ttu-id="0c179-336">如需對應多對多關聯性的詳細資訊，請參閱 [關聯](xref:core/modeling/relationships) 性。</span><span class="sxs-lookup"><span data-stu-id="0c179-336">See [Relationships](xref:core/modeling/relationships) for more information on mapping many-to-many relationships.</span></span>

<span data-ttu-id="0c179-337">略過導覽的外觀和行為，例如一般集合導覽。</span><span class="sxs-lookup"><span data-stu-id="0c179-337">Skip navigations look and behave like normal collection navigations.</span></span> <span data-ttu-id="0c179-338">不過，它們使用外鍵值的方式不同。</span><span class="sxs-lookup"><span data-stu-id="0c179-338">However, the way they work with foreign key values is different.</span></span> <span data-ttu-id="0c179-339">讓我們將貼文與標記建立關聯，但這次使用略過導覽：</span><span class="sxs-lookup"><span data-stu-id="0c179-339">Let's associate a post with a tag, but this time using a skip navigation:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_3](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_3)]

<span data-ttu-id="0c179-340">請注意，此程式碼不會使用聯結實體。</span><span class="sxs-lookup"><span data-stu-id="0c179-340">Notice that this code doesn't use the join entity.</span></span> <span data-ttu-id="0c179-341">它會改為將實體新增至導覽集合，其方式就像是一對多關聯性一樣。</span><span class="sxs-lookup"><span data-stu-id="0c179-341">It instead just adds an entity to a navigation collection in the same way as would be done if this were a one-to-many relationship.</span></span> <span data-ttu-id="0c179-342">產生的偵錯工具基本上與之前相同：</span><span class="sxs-lookup"><span data-stu-id="0c179-342">The resulting debug view is essentially the same as before:</span></span>

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  PostTags: [{PostId: 3, TagId: 1}]
  Tags: [{Id: 1}]
PostTag {PostId: 3, TagId: 1} Added
  PostId: 3 PK FK
  TagId: 1 PK FK
  Post: {Id: 3}
  Tag: {Id: 1}
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  PostTags: [{PostId: 3, TagId: 1}]
  Posts: [{Id: 3}]
```

<span data-ttu-id="0c179-343">請注意，當 `PostTag` FK 值設定為現在相關聯之標記和貼文的 PK 值時，會自動建立聯結實體的實例。</span><span class="sxs-lookup"><span data-stu-id="0c179-343">Notice that an instance of the `PostTag` join entity was created automatically with FK values set to the PK values of the tag and post that are now associated.</span></span> <span data-ttu-id="0c179-344">所有一般參考和集合導覽都已修正，以符合這些 FK 值。</span><span class="sxs-lookup"><span data-stu-id="0c179-344">All the normal reference and collection navigations have been fixed up to match these FK values.</span></span> <span data-ttu-id="0c179-345">此外，因為此模型包含略過導覽，所以也已修正這些問題。</span><span class="sxs-lookup"><span data-stu-id="0c179-345">Also, since this model contains skip navigations, these have also been fixed up.</span></span> <span data-ttu-id="0c179-346">尤其是，即使我們將標籤新增至 [ `Post.Tags` 略過] 導覽， `Tag.Posts` 也已修正此關聯性另一端的反向略過導覽，以包含相關聯的貼文。</span><span class="sxs-lookup"><span data-stu-id="0c179-346">Specifically, even though we added the tag to the `Post.Tags` skip navigation, the `Tag.Posts` inverse skip navigation on the other side of this relationship has also been fixed up to contain the associated post.</span></span>

<span data-ttu-id="0c179-347">值得注意的是，即使在上方略過導覽，也可以直接操作基礎多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="0c179-347">It is worth noting that the underlying many-to-many relationships can still be manipulated directly even when skip navigations have been layered on top.</span></span> <span data-ttu-id="0c179-348">例如，標記和貼文可能會與「略過導覽」簡介之前的相關聯：</span><span class="sxs-lookup"><span data-stu-id="0c179-348">For example, the tag and Post could be associated as we did before introducing skip navigations:</span></span>

<!--
            context.Add(new PostTag { Post = post, Tag = tag });
-->
[!code-csharp[Many_to_many_relationships_4](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_4)]

<span data-ttu-id="0c179-349">或使用 FK 值：</span><span class="sxs-lookup"><span data-stu-id="0c179-349">Or using FK values:</span></span>

<!--
            context.Add(new PostTag { PostId = post.Id, TagId = tag.Id });
-->
[!code-csharp[Many_to_many_relationships_5](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_5)]

<span data-ttu-id="0c179-350">這仍會導致正確地修正 skip 導覽，並產生與上一個範例相同的調試視圖輸出。</span><span class="sxs-lookup"><span data-stu-id="0c179-350">This will still result in the skip navigations being fixed up correctly, resulting in the same debug view output as in the previous example.</span></span>

### <a name="skip-navigations-only"></a><span data-ttu-id="0c179-351">僅略過導覽</span><span class="sxs-lookup"><span data-stu-id="0c179-351">Skip navigations only</span></span>

<span data-ttu-id="0c179-352">在上一節中，我們新增了 skip 導覽 _，除了_ 完整定義兩個基礎的一對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="0c179-352">In the previous section we added skip navigations _in addition to_ fully defining the two underlying one-to-many relationships.</span></span> <span data-ttu-id="0c179-353">這有助於說明 FK 值的情況，但通常不需要。</span><span class="sxs-lookup"><span data-stu-id="0c179-353">This is useful to illustrate what happens to FK values, but is often unnecessary.</span></span> <span data-ttu-id="0c179-354">相反地，您可以使用 [ _略過_ 導覽] 來定義多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="0c179-354">Instead, the many-to-many relationship can be defined using _only skip navigations_.</span></span> <span data-ttu-id="0c179-355">這是在此檔最上方的模型中定義多對多關聯性的方式。</span><span class="sxs-lookup"><span data-stu-id="0c179-355">This is how the many-to-many relationship is defined in the model at the very top of this document.</span></span> <span data-ttu-id="0c179-356">您可以使用此模型，將 post 新增至 `Tag.Posts` skip 導覽 (，或將標記新增至 [ `Post.Tags` 略過] 導覽) ，藉此建立貼文和標記的關聯：</span><span class="sxs-lookup"><span data-stu-id="0c179-356">Using this model, we can again associate a Post and a Tag by adding a post to the `Tag.Posts` skip navigation (or, alternately, adding a tag to the `Post.Tags` skip navigation):</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_6](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Many_to_many_relationships_6)]

<span data-ttu-id="0c179-357">進行這項變更之後，查看 [偵錯工具] 視圖會顯示 EF Core 已建立的實例 `Dictionary<string, object>` 來表示聯結實體。</span><span class="sxs-lookup"><span data-stu-id="0c179-357">Looking at the debug view after making this change reveals that EF Core has created an instance of `Dictionary<string, object>` to represent the join entity.</span></span> <span data-ttu-id="0c179-358">此聯結實體同時包含 `PostsId` 和 `TagsId` 外鍵屬性，這些屬性已設定為符合相關聯之 post 和 TAG 的 PK 值。</span><span class="sxs-lookup"><span data-stu-id="0c179-358">This join entity contains both `PostsId` and `TagsId` foreign key properties which have been set to match the PK values of the post and tag that are associated.</span></span>

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: [{Id: 1}]
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  Posts: [{Id: 3}]
PostTag (Dictionary<string, object>) {PostsId: 3, TagsId: 1} Added
  PostsId: 3 PK FK
  TagsId: 1 PK FK
```

<span data-ttu-id="0c179-359">如需隱含聯結實體和實體類型使用的詳細資訊，請參閱 [關聯](xref:core/modeling/relationships) 性 `Dictionary<string, object>` 。</span><span class="sxs-lookup"><span data-stu-id="0c179-359">See [Relationships](xref:core/modeling/relationships) for more information about implicit join entities and the use of `Dictionary<string, object>` entity types.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0c179-360">依慣例用於聯結實體類型的 CLR 型別，在未來的版本中可能會變更，以改善效能。</span><span class="sxs-lookup"><span data-stu-id="0c179-360">The CLR type used for join entity types by convention may change in future releases to improve performance.</span></span> <span data-ttu-id="0c179-361">除非已明確設定，否則請勿依賴聯結類型 `Dictionary<string, object>` 。</span><span class="sxs-lookup"><span data-stu-id="0c179-361">Do not depend on the join type being `Dictionary<string, object>` unless this has been explicitly configured.</span></span>

### <a name="join-entities-with-payloads"></a><span data-ttu-id="0c179-362">使用承載聯結實體</span><span class="sxs-lookup"><span data-stu-id="0c179-362">Join entities with payloads</span></span>

<span data-ttu-id="0c179-363">到目前為止，所有範例都使用聯結實體型別 (明確或隱含) 只包含多對多關聯性所需的兩個外鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="0c179-363">So far all the examples have used a join entity type (whether explicit or implicit) that contains only the two foreign key properties needed for the many-to-many relationship.</span></span> <span data-ttu-id="0c179-364">在操作關聯性時，這些 FK 值都不需要由應用程式明確設定，因為它們的值來自相關實體的主要索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="0c179-364">Neither of these FK values need to be explicitly set by the application when manipulating relationships because their values come from the primary key properties of the related entities.</span></span> <span data-ttu-id="0c179-365">這可讓 EF Core 在不遺失資料的情況下建立聯結實體的實例。</span><span class="sxs-lookup"><span data-stu-id="0c179-365">This allows EF Core to create instances of the join entity without missing data.</span></span>

#### <a name="payloads-with-generated-values"></a><span data-ttu-id="0c179-366">具有產生值的裝載</span><span class="sxs-lookup"><span data-stu-id="0c179-366">Payloads with generated values</span></span>

<span data-ttu-id="0c179-367">EF Core 支援將其他屬性加入至聯結實體類型。</span><span class="sxs-lookup"><span data-stu-id="0c179-367">EF Core supports adding additional properties to the join entity type.</span></span> <span data-ttu-id="0c179-368">這就是所謂的「裝載」提供「聯結」實體。</span><span class="sxs-lookup"><span data-stu-id="0c179-368">This is known as giving the join entity a "payload".</span></span> <span data-ttu-id="0c179-369">例如，讓我們將 `TaggedOn` 屬性新增至 `PostTag` 聯結實體：</span><span class="sxs-lookup"><span data-stu-id="0c179-369">For example, let's add `TaggedOn` property to the `PostTag` join entity:</span></span>

<!--
    public class PostTag
    {
        public int PostId { get; set; } // Foreign key to Post
        public int TagId { get; set; } // Foreign key to Tag

        public DateTime TaggedOn { get; set; } // Payload
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=Model)]

<span data-ttu-id="0c179-370">當 EF Core 建立聯結實體實例時，不會設定此承載屬性。</span><span class="sxs-lookup"><span data-stu-id="0c179-370">This payload property will not be set when EF Core creates a join entity instance.</span></span> <span data-ttu-id="0c179-371">最常見的處理方式是使用裝載屬性搭配自動產生的值。</span><span class="sxs-lookup"><span data-stu-id="0c179-371">The most common way to deal with this is to use payload properties with automatically generated values.</span></span> <span data-ttu-id="0c179-372">例如，在 `TaggedOn` 插入每個新實體時，可以將屬性設定為使用存放區產生的時間戳記：</span><span class="sxs-lookup"><span data-stu-id="0c179-372">For example, the `TaggedOn` property can be configured to use a store-generated timestamp when each new entity is inserted:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<PostTag>(
                    j => j.HasOne<Tag>().WithMany(),
                    j => j.HasOne<Post>().WithMany(),
                    j => j.Property(e => e.TaggedOn).HasDefaultValueSql("CURRENT_TIMESTAMP"));
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=OnModelCreating)]

<span data-ttu-id="0c179-373">您現在可以使用與之前相同的方式來標記貼文：</span><span class="sxs-lookup"><span data-stu-id="0c179-373">A post can now be tagged in the same way as before:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_7](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=Many_to_many_relationships_7)]

<span data-ttu-id="0c179-374">在呼叫 SaveChanges 之後查看 [變更追蹤器調試](xref:core/change-tracking/debug-views) 程式，會顯示已適當設定承載屬性：</span><span class="sxs-lookup"><span data-stu-id="0c179-374">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) after calling SaveChanges shows that the payload property has been set appropriately:</span></span>

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: [{Id: 1}]
PostTag {PostId: 3, TagId: 1} Unchanged
  PostId: 3 PK FK
  TagId: 1 PK FK
  TaggedOn: '12/29/2020 8:13:21 PM'
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  Posts: [{Id: 3}]
```

#### <a name="explicitly-setting-payload-values"></a><span data-ttu-id="0c179-375">明確設定承載值</span><span class="sxs-lookup"><span data-stu-id="0c179-375">Explicitly setting payload values</span></span>

<span data-ttu-id="0c179-376">在上一個範例中，讓我們新增未使用自動產生之值的承載屬性：</span><span class="sxs-lookup"><span data-stu-id="0c179-376">Following on from the previous example, let's add a payload property that does not use an automatically generated value:</span></span>

<!--
    public class PostTag
    {
        public int PostId { get; set; } // First part of composite PK; FK to Post
        public int TagId { get; set; } // Second part of composite PK; FK to Tag

        public DateTime TaggedOn { get; set; } // Auto-generated payload property
        public string TaggedBy { get; set; } // Not-generated payload property
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=Model)]

<span data-ttu-id="0c179-377">現在可以使用與之前相同的方式來標記貼文，而聯結實體仍會自動建立。</span><span class="sxs-lookup"><span data-stu-id="0c179-377">A post can now be tagged in the same way as before, and the join entity will still be created automatically.</span></span> <span data-ttu-id="0c179-378">然後可以使用 [存取追蹤實體](xref:core/change-tracking/entity-entries)中所述的其中一種機制來存取此實體。</span><span class="sxs-lookup"><span data-stu-id="0c179-378">This entity can then be accessed using one of the mechanisms described in [Accessing Tracked Entities](xref:core/change-tracking/entity-entries).</span></span> <span data-ttu-id="0c179-379">例如，下列程式碼會使用 <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> 來存取聯結實體實例：</span><span class="sxs-lookup"><span data-stu-id="0c179-379">For example, the code below uses <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> to access the join entity instance:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();

            var joinEntity = context.Set<PostTag>().Find(post.Id, tag.Id);

            joinEntity.TaggedBy = "ajcvickers";

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_8](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=Many_to_many_relationships_8)]

<span data-ttu-id="0c179-380">一旦找到聯結實體之後，就可以用一般方式操作它，在此範例中，若要在 `TaggedBy` 呼叫 SaveChanges 之前設定承載屬性。</span><span class="sxs-lookup"><span data-stu-id="0c179-380">Once the join entity has been located it can be manipulated in the normal way--in this example, to set the `TaggedBy` payload property before calling SaveChanges.</span></span>

> [!NOTE]
> <span data-ttu-id="0c179-381">請注意， <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> 此處需要的呼叫，讓 EF Core 有機會偵測導覽屬性變更，並在使用之前建立聯結實體實例 `Find` 。</span><span class="sxs-lookup"><span data-stu-id="0c179-381">Note that a call to <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> is required here to give EF Core a chance to detect the navigation property change and create the join entity instance before `Find` is used.</span></span> <span data-ttu-id="0c179-382">如需詳細資訊，請參閱 [變更偵測和通知](xref:core/change-tracking/change-detection) 。</span><span class="sxs-lookup"><span data-stu-id="0c179-382">See [Change Detection and Notifications](xref:core/change-tracking/change-detection) for more information.</span></span>

<span data-ttu-id="0c179-383">或者，您可以明確地建立聯結實體，以將貼文與標記建立關聯。</span><span class="sxs-lookup"><span data-stu-id="0c179-383">Alternately, the join entity can be created explicitly to associate a post with a tag.</span></span> <span data-ttu-id="0c179-384">例如：</span><span class="sxs-lookup"><span data-stu-id="0c179-384">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            context.Add(new PostTag()
            {
                PostId = post.Id,
                TagId = tag.Id,
                TaggedBy = "ajcvickers"
            });

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_9](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=Many_to_many_relationships_9)]

<span data-ttu-id="0c179-385">最後，設定承載資料的另一種方式是 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> 在更新資料庫之前覆寫或使用事件來處理實體。</span><span class="sxs-lookup"><span data-stu-id="0c179-385">Finally, another way to set payload data is by either overriding <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or using the <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> event to process entities before updating the database.</span></span> <span data-ttu-id="0c179-386">例如：</span><span class="sxs-lookup"><span data-stu-id="0c179-386">For example:</span></span>

<!--
        public override int SaveChanges()
        {
            foreach (var entityEntry in ChangeTracker.Entries<PostTag>())
            {
                if (entityEntry.State == EntityState.Added)
                {
                    entityEntry.Entity.TaggedBy = "ajcvickers";
                }
            }

            return base.SaveChanges();
        }
-->
[!code-csharp[SaveChanges](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=SaveChanges)]
