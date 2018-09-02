---
title: 追蹤與不追蹤的查詢 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e17e060c-929f-4180-8883-40c438fbcc01
uid: core/querying/tracking
ms.openlocfilehash: 985adc795f379199a3bacc985843f32f3168cf64
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993351"
---
# <a name="tracking-vs-no-tracking-queries"></a><span data-ttu-id="d08a1-102">追蹤與不追蹤的查詢</span><span class="sxs-lookup"><span data-stu-id="d08a1-102">Tracking vs. No-Tracking Queries</span></span>

<span data-ttu-id="d08a1-103">追蹤行為可控制 Entity Framework Core 是否將在其變更追蹤程式中保存有關實體執行個體的資訊。</span><span class="sxs-lookup"><span data-stu-id="d08a1-103">Tracking behavior controls whether or not Entity Framework Core will keep information about an entity instance in its change tracker.</span></span> <span data-ttu-id="d08a1-104">如果追蹤實體，即會在 `SaveChanges()` 期間，將實體中偵測到的任何變更保存於資料庫。</span><span class="sxs-lookup"><span data-stu-id="d08a1-104">If an entity is tracked, any changes detected in the entity will be persisted to the database during `SaveChanges()`.</span></span> <span data-ttu-id="d08a1-105">Entity Framework Core 也將在從追蹤查詢中取得的實體，以及先前已載入至 DbContext 執行個體的實體之間修正導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="d08a1-105">Entity Framework Core will also fix-up navigation properties between entities that are obtained from a tracking query and entities that were previously loaded into the DbContext instance.</span></span>

> [!TIP]  
> <span data-ttu-id="d08a1-106">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="d08a1-106">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="tracking-queries"></a><span data-ttu-id="d08a1-107">追蹤查詢</span><span class="sxs-lookup"><span data-stu-id="d08a1-107">Tracking queries</span></span>

<span data-ttu-id="d08a1-108">預設會追蹤傳回實體類型的查詢。</span><span class="sxs-lookup"><span data-stu-id="d08a1-108">By default, queries that return entity types are tracking.</span></span> <span data-ttu-id="d08a1-109">這表示您可以對那些實體執行個體進行變更，並透過 `SaveChanges()` 來保存那些變更。</span><span class="sxs-lookup"><span data-stu-id="d08a1-109">This means you can make changes to those entity instances and have those changes persisted by `SaveChanges()`.</span></span>

<span data-ttu-id="d08a1-110">在下列範例中，將會偵測到對部落格評等的變更，並在 `SaveChanges()` 期間將其保存於資料庫。</span><span class="sxs-lookup"><span data-stu-id="d08a1-110">In the following example, the change to the blogs rating will be detected and persisted to the database during `SaveChanges()`.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Tracking/Sample.cs)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.SingleOrDefault(b => b.BlogId == 1);
    blog.Rating = 5;
    context.SaveChanges();
}
```

## <a name="no-tracking-queries"></a><span data-ttu-id="d08a1-111">無追蹤查詢</span><span class="sxs-lookup"><span data-stu-id="d08a1-111">No-tracking queries</span></span>

<span data-ttu-id="d08a1-112">如果要在唯讀案例中使用結果，則不追蹤的查詢很實用。</span><span class="sxs-lookup"><span data-stu-id="d08a1-112">No tracking queries are useful when the results are used in a read-only scenario.</span></span> <span data-ttu-id="d08a1-113">它們會以更快的速度執行，因為不需要設定變更追蹤資訊。</span><span class="sxs-lookup"><span data-stu-id="d08a1-113">They are quicker to execute because there is no need to setup change tracking information.</span></span>

<span data-ttu-id="d08a1-114">您可以將個別查詢切換為不追蹤：</span><span class="sxs-lookup"><span data-stu-id="d08a1-114">You can swap an individual query to be no-tracking:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Tracking/Sample.cs?highlight=4)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs
        .AsNoTracking()
        .ToList();
}
```

<span data-ttu-id="d08a1-115">您也可以在內容執行個體層級變更預設的追蹤行為：</span><span class="sxs-lookup"><span data-stu-id="d08a1-115">You can also change the default tracking behavior at the context instance level:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Tracking/Sample.cs?highlight=3)] -->
``` csharp
using (var context = new BloggingContext())
{
    context.ChangeTracker.QueryTrackingBehavior = QueryTrackingBehavior.NoTracking;

    var blogs = context.Blogs.ToList();
}
```

> [!NOTE]  
> <span data-ttu-id="d08a1-116">不追蹤的查詢仍會在執行查詢內執行識別解析。</span><span class="sxs-lookup"><span data-stu-id="d08a1-116">No tracking queries still perform identity resolution within the excuting query.</span></span> <span data-ttu-id="d08a1-117">如果結果集多次包含同一個實體，則將針對結果集中的每個相符項目傳回實體類別的相同執行個體。</span><span class="sxs-lookup"><span data-stu-id="d08a1-117">If the result set contains the same entity multiple times, the same instance of the entity class will be returned for each occurrence in the result set.</span></span> <span data-ttu-id="d08a1-118">不過，會使用弱式參考來持續追蹤已經傳回的實體。</span><span class="sxs-lookup"><span data-stu-id="d08a1-118">However, weak references are used to keep track of entities that have already been returned.</span></span> <span data-ttu-id="d08a1-119">如果先前具有相同身分識別的結果超出範圍，且執行記憶體回收，您可能會得到新的實體執行個體。</span><span class="sxs-lookup"><span data-stu-id="d08a1-119">If a previous result with the same identity goes out of scope, and garbage collection runs, you may get a new entity instance.</span></span> <span data-ttu-id="d08a1-120">如需詳細資訊，請參閱[查詢的運作方式](overview.md)。</span><span class="sxs-lookup"><span data-stu-id="d08a1-120">For more information, see [How Query Works](overview.md).</span></span>

## <a name="tracking-and-projections"></a><span data-ttu-id="d08a1-121">追蹤和預測</span><span class="sxs-lookup"><span data-stu-id="d08a1-121">Tracking and projections</span></span>

<span data-ttu-id="d08a1-122">即使查詢的結果類型不是實體類型，但若結果包含實體類型，預設仍將追蹤它們。</span><span class="sxs-lookup"><span data-stu-id="d08a1-122">Even if the result type of the query isn't an entity type, if the result contains entity types they will still be tracked by default.</span></span> <span data-ttu-id="d08a1-123">下列查詢會傳回匿名類型，並且將在結果集中追蹤 `Blog` 的執行個體。</span><span class="sxs-lookup"><span data-stu-id="d08a1-123">In the following query, which returns an anonymous type, the instances of `Blog` in the result set will be tracked.</span></span>

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

<span data-ttu-id="d08a1-124">如果結果集未包含任何實體類型，則不會執行追蹤。</span><span class="sxs-lookup"><span data-stu-id="d08a1-124">If the result set does not contain any entity types, then no tracking is performed.</span></span> <span data-ttu-id="d08a1-125">下列查詢會傳回匿名類型，並具有一些來自實體的值 (但沒有實際實體類型的執行個體)，但不會執行任何追蹤。</span><span class="sxs-lookup"><span data-stu-id="d08a1-125">In the following query, which returns an anonymous type with some of the values from the entity (but no instances of the actual entity type), there is no tracking performed.</span></span>

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
