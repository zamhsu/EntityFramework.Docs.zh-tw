---
title: 載入相關實體-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: c8417e18-a2ee-499c-9ce9-2a48cc5b468a
ms.openlocfilehash: c359d8d32a88049213fd5e98e99fe49d7e3121a3
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417085"
---
# <a name="loading-related-entities"></a><span data-ttu-id="7ba09-102">載入相關實體</span><span class="sxs-lookup"><span data-stu-id="7ba09-102">Loading Related Entities</span></span>

<span data-ttu-id="7ba09-103">Entity Framework 支援三種載入相關資料的方式：積極式載入、延遲載入和明確載入。</span><span class="sxs-lookup"><span data-stu-id="7ba09-103">Entity Framework supports three ways to load related data - eager loading, lazy loading and explicit loading.</span></span> <span data-ttu-id="7ba09-104">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="7ba09-104">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>

## <a name="eagerly-loading"></a><span data-ttu-id="7ba09-105">立即載入</span><span class="sxs-lookup"><span data-stu-id="7ba09-105">Eagerly Loading</span></span>

<span data-ttu-id="7ba09-106">積極式載入是一種處理常式，其中一種實體類型的查詢也會在查詢過程中載入相關的實體。</span><span class="sxs-lookup"><span data-stu-id="7ba09-106">Eager loading is the process whereby a query for one type of entity also loads related entities as part of the query.</span></span> <span data-ttu-id="7ba09-107">您可以使用 Include 方法來完成積極式載入。</span><span class="sxs-lookup"><span data-stu-id="7ba09-107">Eager loading is achieved by use of the Include method.</span></span> <span data-ttu-id="7ba09-108">例如，下列查詢將會載入 blog 和每個 blog 的所有相關文章。</span><span class="sxs-lookup"><span data-stu-id="7ba09-108">For example, the queries below will load blogs and all the posts related to each blog.</span></span>

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs and related posts.
    var blogs1 = context.Blogs
                        .Include(b => b.Posts)
                        .ToList();

    // Load one blog and its related posts.
    var blog1 = context.Blogs
                       .Where(b => b.Name == "ADO.NET Blog")
                       .Include(b => b.Posts)
                       .FirstOrDefault();

    // Load all blogs and related posts
    // using a string to specify the relationship.
    var blogs2 = context.Blogs
                        .Include("Posts")
                        .ToList();

    // Load one blog and its related posts
    // using a string to specify the relationship.
    var blog2 = context.Blogs
                       .Where(b => b.Name == "ADO.NET Blog")
                       .Include("Posts")
                       .FirstOrDefault();
}
```

> [!NOTE]
> <span data-ttu-id="7ba09-109">Include 是 System.web 命名空間中的擴充方法，因此請確定您使用的是該命名空間。</span><span class="sxs-lookup"><span data-stu-id="7ba09-109">Include is an extension method in the System.Data.Entity namespace so make sure you are using that namespace.</span></span>

### <a name="eagerly-loading-multiple-levels"></a><span data-ttu-id="7ba09-110">立即載入多個層級</span><span class="sxs-lookup"><span data-stu-id="7ba09-110">Eagerly loading multiple levels</span></span>

<span data-ttu-id="7ba09-111">也可以立即載入多個相關實體層級。</span><span class="sxs-lookup"><span data-stu-id="7ba09-111">It is also possible to eagerly load multiple levels of related entities.</span></span> <span data-ttu-id="7ba09-112">下列查詢顯示如何針對集合和參考導覽屬性執行這項操作的範例。</span><span class="sxs-lookup"><span data-stu-id="7ba09-112">The queries below show examples of how to do this for both collection and reference navigation properties.</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs, all related posts, and all related comments.
    var blogs1 = context.Blogs
                        .Include(b => b.Posts.Select(p => p.Comments))
                        .ToList();

    // Load all users, their related profiles, and related avatar.
    var users1 = context.Users
                        .Include(u => u.Profile.Avatar)
                        .ToList();

    // Load all blogs, all related posts, and all related comments  
    // using a string to specify the relationships.
    var blogs2 = context.Blogs
                        .Include("Posts.Comments")
                        .ToList();

    // Load all users, their related profiles, and related avatar  
    // using a string to specify the relationships.
    var users2 = context.Users
                        .Include("Profile.Avatar")
                        .ToList();
}
```  

> [!NOTE]
> <span data-ttu-id="7ba09-113">目前無法篩選要載入哪些相關實體。</span><span class="sxs-lookup"><span data-stu-id="7ba09-113">It is not currently possible to filter which related entities are loaded.</span></span> <span data-ttu-id="7ba09-114">Include 一律會帶入所有相關實體。</span><span class="sxs-lookup"><span data-stu-id="7ba09-114">Include will always bring in all related entities.</span></span>  

## <a name="lazy-loading"></a><span data-ttu-id="7ba09-115">消極式載入</span><span class="sxs-lookup"><span data-stu-id="7ba09-115">Lazy Loading</span></span>

<span data-ttu-id="7ba09-116">「消極式載入」是一種程式，會在第一次存取參考實體/實體的屬性時，自動從資料庫載入實體或實體集合。</span><span class="sxs-lookup"><span data-stu-id="7ba09-116">Lazy loading is the process whereby an entity or collection of entities is automatically loaded from the database the first time that a property referring to the entity/entities is accessed.</span></span> <span data-ttu-id="7ba09-117">使用 POCO 實體類型時，可以藉由建立衍生 proxy 類型的實例，然後覆寫虛擬屬性來新增載入攔截，來達到消極式載入。</span><span class="sxs-lookup"><span data-stu-id="7ba09-117">When using POCO entity types, lazy loading is achieved by creating instances of derived proxy types and then overriding virtual properties to add the loading hook.</span></span> <span data-ttu-id="7ba09-118">例如，使用以下定義的 Blog 實體類別時，會在第一次存取張貼導覽屬性時載入相關文章：</span><span class="sxs-lookup"><span data-stu-id="7ba09-118">For example, when using the Blog entity class defined below, the related Posts will be loaded the first time the Posts navigation property is accessed:</span></span>

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }
    public string Tags { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}
```

### <a name="turn-lazy-loading-off-for-serialization"></a><span data-ttu-id="7ba09-119">關閉延遲載入以進行序列化</span><span class="sxs-lookup"><span data-stu-id="7ba09-119">Turn lazy loading off for serialization</span></span>

<span data-ttu-id="7ba09-120">消極式載入和序列化不會混搭，如果您不小心，就可以查詢整個資料庫，因為已啟用消極式載入。</span><span class="sxs-lookup"><span data-stu-id="7ba09-120">Lazy loading and serialization don’t mix well, and if you aren’t careful you can end up querying for your entire database just because lazy loading is enabled.</span></span> <span data-ttu-id="7ba09-121">大部分的序列化程式都可以藉由存取類型實例上的每個屬性來進行。</span><span class="sxs-lookup"><span data-stu-id="7ba09-121">Most serializers work by accessing each property on an instance of a type.</span></span> <span data-ttu-id="7ba09-122">屬性存取會觸發延遲載入，因此會序列化更多實體。</span><span class="sxs-lookup"><span data-stu-id="7ba09-122">Property access triggers lazy loading, so more entities get serialized.</span></span> <span data-ttu-id="7ba09-123">在存取這些實體屬性時，也會載入更多實體。</span><span class="sxs-lookup"><span data-stu-id="7ba09-123">On those entities properties are accessed, and even more entities are loaded.</span></span> <span data-ttu-id="7ba09-124">在序列化實體之前，最好先關閉延遲載入。</span><span class="sxs-lookup"><span data-stu-id="7ba09-124">It’s a good practice to turn lazy loading off before you serialize an entity.</span></span> <span data-ttu-id="7ba09-125">下列各節將示範如何執行這項操作。</span><span class="sxs-lookup"><span data-stu-id="7ba09-125">The following sections show how to do this.</span></span>

### <a name="turning-off-lazy-loading-for-specific-navigation-properties"></a><span data-ttu-id="7ba09-126">關閉特定導覽屬性的消極式載入</span><span class="sxs-lookup"><span data-stu-id="7ba09-126">Turning off lazy loading for specific navigation properties</span></span>

<span data-ttu-id="7ba09-127">將貼文屬性設為非虛擬，即可關閉 Post 集合的消極式載入：</span><span class="sxs-lookup"><span data-stu-id="7ba09-127">Lazy loading of the Posts collection can be turned off by making the Posts property non-virtual:</span></span>

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }
    public string Tags { get; set; }

    public ICollection<Post> Posts { get; set; }
}
```

<span data-ttu-id="7ba09-128">您仍然可以使用積極式載入（請參閱上面的*立即載入*）或 Load 方法來載入貼文集合（請參閱下面的*明確載入*）。</span><span class="sxs-lookup"><span data-stu-id="7ba09-128">Loading of the Posts collection can still be achieved using eager loading (see *Eagerly Loading* above) or the Load method (see *Explicitly Loading* below).</span></span>

### <a name="turn-off-lazy-loading-for-all-entities"></a><span data-ttu-id="7ba09-129">關閉所有實體的延遲載入</span><span class="sxs-lookup"><span data-stu-id="7ba09-129">Turn off lazy loading for all entities</span></span>

<span data-ttu-id="7ba09-130">您可以在 Configuration 屬性上設定旗標，以關閉內容中所有實體的消極式載入。</span><span class="sxs-lookup"><span data-stu-id="7ba09-130">Lazy loading can be turned off for all entities in the context by setting a flag on the Configuration property.</span></span> <span data-ttu-id="7ba09-131">例如：</span><span class="sxs-lookup"><span data-stu-id="7ba09-131">For example:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.LazyLoadingEnabled = false;
    }
}
```

<span data-ttu-id="7ba09-132">您仍然可以使用積極式載入（請參閱上面的*立即載入*）或 Load 方法來載入相關實體（請參閱下面的*明確載入*）。</span><span class="sxs-lookup"><span data-stu-id="7ba09-132">Loading of related entities can still be achieved using eager loading (see *Eagerly Loading* above) or the Load method (see *Explicitly Loading* below).</span></span>

## <a name="explicitly-loading"></a><span data-ttu-id="7ba09-133">明確載入</span><span class="sxs-lookup"><span data-stu-id="7ba09-133">Explicitly Loading</span></span>

<span data-ttu-id="7ba09-134">即使已停用消極式載入，仍然可以延遲載入相關的實體，但必須使用明確的呼叫來完成。</span><span class="sxs-lookup"><span data-stu-id="7ba09-134">Even with lazy loading disabled it is still possible to lazily load related entities, but it must be done with an explicit call.</span></span> <span data-ttu-id="7ba09-135">若要這麼做，請在相關實體的專案上使用 Load 方法。</span><span class="sxs-lookup"><span data-stu-id="7ba09-135">To do so you use the Load method on the related entity’s entry.</span></span> <span data-ttu-id="7ba09-136">例如：</span><span class="sxs-lookup"><span data-stu-id="7ba09-136">For example:</span></span>

``` csharp
using (var context = new BloggingContext())
{
    var post = context.Posts.Find(2);

    // Load the blog related to a given post.
    context.Entry(post).Reference(p => p.Blog).Load();

    // Load the blog related to a given post using a string.
    context.Entry(post).Reference("Blog").Load();

    var blog = context.Blogs.Find(1);

    // Load the posts related to a given blog.
    context.Entry(blog).Collection(p => p.Posts).Load();

    // Load the posts related to a given blog
    // using a string to specify the relationship.
    context.Entry(blog).Collection("Posts").Load();
}
```

> [!NOTE]
> <span data-ttu-id="7ba09-137">當實體具有另一個單一實體的導覽屬性時，應該使用參考方法。</span><span class="sxs-lookup"><span data-stu-id="7ba09-137">The Reference method should be used when an entity has a navigation property to another single entity.</span></span> <span data-ttu-id="7ba09-138">另一方面，當實體具有其他實體集合的導覽屬性時，應該使用集合方法。</span><span class="sxs-lookup"><span data-stu-id="7ba09-138">On the other hand, the Collection method should be used when an entity has a navigation property to a collection of other entities.</span></span>

### <a name="applying-filters-when-explicitly-loading-related-entities"></a><span data-ttu-id="7ba09-139">在明確載入相關實體時套用篩選</span><span class="sxs-lookup"><span data-stu-id="7ba09-139">Applying filters when explicitly loading related entities</span></span>

<span data-ttu-id="7ba09-140">查詢方法可讓您存取 Entity Framework 將在載入相關實體時使用的基礎查詢。</span><span class="sxs-lookup"><span data-stu-id="7ba09-140">The Query method provides access to the underlying query that Entity Framework will use when loading related entities.</span></span> <span data-ttu-id="7ba09-141">接著，您可以使用 LINQ 將篩選套用至查詢，然後再透過呼叫 LINQ 擴充方法（例如 ToList、載入等等）來執行。查詢方法可以同時用於參考和集合導覽屬性，但最適用于可用於只載入集合一部分的集合。</span><span class="sxs-lookup"><span data-stu-id="7ba09-141">You can then use LINQ to apply filters to the query before executing it with a call to a LINQ extension method such as ToList, Load, etc. The Query method can be used with both reference and collection navigation properties but is most useful for collections where it can be used to load only part of the collection.</span></span> <span data-ttu-id="7ba09-142">例如：</span><span class="sxs-lookup"><span data-stu-id="7ba09-142">For example:</span></span>

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog.
    context.Entry(blog)
           .Collection(b => b.Posts)
           .Query()
           .Where(p => p.Tags.Contains("entity-framework"))
           .Load();

    // Load the posts with the 'entity-framework' tag related to a given blog
    // using a string to specify the relationship.
    context.Entry(blog)
           .Collection("Posts")
           .Query()
           .Where(p => p.Tags.Contains("entity-framework"))
           .Load();
}
```

<span data-ttu-id="7ba09-143">使用查詢方法時，通常最好關閉導覽屬性的消極式載入。</span><span class="sxs-lookup"><span data-stu-id="7ba09-143">When using the Query method it is usually best to turn off lazy loading for the navigation property.</span></span> <span data-ttu-id="7ba09-144">這是因為在執行篩選查詢之前或之後，延遲載入機制可能會自動載入整個集合。</span><span class="sxs-lookup"><span data-stu-id="7ba09-144">This is because otherwise the entire collection may get loaded automatically by the lazy loading mechanism either before or after the filtered query has been executed.</span></span>

> [!NOTE]
> <span data-ttu-id="7ba09-145">雖然可以將關聯性指定為字串，而不是 lambda 運算式，但當使用字串時，傳回的 IQueryable 不是泛型，因此通常需要 Cast 方法，才能使用它來完成任何工作。</span><span class="sxs-lookup"><span data-stu-id="7ba09-145">While the relationship can be specified as a string instead of a lambda expression, the returned IQueryable is not generic when a string is used and so the Cast method is usually needed before anything useful can be done with it.</span></span>

## <a name="using-query-to-count-related-entities-without-loading-them"></a><span data-ttu-id="7ba09-146">使用查詢來計算相關實體，而不載入它們</span><span class="sxs-lookup"><span data-stu-id="7ba09-146">Using Query to count related entities without loading them</span></span>

<span data-ttu-id="7ba09-147">有時候，知道有多少實體與資料庫中的另一個實體相關，並不會實際產生載入所有這些實體的成本，這會很有説明。</span><span class="sxs-lookup"><span data-stu-id="7ba09-147">Sometimes it is useful to know how many entities are related to another entity in the database without actually incurring the cost of loading all those entities.</span></span> <span data-ttu-id="7ba09-148">具有 LINQ Count 方法的查詢方法可以用來執行此動作。</span><span class="sxs-lookup"><span data-stu-id="7ba09-148">The Query method with the LINQ Count method can be used to do this.</span></span> <span data-ttu-id="7ba09-149">例如：</span><span class="sxs-lookup"><span data-stu-id="7ba09-149">For example:</span></span>

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Count how many posts the blog has.
    var postCount = context.Entry(blog)
                           .Collection(b => b.Posts)
                           .Query()
                           .Count();
}
```
