---
title: 載入相關資料 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: bfd6e161ed7f7bf96e61946f94c8eeadd24a72f5
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434184"
---
# <a name="loading-related-data"></a><span data-ttu-id="1dddd-102">載入相關資料</span><span class="sxs-lookup"><span data-stu-id="1dddd-102">Loading Related Data</span></span>

<span data-ttu-id="1dddd-103">Entity Framework Core 可讓您在模型中使用導覽屬性來載入相關實體。</span><span class="sxs-lookup"><span data-stu-id="1dddd-103">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="1dddd-104">有三種常見的 O/RM 模式可用來載入相關資料。</span><span class="sxs-lookup"><span data-stu-id="1dddd-104">There are three common O/RM patterns used to load related data.</span></span>

* <span data-ttu-id="1dddd-105">**積極式載入**表示會從資料庫以初始查詢一部分的方式載入相關資料。</span><span class="sxs-lookup"><span data-stu-id="1dddd-105">**Eager loading** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="1dddd-106">**明確式載入**表示會從資料庫於稍後以明確方式載入相關資料。</span><span class="sxs-lookup"><span data-stu-id="1dddd-106">**Explicit loading** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="1dddd-107">**消極式載入**表示會於存取導覽屬性時從資料庫以透明的方式載入相關資料。</span><span class="sxs-lookup"><span data-stu-id="1dddd-107">**Lazy loading** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span>

> [!TIP]  
> <span data-ttu-id="1dddd-108">您可以在 GitHub 上查看本文[的範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying)。</span><span class="sxs-lookup"><span data-stu-id="1dddd-108">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="1dddd-109">積極式載入</span><span class="sxs-lookup"><span data-stu-id="1dddd-109">Eager loading</span></span>

<span data-ttu-id="1dddd-110">您可以使用 `Include` 方法來指定要包含於查詢結果中的相關資料。</span><span class="sxs-lookup"><span data-stu-id="1dddd-110">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="1dddd-111">在下列範例中，於結果中所傳回部落格的 `Posts` 屬性將會填入相關文章。</span><span class="sxs-lookup"><span data-stu-id="1dddd-111">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> <span data-ttu-id="1dddd-112">Entity Framework Core 會將導覽屬性自動修正為先前已載入至內容執行個體的任何其他實體。</span><span class="sxs-lookup"><span data-stu-id="1dddd-112">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="1dddd-113">因此，即使未明確包含導覽屬性的資料，如果先前已載入部分或所有相關的實體，則仍然可能會填入該屬性。</span><span class="sxs-lookup"><span data-stu-id="1dddd-113">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

<span data-ttu-id="1dddd-114">您可以將來自多個關聯性的相關資料包含至單一查詢。</span><span class="sxs-lookup"><span data-stu-id="1dddd-114">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a><span data-ttu-id="1dddd-115">包括多個層級</span><span class="sxs-lookup"><span data-stu-id="1dddd-115">Including multiple levels</span></span>

<span data-ttu-id="1dddd-116">您可以使用 `ThenInclude` 方法，透過關聯性向下切入以包含多個層級的相關資料。</span><span class="sxs-lookup"><span data-stu-id="1dddd-116">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="1dddd-117">下列範例會載入所有部落格、其相關文章，以及每篇文章的作者。</span><span class="sxs-lookup"><span data-stu-id="1dddd-117">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleThenInclude)]

<span data-ttu-id="1dddd-118">您可以將多個呼叫鏈結到 `ThenInclude`，以繼續包含更深層級的相關資料。</span><span class="sxs-lookup"><span data-stu-id="1dddd-118">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

<span data-ttu-id="1dddd-119">您可以結合上述全部，以便在相同查詢中包含來自多個層級和多個根的相關資料。</span><span class="sxs-lookup"><span data-stu-id="1dddd-119">You can combine all of this to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#IncludeTree)]

<span data-ttu-id="1dddd-120">您可能會想要針對所包括的其中一個實體包含多個相關實體。</span><span class="sxs-lookup"><span data-stu-id="1dddd-120">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="1dddd-121">例如，查詢 `Blogs` 時，您包括了 `Posts`，接著想要同時包含 `Posts` 的 `Author` 和 `Tags`。</span><span class="sxs-lookup"><span data-stu-id="1dddd-121">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="1dddd-122">若要執行此動作，您必須指定每個從根開始的包含路徑。</span><span class="sxs-lookup"><span data-stu-id="1dddd-122">To do this, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="1dddd-123">例如 `Blog -> Posts -> Author` 和 `Blog -> Posts -> Tags`。</span><span class="sxs-lookup"><span data-stu-id="1dddd-123">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="1dddd-124">這不表示您將會收到多餘的聯結，在大部分情況下，EF 會在產生 SQL 時合併聯結。</span><span class="sxs-lookup"><span data-stu-id="1dddd-124">This does not mean you will get redundant joins; in most cases, EF will consolidate the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

> [!CAUTION]
> <span data-ttu-id="1dddd-125">自版本 3.0.0`Include`起,每個版本將導致將附加 JOIN 添加到關係提供程式生成的 SQL 查詢中,而早期版本生成了其他 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="1dddd-125">Since version 3.0.0, each `Include` will cause an additional JOIN to be added to SQL queries produced by relational providers, whereas previous versions generated additional SQL queries.</span></span> <span data-ttu-id="1dddd-126">這可以顯著更改查詢的性能,無論好壞。</span><span class="sxs-lookup"><span data-stu-id="1dddd-126">This can significantly change the performance of your queries, for better or worse.</span></span> <span data-ttu-id="1dddd-127">特別是,具有極高數量的運算符的`Include`LINQ 查詢可能需要分解為多個單獨的 LINQ 查詢,以避免卡點分解問題。</span><span class="sxs-lookup"><span data-stu-id="1dddd-127">In particular, LINQ queries with an exceedingly high number of `Include` operators may need to be broken down into multiple separate LINQ queries in order to avoid the cartesian explosion problem.</span></span>

### <a name="filtered-include"></a><span data-ttu-id="1dddd-128">篩選包括</span><span class="sxs-lookup"><span data-stu-id="1dddd-128">Filtered include</span></span>

> [!NOTE]
> <span data-ttu-id="1dddd-129">此功能在 EF Core 5.0 中介紹。</span><span class="sxs-lookup"><span data-stu-id="1dddd-129">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="1dddd-130">當應用"包括"載入相關數據時,可以在包含的集合導航上應用某些枚舉操作,從而允許篩選和排序結果。</span><span class="sxs-lookup"><span data-stu-id="1dddd-130">When applying Include to load related data, you can apply certain enumerable operations on the included collection navigation, which allows for filtering and sorting of the results.</span></span>

<span data-ttu-id="1dddd-131">支援的操作包括: `Where` `OrderBy` `OrderByDescending``Take``ThenBy``ThenByDescending``Skip`、、、、、、 與 。</span><span class="sxs-lookup"><span data-stu-id="1dddd-131">Supported operations are: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take`.</span></span>

<span data-ttu-id="1dddd-132">此類操作應應用於傳遞給 Include 方法的 lambda 中的集合導航上,如下所示:</span><span class="sxs-lookup"><span data-stu-id="1dddd-132">Such operations should be applied on the collection navigation in the lambda passed to the Include method, as shown in example below:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#FilteredInclude)]

<span data-ttu-id="1dddd-133">每個包含的導航只允許一組唯一的篩選器操作。</span><span class="sxs-lookup"><span data-stu-id="1dddd-133">Each included navigation allows only one unique set of filter operations.</span></span> <span data-ttu-id="1dddd-134">如果對給定集合導航應用多個 Include`blog.Posts`操作( 在下面的範例中),只能在其中一個操作上指定篩選器操作:</span><span class="sxs-lookup"><span data-stu-id="1dddd-134">In cases where multiple Include operations are applied for a given collection navigation (`blog.Posts` in the examples below), filter operations can only be specified on one of them:</span></span> 

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered1)]

<span data-ttu-id="1dddd-135">或者,可以對包含多次的每個導航應用相同的操作:</span><span class="sxs-lookup"><span data-stu-id="1dddd-135">Alternatively, identical operations can be applied for each navigation that is included multiple times:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered2)]

### <a name="include-on-derived-types"></a><span data-ttu-id="1dddd-136">衍生類型中的 Include</span><span class="sxs-lookup"><span data-stu-id="1dddd-136">Include on derived types</span></span>

<span data-ttu-id="1dddd-137">您可以使用 `Include` 和 `ThenInclude`來包含只定義於衍生類型上導覽的相關資料。</span><span class="sxs-lookup"><span data-stu-id="1dddd-137">You can include related data from navigations defined only on a derived type using `Include` and `ThenInclude`.</span></span>

<span data-ttu-id="1dddd-138">假設有下列模型：</span><span class="sxs-lookup"><span data-stu-id="1dddd-138">Given the following model:</span></span>

```csharp
public class SchoolContext : DbContext
{
    public DbSet<Person> People { get; set; }
    public DbSet<School> Schools { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<School>().HasMany(s => s.Students).WithOne(s => s.School);
    }
}

public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Student : Person
{
    public School School { get; set; }
}

public class School
{
    public int Id { get; set; }
    public string Name { get; set; }

    public List<Student> Students { get; set; }
}
```

<span data-ttu-id="1dddd-139">身分為學生之所有人員的 `School` 導覽內容可以使用多個模式進行積極式載入：</span><span class="sxs-lookup"><span data-stu-id="1dddd-139">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

* <span data-ttu-id="1dddd-140">使用 cast</span><span class="sxs-lookup"><span data-stu-id="1dddd-140">using cast</span></span>

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* <span data-ttu-id="1dddd-141">使用 `as` 運算子</span><span class="sxs-lookup"><span data-stu-id="1dddd-141">using `as` operator</span></span>

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* <span data-ttu-id="1dddd-142">使用 `Include` 的多載，其會接受類型 `string` 的參數</span><span class="sxs-lookup"><span data-stu-id="1dddd-142">using overload of `Include` that takes parameter of type `string`</span></span>

  ```csharp
  context.People.Include("School").ToList()
  ```

## <a name="explicit-loading"></a><span data-ttu-id="1dddd-143">明確式載入</span><span class="sxs-lookup"><span data-stu-id="1dddd-143">Explicit loading</span></span>

<span data-ttu-id="1dddd-144">您可以透過 `DbContext.Entry(...)` API 來明確地載入導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="1dddd-144">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

<span data-ttu-id="1dddd-145">您也可以透過執行會傳回相關實體的個別查詢，來明確地載入導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="1dddd-145">You can also explicitly load a navigation property by executing a separate query that returns the related entities.</span></span> <span data-ttu-id="1dddd-146">如果啟用變更追蹤，則 EF Core 在載入實體時將會自動設定新載入實體的導覽屬性來參考已經載入的任何實體，並將已載入實體的導覽屬性設定為參考新載入的實體。</span><span class="sxs-lookup"><span data-stu-id="1dddd-146">If change tracking is enabled, then when loading an entity, EF Core will automatically set the navigation properties of the newly-loaded entitiy to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly-loaded entity.</span></span>

### <a name="querying-related-entities"></a><span data-ttu-id="1dddd-147">查詢相關實體</span><span class="sxs-lookup"><span data-stu-id="1dddd-147">Querying related entities</span></span>

<span data-ttu-id="1dddd-148">您也可以取得表示導覽屬性內容的 LINQ 查詢。</span><span class="sxs-lookup"><span data-stu-id="1dddd-148">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="1dddd-149">這可讓您執行像是透過相關實體來執行彙總運算子，而不需將其載入至記憶體之類的動作。</span><span class="sxs-lookup"><span data-stu-id="1dddd-149">This allows you to do things such as running an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

<span data-ttu-id="1dddd-150">您也可以篩選要將哪些相關實體載入至記憶體。</span><span class="sxs-lookup"><span data-stu-id="1dddd-150">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a><span data-ttu-id="1dddd-151">消極式載入</span><span class="sxs-lookup"><span data-stu-id="1dddd-151">Lazy loading</span></span>

<span data-ttu-id="1dddd-152">使用消極式載入的最簡單方式是安裝 [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) \(英文\) 套件，並呼叫 `UseLazyLoadingProxies` 來啟用它。</span><span class="sxs-lookup"><span data-stu-id="1dddd-152">The simplest way to use lazy-loading is by installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package and enabling it with a call to `UseLazyLoadingProxies`.</span></span> <span data-ttu-id="1dddd-153">例如：</span><span class="sxs-lookup"><span data-stu-id="1dddd-153">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```

<span data-ttu-id="1dddd-154">或在使用 AddDbContext 時：</span><span class="sxs-lookup"><span data-stu-id="1dddd-154">Or when using AddDbContext:</span></span>

```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```

<span data-ttu-id="1dddd-155">EF Core 接著將針對可覆寫的所有導覽屬性 (也就是說，它必須是 `virtual` 並位於可繼承的類別上) 啟用消極式載入。</span><span class="sxs-lookup"><span data-stu-id="1dddd-155">EF Core will then enable lazy loading for any navigation property that can be overridden--that is, it must be `virtual` and on a class that can be inherited from.</span></span> <span data-ttu-id="1dddd-156">例如，在下列實體中，系統將會對 `Post.Blog` 和 `Blog.Posts` 導覽屬性進行消極式載入。</span><span class="sxs-lookup"><span data-stu-id="1dddd-156">For example, in the following entities, the `Post.Blog` and `Blog.Posts` navigation properties will be lazy-loaded.</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public virtual Blog Blog { get; set; }
}
```

### <a name="lazy-loading-without-proxies"></a><span data-ttu-id="1dddd-157">沒有 Proxy 的消極式載入</span><span class="sxs-lookup"><span data-stu-id="1dddd-157">Lazy loading without proxies</span></span>

<span data-ttu-id="1dddd-158">消極式載入 Proxy 的運作方式是將 `ILazyLoader` 服務插入至實體，如[實體類型建構函式](../modeling/constructors.md)中所述。</span><span class="sxs-lookup"><span data-stu-id="1dddd-158">Lazy-loading proxies work by injecting the `ILazyLoader` service into an entity, as described in [Entity Type Constructors](../modeling/constructors.md).</span></span> <span data-ttu-id="1dddd-159">例如：</span><span class="sxs-lookup"><span data-stu-id="1dddd-159">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="1dddd-160">這並不需要實體類型為可繼承的，或是導覽屬性為虛擬的，並且可讓使用 `new` 建立的實體執行個體可以在附加到內容之後進行消極式載入。</span><span class="sxs-lookup"><span data-stu-id="1dddd-160">This doesn't require entity types to be inherited from or navigation properties to be virtual, and allows entity instances created with `new` to lazy-load once attached to a context.</span></span> <span data-ttu-id="1dddd-161">不過，它需要對 `ILazyLoader` 服務的參考，這在 [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) 套件中定義。</span><span class="sxs-lookup"><span data-stu-id="1dddd-161">However, it requires a reference to the `ILazyLoader` service, which is defined in the [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) package.</span></span> <span data-ttu-id="1dddd-162">此套件包含最基本的型別集，因此相依於它的影響很小。</span><span class="sxs-lookup"><span data-stu-id="1dddd-162">This package contains a minimal set of types so that there is very little impact in depending on it.</span></span> <span data-ttu-id="1dddd-163">不過，若要完全避免對實體型別中任何 EF Core 套件的相依性，您可以將 `ILazyLoader.Load` 方法插入為委派。</span><span class="sxs-lookup"><span data-stu-id="1dddd-163">However, to completely avoid depending on any EF Core packages in the entity types, it is possible to inject the `ILazyLoader.Load` method as a delegate.</span></span> <span data-ttu-id="1dddd-164">例如：</span><span class="sxs-lookup"><span data-stu-id="1dddd-164">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="1dddd-165">上述程式碼會使用 `Load` 擴充方法，來更簡潔地使用委派：</span><span class="sxs-lookup"><span data-stu-id="1dddd-165">The code above uses a `Load` extension method to make using the delegate a bit cleaner:</span></span>

```csharp
public static class PocoLoadingExtensions
{
    public static TRelated Load<TRelated>(
        this Action<object, string> loader,
        object entity,
        ref TRelated navigationField,
        [CallerMemberName] string navigationName = null)
        where TRelated : class
    {
        loader?.Invoke(entity, navigationName);

        return navigationField;
    }
}
```

> [!NOTE]  
> <span data-ttu-id="1dddd-166">消極式載入委派的建構函式參數必須稱為 "lazyLoader"。</span><span class="sxs-lookup"><span data-stu-id="1dddd-166">The constructor parameter for the lazy-loading delegate must be called "lazyLoader".</span></span> <span data-ttu-id="1dddd-167">預計將於未來版本中推出使用與此不同之名稱的設定。</span><span class="sxs-lookup"><span data-stu-id="1dddd-167">Configuration to use a different name than this is planned for a future release.</span></span>

## <a name="related-data-and-serialization"></a><span data-ttu-id="1dddd-168">相關資料和序列化</span><span class="sxs-lookup"><span data-stu-id="1dddd-168">Related data and serialization</span></span>

<span data-ttu-id="1dddd-169">由於 EF Core 將會自動修正導覽屬性，您的物件圖形最後可能會出現循環。</span><span class="sxs-lookup"><span data-stu-id="1dddd-169">Because EF Core will automatically fix-up navigation properties, you can end up with cycles in your object graph.</span></span> <span data-ttu-id="1dddd-170">例如，載入部落格與其相關文章將會產生參考文章集合的部落格物件。</span><span class="sxs-lookup"><span data-stu-id="1dddd-170">For example, loading a blog and its related posts will result in a blog object that references a collection of posts.</span></span> <span data-ttu-id="1dddd-171">那些文章都會具有針對該部落格的參考。</span><span class="sxs-lookup"><span data-stu-id="1dddd-171">Each of those posts will have a reference back to the blog.</span></span>

<span data-ttu-id="1dddd-172">某些序列化架構並不允許這類循環。</span><span class="sxs-lookup"><span data-stu-id="1dddd-172">Some serialization frameworks do not allow such cycles.</span></span> <span data-ttu-id="1dddd-173">例如，Json.NET 將會在遇到循環時擲回下列例外狀況。</span><span class="sxs-lookup"><span data-stu-id="1dddd-173">For example, Json.NET will throw the following exception if a cycle is encountered.</span></span>

> <span data-ttu-id="1dddd-174">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog' (針對具有類型 'MyApplication.Models.Blog' 的屬性 'Blog' 偵測到自我參考迴圈)。</span><span class="sxs-lookup"><span data-stu-id="1dddd-174">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span></span>

<span data-ttu-id="1dddd-175">如果您使用 ASP.NET Core，則可將 Json.NET 設定為略過它在物件圖形中所找到的循環。</span><span class="sxs-lookup"><span data-stu-id="1dddd-175">If you are using ASP.NET Core, you can configure Json.NET to ignore cycles that it finds in the object graph.</span></span> <span data-ttu-id="1dddd-176">這可在 `Startup.cs` 的 `ConfigureServices(...)` 方法中完成。</span><span class="sxs-lookup"><span data-stu-id="1dddd-176">This is done in the `ConfigureServices(...)` method in `Startup.cs`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddMvc()
        .AddJsonOptions(
            options => options.SerializerSettings.ReferenceLoopHandling = Newtonsoft.Json.ReferenceLoopHandling.Ignore
        );

    ...
}
```

<span data-ttu-id="1dddd-177">另一個替代方式為使用 `[JsonIgnore]` 屬性裝飾導覽屬性的其中一個，它會指示 Json.NET 序列化時不要周遊該導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="1dddd-177">Another alternative is to decorate one of the navigation properties with the `[JsonIgnore]` attribute, which instructs Json.NET to not traverse that navigation property while serializing.</span></span>
