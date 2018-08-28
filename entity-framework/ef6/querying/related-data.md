---
title: 載入相關實體-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: c8417e18-a2ee-499c-9ce9-2a48cc5b468a
ms.openlocfilehash: 091493f60c732573ca63adb8c65bc28606453d34
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995921"
---
# <a name="loading-related-entities"></a><span data-ttu-id="4d850-102">載入相關的實體</span><span class="sxs-lookup"><span data-stu-id="4d850-102">Loading Related Entities</span></span>
<span data-ttu-id="4d850-103">Entity Framework 支援三種方式可以載入相關的資料-積極式載入、 消極式載入和明確式載入。</span><span class="sxs-lookup"><span data-stu-id="4d850-103">Entity Framework supports three ways to load related data - eager loading, lazy loading and explicit loading.</span></span> <span data-ttu-id="4d850-104">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="4d850-104">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="eagerly-loading"></a><span data-ttu-id="4d850-105">立即載入</span><span class="sxs-lookup"><span data-stu-id="4d850-105">Eagerly Loading</span></span>  

<span data-ttu-id="4d850-106">積極式載入是讓某一類型實體的查詢也會載入相關的實體做為查詢一部分的程序。</span><span class="sxs-lookup"><span data-stu-id="4d850-106">Eager loading is the process whereby a query for one type of entity also loads related entities as part of the query.</span></span> <span data-ttu-id="4d850-107">積極式載入之後，即可使用 Include 方法。</span><span class="sxs-lookup"><span data-stu-id="4d850-107">Eager loading is achieved by use of the Include method.</span></span> <span data-ttu-id="4d850-108">例如，下列查詢會載入部落格和每個部落格與相關的所有文章。</span><span class="sxs-lookup"><span data-stu-id="4d850-108">For example, the queries below will load blogs and all the posts related to each blog.</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs and related posts
    var blogs1 = context.Blogs
                          .Include(b => b.Posts)
                          .ToList();

    // Load one blogs and its related posts
    var blog1 = context.Blogs
                        .Where(b => b.Name == "ADO.NET Blog")
                        .Include(b => b.Posts)
                        .FirstOrDefault();

    // Load all blogs and related posts  
    // using a string to specify the relationship
    var blogs2 = context.Blogs
                          .Include("Posts")
                          .ToList();

    // Load one blog and its related posts  
    // using a string to specify the relationship
    var blog2 = context.Blogs
                        .Where(b => b.Name == "ADO.NET Blog")
                        .Include("Posts")
                        .FirstOrDefault();
}
```  

<span data-ttu-id="4d850-109">請注意，包含 System.Data.Entity 命名空間中的擴充方法因此請確定您使用該命名空間。</span><span class="sxs-lookup"><span data-stu-id="4d850-109">Note that Include is an extension method in the System.Data.Entity namespace so make sure you are using that namespace.</span></span>  

### <a name="eagerly-loading-multiple-levels"></a><span data-ttu-id="4d850-110">立即載入多個層級</span><span class="sxs-lookup"><span data-stu-id="4d850-110">Eagerly loading multiple levels</span></span>  

<span data-ttu-id="4d850-111">它也可提早載入相關實體的多個層級。</span><span class="sxs-lookup"><span data-stu-id="4d850-111">It is also possible to eagerly load multiple levels of related entities.</span></span> <span data-ttu-id="4d850-112">下列查詢會示範如何執行此動作的集合和參考導覽屬性的範例。</span><span class="sxs-lookup"><span data-stu-id="4d850-112">The queries below show examples of how to do this for both collection and reference navigation properties.</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs, all related posts, and all related comments
    var blogs1 = context.Blogs
                       .Include(b => b.Posts.Select(p => p.Comments))
                       .ToList();

    // Load all users their related profiles, and related avatar
    var users1 = context.Users
                        .Include(u => u.Profile.Avatar)
                        .ToList();

    // Load all blogs, all related posts, and all related comments  
    // using a string to specify the relationships
    var blogs2 = context.Blogs
                       .Include("Posts.Comments")
                       .ToList();

    // Load all users their related profiles, and related avatar  
    // using a string to specify the relationships
    var users2 = context.Users
                        .Include("Profile.Avatar")
                        .ToList();
}
```  

<span data-ttu-id="4d850-113">請注意，它不是目前無法載入相關的實體的篩選。</span><span class="sxs-lookup"><span data-stu-id="4d850-113">Note that it is not currently possible to filter which related entities are loaded.</span></span> <span data-ttu-id="4d850-114">包含 」 會一律將所有相關的實體。</span><span class="sxs-lookup"><span data-stu-id="4d850-114">Include will always bring in all related entities.</span></span>  

## <a name="lazy-loading"></a><span data-ttu-id="4d850-115">消極式載入</span><span class="sxs-lookup"><span data-stu-id="4d850-115">Lazy Loading</span></span>  

<span data-ttu-id="4d850-116">消極式載入是讓實體集合會自動從資料庫載入第一次存取的實體/實體所參考的屬性程序。</span><span class="sxs-lookup"><span data-stu-id="4d850-116">Lazy loading is the process whereby an entity or collection of entities is automatically loaded from the database the first time that a property referring to the entity/entities is accessed.</span></span> <span data-ttu-id="4d850-117">使用 POCO 實體類型時，消極式載入之後，即可建立衍生的 proxy 型別的執行個體，然後覆寫虛擬屬性，以新增載入勾點。</span><span class="sxs-lookup"><span data-stu-id="4d850-117">When using POCO entity types, lazy loading is achieved by creating instances of derived proxy types and then overriding virtual properties to add the loading hook.</span></span> <span data-ttu-id="4d850-118">比方說，使用下面定義的部落格實體類別時，相關的文章將會載入第一次存取文章導覽屬性時：</span><span class="sxs-lookup"><span data-stu-id="4d850-118">For example, when using the Blog entity class defined below, the related Posts will be loaded the first time the Posts navigation property is accessed:</span></span>  

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

### <a name="turn-lazy-loading-off-for-serialization"></a><span data-ttu-id="4d850-119">開啟的消極式載入關閉序列化</span><span class="sxs-lookup"><span data-stu-id="4d850-119">Turn lazy loading off for serialization</span></span>  

<span data-ttu-id="4d850-120">消極式載入及序列化不要混用，以及如果您不小心得到查詢您的整個資料庫，只是因為在啟用消極式載入。</span><span class="sxs-lookup"><span data-stu-id="4d850-120">Lazy loading and serialization don’t mix well, and if you aren’t careful you can end up querying for your entire database just because lazy loading is enabled.</span></span> <span data-ttu-id="4d850-121">大部分的序列化程式的運作方式存取類型的執行個體上的每個屬性。</span><span class="sxs-lookup"><span data-stu-id="4d850-121">Most serializers work by accessing each property on an instance of a type.</span></span> <span data-ttu-id="4d850-122">屬性存取會觸發消極式載入，因此多個實體進行序列化。</span><span class="sxs-lookup"><span data-stu-id="4d850-122">Property access triggers lazy loading, so more entities get serialized.</span></span> <span data-ttu-id="4d850-123">在這些實體上存取屬性，並載入更多的實體。</span><span class="sxs-lookup"><span data-stu-id="4d850-123">On those entities properties are accessed, and even more entities are loaded.</span></span> <span data-ttu-id="4d850-124">您最好啟用消極式載入關閉之前序列化實體。</span><span class="sxs-lookup"><span data-stu-id="4d850-124">It’s a good practice to turn lazy loading off before you serialize an entity.</span></span> <span data-ttu-id="4d850-125">下列各節示範如何執行這項操作。</span><span class="sxs-lookup"><span data-stu-id="4d850-125">The following sections show how to do this.</span></span>  

### <a name="turning-off-lazy-loading-for-specific-navigation-properties"></a><span data-ttu-id="4d850-126">關閉特定的導覽屬性的消極式載入</span><span class="sxs-lookup"><span data-stu-id="4d850-126">Turning off lazy loading for specific navigation properties</span></span>  

<span data-ttu-id="4d850-127">可以讓 [文章] 屬性的非虛擬，關閉狀態文章集合的消極式載入：</span><span class="sxs-lookup"><span data-stu-id="4d850-127">Lazy loading of the Posts collection can be turned off by making the Posts property non-virtual:</span></span>  

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

<span data-ttu-id="4d850-128">正在載入文章的集合，可仍透過積極式載入 (請參閱*提早載入*上方) 或 Load 方法 (請參閱*明確載入*下方)。</span><span class="sxs-lookup"><span data-stu-id="4d850-128">Loading of the Posts collection can still be achieved using eager loading (see *Eagerly Loading* above) or the Load method (see *Explicitly Loading* below).</span></span>  

### <a name="turn-off-lazy-loading-for-all-entities"></a><span data-ttu-id="4d850-129">關閉所有實體的消極式載入</span><span class="sxs-lookup"><span data-stu-id="4d850-129">Turn off lazy loading for all entities</span></span>  

<span data-ttu-id="4d850-130">消極式載入可關閉的內容中的所有實體的組態屬性上設定旗標。</span><span class="sxs-lookup"><span data-stu-id="4d850-130">Lazy loading can be turned off for all entities in the context by setting a flag on the Configuration property.</span></span> <span data-ttu-id="4d850-131">例如: </span><span class="sxs-lookup"><span data-stu-id="4d850-131">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.LazyLoadingEnabled = false;
    }
}
```  

<span data-ttu-id="4d850-132">載入相關實體，可仍透過積極式載入 (請參閱*提早載入*上方) 或 Load 方法 (請參閱*明確載入*下方)。</span><span class="sxs-lookup"><span data-stu-id="4d850-132">Loading of related entities can still be achieved using eager loading (see *Eagerly Loading* above) or the Load method (see *Explicitly Loading* below).</span></span>  

## <a name="explicitly-loading"></a><span data-ttu-id="4d850-133">明確載入</span><span class="sxs-lookup"><span data-stu-id="4d850-133">Explicitly Loading</span></span>  

<span data-ttu-id="4d850-134">即使是使用已停用消極式載入，仍然有可能延遲載入相關的實體，但必須透過明確呼叫。</span><span class="sxs-lookup"><span data-stu-id="4d850-134">Even with lazy loading disabled it is still possible to lazily load related entities, but it must be done with an explicit call.</span></span> <span data-ttu-id="4d850-135">若要執行這項操作中，您可以使用 Load 方法相關的實體的項目。</span><span class="sxs-lookup"><span data-stu-id="4d850-135">To do so you use the Load method on the related entity’s entry.</span></span> <span data-ttu-id="4d850-136">例如: </span><span class="sxs-lookup"><span data-stu-id="4d850-136">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var post = context.Posts.Find(2);

    // Load the blog related to a given post
    context.Entry(post).Reference(p => p.Blog).Load();

    // Load the blog related to a given post using a string  
    context.Entry(post).Reference("Blog").Load();

    var blog = context.Blogs.Find(1);

    // Load the posts related to a given blog
    context.Entry(blog).Collection(p => p.Posts).Load();

    // Load the posts related to a given blog  
    // using a string to specify the relationship
    context.Entry(blog).Collection("Posts").Load();
}
```  

<span data-ttu-id="4d850-137">請注意當實體有另一個單一實體的導覽屬性，應使用 Reference 方法。</span><span class="sxs-lookup"><span data-stu-id="4d850-137">Note that the Reference method should be used when an entity has a navigation property to another single entity.</span></span> <span data-ttu-id="4d850-138">相反地，當實體有其他實體的集合導覽屬性時，應該使用的收集方法。</span><span class="sxs-lookup"><span data-stu-id="4d850-138">On the other hand, the Collection method should be used when an entity has a navigation property to a collection of other entities.</span></span>  

### <a name="applying-filters-when-explicitly-loading-related-entities"></a><span data-ttu-id="4d850-139">明確載入相關的實體時，套用篩選</span><span class="sxs-lookup"><span data-stu-id="4d850-139">Applying filters when explicitly loading related entities</span></span>  

<span data-ttu-id="4d850-140">查詢方法會提供載入相關的實體時，會使用 Entity Framework 的基礎查詢存取。</span><span class="sxs-lookup"><span data-stu-id="4d850-140">The Query method provides access to the underlying query that Entity Framework will use when loading related entities.</span></span> <span data-ttu-id="4d850-141">您接著可以使用 LINQ 將篩選套用至查詢，然後再執行 ToList、 負載等的 LINQ 擴充方法的呼叫。查詢方法可以搭配參考及集合導覽屬性，但是是最適合用於集合，它可用來載入只有部分的集合。</span><span class="sxs-lookup"><span data-stu-id="4d850-141">You can then use LINQ to apply filters to the query before executing it with a call to a LINQ extension method such as ToList, Load, etc. The Query method can be used with both reference and collection navigation properties but is most useful for collections where it can be used to load only part of the collection.</span></span> <span data-ttu-id="4d850-142">例如: </span><span class="sxs-lookup"><span data-stu-id="4d850-142">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog
    context.Entry(blog)
        .Collection(b => b.Posts)
        .Query()
        .Where(p => p.Tags.Contains("entity-framework")
        .Load();

    // Load the posts with the 'entity-framework' tag related to a given blog  
    // using a string to specify the relationship  
    context.Entry(blog)
        .Collection("Posts")
        .Query()
        .Where(p => p.Tags.Contains("entity-framework")
        .Load();
}
```  

<span data-ttu-id="4d850-143">使用查詢方法時，它通常最好是關閉的導覽屬性的消極式載入。</span><span class="sxs-lookup"><span data-stu-id="4d850-143">When using the Query method it is usually best to turn off lazy loading for the navigation property.</span></span> <span data-ttu-id="4d850-144">這是因為否則整個集合可能會取得自動載入的消極式載入機制之前或之後執行已篩選的查詢。</span><span class="sxs-lookup"><span data-stu-id="4d850-144">This is because otherwise the entire collection may get loaded automatically by the lazy loading mechanism either before or after the filtered query has been executed.</span></span>  

<span data-ttu-id="4d850-145">請注意，雖然可以做為字串，而不是 lambda 運算式中指定的關聯性，傳回的 IQueryable 不泛型字串使用時，因此任何有用的資訊可以使用它之前，通常需要轉換方法。</span><span class="sxs-lookup"><span data-stu-id="4d850-145">Note that while the relationship can be specified as a string instead of a lambda expression, the returned IQueryable is not generic when a string is used and so the Cast method is usually needed before anything useful can be done with it.</span></span>  

## <a name="using-query-to-count-related-entities-without-loading-them"></a><span data-ttu-id="4d850-146">使用查詢計數而不需要載入它們的相關的實體</span><span class="sxs-lookup"><span data-stu-id="4d850-146">Using Query to count related entities without loading them</span></span>  

<span data-ttu-id="4d850-147">有時候最好知道多少實體與相關資料庫中的另一個實體而不實際會產生載入所有這些實體的成本。</span><span class="sxs-lookup"><span data-stu-id="4d850-147">Sometimes it is useful to know how many entities are related to another entity in the database without actually incurring the cost of loading all those entities.</span></span> <span data-ttu-id="4d850-148">查詢方法使用 LINQ 計數方法可用來執行這項操作。</span><span class="sxs-lookup"><span data-stu-id="4d850-148">The Query method with the LINQ Count method can be used to do this.</span></span> <span data-ttu-id="4d850-149">例如: </span><span class="sxs-lookup"><span data-stu-id="4d850-149">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Count how many posts the blog has  
    var postCount = context.Entry(blog)
                          .Collection(b => b.Posts)
                          .Query()
                          .Count();
}
```  
