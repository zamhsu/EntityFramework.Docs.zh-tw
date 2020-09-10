---
title: 載入相關實體-EF6
description: 在 Entity Framework 6 中載入相關實體
author: divega
ms.date: 10/23/2016
ms.assetid: c8417e18-a2ee-499c-9ce9-2a48cc5b468a
uid: ef6/querying/related-data
ms.openlocfilehash: 9b4e8ecda618e11e87cd595502210234b1f3e27d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620272"
---
# <a name="loading-related-entities"></a><span data-ttu-id="d22ae-103">載入相關實體</span><span class="sxs-lookup"><span data-stu-id="d22ae-103">Loading Related Entities</span></span>

<span data-ttu-id="d22ae-104">Entity Framework 支援三種載入相關資料的方式：積極式載入、消極式載入和明確載入。</span><span class="sxs-lookup"><span data-stu-id="d22ae-104">Entity Framework supports three ways to load related data - eager loading, lazy loading and explicit loading.</span></span> <span data-ttu-id="d22ae-105">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="d22ae-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>

## <a name="eagerly-loading"></a><span data-ttu-id="d22ae-106">立即載入</span><span class="sxs-lookup"><span data-stu-id="d22ae-106">Eagerly Loading</span></span>

<span data-ttu-id="d22ae-107">積極式載入是一種處理常式，其中一種實體類型的查詢也會在查詢中載入相關實體。</span><span class="sxs-lookup"><span data-stu-id="d22ae-107">Eager loading is the process whereby a query for one type of entity also loads related entities as part of the query.</span></span> <span data-ttu-id="d22ae-108">您可以使用 Include 方法來達成積極式載入。</span><span class="sxs-lookup"><span data-stu-id="d22ae-108">Eager loading is achieved by use of the Include method.</span></span> <span data-ttu-id="d22ae-109">例如，下列查詢會載入 blog 以及與每個 blog 相關的所有文章。</span><span class="sxs-lookup"><span data-stu-id="d22ae-109">For example, the queries below will load blogs and all the posts related to each blog.</span></span>

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
> <span data-ttu-id="d22ae-110">Include 是 system.string 命名空間中的擴充方法，因此請確定您使用的是該命名空間。</span><span class="sxs-lookup"><span data-stu-id="d22ae-110">Include is an extension method in the System.Data.Entity namespace so make sure you are using that namespace.</span></span>

### <a name="eagerly-loading-multiple-levels"></a><span data-ttu-id="d22ae-111">立即載入多個層級</span><span class="sxs-lookup"><span data-stu-id="d22ae-111">Eagerly loading multiple levels</span></span>

<span data-ttu-id="d22ae-112">您也可以立即載入多個相關實體層級。</span><span class="sxs-lookup"><span data-stu-id="d22ae-112">It is also possible to eagerly load multiple levels of related entities.</span></span> <span data-ttu-id="d22ae-113">下列查詢顯示如何針對集合和參考導覽屬性進行這項作業的範例。</span><span class="sxs-lookup"><span data-stu-id="d22ae-113">The queries below show examples of how to do this for both collection and reference navigation properties.</span></span>  

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
> <span data-ttu-id="d22ae-114">目前無法篩選已載入的相關實體。</span><span class="sxs-lookup"><span data-stu-id="d22ae-114">It is not currently possible to filter which related entities are loaded.</span></span> <span data-ttu-id="d22ae-115">Include 一律會帶入所有相關的實體。</span><span class="sxs-lookup"><span data-stu-id="d22ae-115">Include will always bring in all related entities.</span></span>  

## <a name="lazy-loading"></a><span data-ttu-id="d22ae-116">消極式載入</span><span class="sxs-lookup"><span data-stu-id="d22ae-116">Lazy Loading</span></span>

<span data-ttu-id="d22ae-117">消極式載入是一種處理常式，會在第一次存取參考實體/實體的屬性時，從資料庫自動載入實體或實體集合。</span><span class="sxs-lookup"><span data-stu-id="d22ae-117">Lazy loading is the process whereby an entity or collection of entities is automatically loaded from the database the first time that a property referring to the entity/entities is accessed.</span></span> <span data-ttu-id="d22ae-118">使用 POCO 實體型別時，可以藉由建立衍生 proxy 型別的實例，然後覆寫虛擬屬性來新增載入攔截，來達成消極式載入。</span><span class="sxs-lookup"><span data-stu-id="d22ae-118">When using POCO entity types, lazy loading is achieved by creating instances of derived proxy types and then overriding virtual properties to add the loading hook.</span></span> <span data-ttu-id="d22ae-119">例如，使用以下定義的 Blog 實體類別時，將會在第一次存取 Post 導覽屬性時載入相關文章：</span><span class="sxs-lookup"><span data-stu-id="d22ae-119">For example, when using the Blog entity class defined below, the related Posts will be loaded the first time the Posts navigation property is accessed:</span></span>

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

### <a name="turn-lazy-loading-off-for-serialization"></a><span data-ttu-id="d22ae-120">關閉延遲載入以進行序列化</span><span class="sxs-lookup"><span data-stu-id="d22ae-120">Turn lazy loading off for serialization</span></span>

<span data-ttu-id="d22ae-121">消極式載入和序列化不會有很大的混淆，如果您不小心，只要啟用消極式載入，就能最後查詢整個資料庫。</span><span class="sxs-lookup"><span data-stu-id="d22ae-121">Lazy loading and serialization don’t mix well, and if you aren’t careful you can end up querying for your entire database just because lazy loading is enabled.</span></span> <span data-ttu-id="d22ae-122">大部分的序列化程式都可以藉由存取型別實例上的每個屬性來運作。</span><span class="sxs-lookup"><span data-stu-id="d22ae-122">Most serializers work by accessing each property on an instance of a type.</span></span> <span data-ttu-id="d22ae-123">屬性存取會觸發消極式載入，因此會序列化更多實體。</span><span class="sxs-lookup"><span data-stu-id="d22ae-123">Property access triggers lazy loading, so more entities get serialized.</span></span> <span data-ttu-id="d22ae-124">系統會在這些實體屬性上存取，甚至載入更多實體。</span><span class="sxs-lookup"><span data-stu-id="d22ae-124">On those entities properties are accessed, and even more entities are loaded.</span></span> <span data-ttu-id="d22ae-125">在序列化實體之前，將消極式載入關閉是很好的做法。</span><span class="sxs-lookup"><span data-stu-id="d22ae-125">It’s a good practice to turn lazy loading off before you serialize an entity.</span></span> <span data-ttu-id="d22ae-126">下列各節將示範如何執行這項操作。</span><span class="sxs-lookup"><span data-stu-id="d22ae-126">The following sections show how to do this.</span></span>

### <a name="turning-off-lazy-loading-for-specific-navigation-properties"></a><span data-ttu-id="d22ae-127">關閉特定導覽屬性的消極式載入</span><span class="sxs-lookup"><span data-stu-id="d22ae-127">Turning off lazy loading for specific navigation properties</span></span>

<span data-ttu-id="d22ae-128">Post 集合的消極式載入可以藉由將貼文屬性設為非虛擬來關閉：</span><span class="sxs-lookup"><span data-stu-id="d22ae-128">Lazy loading of the Posts collection can be turned off by making the Posts property non-virtual:</span></span>

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

<span data-ttu-id="d22ae-129">您仍然可以使用積極式載入來完成貼文集合的載入 (請參閱上述的 *立即載入*) 或 Load 方法 (請參閱下面的 *明確載入*) 。</span><span class="sxs-lookup"><span data-stu-id="d22ae-129">Loading of the Posts collection can still be achieved using eager loading (see *Eagerly Loading* above) or the Load method (see *Explicitly Loading* below).</span></span>

### <a name="turn-off-lazy-loading-for-all-entities"></a><span data-ttu-id="d22ae-130">關閉所有實體的延遲載入</span><span class="sxs-lookup"><span data-stu-id="d22ae-130">Turn off lazy loading for all entities</span></span>

<span data-ttu-id="d22ae-131">您可以藉由設定設定屬性上的旗標，關閉內容中所有實體的消極式載入。</span><span class="sxs-lookup"><span data-stu-id="d22ae-131">Lazy loading can be turned off for all entities in the context by setting a flag on the Configuration property.</span></span> <span data-ttu-id="d22ae-132">例如：</span><span class="sxs-lookup"><span data-stu-id="d22ae-132">For example:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.LazyLoadingEnabled = false;
    }
}
```

<span data-ttu-id="d22ae-133">您仍然可以使用積極式載入來完成相關實體的載入 (請參閱上述的 *立即載入*) 或 Load 方法 (請參閱下面的 *明確載入*) 。</span><span class="sxs-lookup"><span data-stu-id="d22ae-133">Loading of related entities can still be achieved using eager loading (see *Eagerly Loading* above) or the Load method (see *Explicitly Loading* below).</span></span>

## <a name="explicitly-loading"></a><span data-ttu-id="d22ae-134">明確載入</span><span class="sxs-lookup"><span data-stu-id="d22ae-134">Explicitly Loading</span></span>

<span data-ttu-id="d22ae-135">即使已停用消極式載入，仍然可以延遲載入相關實體，但必須透過明確呼叫來完成。</span><span class="sxs-lookup"><span data-stu-id="d22ae-135">Even with lazy loading disabled it is still possible to lazily load related entities, but it must be done with an explicit call.</span></span> <span data-ttu-id="d22ae-136">若要這樣做，請在相關實體的專案上使用 Load 方法。</span><span class="sxs-lookup"><span data-stu-id="d22ae-136">To do so you use the Load method on the related entity’s entry.</span></span> <span data-ttu-id="d22ae-137">例如：</span><span class="sxs-lookup"><span data-stu-id="d22ae-137">For example:</span></span>

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
> <span data-ttu-id="d22ae-138">當實體有指向另一個單一實體的導覽屬性時，應該使用參考方法。</span><span class="sxs-lookup"><span data-stu-id="d22ae-138">The Reference method should be used when an entity has a navigation property to another single entity.</span></span> <span data-ttu-id="d22ae-139">相反地，當實體有指向其他實體集合的導覽屬性時，應該使用 Collection 方法。</span><span class="sxs-lookup"><span data-stu-id="d22ae-139">On the other hand, the Collection method should be used when an entity has a navigation property to a collection of other entities.</span></span>

### <a name="applying-filters-when-explicitly-loading-related-entities"></a><span data-ttu-id="d22ae-140">明確載入相關實體時套用篩選</span><span class="sxs-lookup"><span data-stu-id="d22ae-140">Applying filters when explicitly loading related entities</span></span>

<span data-ttu-id="d22ae-141">查詢方法可讓您存取 Entity Framework 將在載入相關實體時使用的基礎查詢。</span><span class="sxs-lookup"><span data-stu-id="d22ae-141">The Query method provides access to the underlying query that Entity Framework will use when loading related entities.</span></span> <span data-ttu-id="d22ae-142">然後，您可以使用 LINQ 將篩選套用至查詢，然後再透過呼叫 LINQ 擴充方法（例如 ToList、載入等）來執行。查詢方法可以搭配參考和集合導覽屬性使用，但對於只能用來載入部分集合的集合而言最有用。</span><span class="sxs-lookup"><span data-stu-id="d22ae-142">You can then use LINQ to apply filters to the query before executing it with a call to a LINQ extension method such as ToList, Load, etc. The Query method can be used with both reference and collection navigation properties but is most useful for collections where it can be used to load only part of the collection.</span></span> <span data-ttu-id="d22ae-143">例如：</span><span class="sxs-lookup"><span data-stu-id="d22ae-143">For example:</span></span>

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

<span data-ttu-id="d22ae-144">使用查詢方法時，通常最好關閉導覽屬性的消極式載入。</span><span class="sxs-lookup"><span data-stu-id="d22ae-144">When using the Query method it is usually best to turn off lazy loading for the navigation property.</span></span> <span data-ttu-id="d22ae-145">這是因為否則，在執行篩選查詢之前或之後，可能會自動載入整個集合。</span><span class="sxs-lookup"><span data-stu-id="d22ae-145">This is because otherwise the entire collection may get loaded automatically by the lazy loading mechanism either before or after the filtered query has been executed.</span></span>

> [!NOTE]
> <span data-ttu-id="d22ae-146">雖然可以將關聯性指定為字串，而不是 lambda 運算式，但傳回的 IQueryable 在使用字串時不是泛型，因此通常需要 Cast 方法，才能使用它來完成任何有用的動作。</span><span class="sxs-lookup"><span data-stu-id="d22ae-146">While the relationship can be specified as a string instead of a lambda expression, the returned IQueryable is not generic when a string is used and so the Cast method is usually needed before anything useful can be done with it.</span></span>

## <a name="using-query-to-count-related-entities-without-loading-them"></a><span data-ttu-id="d22ae-147">使用查詢來計算相關實體，但不載入它們</span><span class="sxs-lookup"><span data-stu-id="d22ae-147">Using Query to count related entities without loading them</span></span>

<span data-ttu-id="d22ae-148">有時候，知道有多少個實體與資料庫中的另一個實體相關，並不會實際產生載入所有這些實體的成本，會很有用。</span><span class="sxs-lookup"><span data-stu-id="d22ae-148">Sometimes it is useful to know how many entities are related to another entity in the database without actually incurring the cost of loading all those entities.</span></span> <span data-ttu-id="d22ae-149">使用 LINQ Count 方法的查詢方法可以用來進行此作業。</span><span class="sxs-lookup"><span data-stu-id="d22ae-149">The Query method with the LINQ Count method can be used to do this.</span></span> <span data-ttu-id="d22ae-150">例如：</span><span class="sxs-lookup"><span data-stu-id="d22ae-150">For example:</span></span>

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
