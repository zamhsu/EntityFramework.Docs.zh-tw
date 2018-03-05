---
title: "正在載入相關的資料-EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
ms.technology: entity-framework-core
uid: core/querying/related-data
ms.openlocfilehash: 0d7705e0e5368435536e98d319c853ea8c732643
ms.sourcegitcommit: 8f3be0a2a394253efb653388ec66bda964e5ee1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2018
---
# <a name="loading-related-data"></a><span data-ttu-id="57f09-102">載入相關的資料</span><span class="sxs-lookup"><span data-stu-id="57f09-102">Loading Related Data</span></span>

<span data-ttu-id="57f09-103">Entity Framework Core 可讓您在模型中使用的導覽屬性，來載入相關的實體。</span><span class="sxs-lookup"><span data-stu-id="57f09-103">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="57f09-104">有三種常見的 O/RM 模式來載入相關的資料。</span><span class="sxs-lookup"><span data-stu-id="57f09-104">There are three common O/RM patterns used to load related data.</span></span>
* <span data-ttu-id="57f09-105">**積極式載入**表示相關的資料從資料庫載入做為初始查詢的一部分。</span><span class="sxs-lookup"><span data-stu-id="57f09-105">**Eager loading** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="57f09-106">**明確式載入**表示相關的資料會明確地載入從資料庫在稍後。</span><span class="sxs-lookup"><span data-stu-id="57f09-106">**Explicit loading** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="57f09-107">**消極式載入**表示，相關的資料會以透明的方式從資料庫載入時存取導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="57f09-107">**Lazy loading** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span>

> [!TIP]  
> <span data-ttu-id="57f09-108">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="57f09-108">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="57f09-109">積極式載入</span><span class="sxs-lookup"><span data-stu-id="57f09-109">Eager loading</span></span>

<span data-ttu-id="57f09-110">您可以使用`Include`方法，以指定要包含在查詢結果的相關的資料。</span><span class="sxs-lookup"><span data-stu-id="57f09-110">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="57f09-111">在下列範例中，會在結果中傳回部落格會有其`Posts`屬性填入相關的文章。</span><span class="sxs-lookup"><span data-stu-id="57f09-111">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> <span data-ttu-id="57f09-112">實體 Framework Core 將會自動修正向上導覽屬性與先前已載入至內容執行個體的任何其他實體。</span><span class="sxs-lookup"><span data-stu-id="57f09-112">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="57f09-113">因此即使沒有明確加入的資料瀏覽屬性，屬性可能仍然會填入某些或所有相關的項目先前已載入。</span><span class="sxs-lookup"><span data-stu-id="57f09-113">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>


<span data-ttu-id="57f09-114">您可以在單一查詢中包含多個關聯性中的相關的資料。</span><span class="sxs-lookup"><span data-stu-id="57f09-114">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a><span data-ttu-id="57f09-115">包括多個層級</span><span class="sxs-lookup"><span data-stu-id="57f09-115">Including multiple levels</span></span>

<span data-ttu-id="57f09-116">您可以向下鑽研到包含多個層級的相關的資料所使用的關聯性`ThenInclude`方法。</span><span class="sxs-lookup"><span data-stu-id="57f09-116">You can drill down thru relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="57f09-117">下列範例會載入所有的部落格、 其相關的文章，每次張貼的作者。</span><span class="sxs-lookup"><span data-stu-id="57f09-117">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#SingleThenInclude)]

> [!NOTE]  
> <span data-ttu-id="57f09-118">目前版本的 Visual Studio 提供不正確的程式碼完成的選項，而且可能會導致標示有語法錯誤時使用的正確運算式`ThenInclude`之後集合導覽屬性的方法。</span><span class="sxs-lookup"><span data-stu-id="57f09-118">Current versions of Visual Studio offer incorrect code completion options and can cause correct expressions to be flagged with syntax errors when using the `ThenInclude` method after a collection navigation property.</span></span> <span data-ttu-id="57f09-119">這是在 https://github.com/dotnet/roslyn/issues/8237 追蹤 IntelliSense 問題的徵兆。</span><span class="sxs-lookup"><span data-stu-id="57f09-119">This is a symptom of an IntelliSense bug tracked at https://github.com/dotnet/roslyn/issues/8237.</span></span> <span data-ttu-id="57f09-120">它可以安全地忽略這些假性的語法錯誤，只要程式碼正確，而且可以成功編譯。</span><span class="sxs-lookup"><span data-stu-id="57f09-120">It is safe to ignore these spurious syntax errors as long as the code is correct and can be compiled successfully.</span></span> 

<span data-ttu-id="57f09-121">您可以多個呼叫鏈結到`ThenInclude`繼續進一步包括層級的相關資料。</span><span class="sxs-lookup"><span data-stu-id="57f09-121">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

<span data-ttu-id="57f09-122">您可以結合這些全部都在相同查詢中包含相關的資料從多個層級和多個根節點。</span><span class="sxs-lookup"><span data-stu-id="57f09-122">You can combine all of this to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#IncludeTree)]

<span data-ttu-id="57f09-123">若要包含的其中一個實體所包含的多個相關的實體。</span><span class="sxs-lookup"><span data-stu-id="57f09-123">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="57f09-124">例如，當查詢`Blog`s，包括`Posts`接著又想將同時包含`Author`和`Tags`的`Posts`。</span><span class="sxs-lookup"><span data-stu-id="57f09-124">For example, when querying `Blog`s, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="57f09-125">若要這樣做，您要指定每個包含從根目錄開始的路徑。</span><span class="sxs-lookup"><span data-stu-id="57f09-125">To do this, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="57f09-126">例如，`Blog -> Posts -> Author`和`Blog -> Posts -> Tags`。</span><span class="sxs-lookup"><span data-stu-id="57f09-126">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="57f09-127">這不表示您會收到多餘的聯結，在大部分情況下，將會合併 EF 聯結產生 SQL 時。</span><span class="sxs-lookup"><span data-stu-id="57f09-127">This does not mean you will get redundant joins, in most cases EF will consolidate the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

### <a name="include-on-derived-types"></a><span data-ttu-id="57f09-128">在衍生類型上包含</span><span class="sxs-lookup"><span data-stu-id="57f09-128">Include on derived types</span></span>

<span data-ttu-id="57f09-129">您可以加入只在使用衍生的類型上定義導覽中的相關的資料`Include`和`ThenInclude`。</span><span class="sxs-lookup"><span data-stu-id="57f09-129">You can include related data from navigations defined only on a derived type using `Include` and `ThenInclude`.</span></span> 

<span data-ttu-id="57f09-130">假設下列模型：</span><span class="sxs-lookup"><span data-stu-id="57f09-130">Given the following model:</span></span>

```Csharp
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

<span data-ttu-id="57f09-131">內容`School`瀏覽的所有人學生可以立即載入使用的數字的模式：</span><span class="sxs-lookup"><span data-stu-id="57f09-131">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

- <span data-ttu-id="57f09-132">使用 cast</span><span class="sxs-lookup"><span data-stu-id="57f09-132">using cast</span></span>
```Csharp
context.People.Include(person => ((Student)person).School).ToList()
```

- <span data-ttu-id="57f09-133">使用`as`運算子</span><span class="sxs-lookup"><span data-stu-id="57f09-133">using `as` operator</span></span>
```Csharp
context.People.Include(person => (person as Student).School).ToList()
```

- <span data-ttu-id="57f09-134">使用的多載`Include`會接受參數的型別 `string`</span><span class="sxs-lookup"><span data-stu-id="57f09-134">using overload of `Include` that takes parameter of type `string`</span></span>
```Csharp
context.People.Include("Student").ToList()
```

### <a name="ignored-includes"></a><span data-ttu-id="57f09-135">忽略包含</span><span class="sxs-lookup"><span data-stu-id="57f09-135">Ignored includes</span></span>

<span data-ttu-id="57f09-136">如果您變更查詢，使它不會再傳回查詢開始的實體類型的執行個體時，會忽略 include 運算子。</span><span class="sxs-lookup"><span data-stu-id="57f09-136">If you change the query so that it no longer returns instances of the entity type that the query began with, then the include operators are ignored.</span></span>

<span data-ttu-id="57f09-137">在下列範例中，包含運算子會根據`Blog`，但然後`Select`運算子用來變更查詢以傳回匿名型別。</span><span class="sxs-lookup"><span data-stu-id="57f09-137">In the following example, the include operators are based on the `Blog`, but then the `Select` operator is used to change the query to return an anonymous type.</span></span> <span data-ttu-id="57f09-138">在此情況下，包含運算子會有任何作用。</span><span class="sxs-lookup"><span data-stu-id="57f09-138">In this case, the include operators have no effect.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#IgnoredInclude)]

<span data-ttu-id="57f09-139">根據預設，EF 核心會記錄警告時包含運算子都會被忽略。</span><span class="sxs-lookup"><span data-stu-id="57f09-139">By default, EF Core will log a warning when include operators are ignored.</span></span> <span data-ttu-id="57f09-140">請參閱[記錄](../miscellaneous/logging.md)檢視記錄輸出的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="57f09-140">See [Logging](../miscellaneous/logging.md) for more information on viewing logging output.</span></span> <span data-ttu-id="57f09-141">對擲回或不執行任何動作，則會忽略 include 運算子時，您可以變更行為。</span><span class="sxs-lookup"><span data-stu-id="57f09-141">You can change the behavior when an include operator is ignored to either throw or do nothing.</span></span> <span data-ttu-id="57f09-142">這是通常在設定為您的內容-選項時`DbContext.OnConfiguring`，或在`Startup.cs`如果您使用 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="57f09-142">This is done when setting up the options for your context - typically in `DbContext.OnConfiguring`, or in `Startup.cs` if you are using ASP.NET Core.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/ThrowOnIgnoredInclude/BloggingContext.cs#OnConfiguring)]

## <a name="explicit-loading"></a><span data-ttu-id="57f09-143">明確式載入</span><span class="sxs-lookup"><span data-stu-id="57f09-143">Explicit loading</span></span>

> [!NOTE]  
> <span data-ttu-id="57f09-144">在 EF 核心 1.1 已引入此功能。</span><span class="sxs-lookup"><span data-stu-id="57f09-144">This feature was introduced in EF Core 1.1.</span></span>

<span data-ttu-id="57f09-145">您可以明確載入的導覽屬性，透過`DbContext.Entry(...)`應用程式開發介面。</span><span class="sxs-lookup"><span data-stu-id="57f09-145">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#Eager)]

<span data-ttu-id="57f09-146">您也會明確可以藉由執行不同的查詢會傳回相關的實體載入導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="57f09-146">You can also explicitly load a navigation property by executing a seperate query that returns the related entities.</span></span> <span data-ttu-id="57f09-147">如果已啟用變更追蹤，則載入時實體，EF Core 將會自動設定來參考任何實體已經載入，新載入實體的導覽屬性並設定參考的已載入實體的導覽屬性新載入的實體。</span><span class="sxs-lookup"><span data-stu-id="57f09-147">If change tracking is enabled, then when loading an entity, EF Core will automatically set the navigation properties of the newly-loaded entitiy to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly-loaded entity.</span></span>

### <a name="querying-related-entities"></a><span data-ttu-id="57f09-148">查詢相關的實體</span><span class="sxs-lookup"><span data-stu-id="57f09-148">Querying related entities</span></span>

<span data-ttu-id="57f09-149">您也可以取得 LINQ 查詢，表示導覽屬性的內容。</span><span class="sxs-lookup"><span data-stu-id="57f09-149">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="57f09-150">這可讓您執行像是透過相關的項目執行彙總運算子，而不載入記憶體。</span><span class="sxs-lookup"><span data-stu-id="57f09-150">This allows you to do things such as running an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

<span data-ttu-id="57f09-151">您也可以篩選哪些相關的實體載入至記憶體。</span><span class="sxs-lookup"><span data-stu-id="57f09-151">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a><span data-ttu-id="57f09-152">消極式載入</span><span class="sxs-lookup"><span data-stu-id="57f09-152">Lazy loading</span></span>

> [!NOTE]  
> <span data-ttu-id="57f09-153">在 EF 核心 2.1 中已引入此功能。</span><span class="sxs-lookup"><span data-stu-id="57f09-153">This feature was introduced in EF Core 2.1.</span></span>

<span data-ttu-id="57f09-154">若要使用消極式載入最簡單的方式是安裝[Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/)封裝，並讓它藉由呼叫`UseLazyLoadingProxies`。</span><span class="sxs-lookup"><span data-stu-id="57f09-154">The simplest way to use lazy-loading is by installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package and enabling it with a call to `UseLazyLoadingProxies`.</span></span> <span data-ttu-id="57f09-155">例如: </span><span class="sxs-lookup"><span data-stu-id="57f09-155">For example:</span></span>
```Csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```
<span data-ttu-id="57f09-156">或使用 AddDbContext 時：</span><span class="sxs-lookup"><span data-stu-id="57f09-156">Or when using AddDbContext:</span></span>
```Csharp
    .AddDbContext<BloggingContext>(
        b => b.UseLazyLoadingProxies()
              .UseSqlServer(myConnectionString));
```
<span data-ttu-id="57f09-157">EF 核心然後會啟用消極式載入瀏覽的所有屬性會覆寫-也就是說，它必須是`virtual`和可以繼承自一個類別上。</span><span class="sxs-lookup"><span data-stu-id="57f09-157">EF Core will then enable lazy-loading for any navigation property that can be overridden--that is, it must be `virtual` and on a class that can be inherited from.</span></span> <span data-ttu-id="57f09-158">例如，在下列的實體，`Post.Blog`和`Blog.Posts`導覽屬性將會延遲載入。</span><span class="sxs-lookup"><span data-stu-id="57f09-158">For example, in the following entities, the `Post.Blog` and `Blog.Posts` navigation properties will be lazy-loaded.</span></span>
```Csharp
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
### <a name="lazy-loading-without-proxies"></a><span data-ttu-id="57f09-159">沒有 proxy 的延遲載入</span><span class="sxs-lookup"><span data-stu-id="57f09-159">Lazy-loading without proxies</span></span>

<span data-ttu-id="57f09-160">延遲載入 proxy 的運作方式是將，以便`ILazyLoader`中所述，服務到實體，[實體型別建構函式](../modeling/constructors.md)。</span><span class="sxs-lookup"><span data-stu-id="57f09-160">Lazy-loading proxies work by injecting the `ILazyLoader` service into an entity, as described in [Entity Type Constructors](../modeling/constructors.md).</span></span> <span data-ttu-id="57f09-161">例如: </span><span class="sxs-lookup"><span data-stu-id="57f09-161">For example:</span></span>
```Csharp
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
        get => LazyLoader?.Load(this, ref _posts);
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
        get => LazyLoader?.Load(this, ref _blog);
        set => _blog = value;
    }
}
```
<span data-ttu-id="57f09-162">這不需要用於繼承的實體類型或為虛擬的導覽屬性，並可讓實體執行個體，以建立`new`延遲載入一次附加至內容。</span><span class="sxs-lookup"><span data-stu-id="57f09-162">This doesn't require entity types to be inherited from or navigation properties to be virtual and allows entity instances created with `new` to lazy-load once attached to a context.</span></span> <span data-ttu-id="57f09-163">不過，它需要的參考`ILazyLoader`涉入 EF 核心組件的實體類型的服務。</span><span class="sxs-lookup"><span data-stu-id="57f09-163">However, it requires a reference to the `ILazyLoader` service, which couples entity types to the EF Core assembly.</span></span> <span data-ttu-id="57f09-164">若要避免此 EF 核心允許`ILazyLoader.Load`無法插入為委派的方法。</span><span class="sxs-lookup"><span data-stu-id="57f09-164">To avoid this EF Core allows the `ILazyLoader.Load` method to be injected as a delegate.</span></span> <span data-ttu-id="57f09-165">例如: </span><span class="sxs-lookup"><span data-stu-id="57f09-165">For example:</span></span>
```Csharp
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
        get => LazyLoader?.Load(this, ref _posts);
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
        get => LazyLoader?.Load(this, ref _blog);
        set => _blog = value;
    }
}
```
<span data-ttu-id="57f09-166">使用上方的程式碼`Load`擴充方法，請使用 「 委派 」 的位元會清除：</span><span class="sxs-lookup"><span data-stu-id="57f09-166">The code above uses a `Load` extension method to make using the delegate a bit cleaner:</span></span>
```Csharp
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
> <span data-ttu-id="57f09-167">消極式載入委派建構函式參數必須為"lazyLoader"。</span><span class="sxs-lookup"><span data-stu-id="57f09-167">The constructor parameter for the lazy-loading delegate must be called "lazyLoader".</span></span> <span data-ttu-id="57f09-168">若要使用不同的名稱，這計劃在未來的版本設定。</span><span class="sxs-lookup"><span data-stu-id="57f09-168">Configuration to use a different name this is planned for a future release.</span></span>

## <a name="related-data-and-serialization"></a><span data-ttu-id="57f09-169">相關的資料和序列化</span><span class="sxs-lookup"><span data-stu-id="57f09-169">Related data and serialization</span></span>

<span data-ttu-id="57f09-170">因為 EF 核心會自動修正向上導覽屬性，您可以得到循環物件圖形中。</span><span class="sxs-lookup"><span data-stu-id="57f09-170">Because EF Core will automatically fix-up navigation properties, you can end up with cycles in your object graph.</span></span> <span data-ttu-id="57f09-171">例如，載入部落格和它相關的文章將會導致參考的文章集合部落格物件。</span><span class="sxs-lookup"><span data-stu-id="57f09-171">For example, Loading a blog and it's related posts will result in a blog object that references a collection of posts.</span></span> <span data-ttu-id="57f09-172">這些文章中的每個都會有部落格的參考。</span><span class="sxs-lookup"><span data-stu-id="57f09-172">Each of those posts will have a reference back to the blog.</span></span>

<span data-ttu-id="57f09-173">某些序列化架構不允許這類循環。</span><span class="sxs-lookup"><span data-stu-id="57f09-173">Some serialization frameworks do not allow such cycles.</span></span> <span data-ttu-id="57f09-174">例如，Json.NET 將會擲回下列例外狀況，如果發生循環。</span><span class="sxs-lookup"><span data-stu-id="57f09-174">For example, Json.NET will throw the following exception if a cycle is encountered.</span></span>

> <span data-ttu-id="57f09-175">Newtonsoft.Json.JsonSerializationException： 自我參考型別 'MyApplication.Models.Blog' 屬性 '部落格' 偵測到迴圈。</span><span class="sxs-lookup"><span data-stu-id="57f09-175">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span></span>

<span data-ttu-id="57f09-176">如果您使用 ASP.NET Core，您可以設定 Json.NET 忽略它找到的物件圖形中的循環。</span><span class="sxs-lookup"><span data-stu-id="57f09-176">If you are using ASP.NET Core, you can configure Json.NET to ignore cycles that it finds in the object graph.</span></span> <span data-ttu-id="57f09-177">這是`ConfigureServices(...)`方法中的`Startup.cs`。</span><span class="sxs-lookup"><span data-stu-id="57f09-177">This is done in the `ConfigureServices(...)` method in `Startup.cs`.</span></span>

``` csharp
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
