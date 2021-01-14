---
title: 明確追蹤實體-EF Core
description: 使用 [新增]、[附加]、[更新] 和 [移除] 以 DbCoNtext 明確追蹤實體
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/explicit-tracking
ms.openlocfilehash: 28a6ec3e3c25dad70882b8681f78744a5979efe6
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129707"
---
# <a name="explicitly-tracking-entities"></a><span data-ttu-id="82407-103">明確追蹤實體</span><span class="sxs-lookup"><span data-stu-id="82407-103">Explicitly Tracking Entities</span></span>

<span data-ttu-id="82407-104">每個 <xref:Microsoft.EntityFrameworkCore.DbContext> 實例都會追蹤對實體所做的變更。</span><span class="sxs-lookup"><span data-stu-id="82407-104">Each <xref:Microsoft.EntityFrameworkCore.DbContext> instance tracks changes made to entities.</span></span> <span data-ttu-id="82407-105">接著，這些追蹤的實體會在呼叫時驅動資料庫的變更 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 。</span><span class="sxs-lookup"><span data-stu-id="82407-105">These tracked entities in turn drive the changes to the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span>

<span data-ttu-id="82407-106">Entity Framework Core (EF Core) 當相同的 <xref:Microsoft.EntityFrameworkCore.DbContext> 實例用於查詢實體，並藉由呼叫來更新時，變更追蹤的效果最佳。 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A></span><span class="sxs-lookup"><span data-stu-id="82407-106">Entity Framework Core (EF Core) change tracking works best when the same <xref:Microsoft.EntityFrameworkCore.DbContext> instance is used to both query for entities and update them by calling <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>.</span></span> <span data-ttu-id="82407-107">這是因為 EF Core 會自動追蹤查詢實體的狀態，然後在呼叫 SaveChanges 時，偵測對這些實體所做的任何變更。</span><span class="sxs-lookup"><span data-stu-id="82407-107">This is because EF Core automatically tracks the state of queried entities and then detects any changes made to these entities when SaveChanges is called.</span></span> <span data-ttu-id="82407-108">這種方法會在 [EF Core 的變更追蹤中](xref:core/change-tracking/index)涵蓋。</span><span class="sxs-lookup"><span data-stu-id="82407-108">This approach is covered in [Change Tracking in EF Core](xref:core/change-tracking/index).</span></span>

> [!TIP]
> <span data-ttu-id="82407-109">本檔假設您已瞭解實體狀態以及 EF Core 變更追蹤的基本概念。</span><span class="sxs-lookup"><span data-stu-id="82407-109">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="82407-110">如需有關這些主題的詳細資訊，請參閱 [EF Core 中的變更追蹤](xref:core/change-tracking/index) 。</span><span class="sxs-lookup"><span data-stu-id="82407-110">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="82407-111">您可以 [從 GitHub 下載範例程式碼](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore)，以執行並偵測到本檔中的所有程式碼。</span><span class="sxs-lookup"><span data-stu-id="82407-111">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore).</span></span>

> [!TIP]
> <span data-ttu-id="82407-112">為了簡單起見，本檔會使用和參考同步方法（例如）， <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 而不是其非同步對應專案（例如） <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="82407-112">For simplicity, this document uses and references synchronous methods such as <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> rather their async equivalents such as <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>.</span></span> <span data-ttu-id="82407-113">除非另有說明，否則呼叫和等候 async 方法可以替代。</span><span class="sxs-lookup"><span data-stu-id="82407-113">Calling and awaiting the async method can be substituted unless otherwise noted.</span></span>

## <a name="introduction"></a><span data-ttu-id="82407-114">簡介</span><span class="sxs-lookup"><span data-stu-id="82407-114">Introduction</span></span>

<span data-ttu-id="82407-115">實體可以明確地「附加」到 <xref:Microsoft.EntityFrameworkCore.DbContext> ，讓內容接著追蹤這些實體。</span><span class="sxs-lookup"><span data-stu-id="82407-115">Entities can be explicitly "attached" to a <xref:Microsoft.EntityFrameworkCore.DbContext> such that the context then tracks those entities.</span></span> <span data-ttu-id="82407-116">這在下列情況下很有用：</span><span class="sxs-lookup"><span data-stu-id="82407-116">This is primarily useful when:</span></span>

1. <span data-ttu-id="82407-117">建立將插入資料庫的新實體。</span><span class="sxs-lookup"><span data-stu-id="82407-117">Creating new entities that will be inserted into the database.</span></span>
2. <span data-ttu-id="82407-118">重新附加先前由 _不同_ DbCoNtext 實例查詢的已中斷連接實體。</span><span class="sxs-lookup"><span data-stu-id="82407-118">Re-attaching disconnected entities that were previously queried by a _different_ DbContext instance.</span></span>

<span data-ttu-id="82407-119">大部分的應用程式都需要這些應用程式的第一個，而且是由方法所處理的主要 <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="82407-119">The first of these will be needed by most applications, and is primary handled by the <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> methods.</span></span>

<span data-ttu-id="82407-120">只有在 **_未追蹤實體時_**，變更實體或其關聯性的應用程式需要第二個。</span><span class="sxs-lookup"><span data-stu-id="82407-120">The second is only needed by applications that change entities or their relationships **_while the entities are not being tracked_**.</span></span> <span data-ttu-id="82407-121">例如，web 應用程式可能會將實體傳送至 web 用戶端，讓使用者進行變更，並將實體送回。</span><span class="sxs-lookup"><span data-stu-id="82407-121">For example, a web application may send entities to the web client where the user makes changes and sends the entities back.</span></span> <span data-ttu-id="82407-122">這些實體稱為「已中斷連線」，因為它們最初是從 DbCoNtext 查詢，但在傳送至用戶端時，會從該內容中斷連線。</span><span class="sxs-lookup"><span data-stu-id="82407-122">These entities are referred to as "disconnected" since they were originally queried from a DbContext, but were then disconnected from that context when sent to the client.</span></span>

<span data-ttu-id="82407-123">Web 應用程式現在必須重新附加這些實體，才能再次追蹤這些實體，並指出已進行的變更，以便對 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 資料庫進行適當的更新。</span><span class="sxs-lookup"><span data-stu-id="82407-123">The web application must now re-attach these entities so that they are again tracked and indicate the changes that have been made such that <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> can make appropriate updates to the database.</span></span> <span data-ttu-id="82407-124">這主要是由 <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> 和方法處理 <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="82407-124">This is primarily handled by the <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> methods.</span></span>

> [!TIP]
> <span data-ttu-id="82407-125">通常不需要將實體附加至其查詢來源的 _相同 DbCoNtext 實例_ 。</span><span class="sxs-lookup"><span data-stu-id="82407-125">Attaching entities to the _same DbContext instance_ that they were queried from should not normally be needed.</span></span> <span data-ttu-id="82407-126">請勿定期執行無追蹤查詢，然後將傳回的實體附加至相同的內容。</span><span class="sxs-lookup"><span data-stu-id="82407-126">Do not routinely perform a no-tracking query and then attach the returned entities to the same context.</span></span> <span data-ttu-id="82407-127">這會比使用追蹤查詢更慢，而且也可能會導致問題（例如遺漏陰影屬性值），使其更難正確。</span><span class="sxs-lookup"><span data-stu-id="82407-127">This will be slower than using a tracking query, and may also result in issues such as missing shadow property values, making it harder to get right.</span></span>

### <a name="generated-verses-explicit-key-values"></a><span data-ttu-id="82407-128">產生的辭句明確索引鍵值</span><span class="sxs-lookup"><span data-stu-id="82407-128">Generated verses explicit key values</span></span>

<span data-ttu-id="82407-129">依預設，會將整數和 GUID 索引 [鍵屬性](xref:core/modeling/keys) 設定為使用 [自動產生](xref:core/modeling/generated-properties)的索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="82407-129">By default, integer and GUID [key properties](xref:core/modeling/keys) are configured to use [automatically generated key values](xref:core/modeling/generated-properties).</span></span> <span data-ttu-id="82407-130">這主要是 **變更追蹤的優點：未設定的索引鍵值表示實體為 "new"**。</span><span class="sxs-lookup"><span data-stu-id="82407-130">This has a **major advantage for change tracking: an unset key value indicates that the entity is "new"**.</span></span> <span data-ttu-id="82407-131">「新增」表示它尚未插入資料庫中。</span><span class="sxs-lookup"><span data-stu-id="82407-131">By "new", we mean that it has not yet been inserted into the database.</span></span>

<span data-ttu-id="82407-132">下列各節會使用兩個模型。</span><span class="sxs-lookup"><span data-stu-id="82407-132">Two models are used in the following sections.</span></span> <span data-ttu-id="82407-133">第一個設定為 **不** 使用產生的索引鍵值：</span><span class="sxs-lookup"><span data-stu-id="82407-133">The first is configured to **not** use generated key values:</span></span>

<!--
    public class Blog
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
    }

    public class Post
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Model)]

<span data-ttu-id="82407-134">非產生的 (也就是明確設定) 的索引鍵值會先顯示在每個範例中，因為一切都非常明確且容易遵循。</span><span class="sxs-lookup"><span data-stu-id="82407-134">Non-generated (i.e. explicitly set) key values are shown first in each example because everything is very explicit and easy to follow.</span></span> <span data-ttu-id="82407-135">接下來是使用產生的索引鍵值的範例：</span><span class="sxs-lookup"><span data-stu-id="82407-135">This is then followed by an example where generated key values are used:</span></span>

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

<span data-ttu-id="82407-136">請注意，此模型中的索引鍵屬性不需要其他設定，因為使用產生的索引鍵值是 [簡單整數索引鍵的預設](xref:core/modeling/generated-properties)值。</span><span class="sxs-lookup"><span data-stu-id="82407-136">Notice that the key properties in this model need no additional configuration here since using generated key values is the [default for simple integer keys](xref:core/modeling/generated-properties).</span></span>

## <a name="inserting-new-entities"></a><span data-ttu-id="82407-137">插入新的實體</span><span class="sxs-lookup"><span data-stu-id="82407-137">Inserting new entities</span></span>

### <a name="explicit-key-values"></a><span data-ttu-id="82407-138">明確的索引鍵值</span><span class="sxs-lookup"><span data-stu-id="82407-138">Explicit key values</span></span>

<span data-ttu-id="82407-139">您必須在要插入的狀態中追蹤實體 `Added` <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 。</span><span class="sxs-lookup"><span data-stu-id="82407-139">An entity must be tracked in the `Added` state to be inserted by <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>.</span></span> <span data-ttu-id="82407-140">實體通常會藉由呼叫 <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> 、、 <xref:Microsoft.EntityFrameworkCore.DbContext.AddRange%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.AddAsync%2A?displayProperty=nameWithType> 、 <xref:Microsoft.EntityFrameworkCore.DbContext.AddRangeAsync%2A?displayProperty=nameWithType> 或中的對等方法 <xref:Microsoft.EntityFrameworkCore.DbSet%601> 的其中一個來進入加入的狀態。</span><span class="sxs-lookup"><span data-stu-id="82407-140">Entities are typically put in the Added state by calling one of <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.AddRange%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.AddAsync%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.AddRangeAsync%2A?displayProperty=nameWithType>, or the equivalent methods on <xref:Microsoft.EntityFrameworkCore.DbSet%601>.</span></span>

> [!TIP]
> <span data-ttu-id="82407-141">在變更追蹤的內容中，這些方法都是以相同的方式運作。</span><span class="sxs-lookup"><span data-stu-id="82407-141">These methods all work in the same way in the context of change tracking.</span></span> <span data-ttu-id="82407-142">如需詳細資訊，請參閱 [其他變更追蹤功能](xref:core/change-tracking/miscellaneous) 。</span><span class="sxs-lookup"><span data-stu-id="82407-142">See [Additional Change Tracking Features](xref:core/change-tracking/miscellaneous) for more information.</span></span>

<span data-ttu-id="82407-143">例如，若要開始追蹤新的 blog：</span><span class="sxs-lookup"><span data-stu-id="82407-143">For example, to start tracking a new blog:</span></span>

<!--
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Inserting_new_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Inserting_new_entities_1)]

<span data-ttu-id="82407-144">檢查此呼叫之後的 [變更追蹤器調試](xref:core/change-tracking/debug-views) 程式，會顯示內容正在追蹤狀態中的新實體 `Added` ：</span><span class="sxs-lookup"><span data-stu-id="82407-144">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following this call shows that the context is tracking the new entity in the `Added` state:</span></span>

```output
Blog {Id: 1} Added
  Id: 1 PK
  Name: '.NET Blog'
  Posts: []
```

<span data-ttu-id="82407-145">不過，Add 方法不只是在個別實體上運作。</span><span class="sxs-lookup"><span data-stu-id="82407-145">However, the Add methods don't just work on an individual entity.</span></span> <span data-ttu-id="82407-146">它們實際上會開始追蹤 _相關實體的整個圖形_，將它們全部放在 `Added` 狀態。</span><span class="sxs-lookup"><span data-stu-id="82407-146">They actually start tracking an _entire graph of related entities_, putting them all to the `Added` state.</span></span> <span data-ttu-id="82407-147">例如，若要插入新的 blog 和相關的新文章：</span><span class="sxs-lookup"><span data-stu-id="82407-147">For example, to insert a new blog and associated new posts:</span></span>

<!--
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Inserting_new_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Inserting_new_entities_2)]

<span data-ttu-id="82407-148">內容現在會將這些實體追蹤為 `Added` ：</span><span class="sxs-lookup"><span data-stu-id="82407-148">The context is now tracking all these entities as `Added`:</span></span>

```output
Blog {Id: 1} Added
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Added
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Added
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="82407-149">請注意，已針對 `Id` 上述範例中的索引鍵屬性設定明確值。</span><span class="sxs-lookup"><span data-stu-id="82407-149">Notice that explicit values have been set for the `Id` key properties in the examples above.</span></span> <span data-ttu-id="82407-150">這是因為這裡的模型已設定為使用明確設定的索引鍵值，而不是自動產生的索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="82407-150">This is because the model here has been configured to use explicitly set key values, rather than automatically generated key values.</span></span> <span data-ttu-id="82407-151">未使用產生的索引鍵時，必須在呼叫 _之前_ 明確設定索引鍵屬性 `Add` 。</span><span class="sxs-lookup"><span data-stu-id="82407-151">When not using generated keys, the key properties must be explicitly set _before_ calling `Add`.</span></span> <span data-ttu-id="82407-152">然後在呼叫 SaveChanges 時插入這些索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="82407-152">These key values are then inserted when SaveChanges is called.</span></span> <span data-ttu-id="82407-153">例如，在使用 SQLite 時：</span><span class="sxs-lookup"><span data-stu-id="82407-153">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Blogs" ("Id", "Name")
VALUES (@p0, @p1);

-- Executed DbCommand (0ms) [Parameters=[@p2='1' (DbType = String), @p3='1' (DbType = String), @p4='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p5='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("Id", "BlogId", "Content", "Title")
VALUES (@p2, @p3, @p4, @p5);

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String), @p1='1' (DbType = String), @p2='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p3='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("Id", "BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2, @p3);
```

<span data-ttu-id="82407-154">在 SaveChanges 完成之後，這些實體都會在狀態中追蹤 `Unchanged` ，因為這些實體現在存在於資料庫中：</span><span class="sxs-lookup"><span data-stu-id="82407-154">All of these entities are tracked in the `Unchanged` state after SaveChanges completes, since these entities now exist in the database:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
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

### <a name="generated-key-values"></a><span data-ttu-id="82407-155">產生的索引鍵值</span><span class="sxs-lookup"><span data-stu-id="82407-155">Generated key values</span></span>

<span data-ttu-id="82407-156">如上所述，根據預設，會將整數和 GUID 索引 [鍵屬性](xref:core/modeling/keys) 設定為使用 [自動產生](xref:core/modeling/generated-properties) 的索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="82407-156">As mentioned above, integer and GUID [key properties](xref:core/modeling/keys) are configured to use [automatically generated key values](xref:core/modeling/generated-properties) by default.</span></span> <span data-ttu-id="82407-157">這表示應用程式 _不得明確地設定任何索引鍵值_。</span><span class="sxs-lookup"><span data-stu-id="82407-157">This means that the application _must not set any key value explicitly_.</span></span> <span data-ttu-id="82407-158">例如，若要插入新的 blog，並以產生的金鑰值張貼所有文章：</span><span class="sxs-lookup"><span data-stu-id="82407-158">For example, to insert a new blog and posts all with generated key values:</span></span>

<!--
            context.Add(
                new Blog
                {
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Inserting_new_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Inserting_new_entities_3)]

<span data-ttu-id="82407-159">如同明確的索引鍵值，內容現在會將這些實體追蹤為 `Added` ：</span><span class="sxs-lookup"><span data-stu-id="82407-159">As with explicit key values, the context is now tracking all these entities as `Added`:</span></span>

```output
Blog {Id: -2147482644} Added
  Id: -2147482644 PK Temporary
  Name: '.NET Blog'
  Posts: [{Id: -2147482637}, {Id: -2147482636}]
Post {Id: -2147482637} Added
  Id: -2147482637 PK Temporary
  BlogId: -2147482644 FK Temporary
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: -2147482644}
Post {Id: -2147482636} Added
  Id: -2147482636 PK Temporary
  BlogId: -2147482644 FK Temporary
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: -2147482644}
```

<span data-ttu-id="82407-160">請注意，在此案例中，已為每個實體產生 [暫存](xref:core/change-tracking/miscellaneous#temporary-values) 索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="82407-160">Notice in this case that [temporary key values](xref:core/change-tracking/miscellaneous#temporary-values) have been generated for each entity.</span></span> <span data-ttu-id="82407-161">EF Core 會使用這些值，直到呼叫 SaveChanges 為止，此時會從資料庫中讀取實際的索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="82407-161">These values are used by EF Core until SaveChanges is called, at which point real key values are read back from the database.</span></span> <span data-ttu-id="82407-162">例如，在使用 SQLite 時：</span><span class="sxs-lookup"><span data-stu-id="82407-162">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Blogs" ("Name")
VALUES (@p0);
SELECT "Id"
FROM "Blogs"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p2='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p3='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p1, @p2, @p3);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

<span data-ttu-id="82407-163">完成 SaveChanges 之後，所有實體都會以其真正的索引鍵值進行更新，並在狀態中追蹤， `Unchanged` 因為它們現在符合資料庫中的狀態：</span><span class="sxs-lookup"><span data-stu-id="82407-163">After SaveChanges completes, all of the entities have been updated with their real key values and are tracked in the `Unchanged` state since they now match the state in the database:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
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

<span data-ttu-id="82407-164">這與上一個使用明確索引鍵值的範例完全相同。</span><span class="sxs-lookup"><span data-stu-id="82407-164">This is exactly the same end-state as the previous example that used explicit key values.</span></span>

> [!TIP]
> <span data-ttu-id="82407-165">即使使用產生的索引鍵值，仍然可以設定明確的索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="82407-165">An explicit key value can still be set even when using generated key values.</span></span> <span data-ttu-id="82407-166">EF Core 接著會嘗試使用此索引鍵值來插入。</span><span class="sxs-lookup"><span data-stu-id="82407-166">EF Core will then attempt to insert using this key value.</span></span> <span data-ttu-id="82407-167">某些資料庫設定（包括具有識別資料行的 SQL Server）不支援這類插入，且將會擲回。</span><span class="sxs-lookup"><span data-stu-id="82407-167">Some database configurations, including SQL Server with Identity columns, do not support such inserts and will throw.</span></span>

## <a name="attaching-existing-entities"></a><span data-ttu-id="82407-168">附加現有的實體</span><span class="sxs-lookup"><span data-stu-id="82407-168">Attaching existing entities</span></span>

### <a name="explicit-key-values"></a><span data-ttu-id="82407-169">明確的索引鍵值</span><span class="sxs-lookup"><span data-stu-id="82407-169">Explicit key values</span></span>

<span data-ttu-id="82407-170">從查詢傳回的實體會在狀態中追蹤 `Unchanged` 。</span><span class="sxs-lookup"><span data-stu-id="82407-170">Entities returned from queries are tracked in the `Unchanged` state.</span></span> <span data-ttu-id="82407-171">`Unchanged`狀態表示實體自從查詢之後尚未經過修改。</span><span class="sxs-lookup"><span data-stu-id="82407-171">The `Unchanged` state means that the entity has not been modified since it was queried.</span></span> <span data-ttu-id="82407-172">已中斷連線的實體（可能是從 HTTP 要求中的 web 用戶端傳回）可以使用 <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> 、 <xref:Microsoft.EntityFrameworkCore.DbContext.AttachRange%2A?displayProperty=nameWithType> 或中的對等方法，進入此狀態 <xref:Microsoft.EntityFrameworkCore.DbSet%601> 。</span><span class="sxs-lookup"><span data-stu-id="82407-172">A disconnected entity, perhaps returned from a web client in an HTTP request, can be put into this state using either <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.AttachRange%2A?displayProperty=nameWithType>, or the equivalent methods on <xref:Microsoft.EntityFrameworkCore.DbSet%601>.</span></span> <span data-ttu-id="82407-173">例如，若要開始追蹤現有的 blog：</span><span class="sxs-lookup"><span data-stu-id="82407-173">For example, to start tracking an existing blog:</span></span>

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Attaching_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Attaching_existing_entities_1)]

> [!NOTE]
> <span data-ttu-id="82407-174">這裡的範例是為了簡單起見，明確地建立實體 `new` 。</span><span class="sxs-lookup"><span data-stu-id="82407-174">The examples here are creating entities explicitly with `new` for simplicity.</span></span> <span data-ttu-id="82407-175">一般來說，實體實例會來自另一個來源，例如從用戶端還原序列化，或從 HTTP Post 中的資料建立。</span><span class="sxs-lookup"><span data-stu-id="82407-175">Normally the entity instances will have come from another source, such as being deserialized from a client, or being created from data in an HTTP Post.</span></span>

<span data-ttu-id="82407-176">檢查此呼叫之後的 [變更追蹤器調試](xref:core/change-tracking/debug-views) 程式，會顯示在狀態中追蹤的實體 `Unchanged` ：</span><span class="sxs-lookup"><span data-stu-id="82407-176">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following this call shows that the entity is tracked in the `Unchanged` state:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: []
```

<span data-ttu-id="82407-177">就像 `Add` ， `Attach` 實際上會將連接實體的整個圖形設定為 `Unchanged` 狀態。</span><span class="sxs-lookup"><span data-stu-id="82407-177">Just like `Add`, `Attach` actually sets an entire graph of connected entities to the `Unchanged` state.</span></span> <span data-ttu-id="82407-178">例如，若要附加現有的 blog 和相關聯的現有文章：</span><span class="sxs-lookup"><span data-stu-id="82407-178">For example, to attach an existing blog and associated existing posts:</span></span>

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Attaching_existing_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Attaching_existing_entities_2)]

<span data-ttu-id="82407-179">內容現在會將這些實體追蹤為 `Unchanged` ：</span><span class="sxs-lookup"><span data-stu-id="82407-179">The context is now tracking all these entities as `Unchanged`:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
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

<span data-ttu-id="82407-180">此時呼叫 SaveChanges 將沒有任何作用。</span><span class="sxs-lookup"><span data-stu-id="82407-180">Calling SaveChanges at this point will have no effect.</span></span> <span data-ttu-id="82407-181">所有實體都會標示為 `Unchanged` ，因此資料庫中不會更新任何專案。</span><span class="sxs-lookup"><span data-stu-id="82407-181">All the entities are marked as `Unchanged`, so there is nothing to update in the database.</span></span>

### <a name="generated-key-values"></a><span data-ttu-id="82407-182">產生的索引鍵值</span><span class="sxs-lookup"><span data-stu-id="82407-182">Generated key values</span></span>

<span data-ttu-id="82407-183">如上所述，根據預設，會將整數和 GUID 索引 [鍵屬性](xref:core/modeling/keys) 設定為使用 [自動產生](xref:core/modeling/generated-properties) 的索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="82407-183">As mentioned above, integer and GUID [key properties](xref:core/modeling/keys) are configured to use [automatically generated key values](xref:core/modeling/generated-properties) by default.</span></span> <span data-ttu-id="82407-184">使用已中斷連線的實體時，這有一個主要優點：未設定的索引鍵值表示實體尚未插入資料庫。</span><span class="sxs-lookup"><span data-stu-id="82407-184">This has a major advantage when working with disconnected entities: an unset key value indicates that the entity has not yet been inserted into the database.</span></span> <span data-ttu-id="82407-185">這可讓變更追蹤器自動偵測新的實體，並將它們放入 `Added` 狀態。</span><span class="sxs-lookup"><span data-stu-id="82407-185">This allows the change tracker to automatically detect new entities and put them in the `Added` state.</span></span> <span data-ttu-id="82407-186">例如，請考慮附加這份有關 blog 和 post 的圖表：</span><span class="sxs-lookup"><span data-stu-id="82407-186">For example, consider attaching this graph of a blog and posts:</span></span>

```c#
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        },
                        new Post
                        {
                            Title = "Announcing .NET 5.0",
                            Content = ".NET 5.0 includes many enhancements, including single file applications, more..."
                        },
                    }
                });
```

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        },
                        new Post
                        {
                            Title = "Announcing .NET 5.0",
                            Content = ".NET 5.0 includes many enhancements, including single file applications, more..."
                        },
                    }
                });
-->
[!code-csharp[Attaching_existing_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Attaching_existing_entities_3)]

<span data-ttu-id="82407-187">此 blog 的索引鍵值為1，表示它已經存在於資料庫中。</span><span class="sxs-lookup"><span data-stu-id="82407-187">The blog has a key value of 1, indicating that it already exists in the database.</span></span> <span data-ttu-id="82407-188">這兩篇文章也會設定索引鍵值，但第三篇則否。</span><span class="sxs-lookup"><span data-stu-id="82407-188">Two of the posts also have key values set, but the third does not.</span></span> <span data-ttu-id="82407-189">EF Core 將會看到此索引鍵值為0，這是一個整數的 CLR 預設值。</span><span class="sxs-lookup"><span data-stu-id="82407-189">EF Core will see this key value as 0, the CLR default for an integer.</span></span> <span data-ttu-id="82407-190">這會導致 EF Core 將新實體標示為， `Added` 而不是 `Unchanged` ：</span><span class="sxs-lookup"><span data-stu-id="82407-190">This results in EF Core marking the new entity as `Added` instead of `Unchanged`:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482636}]
Post {Id: -2147482636} Added
  Id: -2147482636 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 includes many enhancements, including single file a...'
  Title: 'Announcing .NET 5.0'
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
```

<span data-ttu-id="82407-191">此時呼叫 SaveChanges 並不會對實體執行任何動作 `Unchanged` ，但是會將新的實體插入資料庫中。</span><span class="sxs-lookup"><span data-stu-id="82407-191">Calling SaveChanges at this point does nothing with the `Unchanged` entities, but inserts the new entity into the database.</span></span> <span data-ttu-id="82407-192">例如，在使用 SQLite 時：</span><span class="sxs-lookup"><span data-stu-id="82407-192">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 includes many enhancements, including single file applications, more...' (Size = 80), @p2='Announcing .NET 5.0' (Size = 19)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

<span data-ttu-id="82407-193">這裡要注意的重點是，使用產生的索引鍵值時，EF Core 可以 **自動區別已中斷連線圖形中現有實體的新** 專案。</span><span class="sxs-lookup"><span data-stu-id="82407-193">The important point to notice here is that, with generated key values, EF Core is able to **automatically distinguish new from existing entities in a disconnected graph**.</span></span> <span data-ttu-id="82407-194">總而言之，使用產生的索引鍵時，EF Core 一律會在實體未設定索引鍵值時插入實體。</span><span class="sxs-lookup"><span data-stu-id="82407-194">In a nutshell, when using generated keys, EF Core will always insert an entity when that entity has no key value set.</span></span>

## <a name="updating-existing-entities"></a><span data-ttu-id="82407-195">更新現有實體</span><span class="sxs-lookup"><span data-stu-id="82407-195">Updating existing entities</span></span>

### <a name="explicit-key-values"></a><span data-ttu-id="82407-196">明確的索引鍵值</span><span class="sxs-lookup"><span data-stu-id="82407-196">Explicit key values</span></span>

<span data-ttu-id="82407-197"><xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType>、 <xref:Microsoft.EntityFrameworkCore.DbContext.UpdateRange%2A?displayProperty=nameWithType> 和等方法的 <xref:Microsoft.EntityFrameworkCore.DbSet%601> 行為與 `Attach` 上面所述的方法完全相同，不同之處在于實體會放入 `Modfied` 而非 `Unchanged` 狀態中。</span><span class="sxs-lookup"><span data-stu-id="82407-197"><xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.UpdateRange%2A?displayProperty=nameWithType>, and the equivalent methods on <xref:Microsoft.EntityFrameworkCore.DbSet%601> behave exactly as the `Attach` methods described above, except that entities are put into the `Modfied` instead of the `Unchanged` state.</span></span> <span data-ttu-id="82407-198">例如，若要開始將現有的 blog 追蹤為 `Modified` ：</span><span class="sxs-lookup"><span data-stu-id="82407-198">For example, to start tracking an existing blog as `Modified`:</span></span>

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Updating_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Updating_existing_entities_1)]

<span data-ttu-id="82407-199">檢查此呼叫之後的 [變更追蹤器調試](xref:core/change-tracking/debug-views) 程式，會顯示內容正在追蹤此實體的 `Modified` 狀態：</span><span class="sxs-lookup"><span data-stu-id="82407-199">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following this call shows that the context is tracking this entity in the `Modified` state:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: []
```

<span data-ttu-id="82407-200">如同 `Add` 和 `Attach` ，實際上會 `Update` 將相關實體的 _整個圖形_ 標示為 `Modified` 。</span><span class="sxs-lookup"><span data-stu-id="82407-200">Just like with `Add` and `Attach`, `Update` actually marks an _entire graph_ of related entities as `Modified`.</span></span> <span data-ttu-id="82407-201">例如，若要將現有的 blog 和相關聯的現有文章附加為 `Modified` ：</span><span class="sxs-lookup"><span data-stu-id="82407-201">For example, to attach an existing blog and associated existing posts as `Modified`:</span></span>

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Updating_existing_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Updating_existing_entities_2)]

<span data-ttu-id="82407-202">內容現在會將這些實體追蹤為 `Modified` ：</span><span class="sxs-lookup"><span data-stu-id="82407-202">The context is now tracking all these entities as `Modified`:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...' Modified
  Title: 'Announcing the Release of EF Core 5.0' Modified
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'F# 5 is the latest version of F#, the functional programming...' Modified
  Title: 'Announcing F# 5' Modified
  Blog: {Id: 1}
```

<span data-ttu-id="82407-203">此時呼叫 SaveChanges 會導致所有這些實體的更新傳送至資料庫。</span><span class="sxs-lookup"><span data-stu-id="82407-203">Calling SaveChanges at this point will cause updates to be sent to the database for all these entities.</span></span> <span data-ttu-id="82407-204">例如，在使用 SQLite 時：</span><span class="sxs-lookup"><span data-stu-id="82407-204">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='1' (DbType = String), @p0='1' (DbType = String), @p1='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p2='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='2' (DbType = String), @p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();
```

### <a name="generated-key-values"></a><span data-ttu-id="82407-205">產生的索引鍵值</span><span class="sxs-lookup"><span data-stu-id="82407-205">Generated key values</span></span>

<span data-ttu-id="82407-206">使用時 `Attach` ，產生的索引鍵值與的主要優點相同：未設定的索引鍵值 `Update` 表示實體是新的，而且尚未插入資料庫。</span><span class="sxs-lookup"><span data-stu-id="82407-206">As with `Attach`, generated key values have the same major benefit for `Update`: an unset key value indicates that the entity is new and has not yet been inserted into the database.</span></span> <span data-ttu-id="82407-207">同樣地 `Attach` ，這可讓 DbCoNtext 自動偵測新的實體，並將它們放入 `Added` 狀態。</span><span class="sxs-lookup"><span data-stu-id="82407-207">As with `Attach`, this allows the DbContext to automatically detect new entities and put them in the `Added` state.</span></span> <span data-ttu-id="82407-208">例如，請考慮 `Update` 使用此 blog 和文章的圖表來呼叫：</span><span class="sxs-lookup"><span data-stu-id="82407-208">For example, consider calling `Update` with this graph of a blog and posts:</span></span>

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Updating_existing_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Updating_existing_entities_3)]

<span data-ttu-id="82407-209">就像 `Attach` 範例一樣，會偵測到沒有金鑰值的 post，並將其設定為 `Added` 狀態。</span><span class="sxs-lookup"><span data-stu-id="82407-209">As with the `Attach` example, the post with no key value is detected as new and set to the `Added` state.</span></span> <span data-ttu-id="82407-210">其他實體會標示為 `Modified` ：</span><span class="sxs-lookup"><span data-stu-id="82407-210">The other entities are marked as `Modified`:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482633}]
Post {Id: -2147482633} Added
  Id: -2147482633 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 includes many enhancements, including single file a...'
  Title: 'Announcing .NET 5.0'
  Blog: {Id: 1}
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...' Modified
  Title: 'Announcing the Release of EF Core 5.0' Modified
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'F# 5 is the latest version of F#, the functional programming...' Modified
  Title: 'Announcing F# 5' Modified
  Blog: {Id: 1}
```

<span data-ttu-id="82407-211">此時呼叫 `SaveChanges` 會導致將更新傳送至資料庫，以供所有現有的實體，同時插入新的實體。</span><span class="sxs-lookup"><span data-stu-id="82407-211">Calling `SaveChanges` at this point will cause updates to be sent to the database for all the existing entities, while the new entity is inserted.</span></span> <span data-ttu-id="82407-212">例如，在使用 SQLite 時：</span><span class="sxs-lookup"><span data-stu-id="82407-212">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='1' (DbType = String), @p0='1' (DbType = String), @p1='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p2='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='2' (DbType = String), @p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 includes many enhancements, including single file applications, more...' (Size = 80), @p2='Announcing .NET 5.0' (Size = 19)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

<span data-ttu-id="82407-213">這是從中斷連線的圖形產生更新和插入的簡易方式。</span><span class="sxs-lookup"><span data-stu-id="82407-213">This is a very easy way to generate updates and inserts from a disconnected graph.</span></span> <span data-ttu-id="82407-214">不過，它會導致每個所追蹤實體的每個屬性都會將更新或插入傳送至資料庫，即使某些屬性值可能尚未變更也是一樣。</span><span class="sxs-lookup"><span data-stu-id="82407-214">However, it results in updates or inserts being sent to the database for every property of every tracked entity, even when some property values may not have been changed.</span></span> <span data-ttu-id="82407-215">不太害怕，對於具有小型圖形的許多應用程式而言，這可能是產生更新的簡單、實用方式。</span><span class="sxs-lookup"><span data-stu-id="82407-215">Don't be too scared by this; for many applications with small graphs, this can be an easy and pragmatic way of generating updates.</span></span> <span data-ttu-id="82407-216">話雖如此，其他更複雜的模式有時可能會產生更有效率的更新，如 [EF Core 的身分識別解析](xref:core/change-tracking/identity-resolution)中所述。</span><span class="sxs-lookup"><span data-stu-id="82407-216">That being said, other more complex patterns can sometimes result in more efficient updates, as described in [Identity Resolution in EF Core](xref:core/change-tracking/identity-resolution).</span></span>

## <a name="deleting-existing-entities"></a><span data-ttu-id="82407-217">刪除現有實體</span><span class="sxs-lookup"><span data-stu-id="82407-217">Deleting existing entities</span></span>

<span data-ttu-id="82407-218">對於要由 SaveChanges 刪除的實體，必須在狀態中追蹤 `Deleted` 。</span><span class="sxs-lookup"><span data-stu-id="82407-218">For an entity to be deleted by SaveChanges it must be tracked in the `Deleted` state.</span></span> <span data-ttu-id="82407-219">實體通常會藉 `Deleted` 由呼叫的其中一個 <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> 、或對 <xref:Microsoft.EntityFrameworkCore.DbContext.RemoveRange%2A?displayProperty=nameWithType> 等的方法來置於狀態 <xref:Microsoft.EntityFrameworkCore.DbSet%601> 。</span><span class="sxs-lookup"><span data-stu-id="82407-219">Entities are typically put in the `Deleted` state by calling one of <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.RemoveRange%2A?displayProperty=nameWithType>, or the equivalent methods on <xref:Microsoft.EntityFrameworkCore.DbSet%601>.</span></span> <span data-ttu-id="82407-220">例如，若要將現有的文章標示為 `Deleted` ：</span><span class="sxs-lookup"><span data-stu-id="82407-220">For example, to mark an existing post as `Deleted`:</span></span>

<!--
            context.Remove(
                new Post
                {
                    Id = 2
                });
-->
[!code-csharp[Deleting_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_existing_entities_1)]

<span data-ttu-id="82407-221">檢查此呼叫之後的 [變更追蹤器調試](xref:core/change-tracking/debug-views) 程式，會顯示內容正在追蹤狀態中的實體 `Deleted` ：</span><span class="sxs-lookup"><span data-stu-id="82407-221">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following this call shows that the context is tracking the entity in the `Deleted` state:</span></span>

```output
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: <null> FK
  Content: <null>
  Title: <null>
  Blog: <null>
```

<span data-ttu-id="82407-222">呼叫 SaveChanges 時，將會刪除此實體。</span><span class="sxs-lookup"><span data-stu-id="82407-222">This entity will be deleted when SaveChanges is called.</span></span> <span data-ttu-id="82407-223">例如，在使用 SQLite 時：</span><span class="sxs-lookup"><span data-stu-id="82407-223">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

<span data-ttu-id="82407-224">完成 SaveChanges 之後，已刪除的實體會從 DbCoNtext 卸離，因為它已不存在於資料庫中。</span><span class="sxs-lookup"><span data-stu-id="82407-224">After SaveChanges completes, the deleted entity is detached from the DbContext since it no longer exists in the database.</span></span> <span data-ttu-id="82407-225">因為沒有正在追蹤的實體，所以 debug view 是空的。</span><span class="sxs-lookup"><span data-stu-id="82407-225">The debug view is therefore empty because no entities are being tracked.</span></span>

## <a name="deleting-dependentchild-entities"></a><span data-ttu-id="82407-226">刪除相依/子系實體</span><span class="sxs-lookup"><span data-stu-id="82407-226">Deleting dependent/child entities</span></span>

<span data-ttu-id="82407-227">從圖形刪除相依/子系實體，比刪除主體/父實體更簡單。</span><span class="sxs-lookup"><span data-stu-id="82407-227">Deleting dependent/child entities from a graph is more straightforward than deleting principal/parent entities.</span></span> <span data-ttu-id="82407-228">如需詳細資訊，請參閱下一節及 [變更外鍵和](xref:core/change-tracking/relationship-changes) 導覽。</span><span class="sxs-lookup"><span data-stu-id="82407-228">See the next section and [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for more information.</span></span>

<span data-ttu-id="82407-229">`Remove`在以建立的實體上呼叫是不尋常的 `new` 。</span><span class="sxs-lookup"><span data-stu-id="82407-229">It is unusual to call `Remove` on an entity created with `new`.</span></span> <span data-ttu-id="82407-230">此外，與和不同的是，在 `Add` `Attach` `Update` `Remove` 或狀態中尚未追蹤的實體上呼叫是很罕見的 `Unchanged` `Modified` 。</span><span class="sxs-lookup"><span data-stu-id="82407-230">Further, unlike `Add`, `Attach` and `Update`, it is uncommon to call `Remove` on an entity that isn't already tracked in the `Unchanged` or `Modified` state.</span></span> <span data-ttu-id="82407-231">相反地，通常會追蹤相關實體的單一實體或圖形，然後呼叫 `Remove` 應該刪除的實體。</span><span class="sxs-lookup"><span data-stu-id="82407-231">Instead it is typical to track a single entity or graph of related entities, and then call `Remove` on the entities that should be deleted.</span></span> <span data-ttu-id="82407-232">追蹤實體的這個圖形通常是透過下列其中一種方式來建立：</span><span class="sxs-lookup"><span data-stu-id="82407-232">This graph of tracked entities is typically created by either:</span></span>

1. <span data-ttu-id="82407-233">執行實體的查詢</span><span class="sxs-lookup"><span data-stu-id="82407-233">Running a query for the entities</span></span>
2. <span data-ttu-id="82407-234">在已 `Attach` `Update` 中斷連線的實體圖形上使用或方法，如先前章節所述。</span><span class="sxs-lookup"><span data-stu-id="82407-234">Using the `Attach` or `Update` methods on a graph of disconnected entities, as described in the preceding sections.</span></span>

<span data-ttu-id="82407-235">例如，上一節中的程式碼比較可能從用戶端取得 post，然後執行如下的動作：</span><span class="sxs-lookup"><span data-stu-id="82407-235">For example, the code in the previous section is more likely obtain a post from a client and then do something like this:</span></span>

<!--
            context.Attach(post);
            context.Remove(post);
-->
[!code-csharp[Deleting_dependent_child_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_dependent_child_entities_1)]

<span data-ttu-id="82407-236">這與上一個範例的行為完全相同，因為 `Remove` 在取消追蹤的實體上呼叫會導致它先附加，然後標記為 `Deleted` 。</span><span class="sxs-lookup"><span data-stu-id="82407-236">This behaves exactly the same way as the previous example, since calling `Remove` on an un-tracked entity causes it to first be attached and then marked as `Deleted`.</span></span>

<span data-ttu-id="82407-237">在更實際的範例中，會先附加實體的圖表，然後將某些實體標示為已刪除。</span><span class="sxs-lookup"><span data-stu-id="82407-237">In more realistic examples, a graph of entities is first attached, and then some of those entities are marked as deleted.</span></span> <span data-ttu-id="82407-238">例如：</span><span class="sxs-lookup"><span data-stu-id="82407-238">For example:</span></span>

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_dependent_child_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_dependent_child_entities_2)]

<span data-ttu-id="82407-239">所有實體都會標示為 `Unchanged` ，但呼叫的實體除外 `Remove` ：</span><span class="sxs-lookup"><span data-stu-id="82407-239">All entities are marked as `Unchanged`, except the one on which `Remove` was called:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
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

<span data-ttu-id="82407-240">呼叫 SaveChanges 時，將會刪除此實體。</span><span class="sxs-lookup"><span data-stu-id="82407-240">This entity will be deleted when SaveChanges is called.</span></span> <span data-ttu-id="82407-241">例如，在使用 SQLite 時：</span><span class="sxs-lookup"><span data-stu-id="82407-241">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

<span data-ttu-id="82407-242">完成 SaveChanges 之後，已刪除的實體會從 DbCoNtext 卸離，因為它已不存在於資料庫中。</span><span class="sxs-lookup"><span data-stu-id="82407-242">After SaveChanges completes, the deleted entity is detached from the DbContext since it no longer exists in the database.</span></span> <span data-ttu-id="82407-243">其他實體仍處於 `Unchanged` 狀態：</span><span class="sxs-lookup"><span data-stu-id="82407-243">Other entities remain in the `Unchanged` state:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
```

## <a name="deleting-principalparent-entities"></a><span data-ttu-id="82407-244">刪除主體/父實體</span><span class="sxs-lookup"><span data-stu-id="82407-244">Deleting principal/parent entities</span></span>

<span data-ttu-id="82407-245">連接兩個實體類型的每個關聯性都有一個主體或父端，以及一個相依或子系。</span><span class="sxs-lookup"><span data-stu-id="82407-245">Each relationship that connects two entity types has a principal or parent end, and a dependent or child end.</span></span> <span data-ttu-id="82407-246">相依/子系實體是具有外鍵屬性的實體。</span><span class="sxs-lookup"><span data-stu-id="82407-246">The dependent/child entity is the one with the foreign key property.</span></span> <span data-ttu-id="82407-247">在一對多關聯性中，主體/父系是在「一」端，而相依/子系位於「多」端。</span><span class="sxs-lookup"><span data-stu-id="82407-247">In a one-to-many relationship, the principal/parent is on the "one" side, and the dependent/child is on the "many" side.</span></span> <span data-ttu-id="82407-248">如需詳細資訊，請參閱 [關聯](xref:core/modeling/relationships) 性。</span><span class="sxs-lookup"><span data-stu-id="82407-248">See [Relationships](xref:core/modeling/relationships) for more information.</span></span>

<span data-ttu-id="82407-249">在上述範例中，我們會刪除貼文，也就是 blog 文章一對多關聯性中的相依/子系實體。</span><span class="sxs-lookup"><span data-stu-id="82407-249">In the preceding examples we were deleting a post, which is a dependent/child entity in the blog-posts one-to-many relationship.</span></span> <span data-ttu-id="82407-250">這相當簡單，因為移除相依/子系實體並不會影響其他實體。</span><span class="sxs-lookup"><span data-stu-id="82407-250">This is relatively straightforward since removal of a dependent/child entity does not have any impact on other entities.</span></span> <span data-ttu-id="82407-251">另一方面，刪除主體/父實體也必須影響任何相依/子系實體。</span><span class="sxs-lookup"><span data-stu-id="82407-251">On the other hand, deleting a principal/parent entity must also impact any dependent/child entities.</span></span> <span data-ttu-id="82407-252">若未這麼做，則會保留外鍵值，參考已不存在的主鍵值。</span><span class="sxs-lookup"><span data-stu-id="82407-252">Not doing so would leave a foreign key value referencing a primary key value that no longer exists.</span></span> <span data-ttu-id="82407-253">這是不正確模型狀態，而且在大部分資料庫中都會產生參考條件約束錯誤。</span><span class="sxs-lookup"><span data-stu-id="82407-253">This is an invalid model state and results in a referential constraint error in most databases.</span></span>

<span data-ttu-id="82407-254">您可以透過兩種方式來處理這個不正確模型狀態：</span><span class="sxs-lookup"><span data-stu-id="82407-254">This invalid model state can be handled in two ways:</span></span>

1. <span data-ttu-id="82407-255">將 FK 值設定為 null。</span><span class="sxs-lookup"><span data-stu-id="82407-255">Setting FK values to null.</span></span> <span data-ttu-id="82407-256">這表示相依項/子系不再與任何主體/父系相關。</span><span class="sxs-lookup"><span data-stu-id="82407-256">This indicates that the dependents/children are no longer related to any principal/parent.</span></span> <span data-ttu-id="82407-257">這是選擇性關聯性的預設值，其中外鍵必須是可為 null。</span><span class="sxs-lookup"><span data-stu-id="82407-257">This is the default for optional relationships where the foreign key must be nullable.</span></span> <span data-ttu-id="82407-258">將 FK 設定為 null 對必要的關聯性而言是不正確，因為外鍵通常不能為 null。</span><span class="sxs-lookup"><span data-stu-id="82407-258">Setting the FK to null is not valid for required relationships, where the foreign key is typically non-nullable.</span></span>
2. <span data-ttu-id="82407-259">正在刪除相依項/子系。</span><span class="sxs-lookup"><span data-stu-id="82407-259">Deleting the the dependents/children.</span></span> <span data-ttu-id="82407-260">這是必要關聯性的預設值，也適用于選擇性的關聯性。</span><span class="sxs-lookup"><span data-stu-id="82407-260">This is the default for required relationships, and is also valid for optional relationships.</span></span>

<span data-ttu-id="82407-261">如需變更追蹤和關聯性的詳細資訊，請參閱變更 [外鍵和](xref:core/change-tracking/relationship-changes) 導覽。</span><span class="sxs-lookup"><span data-stu-id="82407-261">See [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for detailed information on change tracking and relationships.</span></span>

### <a name="optional-relationships"></a><span data-ttu-id="82407-262">組織關係</span><span class="sxs-lookup"><span data-stu-id="82407-262">Optional relationships</span></span>

<span data-ttu-id="82407-263">在 `Post.BlogId` 我們使用的模型中，外鍵屬性可為 null。</span><span class="sxs-lookup"><span data-stu-id="82407-263">The `Post.BlogId` foreign key property is nullable in the model we have been using.</span></span> <span data-ttu-id="82407-264">這表示關聯性是選擇性的，因此 EF Core 的預設行為是在 `BlogId` 刪除 blog 時將外鍵屬性設定為 null。</span><span class="sxs-lookup"><span data-stu-id="82407-264">This means the relationship is optional, and hence the default behavior of EF Core is to set `BlogId` foreign key properties to null when the blog is deleted.</span></span> <span data-ttu-id="82407-265">例如：</span><span class="sxs-lookup"><span data-stu-id="82407-265">For example:</span></span>

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_principal_parent_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_principal_parent_entities_1)]

<span data-ttu-id="82407-266">在呼叫後檢查 [變更追蹤](xref:core/change-tracking/debug-views) 器的偵錯工具，就 `Remove` 會如預期般將 blog 標示為 `Deleted` ：</span><span class="sxs-lookup"><span data-stu-id="82407-266">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following the call to `Remove` shows that, as expected, the blog is now marked as `Deleted`:</span></span>

```output
Blog {Id: 1} Deleted
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: <null>
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
```

<span data-ttu-id="82407-267">更有趣的是，所有相關貼文現在都會標示為 `Modified` 。</span><span class="sxs-lookup"><span data-stu-id="82407-267">More interestingly, all the related posts are now marked as `Modified`.</span></span> <span data-ttu-id="82407-268">這是因為每個實體中的外鍵屬性都已設為 null。</span><span class="sxs-lookup"><span data-stu-id="82407-268">This is because the foreign key property in each entity has been set to null.</span></span> <span data-ttu-id="82407-269">呼叫 SaveChanges 會先在資料庫中，將每個 post 的外鍵值更新為 null，然後再刪除 blog：</span><span class="sxs-lookup"><span data-stu-id="82407-269">Calling SaveChanges updates the foreign key value for each post to null in the database, before then deleting the blog:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='2' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p2='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Blogs"
WHERE "Id" = @p2;
SELECT changes();
```

<span data-ttu-id="82407-270">完成 SaveChanges 之後，已刪除的實體會從 DbCoNtext 卸離，因為它已不存在於資料庫中。</span><span class="sxs-lookup"><span data-stu-id="82407-270">After SaveChanges completes, the deleted entity is detached from the DbContext since it no longer exists in the database.</span></span> <span data-ttu-id="82407-271">其他實體現在會標示為 `Unchanged` 具有 null 外鍵值，這些值符合資料庫的狀態：</span><span class="sxs-lookup"><span data-stu-id="82407-271">Other entities are now marked as `Unchanged` with null foreign key values, which matches the state of the database:</span></span>

```output
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: <null> FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: <null>
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: <null> FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
```

### <a name="required-relationships"></a><span data-ttu-id="82407-272">必要關係</span><span class="sxs-lookup"><span data-stu-id="82407-272">Required relationships</span></span>

<span data-ttu-id="82407-273">如果 `Post.BlogId` 外鍵屬性不可為 null，則 blog 和 post 之間的關聯性會變成「必要」。</span><span class="sxs-lookup"><span data-stu-id="82407-273">If the `Post.BlogId` foreign key property is non-nullable, then the relationship between blogs and posts becomes "required".</span></span> <span data-ttu-id="82407-274">在這種情況下，EF Core 預設會在刪除主體/父系時，刪除相依/子系實體。</span><span class="sxs-lookup"><span data-stu-id="82407-274">In this situation, EF Core will, by default, delete dependent/child entities when the principal/parent is deleted.</span></span> <span data-ttu-id="82407-275">例如，刪除包含相關文章的 blog，如先前的範例所示：</span><span class="sxs-lookup"><span data-stu-id="82407-275">For example, deleting a blog with related posts as in the previous example:</span></span>

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_principal_parent_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysRequiredSamples.cs?name=Deleting_principal_parent_entities_1)]

<span data-ttu-id="82407-276">在呼叫後檢查 [變更追蹤](xref:core/change-tracking/debug-views) 器的偵錯工具，就 `Remove` 會如預期般將 blog 標示為 `Deleted` ：</span><span class="sxs-lookup"><span data-stu-id="82407-276">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following the call to `Remove` shows that, as expected, the blog is again marked as `Deleted`:</span></span>

```output
Blog {Id: 1} Deleted
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Deleted
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

<span data-ttu-id="82407-277">在此情況下，更有趣的是，所有相關文章也都標示為 `Deleted` 。</span><span class="sxs-lookup"><span data-stu-id="82407-277">More interestingly in this case is that all related posts have also been marked as `Deleted`.</span></span> <span data-ttu-id="82407-278">呼叫 SaveChanges 會導致從資料庫中刪除 blog 和所有相關文章：</span><span class="sxs-lookup"><span data-stu-id="82407-278">Calling SaveChanges causes the blog and all related posts to be deleted from the database:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Blogs"
WHERE "Id" = @p1;
```

<span data-ttu-id="82407-279">完成 SaveChanges 之後，所有刪除的實體都會從 DbCoNtext 卸離，因為它們不再存在於資料庫中。</span><span class="sxs-lookup"><span data-stu-id="82407-279">After SaveChanges completes, all the deleted entities are detached from the DbContext since they no longer exist in the database.</span></span> <span data-ttu-id="82407-280">因此，偵錯工具的輸出會是空的。</span><span class="sxs-lookup"><span data-stu-id="82407-280">Output from the debug view is therefore empty.</span></span>

> [!NOTE]
> <span data-ttu-id="82407-281">這份檔只會在 EF Core 中使用關聯性的表面上有劃痕。</span><span class="sxs-lookup"><span data-stu-id="82407-281">This document only scratches the surface on working with relationships in EF Core.</span></span> <span data-ttu-id="82407-282">如需有關模型關聯性的詳細資訊，[以及如何](xref:core/change-tracking/relationship-changes)在呼叫 SaveChanges 時更新/刪除相依/子系實體的詳細資訊，請參閱[關聯](xref:core/modeling/relationships)性。</span><span class="sxs-lookup"><span data-stu-id="82407-282">See [Relationships](xref:core/modeling/relationships) for more information on modeling relationships, and [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for more information on updating/deleting dependent/child entities when calling SaveChanges.</span></span>

## <a name="custom-tracking-with-trackgraph"></a><span data-ttu-id="82407-283">使用 TrackGraph 的自訂追蹤</span><span class="sxs-lookup"><span data-stu-id="82407-283">Custom tracking with TrackGraph</span></span>

<span data-ttu-id="82407-284"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> 的運作方式類似 `Add` ，但 `Attach` `Update` 它會在追蹤之前為每個實體實例產生回呼。</span><span class="sxs-lookup"><span data-stu-id="82407-284"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> works like `Add`, `Attach` and `Update` except that it generates a callback for every entity instance before tracking it.</span></span> <span data-ttu-id="82407-285">這可讓您在決定如何追蹤圖形中的個別實體時使用自訂邏輯。</span><span class="sxs-lookup"><span data-stu-id="82407-285">This allows custom logic to be used when determining how to track individual entities in a graph.</span></span>

<span data-ttu-id="82407-286">例如，請考慮在使用產生的索引鍵值追蹤實體時，EF Core 所使用的規則：如果 kye 值為零，則實體是新的，而且應該插入。</span><span class="sxs-lookup"><span data-stu-id="82407-286">For example, consider the rule EF Core uses when tracking entities with generated key values: if the kye value is zero, then the entity is new and should be inserted.</span></span> <span data-ttu-id="82407-287">讓我們擴充此規則，以找出索引鍵值是否為負數，則應該刪除實體。</span><span class="sxs-lookup"><span data-stu-id="82407-287">Let's extend this rule to say if the key value is negative, then the entity should be deleted.</span></span> <span data-ttu-id="82407-288">這可讓我們變更已中斷連線圖形之實體中的主鍵值，以標記已刪除的實體：</span><span class="sxs-lookup"><span data-stu-id="82407-288">This allows us to change the primary key values in entities of a disconnected graph to mark deleted entities:</span></span>

<!--
            blog.Posts.Add(
                new Post
                {
                    Title = "Announcing .NET 5.0",
                    Content = ".NET 5.0 includes many enhancements, including single file applications, more..."
                }
            );

            var toDelete = blog.Posts.Single(e => e.Title == "Announcing F# 5");
            toDelete.Id = -toDelete.Id;
-->
[!code-csharp[Custom_tracking_with_TrackGraph_1a](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Custom_tracking_with_TrackGraph_1a)]

<span data-ttu-id="82407-289">然後，您可以使用 TrackGraph 來追蹤這個已中斷連線的圖形：</span><span class="sxs-lookup"><span data-stu-id="82407-289">This disconnected graph can then be tracked using TrackGraph:</span></span>

<!--
        public static void UpdateBlog(Blog blog)
        {
            using var context = new BlogsContext();

            context.ChangeTracker.TrackGraph(
                blog, node =>
                    {
                        var propertyEntry = node.Entry.Property("Id");
                        var keyValue = (int)propertyEntry.CurrentValue;

                        if (keyValue == 0)
                        {
                            node.Entry.State = EntityState.Added;
                        }
                        else if (keyValue < 0)
                        {
                            propertyEntry.CurrentValue = -keyValue;
                            node.Entry.State = EntityState.Deleted;
                        }
                        else
                        {
                            node.Entry.State = EntityState.Modified;
                        }

                        Console.WriteLine($"Tracking {node.Entry.Metadata.DisplayName()} with key value {keyValue} as {node.Entry.State}");

                    });

            context.SaveChanges();
        }
-->
[!code-csharp[Custom_tracking_with_TrackGraph_1b](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Custom_tracking_with_TrackGraph_1b)]

<span data-ttu-id="82407-290">針對圖形中的每個實體，上述程式碼會在 _追蹤實體之前，先_ 檢查主鍵值。</span><span class="sxs-lookup"><span data-stu-id="82407-290">For each entity in the graph, the code above checks the primary key value _before tracking the entity_.</span></span> <span data-ttu-id="82407-291">針對取消設定 (零) 索引鍵值，程式碼會執行 EF Core 通常會執行的動作。</span><span class="sxs-lookup"><span data-stu-id="82407-291">For unset (zero) key values, the code does what EF Core would normally do.</span></span> <span data-ttu-id="82407-292">亦即，如果未設定索引鍵，則會將實體標示為 `Added` 。</span><span class="sxs-lookup"><span data-stu-id="82407-292">That is, if the key is not set, then the entity is marked as `Added`.</span></span> <span data-ttu-id="82407-293">如果已設定索引鍵且值為非負數，則會將實體標示為 `Modified` 。</span><span class="sxs-lookup"><span data-stu-id="82407-293">If the key is set and the value is non-negative, then the entity is marked as `Modified`.</span></span> <span data-ttu-id="82407-294">但是，如果找到負的索引鍵值，則會還原其真實的非負數值，並將該實體追蹤為 `Deleted` 。</span><span class="sxs-lookup"><span data-stu-id="82407-294">However, if a negative key value is found, then its real, non-negative value is restored and the entity is tracked as `Deleted`.</span></span>

<span data-ttu-id="82407-295">執行此程式碼的輸出為：</span><span class="sxs-lookup"><span data-stu-id="82407-295">The output from running this code is:</span></span>

```output
Tracking Blog with key value 1 as Modified
Tracking Post with key value 1 as Modified
Tracking Post with key value -2 as Deleted
Tracking Post with key value 0 as Added
```

> [!NOTE]
> <span data-ttu-id="82407-296">為了簡單起見，此程式碼假設每個實體都有一個稱為的整數主要索引鍵屬性 `Id` 。</span><span class="sxs-lookup"><span data-stu-id="82407-296">For simplicity, this code assumes each entity has an integer primary key property called `Id`.</span></span> <span data-ttu-id="82407-297">這可能會編寫至抽象基類或介面。</span><span class="sxs-lookup"><span data-stu-id="82407-297">This could be codified into an abstract base class or interface.</span></span> <span data-ttu-id="82407-298">或者，您可以從中繼資料取得 primary key 屬性或屬性， <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> 讓此程式碼能夠使用任何類型的實體。</span><span class="sxs-lookup"><span data-stu-id="82407-298">Alternately, the primary key property or properties could be obtained from the <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadata such that this code would work with any type of entity.</span></span>

<span data-ttu-id="82407-299">TrackGraph 有兩個多載。</span><span class="sxs-lookup"><span data-stu-id="82407-299">TrackGraph has two overloads.</span></span> <span data-ttu-id="82407-300">在上述使用的簡單多載中，EF Core 會決定何時停止遍歷圖形。</span><span class="sxs-lookup"><span data-stu-id="82407-300">In the simple overload used above, EF Core determines when to stop traversing the graph.</span></span> <span data-ttu-id="82407-301">具體而言，它會在該實體已被追蹤或回呼未開始追蹤實體時，停止從指定的實體造訪新的相關實體。</span><span class="sxs-lookup"><span data-stu-id="82407-301">Specifically, it stops visiting new related entities from a given entity when that entity is either already tracked, or when the callback does not start tracking the entity.</span></span>

<span data-ttu-id="82407-302">Advanced 多載（ <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%60%601(System.Object,%60%600,System.Func{Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntryGraphNode{%60%600},System.Boolean})?displayProperty=nameWithType> ）具有傳回 bool 的回呼。</span><span class="sxs-lookup"><span data-stu-id="82407-302">The advanced overload, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%60%601(System.Object,%60%600,System.Func{Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntryGraphNode{%60%600},System.Boolean})?displayProperty=nameWithType>, has a callback that returns a bool.</span></span> <span data-ttu-id="82407-303">如果回呼傳回 false，則會停止圖形遍歷，否則會繼續進行。</span><span class="sxs-lookup"><span data-stu-id="82407-303">If the callback returns false, then graph traversal stops, otherwise it continues.</span></span> <span data-ttu-id="82407-304">使用此多載時，必須小心避免無限迴圈。</span><span class="sxs-lookup"><span data-stu-id="82407-304">Care must be taken to avoid infinite loops when using this overload.</span></span>

<span data-ttu-id="82407-305">Advanced 多載也可讓您將狀態提供給 TrackGraph，此狀態接著會傳遞至每個回呼。</span><span class="sxs-lookup"><span data-stu-id="82407-305">The advanced overload also allows state to be supplied to TrackGraph and this state is then passed to each callback.</span></span>