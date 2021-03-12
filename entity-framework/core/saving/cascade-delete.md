---
title: 串聯刪除 - EF Core
description: 設定當實體從其主體/父系刪除或中斷時，所觸發的串聯行為
author: ajcvickers
ms.date: 01/07/2021
uid: core/saving/cascade-delete
ms.openlocfilehash: 6e897be6b4f5e6550d9ed3590445df60bef4fb3c
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023610"
---
# <a name="cascade-delete"></a><span data-ttu-id="b8cce-103">串聯刪除</span><span class="sxs-lookup"><span data-stu-id="b8cce-103">Cascade Delete</span></span>

<span data-ttu-id="b8cce-104">Entity Framework Core (EF Core) 代表使用外鍵的關聯性。</span><span class="sxs-lookup"><span data-stu-id="b8cce-104">Entity Framework Core (EF Core) represents relationships using foreign keys.</span></span> <span data-ttu-id="b8cce-105">具有外鍵的實體是關聯性中的子系或相依實體。</span><span class="sxs-lookup"><span data-stu-id="b8cce-105">An entity with a foreign key is the child or dependent entity in the relationship.</span></span> <span data-ttu-id="b8cce-106">此實體的外鍵值必須符合相關主體/父實體的主鍵值 (或替代索引鍵值) 。</span><span class="sxs-lookup"><span data-stu-id="b8cce-106">This entity's foreign key value must match the primary key value (or an alternate key value) of the related principal/parent entity.</span></span>

<span data-ttu-id="b8cce-107">如果刪除主體/父系實體，則相依項/子系的外鍵值將不再符合 _任何_ 主體/父系的主要或替代索引鍵。</span><span class="sxs-lookup"><span data-stu-id="b8cce-107">If the principal/parent entity is deleted, then the foreign key values of the dependents/children will no longer match the primary or alternate key of _any_ principal/parent.</span></span> <span data-ttu-id="b8cce-108">這是不正確狀態，而且在大多數資料庫中都會導致參考條件約束違規。</span><span class="sxs-lookup"><span data-stu-id="b8cce-108">This is an invalid state, and will cause a referential constraint violation in most databases.</span></span>

<span data-ttu-id="b8cce-109">有兩個選項可避免這種參考條件約束違規：</span><span class="sxs-lookup"><span data-stu-id="b8cce-109">There are two options to avoid this referential constraint violation:</span></span>

1. <span data-ttu-id="b8cce-110">將 FK 值設定為 null</span><span class="sxs-lookup"><span data-stu-id="b8cce-110">Set the FK values to null</span></span>
2. <span data-ttu-id="b8cce-111">也刪除相依/子系實體</span><span class="sxs-lookup"><span data-stu-id="b8cce-111">Also delete the dependent/child entities</span></span>

<span data-ttu-id="b8cce-112">第一個選項只適用于選擇性的關聯性，其中外鍵屬性 (以及其所對應的資料庫資料行) 必須是可為 null。</span><span class="sxs-lookup"><span data-stu-id="b8cce-112">The first option in only valid for optional relationships where the foreign key property (and the database column to which it is mapped) must be nullable.</span></span>

<span data-ttu-id="b8cce-113">第二個選項適用于任何種類的關聯性，也稱為「串聯刪除」。</span><span class="sxs-lookup"><span data-stu-id="b8cce-113">The second option is valid for any kind of relationship and is known as "cascade delete".</span></span>

> [!TIP]
> <span data-ttu-id="b8cce-114">本檔說明串聯刪除 (，以及從更新資料庫的觀點來刪除孤立的) 。</span><span class="sxs-lookup"><span data-stu-id="b8cce-114">This document describes cascade deletes (and deleting orphans) from the perspective of updating the database.</span></span> <span data-ttu-id="b8cce-115">它會大量使用 EF Core 中的 [變更追蹤](xref:core/change-tracking/index) 所引進的概念，以及 [變更外鍵和](xref:core/change-tracking/relationship-changes)導覽。</span><span class="sxs-lookup"><span data-stu-id="b8cce-115">It makes heavy use of concepts introduced in [Change Tracking in EF Core](xref:core/change-tracking/index) and [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes).</span></span> <span data-ttu-id="b8cce-116">在這裡處理材質之前，請務必完整瞭解這些概念。</span><span class="sxs-lookup"><span data-stu-id="b8cce-116">Make sure to fully understand these concepts before tackling the material here.</span></span>

> [!TIP]  
> <span data-ttu-id="b8cce-117">您可以 [從 GitHub 下載範例程式碼](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/CascadeDeletes)，以執行並偵測到本檔中的所有程式碼。</span><span class="sxs-lookup"><span data-stu-id="b8cce-117">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/CascadeDeletes).</span></span>

## <a name="when-cascading-behaviors-happen"></a><span data-ttu-id="b8cce-118">當級聯行為發生時</span><span class="sxs-lookup"><span data-stu-id="b8cce-118">When cascading behaviors happen</span></span>

<span data-ttu-id="b8cce-119">當相依/子系實體無法再與其目前的主體/父系相關聯時，就需要串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="b8cce-119">Cascading deletes are needed when a dependent/child entity can no longer be associated with its current principal/parent.</span></span> <span data-ttu-id="b8cce-120">這可能是因為主體/父系已刪除，或者當主體/父系仍然存在但相依/子系不再與其相關聯時，就會發生這種情況。</span><span class="sxs-lookup"><span data-stu-id="b8cce-120">This can happen because the principal/parent is deleted, or it can happen when the principal/parent still exists but the dependent/child is no longer associated with it.</span></span>

### <a name="deleting-a-principalparent"></a><span data-ttu-id="b8cce-121">刪除主體/父系</span><span class="sxs-lookup"><span data-stu-id="b8cce-121">Deleting a principal/parent</span></span>

<span data-ttu-id="b8cce-122">請考慮這個簡單的模型 `Blog` ，其中是與關聯性的主體/父系 `Post` ，也就是相依/子系。</span><span class="sxs-lookup"><span data-stu-id="b8cce-122">Consider this simple model where `Blog` is the principal/parent in a relationship with `Post`, which is the dependent/child.</span></span> <span data-ttu-id="b8cce-123">`Post.BlogId` 這是外鍵屬性，其值必須符合 `Post.Id` blog 所屬之 post 的主要索引鍵。</span><span class="sxs-lookup"><span data-stu-id="b8cce-123">`Post.BlogId` is a foreign key property, the value of which must match the `Post.Id` primary key of the post to which the blog belongs.</span></span>

<!--
    public class Blog
    {
        public int Id { get; set; }

        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
    }

    public class Post
    {
        public int Id { get; set; }

        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Model)]

<span data-ttu-id="b8cce-124">依照慣例，此關聯性會設定為必要項，因為 `Post.BlogId` 外鍵屬性不可為 null。</span><span class="sxs-lookup"><span data-stu-id="b8cce-124">By convention, this relationship is configured as a required, since the `Post.BlogId` foreign key property is non-nullable.</span></span> <span data-ttu-id="b8cce-125">必要的關聯性會設定為預設使用串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="b8cce-125">Required relationships are configured to use cascade deletes by default.</span></span> <span data-ttu-id="b8cce-126">如需模型關聯性的詳細資訊，請參閱 [關聯](xref:core/modeling/relationships) 性。</span><span class="sxs-lookup"><span data-stu-id="b8cce-126">See [Relationships](xref:core/modeling/relationships) for more information on modeling relationships.</span></span>

<span data-ttu-id="b8cce-127">刪除 blog 時，所有貼文都會進行串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="b8cce-127">When deleting a blog, all posts are cascade deleted.</span></span> <span data-ttu-id="b8cce-128">例如：</span><span class="sxs-lookup"><span data-stu-id="b8cce-128">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Deleting_principal_parent_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Deleting_principal_parent_1)]

<span data-ttu-id="b8cce-129">SaveChanges 會使用 SQL Server 做為範例，產生下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="b8cce-129">SaveChanges generates the following SQL, using SQL Server as an example:</span></span>

```sql
-- Executed DbCommand (1ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p0='2'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (2ms) [Parameters=[@p1='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="severing-a-relationship"></a><span data-ttu-id="b8cce-130">切斷關聯性</span><span class="sxs-lookup"><span data-stu-id="b8cce-130">Severing a relationship</span></span>

<span data-ttu-id="b8cce-131">我們可以改為在每個貼文與其 blog 之間的關聯性，而不是刪除 blog。</span><span class="sxs-lookup"><span data-stu-id="b8cce-131">Rather than deleting the blog, we could instead sever the relationship between each post and its blog.</span></span> <span data-ttu-id="b8cce-132">這可以藉由將 `Post.Blog` 每一篇文章的參考導覽設定為 null 來完成：</span><span class="sxs-lookup"><span data-stu-id="b8cce-132">This can be done by setting the reference navigation `Post.Blog` to null for each post:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            foreach (var post in blog.Posts)
            {
                post.Blog = null;
            }
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Severing_a_relationship_1)]

<span data-ttu-id="b8cce-133">您也可以藉由移除集合導覽中的每個貼文來切斷關聯性 `Blog.Posts` ：</span><span class="sxs-lookup"><span data-stu-id="b8cce-133">The relationship can also be severed by removing each post from the `Blog.Posts` collection navigation:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            blog.Posts.Clear();
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_2](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Severing_a_relationship_2)]

<span data-ttu-id="b8cce-134">在任一種情況下，結果都是一樣的：不會刪除 blog，但不再與任何 blog 相關聯的貼文會被刪除：</span><span class="sxs-lookup"><span data-stu-id="b8cce-134">In either case the result is the same: the blog is not deleted, but the posts that are no longer associated with any blog are deleted:</span></span>

```sql
-- Executed DbCommand (1ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p0='2'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="b8cce-135">刪除不再與任何主體/相依相關聯的實體就稱為「刪除孤立」。</span><span class="sxs-lookup"><span data-stu-id="b8cce-135">Deleting entities that are no longer associated with any principal/dependent is known as "deleting orphans".</span></span>

> [!TIP]
> <span data-ttu-id="b8cce-136">串聯刪除和刪除孤立的緊密相關。</span><span class="sxs-lookup"><span data-stu-id="b8cce-136">Cascade delete and deleting orphans are closely related.</span></span> <span data-ttu-id="b8cce-137">這兩種方式都會在中斷與所需主體/父系的關聯性時，刪除相依/子系實體。</span><span class="sxs-lookup"><span data-stu-id="b8cce-137">Both result in deleting dependent/child entities when the relationship to their required principal/parent is severed.</span></span> <span data-ttu-id="b8cce-138">若是串聯刪除，則會發生此切斷，是因為主體/父系本身已被刪除。</span><span class="sxs-lookup"><span data-stu-id="b8cce-138">For cascade delete, this severing happens because the principal/parent is itself deleted.</span></span> <span data-ttu-id="b8cce-139">若為孤立，主體/父實體仍存在，但不再與相依/子系實體相關。</span><span class="sxs-lookup"><span data-stu-id="b8cce-139">For orphans, the principal/parent entity still exists, but is no longer related to the dependent/child entities.</span></span>  

## <a name="where-cascading-behaviors-happen"></a><span data-ttu-id="b8cce-140">發生串聯行為的情況</span><span class="sxs-lookup"><span data-stu-id="b8cce-140">Where cascading behaviors happen</span></span>

<span data-ttu-id="b8cce-141">串聯行為可套用至：</span><span class="sxs-lookup"><span data-stu-id="b8cce-141">Cascading behaviors can be applied to:</span></span>

- <span data-ttu-id="b8cce-142">目前所追蹤的實體 <xref:Microsoft.EntityFrameworkCore.DbContext></span><span class="sxs-lookup"><span data-stu-id="b8cce-142">Entities tracked by the current <xref:Microsoft.EntityFrameworkCore.DbContext></span></span>
- <span data-ttu-id="b8cce-143">資料庫中尚未載入至內容的實體</span><span class="sxs-lookup"><span data-stu-id="b8cce-143">Entities in the database that have not been loaded into the context</span></span>

### <a name="cascade-delete-of-tracked-entities"></a><span data-ttu-id="b8cce-144">已追蹤實體的 Cascade 刪除</span><span class="sxs-lookup"><span data-stu-id="b8cce-144">Cascade delete of tracked entities</span></span>

<span data-ttu-id="b8cce-145">EF Core 一律會將設定的串聯行為套用至追蹤的實體。</span><span class="sxs-lookup"><span data-stu-id="b8cce-145">EF Core always applies configured cascading behaviors to tracked entities.</span></span> <span data-ttu-id="b8cce-146">這表示，如果應用程式將所有相關的相依/子系實體載入 DbCoNtext （如上述範例所示），則不論資料庫的設定方式為何，都會正確套用串聯行為。</span><span class="sxs-lookup"><span data-stu-id="b8cce-146">This means that if the application loads all relevant dependent/child entities into the DbContext, as is shown in the examples above, then cascading behaviors will be correctly applied regardless of how the database is configured.</span></span>

> [!TIP]
> <span data-ttu-id="b8cce-147">當追蹤實體發生串聯行為時的確切時間，可以使用和來 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> 控制 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="b8cce-147">The exact timing of when cascading behaviors happen to tracked entities can be controlled using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType>.</span></span> <span data-ttu-id="b8cce-148">如需詳細資訊，請參閱 [變更外鍵和](xref:core/change-tracking/relationship-changes) 導覽。</span><span class="sxs-lookup"><span data-stu-id="b8cce-148">See [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for more information.</span></span>

### <a name="cascade-delete-in-the-database"></a><span data-ttu-id="b8cce-149">資料庫中的 Cascade delete</span><span class="sxs-lookup"><span data-stu-id="b8cce-149">Cascade delete in the database</span></span>

<span data-ttu-id="b8cce-150">許多資料庫系統也會提供在資料庫中刪除實體時所觸發的串聯行為。</span><span class="sxs-lookup"><span data-stu-id="b8cce-150">Many database systems also offer cascading behaviors that are triggered when an entity is deleted in the database.</span></span> <span data-ttu-id="b8cce-151">EF core 會在使用 <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> 或 Ef core 遷移建立資料庫時，根據 EF core 模型中的串聯刪除行為來設定這些行為。</span><span class="sxs-lookup"><span data-stu-id="b8cce-151">EF Core configures these behaviors based on the cascade delete behavior in the EF Core model when a database is created using <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> or EF Core migrations.</span></span> <span data-ttu-id="b8cce-152">例如，使用上述模型時，會針對使用 SQL Server 的文章建立下表：</span><span class="sxs-lookup"><span data-stu-id="b8cce-152">For example, using the model above, the following table is created for posts when using SQL Server:</span></span>

```sql
CREATE TABLE [Posts] (
    [Id] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Content] nvarchar(max) NULL,
    [BlogId] int NOT NULL,
    CONSTRAINT [PK_Posts] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Posts_Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [Blogs] ([Id]) ON DELETE CASCADE
);
```

<span data-ttu-id="b8cce-153">請注意，定義 blog 和 post 之間關聯性的 foreign key 條件約束是使用來設定的 `ON DELETE CASCADE` 。</span><span class="sxs-lookup"><span data-stu-id="b8cce-153">Notice that the foreign key constraint defining the relationship between blogs and posts is configured with `ON DELETE CASCADE`.</span></span>

<span data-ttu-id="b8cce-154">如果知道資料庫的設定就像這樣，則可以刪除 _沒有先載入貼_ 文的 blog，資料庫將負責刪除與該 blog 相關的所有文章。</span><span class="sxs-lookup"><span data-stu-id="b8cce-154">If we know that the database is configured like this, then we can delete a blog _without first loading posts_ and the database will take care of deleting all the posts that were related to that blog.</span></span> <span data-ttu-id="b8cce-155">例如：</span><span class="sxs-lookup"><span data-stu-id="b8cce-155">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Where_cascading_behaviors_happen_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Where_cascading_behaviors_happen_1)]

<span data-ttu-id="b8cce-156">請注意，沒有 `Include` 貼文，因此不會載入。</span><span class="sxs-lookup"><span data-stu-id="b8cce-156">Notice that there is no `Include` for posts, so they are not loaded.</span></span> <span data-ttu-id="b8cce-157">在此情況下，SaveChanges 只會刪除 blog，因為這是唯一正在追蹤的實體：</span><span class="sxs-lookup"><span data-stu-id="b8cce-157">SaveChanges in this case will delete just the blog, since that's the only entity being tracked:</span></span>

```sql
-- Executed DbCommand (6ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="b8cce-158">如果資料庫中的外鍵條件約束未設定成串聯刪除，這會導致例外狀況。</span><span class="sxs-lookup"><span data-stu-id="b8cce-158">This would result in an exception if the foreign key constraint in the database is not configured for cascade deletes.</span></span> <span data-ttu-id="b8cce-159">不過，在此情況下，資料庫會刪除貼文，因為它在 `ON DELETE CASCADE` 建立時已設定。</span><span class="sxs-lookup"><span data-stu-id="b8cce-159">However, in this case the posts are deleted by the database because it has been configured with `ON DELETE CASCADE` when it was created.</span></span>

> [!NOTE]
> <span data-ttu-id="b8cce-160">資料庫通常不會有任何方法可以自動刪除孤立。</span><span class="sxs-lookup"><span data-stu-id="b8cce-160">Databases don't typically have any way to automatically delete orphans.</span></span> <span data-ttu-id="b8cce-161">這是因為當 EF Core 表示使用導覽的關聯性，以及外鍵時，資料庫只會有外鍵且沒有導覽。</span><span class="sxs-lookup"><span data-stu-id="b8cce-161">This is because while EF Core represents relationships using navigations as well of foreign keys, databases have only foreign keys and no navigations.</span></span> <span data-ttu-id="b8cce-162">這表示，通常不可能在不將雙方載入至 DbCoNtext 的情況下，伺服器關聯性。</span><span class="sxs-lookup"><span data-stu-id="b8cce-162">This means that it is usually not possible to sever a relationship without loading both sides into the DbContext.</span></span>

> [!NOTE]
> <span data-ttu-id="b8cce-163">EF Core 記憶體內部資料庫目前不支援資料庫中的串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="b8cce-163">The EF Core in-memory database does not currently support cascade deletes in the database.</span></span>

> [!WARNING]
> <span data-ttu-id="b8cce-164">當虛刪除實體時，請勿在資料庫中設定串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="b8cce-164">Do not configure cascade delete in the database when soft-deleting entities.</span></span> <span data-ttu-id="b8cce-165">這可能會導致意外刪除實體，而不是虛刪除。</span><span class="sxs-lookup"><span data-stu-id="b8cce-165">This may cause entities to be accidentally really deleted instead of soft-deleted.</span></span>

### <a name="database-cascade-limitations"></a><span data-ttu-id="b8cce-166">資料庫串聯限制</span><span class="sxs-lookup"><span data-stu-id="b8cce-166">Database cascade limitations</span></span>

<span data-ttu-id="b8cce-167">某些資料庫（最值得注意的是 SQL Server）對於形成迴圈的串聯行為有限制。</span><span class="sxs-lookup"><span data-stu-id="b8cce-167">Some databases, most notably SQL Server, have limitations on the cascade behaviors that form cycles.</span></span> <span data-ttu-id="b8cce-168">例如，請考慮下列模型：</span><span class="sxs-lookup"><span data-stu-id="b8cce-168">For example, consider the following model:</span></span>

<!--
    public class Blog
    {
        public int Id { get; set; }
        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
        
        public int OwnerId { get; set; }
        public Person Owner { get; set; }
    }

    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
        
        public int AuthorId { get; set; }
        public Person Author { get; set; }
    }

    public class Person
    {
        public int Id { get; set; }
        public string Name { get; set; }
        
        public IList<Post> Posts { get; } = new List<Post>();

        public Blog OwnedBlog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Model)]

<span data-ttu-id="b8cce-169">此模型有三個關聯性，全都是必要的，因此設定為依慣例進行串聯刪除：</span><span class="sxs-lookup"><span data-stu-id="b8cce-169">This model has three relationships, all required and therefore configured to cascade delete by convention:</span></span>

- <span data-ttu-id="b8cce-170">刪除 blog 將會串聯刪除所有相關文章</span><span class="sxs-lookup"><span data-stu-id="b8cce-170">Deleting a blog will cascade delete all the related posts</span></span>
- <span data-ttu-id="b8cce-171">刪除貼文的作者將會導致撰寫的貼文被刪除</span><span class="sxs-lookup"><span data-stu-id="b8cce-171">Deleting the author of posts will cause the authored posts to be cascade deleted</span></span>
- <span data-ttu-id="b8cce-172">刪除 blog 的擁有者會導致將 blog 刪除</span><span class="sxs-lookup"><span data-stu-id="b8cce-172">Deleting the owner of a blog will cause the blog to be cascade deleted</span></span>

<span data-ttu-id="b8cce-173">如果在 blog 管理原則中定下嚴格，這是合理的 (！ ) 但嘗試以這些已設定的已設定層級建立 SQL Server 資料庫時，會產生下列例外狀況：</span><span class="sxs-lookup"><span data-stu-id="b8cce-173">This is all reasonable (if a bit draconian in blog management policies!) but attempting to create a SQL Server database with these cascades configured results in the following exception:</span></span>

> <span data-ttu-id="b8cce-174">SqlClient. SqlException (0x80131904) ：在資料表 ' Post ' 上導入 FOREIGN KEY 條件約束 ' FK_Posts_Person_AuthorId ' 可能會造成迴圈或多個串聯路徑。</span><span class="sxs-lookup"><span data-stu-id="b8cce-174">Microsoft.Data.SqlClient.SqlException (0x80131904): Introducing FOREIGN KEY constraint 'FK_Posts_Person_AuthorId' on table 'Posts' may cause cycles or multiple cascade paths.</span></span> <span data-ttu-id="b8cce-175">請指定 ON DELETE NO ACTION 或 ON UPDATE NO ACTION，或者修改其他 FOREIGN KEY 條件約束。</span><span class="sxs-lookup"><span data-stu-id="b8cce-175">Specify ON DELETE NO ACTION or ON UPDATE NO ACTION, or modify other FOREIGN KEY constraints.</span></span>

<span data-ttu-id="b8cce-176">有兩種方式可以處理這種情況：</span><span class="sxs-lookup"><span data-stu-id="b8cce-176">There are two ways to handle this situation:</span></span>

1. <span data-ttu-id="b8cce-177">將一或多個關聯性變更為 [不串聯刪除]。</span><span class="sxs-lookup"><span data-stu-id="b8cce-177">Change one or more of the relationships to not cascade delete.</span></span>
2. <span data-ttu-id="b8cce-178">在不包含一或多個這些串聯刪除的情況下設定資料庫，然後確定已載入所有相依的實體，以便 EF Core 可以執行串聯行為。</span><span class="sxs-lookup"><span data-stu-id="b8cce-178">Configure the database without one or more of these cascade deletes, then ensure all dependent entities are loaded so that EF Core can perform the cascading behavior.</span></span>

<span data-ttu-id="b8cce-179">利用我們的範例採用第一種方法，我們可以為它提供可為 null 的外鍵屬性，讓您可以選擇建立 blog 擁有者關係：</span><span class="sxs-lookup"><span data-stu-id="b8cce-179">Taking the first approach with our example, we could make the blog-owner relationship optional by giving it a nullable foreign key property:</span></span>

<!--
            public int? BlogId { get; set; }
-->
[!code-csharp[NullableBlogId](../../../samples/core/CascadeDeletes/OptionalDependentsSamples.cs?name=NullableBlogId)]

<span data-ttu-id="b8cce-180">選擇性的關聯性可讓 blog 在沒有擁有者的情況下存在，這表示預設不會再設定串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="b8cce-180">An optional relationship allows the blog to exist without an owner, which means cascade delete will no longer be configured by default.</span></span> <span data-ttu-id="b8cce-181">這表示串聯動作不再有迴圈，而且在 SQL Server 上建立資料庫時不會發生錯誤。</span><span class="sxs-lookup"><span data-stu-id="b8cce-181">This means there is no longer a cycle in cascading actions, and the database can be created without error on SQL Server.</span></span>

<span data-ttu-id="b8cce-182">改為採用第二種方法，我們可以保持所需的 blog 擁有者關係並設定串聯刪除，但讓此設定只適用于追蹤的實體，而不是資料庫：</span><span class="sxs-lookup"><span data-stu-id="b8cce-182">Taking the second approach instead, we can keep the blog-owner relationship required and configured for cascade delete, but make this configuration only apply to tracked entities, not the database:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<Blog>()
                .HasOne(e => e.Owner)
                .WithOne(e => e.OwnedBlog)
                .OnDelete(DeleteBehavior.ClientCascade);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=OnModelCreating)]

<span data-ttu-id="b8cce-183">現在，如果我們同時載入個人和他們所擁有的 blog，就會發生什麼事，然後刪除該人？</span><span class="sxs-lookup"><span data-stu-id="b8cce-183">Now what happens if we load both a person and the blog they own, then delete the person?</span></span>

<!--
            using var context = new BlogsContext();

            var owner = context.People.Single(e => e.Name == "ajcvickers");
            var blog = context.Blogs.Single(e => e.Owner == owner);

            context.Remove(owner);
            
            context.SaveChanges();
-->
[!code-csharp[Database_cascade_limitations_1](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Database_cascade_limitations_1)]

<span data-ttu-id="b8cce-184">EF Core 會將擁有者的刪除重迭，如此一來，也會刪除該 blog：</span><span class="sxs-lookup"><span data-stu-id="b8cce-184">EF Core will cascade the delete of the owner so that the blog is also deleted:</span></span>

```sql
-- Executed DbCommand (8ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (2ms) [Parameters=[@p1='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [People]
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="b8cce-185">但是，如果在刪除擁有者時未載入 blog：</span><span class="sxs-lookup"><span data-stu-id="b8cce-185">However, if the blog is not loaded when the owner is deleted:</span></span>

<!--
                using var context = new BlogsContext();

                var owner = context.People.Single(e => e.Name == "ajcvickers");

                context.Remove(owner);
            
                context.SaveChanges();
-->
[!code-csharp[Database_cascade_limitations_2](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Database_cascade_limitations_2)]

<span data-ttu-id="b8cce-186">然後會擲回例外狀況，因為資料庫中的 foreign key 條件約束違規：</span><span class="sxs-lookup"><span data-stu-id="b8cce-186">Then an exception will be thrown due to violation of the foreign key constraint in the database:</span></span>

> <span data-ttu-id="b8cce-187">SqlClient. SqlException： DELETE 子句與參考條件約束 "FK_Blogs_People_OwnerId" 衝突。</span><span class="sxs-lookup"><span data-stu-id="b8cce-187">Microsoft.Data.SqlClient.SqlException: The DELETE statement conflicted with the REFERENCE constraint "FK_Blogs_People_OwnerId".</span></span> <span data-ttu-id="b8cce-188">衝突發生在資料庫 "臨時" 中，資料表 "dbo。Blog "，資料行 ' OwnerId '。</span><span class="sxs-lookup"><span data-stu-id="b8cce-188">The conflict occurred in database "Scratch", table "dbo.Blogs", column 'OwnerId'.</span></span>
<span data-ttu-id="b8cce-189">陳述式已經結束。</span><span class="sxs-lookup"><span data-stu-id="b8cce-189">The statement has been terminated.</span></span>

## <a name="cascading-nulls"></a><span data-ttu-id="b8cce-190">串聯 null</span><span class="sxs-lookup"><span data-stu-id="b8cce-190">Cascading nulls</span></span>

<span data-ttu-id="b8cce-191">選擇性關聯性具有可為 null 的外鍵屬性對應至可為 null 的資料庫資料行。</span><span class="sxs-lookup"><span data-stu-id="b8cce-191">Optional relationships have nullable foreign key properties mapped to nullable database columns.</span></span> <span data-ttu-id="b8cce-192">這表示，當目前的主體/父系已刪除或從相依/子系中斷時，外鍵值可以設定為 null。</span><span class="sxs-lookup"><span data-stu-id="b8cce-192">This means that the foreign key value can be set to null when the current principal/parent is deleted or is severed from the dependent/child.</span></span>

<span data-ttu-id="b8cce-193">讓我們再次看看 [發生串聯行為時](#when-cascading-behaviors-happen)的範例，但這次具有以可為 null 的外鍵屬性工作表示的選擇性關聯性 `Post.BlogId` ：</span><span class="sxs-lookup"><span data-stu-id="b8cce-193">Let's look again at the examples from [When cascading behaviors happen](#when-cascading-behaviors-happen), but this time with an optional relationship represented by a nullable `Post.BlogId` foreign key property:</span></span>

<!--
            public int? BlogId { get; set; }
-->
[!code-csharp[NullableBlogId](../../../samples/core/CascadeDeletes/OptionalDependentsSamples.cs?name=NullableBlogId)]

<span data-ttu-id="b8cce-194">當每個貼文的相關 blog 刪除時，此外鍵屬性將會設為 null。</span><span class="sxs-lookup"><span data-stu-id="b8cce-194">This foreign key property will be set to null for each post when its related blog is deleted.</span></span> <span data-ttu-id="b8cce-195">例如，此程式碼與之前相同：</span><span class="sxs-lookup"><span data-stu-id="b8cce-195">For example, this code, which is the same as before:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Deleting_principal_parent_1b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Deleting_principal_parent_1b)]

<span data-ttu-id="b8cce-196">現在會在呼叫 SaveChanges 時產生下列資料庫更新：</span><span class="sxs-lookup"><span data-stu-id="b8cce-196">Will now result in the following database updates when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (2ms) [Parameters=[@p1='1', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p1='2', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (1ms) [Parameters=[@p2='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p2;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="b8cce-197">同樣地，如果使用上述其中一個範例來切斷關聯性：</span><span class="sxs-lookup"><span data-stu-id="b8cce-197">Likewise, if the relationship is severed using either of the examples from above:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            foreach (var post in blog.Posts)
            {
                post.Blog = null;
            }
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_1b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Severing_a_relationship_1b)]

<span data-ttu-id="b8cce-198">或：</span><span class="sxs-lookup"><span data-stu-id="b8cce-198">Or:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            blog.Posts.Clear();
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_2b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Severing_a_relationship_2b)]

<span data-ttu-id="b8cce-199">然後，當呼叫 SaveChanges 時，會使用 null 外鍵值更新貼文：</span><span class="sxs-lookup"><span data-stu-id="b8cce-199">Then the posts are updated with null foreign key values when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (2ms) [Parameters=[@p1='1', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p1='2', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="b8cce-200">如需 EF Core 如何在其值變更時管理外鍵和導覽的詳細資訊，請參閱 [變更外鍵和](xref:core/change-tracking/relationship-changes) 導覽。</span><span class="sxs-lookup"><span data-stu-id="b8cce-200">See [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for more information on how EF Core manages foreign keys and navigations as their values are changed.</span></span>

> [!NOTE]
> <span data-ttu-id="b8cce-201">這類關聯性的修正已成為 Entity Framework 的預設行為，因為它是2008的第一個版本。</span><span class="sxs-lookup"><span data-stu-id="b8cce-201">The fixup of relationships like this has been the default behavior of Entity Framework since the first version in 2008.</span></span> <span data-ttu-id="b8cce-202">在 EF Core 之前，它沒有名稱，也不能變更。</span><span class="sxs-lookup"><span data-stu-id="b8cce-202">Prior to EF Core it didn't have a name and was not possible to change.</span></span> <span data-ttu-id="b8cce-203">現在稱為 `ClientSetNull` 下一節中所述。</span><span class="sxs-lookup"><span data-stu-id="b8cce-203">It is now known as `ClientSetNull` as described in the next section.</span></span>

<span data-ttu-id="b8cce-204">當您刪除選擇性關聯性中的主體/父系時，也可以將資料庫設定為以如下方式串聯 null。</span><span class="sxs-lookup"><span data-stu-id="b8cce-204">Databases can also be configured to cascade nulls like this when a principal/parent in an optional relationship is deleted.</span></span> <span data-ttu-id="b8cce-205">不過，這比使用資料庫中的串聯式刪除更不常見。</span><span class="sxs-lookup"><span data-stu-id="b8cce-205">However, this is much less common than using cascading deletes in the database.</span></span> <span data-ttu-id="b8cce-206">在資料庫中同時使用串聯刪除和串聯 null，在使用 SQL Server 時，幾乎一律會產生關聯性迴圈。</span><span class="sxs-lookup"><span data-stu-id="b8cce-206">Using cascading deletes and cascading nulls in the database at the same time will almost always result in relationship cycles when using SQL Server.</span></span> <span data-ttu-id="b8cce-207">如需設定串聯 null 的詳細資訊，請參閱下一節。</span><span class="sxs-lookup"><span data-stu-id="b8cce-207">See the next section for more information on configuring cascading nulls.</span></span>

## <a name="configuring-cascading-behaviors"></a><span data-ttu-id="b8cce-208">設定串聯行為</span><span class="sxs-lookup"><span data-stu-id="b8cce-208">Configuring cascading behaviors</span></span>

> [!TIP]
> <span data-ttu-id="b8cce-209">在此之前，請務必閱讀上述各節。</span><span class="sxs-lookup"><span data-stu-id="b8cce-209">Be sure to read sections above before coming here.</span></span> <span data-ttu-id="b8cce-210">如果無法理解先前的材質，設定選項可能不會有意義。</span><span class="sxs-lookup"><span data-stu-id="b8cce-210">The configuration options will likely not make sense if the preceding material is not understood.</span></span>

<span data-ttu-id="b8cce-211">串聯行為是使用中的方法，為每個關聯性設定 <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.ReferenceCollectionBuilder.OnDelete%2A> <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> 。</span><span class="sxs-lookup"><span data-stu-id="b8cce-211">Cascade behaviors are configured per relationship using the <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.ReferenceCollectionBuilder.OnDelete%2A> method in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="b8cce-212">例如：</span><span class="sxs-lookup"><span data-stu-id="b8cce-212">For example:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<Blog>()
                .HasOne(e => e.Owner)
                .WithOne(e => e.OwnedBlog)
                .OnDelete(DeleteBehavior.ClientCascade);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=OnModelCreating)]

<span data-ttu-id="b8cce-213">如需設定實體類型之間關聯性的詳細資訊，請參閱 [關聯](xref:core/modeling/relationships) 性。</span><span class="sxs-lookup"><span data-stu-id="b8cce-213">See [Relationships](xref:core/modeling/relationships) for more information on configuring relationships between entity types.</span></span>

<span data-ttu-id="b8cce-214">`OnDelete` 接受來自的值，不可否認的混淆， <xref:Microsoft.EntityFrameworkCore.DeleteBehavior> 列舉。</span><span class="sxs-lookup"><span data-stu-id="b8cce-214">`OnDelete` accepts a value from the, admittedly confusing, <xref:Microsoft.EntityFrameworkCore.DeleteBehavior> enum.</span></span> <span data-ttu-id="b8cce-215">此列舉會在追蹤的實體上定義 EF Core 的行為，以及在使用 EF 來建立架構時，在資料庫中設定串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="b8cce-215">This enum defines both the behavior of EF Core on tracked entities, and the configuration of cascade delete in the database when EF is used to create the schema.</span></span>

### <a name="impact-on-database-schema"></a><span data-ttu-id="b8cce-216">對資料庫架構的影響</span><span class="sxs-lookup"><span data-stu-id="b8cce-216">Impact on database schema</span></span>

<span data-ttu-id="b8cce-217">下表顯示 `OnDelete` EF Core 遷移或的外鍵條件約束所建立的每個值的結果 <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> 。</span><span class="sxs-lookup"><span data-stu-id="b8cce-217">The following table shows the result of each `OnDelete` value on the foreign key constraint created by EF Core migrations or <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A>.</span></span>

| <span data-ttu-id="b8cce-218">Deletebehavior.restrict</span><span class="sxs-lookup"><span data-stu-id="b8cce-218">DeleteBehavior</span></span>        | <span data-ttu-id="b8cce-219">對資料庫架構的影響</span><span class="sxs-lookup"><span data-stu-id="b8cce-219">Impact on database schema</span></span>
|:----------------------|--------------------------
| <span data-ttu-id="b8cce-220">Cascade</span><span class="sxs-lookup"><span data-stu-id="b8cce-220">Cascade</span></span>               | <span data-ttu-id="b8cce-221">ON DELETE CASCADE</span><span class="sxs-lookup"><span data-stu-id="b8cce-221">ON DELETE CASCADE</span></span>
| <span data-ttu-id="b8cce-222">限制</span><span class="sxs-lookup"><span data-stu-id="b8cce-222">Restrict</span></span>              | <span data-ttu-id="b8cce-223">ON DELETE NO ACTION</span><span class="sxs-lookup"><span data-stu-id="b8cce-223">ON DELETE NO ACTION</span></span>
| <span data-ttu-id="b8cce-224">NoAction</span><span class="sxs-lookup"><span data-stu-id="b8cce-224">NoAction</span></span>              | <span data-ttu-id="b8cce-225">資料庫預設值</span><span class="sxs-lookup"><span data-stu-id="b8cce-225">database default</span></span>
| <span data-ttu-id="b8cce-226">SetNull</span><span class="sxs-lookup"><span data-stu-id="b8cce-226">SetNull</span></span>               | <span data-ttu-id="b8cce-227">ON DELETE SET NULL</span><span class="sxs-lookup"><span data-stu-id="b8cce-227">ON DELETE SET NULL</span></span>
| <span data-ttu-id="b8cce-228">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="b8cce-228">ClientSetNull</span></span>         | <span data-ttu-id="b8cce-229">ON DELETE NO ACTION</span><span class="sxs-lookup"><span data-stu-id="b8cce-229">ON DELETE NO ACTION</span></span>
| <span data-ttu-id="b8cce-230">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="b8cce-230">ClientCascade</span></span>         | <span data-ttu-id="b8cce-231">ON DELETE NO ACTION</span><span class="sxs-lookup"><span data-stu-id="b8cce-231">ON DELETE NO ACTION</span></span>
| <span data-ttu-id="b8cce-232">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="b8cce-232">ClientNoAction</span></span>        | <span data-ttu-id="b8cce-233">資料庫預設值</span><span class="sxs-lookup"><span data-stu-id="b8cce-233">database default</span></span>

> [!NOTE]
> <span data-ttu-id="b8cce-234">此資料表很令人困惑，我們計畫在未來的版本中重新討論。</span><span class="sxs-lookup"><span data-stu-id="b8cce-234">This table is confusing and we plan to revisit this in a future release.</span></span> <span data-ttu-id="b8cce-235">請參閱 [GitHub 問題 #21252](https://github.com/dotnet/efcore/issues/21252)。</span><span class="sxs-lookup"><span data-stu-id="b8cce-235">See [GitHub Issue #21252](https://github.com/dotnet/efcore/issues/21252).</span></span>

<span data-ttu-id="b8cce-236">`ON DELETE NO ACTION` `ON DELETE RESTRICT` 在關係資料庫中和的行為通常是完全相同或非常相似。</span><span class="sxs-lookup"><span data-stu-id="b8cce-236">The behaviors of `ON DELETE NO ACTION` and `ON DELETE RESTRICT` in relational databases are typically either identical or very similar.</span></span> <span data-ttu-id="b8cce-237">無論如何 `NO ACTION` ，這兩個選項都會導致強制執行參考條件約束。</span><span class="sxs-lookup"><span data-stu-id="b8cce-237">Despite what `NO ACTION` may imply, both of these options cause referential constraints to be enforced.</span></span> <span data-ttu-id="b8cce-238">如果有的話，差異就是資料庫檢查條件約束的 _時間_ 。</span><span class="sxs-lookup"><span data-stu-id="b8cce-238">The difference, when there is one, is _when_ the database checks the constraints.</span></span>  <span data-ttu-id="b8cce-239">請檢查您的資料庫檔案，瞭解 `ON DELETE NO ACTION` 資料庫系統與之間的特定差異 `ON DELETE RESTRICT` 。</span><span class="sxs-lookup"><span data-stu-id="b8cce-239">Check your database documentation for the specific differences between `ON DELETE NO ACTION` and `ON DELETE RESTRICT` on your database system.</span></span>

<span data-ttu-id="b8cce-240">將在資料庫上造成串聯行為的唯一值為 `Cascade` 和 `SetNull` 。</span><span class="sxs-lookup"><span data-stu-id="b8cce-240">The only values that will cause cascading behaviors on the database are `Cascade` and `SetNull`.</span></span> <span data-ttu-id="b8cce-241">所有其他值都會將資料庫設定為不串聯任何變更。</span><span class="sxs-lookup"><span data-stu-id="b8cce-241">All other values will configure the database to not cascade any changes.</span></span>

### <a name="impact-on-savechanges-behavior"></a><span data-ttu-id="b8cce-242">對 SaveChanges 行為的影響</span><span class="sxs-lookup"><span data-stu-id="b8cce-242">Impact on SaveChanges behavior</span></span>

<span data-ttu-id="b8cce-243">下列各節中的表格說明當刪除主體/父系或其相依/子系實體的關聯性時，相依/子系實體會發生什麼事。</span><span class="sxs-lookup"><span data-stu-id="b8cce-243">The tables in the following sections cover what happens to dependent/child entities when the principal/parent is deleted, or its relationship to the dependent/child entities is severed.</span></span> <span data-ttu-id="b8cce-244">每個資料表都包含下列其中一個：</span><span class="sxs-lookup"><span data-stu-id="b8cce-244">Each table covers one of:</span></span>

- <span data-ttu-id="b8cce-245">選擇性 (可為 null 的 FK) 和必要 (不可為 null 的 FK) 關聯性</span><span class="sxs-lookup"><span data-stu-id="b8cce-245">Optional (nullable FK) and required (non-nullable FK) relationships</span></span>
- <span data-ttu-id="b8cce-246">當相依項/子系由 DbCoNtext 載入和追蹤，而且只存在於資料庫中時</span><span class="sxs-lookup"><span data-stu-id="b8cce-246">When dependents/children are loaded and tracked by the DbContext and when they exist only in the database</span></span>

#### <a name="required-relationship-with-dependentschildren-loaded"></a><span data-ttu-id="b8cce-247">已載入相依項/子系的必要關聯性</span><span class="sxs-lookup"><span data-stu-id="b8cce-247">Required relationship with dependents/children loaded</span></span>

| <span data-ttu-id="b8cce-248">Deletebehavior.restrict</span><span class="sxs-lookup"><span data-stu-id="b8cce-248">DeleteBehavior</span></span>    | <span data-ttu-id="b8cce-249">刪除主體/父系時</span><span class="sxs-lookup"><span data-stu-id="b8cce-249">On deleting principal/parent</span></span>             | <span data-ttu-id="b8cce-250">從 principal/parent 切斷</span><span class="sxs-lookup"><span data-stu-id="b8cce-250">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="b8cce-251">Cascade</span><span class="sxs-lookup"><span data-stu-id="b8cce-251">Cascade</span></span>           | <span data-ttu-id="b8cce-252">EF Core 刪除的相依項</span><span class="sxs-lookup"><span data-stu-id="b8cce-252">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="b8cce-253">EF Core 刪除的相依項</span><span class="sxs-lookup"><span data-stu-id="b8cce-253">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="b8cce-254">限制</span><span class="sxs-lookup"><span data-stu-id="b8cce-254">Restrict</span></span>          | `InvalidOperationException`              | `InvalidOperationException`
| <span data-ttu-id="b8cce-255">NoAction</span><span class="sxs-lookup"><span data-stu-id="b8cce-255">NoAction</span></span>          | `InvalidOperationException`              | `InvalidOperationException`
| <span data-ttu-id="b8cce-256">SetNull</span><span class="sxs-lookup"><span data-stu-id="b8cce-256">SetNull</span></span>           | <span data-ttu-id="b8cce-257">`SqlException` 建立資料庫時</span><span class="sxs-lookup"><span data-stu-id="b8cce-257">`SqlException` on creating database</span></span>      | <span data-ttu-id="b8cce-258">`SqlException` 建立資料庫時</span><span class="sxs-lookup"><span data-stu-id="b8cce-258">`SqlException` on creating database</span></span>
| <span data-ttu-id="b8cce-259">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="b8cce-259">ClientSetNull</span></span>     | `InvalidOperationException`              | `InvalidOperationException`
| <span data-ttu-id="b8cce-260">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="b8cce-260">ClientCascade</span></span>     | <span data-ttu-id="b8cce-261">EF Core 刪除的相依項</span><span class="sxs-lookup"><span data-stu-id="b8cce-261">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="b8cce-262">EF Core 刪除的相依項</span><span class="sxs-lookup"><span data-stu-id="b8cce-262">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="b8cce-263">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="b8cce-263">ClientNoAction</span></span>    | `DbUpdateException`                      | `InvalidOperationException`

<span data-ttu-id="b8cce-264">注意：</span><span class="sxs-lookup"><span data-stu-id="b8cce-264">Notes:</span></span>

- <span data-ttu-id="b8cce-265">這類必要關聯性的預設值為 `Cascade` 。</span><span class="sxs-lookup"><span data-stu-id="b8cce-265">The default for required relationships like this is `Cascade`.</span></span>
- <span data-ttu-id="b8cce-266">針對必要關聯性使用 cascade delete 以外的任何其他作業，會在呼叫 SaveChanges 時產生例外狀況。</span><span class="sxs-lookup"><span data-stu-id="b8cce-266">Using anything other than cascade delete for required relationships will result in an exception when SaveChanges is called.</span></span>
  - <span data-ttu-id="b8cce-267">一般而言，這是 `InvalidOperationException` 來自 EF Core 的，因為在載入的子系/相依項中偵測到不正確狀態。</span><span class="sxs-lookup"><span data-stu-id="b8cce-267">Typically, this is an `InvalidOperationException` from EF Core since the invalid state is detected in the loaded children/dependents.</span></span>
  - <span data-ttu-id="b8cce-268">`ClientNoAction` 強制 EF Core 在將其傳送至資料庫之前，不檢查修復相依項，因此在此情況下，資料庫會擲回例外狀況，然後 `DbUpdateException` 由 SaveChanges 包裝在中。</span><span class="sxs-lookup"><span data-stu-id="b8cce-268">`ClientNoAction` forces EF Core to not check fixup dependents before sending them to the database, so in this case the database throws an exception, which is then wrapped in a `DbUpdateException` by SaveChanges.</span></span>
  - <span data-ttu-id="b8cce-269">`SetNull` 在建立資料庫時拒絕，因為外鍵資料行不可為 null。</span><span class="sxs-lookup"><span data-stu-id="b8cce-269">`SetNull` is rejected when creating the database since the foreign key column is not nullable.</span></span>
- <span data-ttu-id="b8cce-270">由於相依項/子系已載入，因此 EF Core 一律會刪除這些相依項/子系，而且永遠不會離開資料庫來刪除。</span><span class="sxs-lookup"><span data-stu-id="b8cce-270">Since dependents/children are loaded, they are always deleted by EF Core, and never left for the database to delete.</span></span>

#### <a name="required-relationship-with-dependentschildren-not-loaded"></a><span data-ttu-id="b8cce-271">未載入相依項/子系的必要關聯性</span><span class="sxs-lookup"><span data-stu-id="b8cce-271">Required relationship with dependents/children not loaded</span></span>

| <span data-ttu-id="b8cce-272">Deletebehavior.restrict</span><span class="sxs-lookup"><span data-stu-id="b8cce-272">DeleteBehavior</span></span>    | <span data-ttu-id="b8cce-273">刪除主體/父系時</span><span class="sxs-lookup"><span data-stu-id="b8cce-273">On deleting principal/parent</span></span>             | <span data-ttu-id="b8cce-274">從 principal/parent 切斷</span><span class="sxs-lookup"><span data-stu-id="b8cce-274">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="b8cce-275">Cascade</span><span class="sxs-lookup"><span data-stu-id="b8cce-275">Cascade</span></span>           | <span data-ttu-id="b8cce-276">資料庫刪除的相依項</span><span class="sxs-lookup"><span data-stu-id="b8cce-276">Dependents deleted by database</span></span>           | <span data-ttu-id="b8cce-277">N/A</span><span class="sxs-lookup"><span data-stu-id="b8cce-277">N/A</span></span>
| <span data-ttu-id="b8cce-278">限制</span><span class="sxs-lookup"><span data-stu-id="b8cce-278">Restrict</span></span>          | `DbUpdateException`                      | <span data-ttu-id="b8cce-279">N/A</span><span class="sxs-lookup"><span data-stu-id="b8cce-279">N/A</span></span>
| <span data-ttu-id="b8cce-280">NoAction</span><span class="sxs-lookup"><span data-stu-id="b8cce-280">NoAction</span></span>          | `DbUpdateException`                      | <span data-ttu-id="b8cce-281">N/A</span><span class="sxs-lookup"><span data-stu-id="b8cce-281">N/A</span></span>
| <span data-ttu-id="b8cce-282">SetNull</span><span class="sxs-lookup"><span data-stu-id="b8cce-282">SetNull</span></span>           | <span data-ttu-id="b8cce-283">`SqlException` 建立資料庫時</span><span class="sxs-lookup"><span data-stu-id="b8cce-283">`SqlException` on creating database</span></span>      | <span data-ttu-id="b8cce-284">N/A</span><span class="sxs-lookup"><span data-stu-id="b8cce-284">N/A</span></span>
| <span data-ttu-id="b8cce-285">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="b8cce-285">ClientSetNull</span></span>     | `DbUpdateException`                      | <span data-ttu-id="b8cce-286">N/A</span><span class="sxs-lookup"><span data-stu-id="b8cce-286">N/A</span></span>
| <span data-ttu-id="b8cce-287">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="b8cce-287">ClientCascade</span></span>     | `DbUpdateException`                      | <span data-ttu-id="b8cce-288">N/A</span><span class="sxs-lookup"><span data-stu-id="b8cce-288">N/A</span></span>
| <span data-ttu-id="b8cce-289">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="b8cce-289">ClientNoAction</span></span>    | `DbUpdateException`                      | <span data-ttu-id="b8cce-290">N/A</span><span class="sxs-lookup"><span data-stu-id="b8cce-290">N/A</span></span>

<span data-ttu-id="b8cce-291">注意：</span><span class="sxs-lookup"><span data-stu-id="b8cce-291">Notes:</span></span>

- <span data-ttu-id="b8cce-292">切斷關聯性在此無效，因為未載入相依項/子系。</span><span class="sxs-lookup"><span data-stu-id="b8cce-292">Severing a relationship is not valid here since the dependents/children are not loaded.</span></span>
- <span data-ttu-id="b8cce-293">這類必要關聯性的預設值為 `Cascade` 。</span><span class="sxs-lookup"><span data-stu-id="b8cce-293">The default for required relationships like this is `Cascade`.</span></span>
- <span data-ttu-id="b8cce-294">針對必要關聯性使用 cascade delete 以外的任何其他作業，會在呼叫 SaveChanges 時產生例外狀況。</span><span class="sxs-lookup"><span data-stu-id="b8cce-294">Using anything other than cascade delete for required relationships will result in an exception when SaveChanges is called.</span></span>
  - <span data-ttu-id="b8cce-295">一般來說，這是 `DbUpdateException` 因為不會載入相依項/子系，因此只能由資料庫偵測到不正確狀態。</span><span class="sxs-lookup"><span data-stu-id="b8cce-295">Typically, this is a `DbUpdateException` because the dependents/children are not loaded, and hence the invalid state can only be detected by the database.</span></span> <span data-ttu-id="b8cce-296">SaveChanges 接著會將資料庫例外狀況包裝在中 `DbUpdateException` 。</span><span class="sxs-lookup"><span data-stu-id="b8cce-296">SaveChanges then wraps the database exception in a `DbUpdateException`.</span></span>
  - <span data-ttu-id="b8cce-297">`SetNull` 在建立資料庫時拒絕，因為外鍵資料行不可為 null。</span><span class="sxs-lookup"><span data-stu-id="b8cce-297">`SetNull` is rejected when creating the database since the foreign key column is not nullable.</span></span>

#### <a name="optional-relationship-with-dependentschildren-loaded"></a><span data-ttu-id="b8cce-298">已載入相依項/子系的選擇性關聯性</span><span class="sxs-lookup"><span data-stu-id="b8cce-298">Optional relationship with dependents/children loaded</span></span>

| <span data-ttu-id="b8cce-299">Deletebehavior.restrict</span><span class="sxs-lookup"><span data-stu-id="b8cce-299">DeleteBehavior</span></span>    | <span data-ttu-id="b8cce-300">刪除主體/父系時</span><span class="sxs-lookup"><span data-stu-id="b8cce-300">On deleting principal/parent</span></span>             | <span data-ttu-id="b8cce-301">從 principal/parent 切斷</span><span class="sxs-lookup"><span data-stu-id="b8cce-301">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="b8cce-302">Cascade</span><span class="sxs-lookup"><span data-stu-id="b8cce-302">Cascade</span></span>           | <span data-ttu-id="b8cce-303">EF Core 刪除的相依項</span><span class="sxs-lookup"><span data-stu-id="b8cce-303">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="b8cce-304">EF Core 刪除的相依項</span><span class="sxs-lookup"><span data-stu-id="b8cce-304">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="b8cce-305">限制</span><span class="sxs-lookup"><span data-stu-id="b8cce-305">Restrict</span></span>          | <span data-ttu-id="b8cce-306">EF Core 將相依 Fk 設為 null</span><span class="sxs-lookup"><span data-stu-id="b8cce-306">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="b8cce-307">EF Core 將相依 Fk 設為 null</span><span class="sxs-lookup"><span data-stu-id="b8cce-307">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="b8cce-308">NoAction</span><span class="sxs-lookup"><span data-stu-id="b8cce-308">NoAction</span></span>          | <span data-ttu-id="b8cce-309">EF Core 將相依 Fk 設為 null</span><span class="sxs-lookup"><span data-stu-id="b8cce-309">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="b8cce-310">EF Core 將相依 Fk 設為 null</span><span class="sxs-lookup"><span data-stu-id="b8cce-310">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="b8cce-311">SetNull</span><span class="sxs-lookup"><span data-stu-id="b8cce-311">SetNull</span></span>           | <span data-ttu-id="b8cce-312">EF Core 將相依 Fk 設為 null</span><span class="sxs-lookup"><span data-stu-id="b8cce-312">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="b8cce-313">EF Core 將相依 Fk 設為 null</span><span class="sxs-lookup"><span data-stu-id="b8cce-313">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="b8cce-314">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="b8cce-314">ClientSetNull</span></span>     | <span data-ttu-id="b8cce-315">EF Core 將相依 Fk 設為 null</span><span class="sxs-lookup"><span data-stu-id="b8cce-315">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="b8cce-316">EF Core 將相依 Fk 設為 null</span><span class="sxs-lookup"><span data-stu-id="b8cce-316">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="b8cce-317">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="b8cce-317">ClientCascade</span></span>     | <span data-ttu-id="b8cce-318">EF Core 刪除的相依項</span><span class="sxs-lookup"><span data-stu-id="b8cce-318">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="b8cce-319">EF Core 刪除的相依項</span><span class="sxs-lookup"><span data-stu-id="b8cce-319">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="b8cce-320">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="b8cce-320">ClientNoAction</span></span>    | `DbUpdateException`                      | <span data-ttu-id="b8cce-321">EF Core 將相依 Fk 設為 null</span><span class="sxs-lookup"><span data-stu-id="b8cce-321">Dependent FKs set to null by EF Core</span></span>

<span data-ttu-id="b8cce-322">注意：</span><span class="sxs-lookup"><span data-stu-id="b8cce-322">Notes:</span></span>

- <span data-ttu-id="b8cce-323">這類選擇性關聯性的預設值為 `ClientSetNull` 。</span><span class="sxs-lookup"><span data-stu-id="b8cce-323">The default for optional relationships like this is `ClientSetNull`.</span></span>
- <span data-ttu-id="b8cce-324">除非已 `Cascade` 設定或，否則不會刪除相依項/子系 `ClientCascade` 。</span><span class="sxs-lookup"><span data-stu-id="b8cce-324">Dependents/children are never deleted unless `Cascade` or `ClientCascade` are configured.</span></span>
- <span data-ttu-id="b8cce-325">所有其他值都會導致 EF Core 將相依 Fk 設為 null。</span><span class="sxs-lookup"><span data-stu-id="b8cce-325">All other values cause the dependent FKs to be set to null by EF Core...</span></span>
  - <span data-ttu-id="b8cce-326">...但 `ClientNoAction` 會在刪除主體/父系時，告知 EF Core 不會接觸相依項/子系的外鍵。</span><span class="sxs-lookup"><span data-stu-id="b8cce-326">...except `ClientNoAction` which tells EF Core not to touch the foreign keys of dependents/children when the principal/parent is deleted.</span></span> <span data-ttu-id="b8cce-327">因此，資料庫會擲回例外狀況，此例外狀況會 `DbUpdateException` 由 SaveChanges 包裝為。</span><span class="sxs-lookup"><span data-stu-id="b8cce-327">The database therefore throws an exception, which is wrapped as a `DbUpdateException` by SaveChanges.</span></span>

#### <a name="optional-relationship-with-dependentschildren-not-loaded"></a><span data-ttu-id="b8cce-328">未載入相依項/子系的選擇性關聯性</span><span class="sxs-lookup"><span data-stu-id="b8cce-328">Optional relationship with dependents/children not loaded</span></span>

| <span data-ttu-id="b8cce-329">Deletebehavior.restrict</span><span class="sxs-lookup"><span data-stu-id="b8cce-329">DeleteBehavior</span></span>    | <span data-ttu-id="b8cce-330">刪除主體/父系時</span><span class="sxs-lookup"><span data-stu-id="b8cce-330">On deleting principal/parent</span></span>             | <span data-ttu-id="b8cce-331">從 principal/parent 切斷</span><span class="sxs-lookup"><span data-stu-id="b8cce-331">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="b8cce-332">Cascade</span><span class="sxs-lookup"><span data-stu-id="b8cce-332">Cascade</span></span>           | <span data-ttu-id="b8cce-333">資料庫刪除的相依項</span><span class="sxs-lookup"><span data-stu-id="b8cce-333">Dependents deleted by database</span></span>           | <span data-ttu-id="b8cce-334">N/A</span><span class="sxs-lookup"><span data-stu-id="b8cce-334">N/A</span></span>
| <span data-ttu-id="b8cce-335">限制</span><span class="sxs-lookup"><span data-stu-id="b8cce-335">Restrict</span></span>          | `DbUpdateException`                      | <span data-ttu-id="b8cce-336">N/A</span><span class="sxs-lookup"><span data-stu-id="b8cce-336">N/A</span></span>
| <span data-ttu-id="b8cce-337">NoAction</span><span class="sxs-lookup"><span data-stu-id="b8cce-337">NoAction</span></span>          | `DbUpdateException`                      | <span data-ttu-id="b8cce-338">N/A</span><span class="sxs-lookup"><span data-stu-id="b8cce-338">N/A</span></span>
| <span data-ttu-id="b8cce-339">SetNull</span><span class="sxs-lookup"><span data-stu-id="b8cce-339">SetNull</span></span>           | <span data-ttu-id="b8cce-340">依資料庫將相依 Fk 設為 null</span><span class="sxs-lookup"><span data-stu-id="b8cce-340">Dependent FKs set to null by database</span></span>    | <span data-ttu-id="b8cce-341">N/A</span><span class="sxs-lookup"><span data-stu-id="b8cce-341">N/A</span></span>
| <span data-ttu-id="b8cce-342">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="b8cce-342">ClientSetNull</span></span>     | `DbUpdateException`                      | <span data-ttu-id="b8cce-343">N/A</span><span class="sxs-lookup"><span data-stu-id="b8cce-343">N/A</span></span>
| <span data-ttu-id="b8cce-344">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="b8cce-344">ClientCascade</span></span>     | `DbUpdateException`                      | <span data-ttu-id="b8cce-345">N/A</span><span class="sxs-lookup"><span data-stu-id="b8cce-345">N/A</span></span>
| <span data-ttu-id="b8cce-346">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="b8cce-346">ClientNoAction</span></span>    | `DbUpdateException`                      | <span data-ttu-id="b8cce-347">N/A</span><span class="sxs-lookup"><span data-stu-id="b8cce-347">N/A</span></span>

<span data-ttu-id="b8cce-348">注意：</span><span class="sxs-lookup"><span data-stu-id="b8cce-348">Notes:</span></span>

- <span data-ttu-id="b8cce-349">切斷關聯性在此無效，因為未載入相依項/子系。</span><span class="sxs-lookup"><span data-stu-id="b8cce-349">Severing a relationship is not valid here since the dependents/children are not loaded.</span></span>
- <span data-ttu-id="b8cce-350">這類選擇性關聯性的預設值為 `ClientSetNull` 。</span><span class="sxs-lookup"><span data-stu-id="b8cce-350">The default for optional relationships like this is `ClientSetNull`.</span></span>
- <span data-ttu-id="b8cce-351">必須載入相依項/子系，以避免發生資料庫例外狀況，除非資料庫已設定為串聯刪除或 null。</span><span class="sxs-lookup"><span data-stu-id="b8cce-351">Dependents/children must be loaded to avoid a database exception unless the database has been configured to cascade either deletes or nulls.</span></span>
