---
title: "追蹤 vs。不是追蹤查詢的 EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: e17e060c-929f-4180-8883-40c438fbcc01
ms.technology: entity-framework-core
uid: core/querying/tracking
ms.openlocfilehash: 9a22c893f3b1e9991560e25e0252287a2844b39e
ms.sourcegitcommit: 3b6159db8a6c0653f13c7b528367b4e69ac3d51e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="tracking-vs-no-tracking-queries"></a><span data-ttu-id="97b7f-102">追蹤 vs。不追蹤查詢</span><span class="sxs-lookup"><span data-stu-id="97b7f-102">Tracking vs. No-Tracking Queries</span></span>

<span data-ttu-id="97b7f-103">Entity Framework Core 會保留在其變更追蹤程式中的實體執行個體的相關資訊，請追蹤行為的控制項。</span><span class="sxs-lookup"><span data-stu-id="97b7f-103">Tracking behavior controls whether or not Entity Framework Core will keep information about an entity instance in its change tracker.</span></span> <span data-ttu-id="97b7f-104">如果在追蹤實體，實體中偵測到的任何變更將會保留在資料庫`SaveChanges()`。</span><span class="sxs-lookup"><span data-stu-id="97b7f-104">If an entity is tracked, any changes detected in the entity will be persisted to the database during `SaveChanges()`.</span></span> <span data-ttu-id="97b7f-105">Entity Framework Core 將也 v-table 修正導覽屬性取得追蹤查詢的實體與先前已載入至 DbContext 執行個體的實體。</span><span class="sxs-lookup"><span data-stu-id="97b7f-105">Entity Framework Core will also fix-up navigation properties between entities that are obtained from a tracking query and entities that were previously loaded into the DbContext instance.</span></span>

> [!TIP]  
> <span data-ttu-id="97b7f-106">您可以檢視這篇文章[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)GitHub 上。</span><span class="sxs-lookup"><span data-stu-id="97b7f-106">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="tracking-queries"></a><span data-ttu-id="97b7f-107">追蹤查詢</span><span class="sxs-lookup"><span data-stu-id="97b7f-107">Tracking queries</span></span>

<span data-ttu-id="97b7f-108">根據預設，追蹤的查詢來傳回實體類型。</span><span class="sxs-lookup"><span data-stu-id="97b7f-108">By default, queries that return entity types are tracking.</span></span> <span data-ttu-id="97b7f-109">這表示您可以對這些實體執行個體中的變更和這些變更已保存`SaveChanges()`。</span><span class="sxs-lookup"><span data-stu-id="97b7f-109">This means you can make changes to those entity instances and have those changes persisted by `SaveChanges()`.</span></span>

<span data-ttu-id="97b7f-110">在下列範例中，部落格評等的變更會被偵測到並保存到資料庫期間`SaveChanges()`。</span><span class="sxs-lookup"><span data-stu-id="97b7f-110">In the following example, the change to the blogs rating will be detected and persisted to the database during `SaveChanges()`.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Tracking/Sample.cs)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.SingleOrDefault(b => b.BlogId == 1);
    blog.Rating = 5;
    context.SaveChanges();
}
```

## <a name="no-tracking-queries"></a><span data-ttu-id="97b7f-111">不追蹤查詢</span><span class="sxs-lookup"><span data-stu-id="97b7f-111">No-tracking queries</span></span>

<span data-ttu-id="97b7f-112">結果會以唯讀狀態的案例時，則沒有追蹤的查詢會很有用。</span><span class="sxs-lookup"><span data-stu-id="97b7f-112">No tracking queries are useful when the results are used in a read-only scenario.</span></span> <span data-ttu-id="97b7f-113">它們是更快速地執行，因為不需要安裝程式變更追蹤資訊。</span><span class="sxs-lookup"><span data-stu-id="97b7f-113">They are quicker to execute because there is no need to setup change tracking information.</span></span>

<span data-ttu-id="97b7f-114">您可以交換是沒有追蹤的個別查詢：</span><span class="sxs-lookup"><span data-stu-id="97b7f-114">You can swap an individual query to be no-tracking:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Tracking/Sample.cs?highlight=4)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs
        .AsNoTracking()
        .ToList();
}
```

<span data-ttu-id="97b7f-115">您也可以變更預設的追蹤內容執行個體層級的行為：</span><span class="sxs-lookup"><span data-stu-id="97b7f-115">You can also change the default tracking behavior at the context instance level:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Tracking/Sample.cs?highlight=3)] -->
``` csharp
using (var context = new BloggingContext())
{
    context.ChangeTracker.QueryTrackingBehavior = QueryTrackingBehavior.NoTracking;

    var blogs = context.Blogs.ToList();
}
```

> [!NOTE]  
> <span data-ttu-id="97b7f-116">沒有追蹤的查詢仍會執行識別解析內執行查詢。</span><span class="sxs-lookup"><span data-stu-id="97b7f-116">No tracking queries still perform identity resolution within the excuting query.</span></span> <span data-ttu-id="97b7f-117">如果結果集包含同一個實體多次，相同的實體類別的執行個體將會傳回結果集中的每個相符項目。</span><span class="sxs-lookup"><span data-stu-id="97b7f-117">If the result set contains the same entity multiple times, the same instance of the entity class will be returned for each occurrence in the result set.</span></span> <span data-ttu-id="97b7f-118">不過，弱式參考可用來追蹤已傳回的實體。</span><span class="sxs-lookup"><span data-stu-id="97b7f-118">However, weak references are used to keep track of entities that have already been returned.</span></span> <span data-ttu-id="97b7f-119">如果具有相同識別是之前的結果超出範圍，而且執行記憶體回收，可能會收到新的實體執行個體。</span><span class="sxs-lookup"><span data-stu-id="97b7f-119">If a previous result with the same identity goes out of scope, and garbage collection runs, you may get a new entity instance.</span></span> <span data-ttu-id="97b7f-120">如需詳細資訊，請參閱[查詢的運作方式](overview.md)。</span><span class="sxs-lookup"><span data-stu-id="97b7f-120">For more information, see [How Query Works](overview.md).</span></span>

## <a name="tracking-and-projections"></a><span data-ttu-id="97b7f-121">追蹤和預測</span><span class="sxs-lookup"><span data-stu-id="97b7f-121">Tracking and projections</span></span>

<span data-ttu-id="97b7f-122">即使查詢的結果型別不是實體類型，如果結果包含實體類型仍被追蹤的預設。</span><span class="sxs-lookup"><span data-stu-id="97b7f-122">Even if the result type of the query isn't an entity type, if the result contains entity types they will still be tracked by default.</span></span> <span data-ttu-id="97b7f-123">在下列查詢中，它會傳回匿名類型的執行個體`Blog`組將追蹤結果中。</span><span class="sxs-lookup"><span data-stu-id="97b7f-123">In the following query, which returns an anonymous type, the instances of `Blog` in the result set will be tracked.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Tracking/Sample.cs?highlight=7)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs
        .Select(b =>
            new
            {
                Blog = b,
                Posts = b.Posts.Count()
            });
}
```

<span data-ttu-id="97b7f-124">如果結果集未包含任何實體類型，則會不執行任何追蹤。</span><span class="sxs-lookup"><span data-stu-id="97b7f-124">If the result set does not contain any entity types, then no tracking is performed.</span></span> <span data-ttu-id="97b7f-125">在下列查詢中，它會傳回匿名型別有某些實體 （但實際的實體類型的任何執行個體） 的值，就不追蹤執行。</span><span class="sxs-lookup"><span data-stu-id="97b7f-125">In the following query, which returns an anonymous type with some of the values from the entity (but no instances of the actual entity type), there is no tracking performed.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Tracking/Sample.cs)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs
        .Select(b =>
            new
            {
                Id = b.BlogId,
                Url = b.Url
            });
}
```
