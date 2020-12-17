---
title: 相關資料的消極式載入-EF Core
description: 使用 Entity Framework Core 消極式載入相關資料
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/lazy
ms.openlocfilehash: 55622b9c5a8f70ef4e7246d6eb14678036948f18
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635454"
---
# <a name="lazy-loading-of-related-data"></a><span data-ttu-id="38a75-103">延遲載入相關資料</span><span class="sxs-lookup"><span data-stu-id="38a75-103">Lazy Loading of Related Data</span></span>

## <a name="lazy-loading-with-proxies"></a><span data-ttu-id="38a75-104">使用 proxy 的消極式載入</span><span class="sxs-lookup"><span data-stu-id="38a75-104">Lazy loading with proxies</span></span>

<span data-ttu-id="38a75-105">使用消極式載入的最簡單方式是安裝 [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) \(英文\) 套件，並呼叫 `UseLazyLoadingProxies` 來啟用它。</span><span class="sxs-lookup"><span data-stu-id="38a75-105">The simplest way to use lazy-loading is by installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package and enabling it with a call to `UseLazyLoadingProxies`.</span></span> <span data-ttu-id="38a75-106">例如：</span><span class="sxs-lookup"><span data-stu-id="38a75-106">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```

<span data-ttu-id="38a75-107">或在使用 AddDbContext 時：</span><span class="sxs-lookup"><span data-stu-id="38a75-107">Or when using AddDbContext:</span></span>

```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```

<span data-ttu-id="38a75-108">EF Core 接著將針對可覆寫的所有導覽屬性 (也就是說，它必須是 `virtual` 並位於可繼承的類別上) 啟用消極式載入。</span><span class="sxs-lookup"><span data-stu-id="38a75-108">EF Core will then enable lazy loading for any navigation property that can be overridden--that is, it must be `virtual` and on a class that can be inherited from.</span></span> <span data-ttu-id="38a75-109">例如，在下列實體中，系統將會對 `Post.Blog` 和 `Blog.Posts` 導覽屬性進行消極式載入。</span><span class="sxs-lookup"><span data-stu-id="38a75-109">For example, in the following entities, the `Post.Blog` and `Blog.Posts` navigation properties will be lazy-loaded.</span></span>

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

> [!WARNING]
> <span data-ttu-id="38a75-110">消極式載入可能會導致不必要的額外資料庫往返發生 (所謂的 N + 1 問題) ，而且應該小心避免此情況。</span><span class="sxs-lookup"><span data-stu-id="38a75-110">Lazy loading can cause unneeded extra database roundtrips to occur (the so-called N+1 problem), and care should be taken to avoid this.</span></span> <span data-ttu-id="38a75-111">如需詳細資訊，請參閱 [ [效能] 區段](xref:core/performance/efficient-querying#beware-of-lazy-loading) 。</span><span class="sxs-lookup"><span data-stu-id="38a75-111">See the [performance section](xref:core/performance/efficient-querying#beware-of-lazy-loading) for more details.</span></span>

## <a name="lazy-loading-without-proxies"></a><span data-ttu-id="38a75-112">沒有 Proxy 的消極式載入</span><span class="sxs-lookup"><span data-stu-id="38a75-112">Lazy loading without proxies</span></span>

<span data-ttu-id="38a75-113">消極式載入 Proxy 的運作方式是將 `ILazyLoader` 服務插入至實體，如[實體類型建構函式](xref:core/modeling/constructors)中所述。</span><span class="sxs-lookup"><span data-stu-id="38a75-113">Lazy-loading proxies work by injecting the `ILazyLoader` service into an entity, as described in [Entity Type Constructors](xref:core/modeling/constructors).</span></span> <span data-ttu-id="38a75-114">例如：</span><span class="sxs-lookup"><span data-stu-id="38a75-114">For example:</span></span>

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

<span data-ttu-id="38a75-115">這個方法不需要繼承自或導覽屬性為虛擬的實體類型，而且可讓建立的實體實例在 `new` 附加至內容之後，延遲載入。</span><span class="sxs-lookup"><span data-stu-id="38a75-115">This method doesn't require entity types to be inherited from or navigation properties to be virtual, and allows entity instances created with `new` to lazy-load once attached to a context.</span></span> <span data-ttu-id="38a75-116">不過，它需要對 `ILazyLoader` 服務的參考，這在 [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) 套件中定義。</span><span class="sxs-lookup"><span data-stu-id="38a75-116">However, it requires a reference to the `ILazyLoader` service, which is defined in the [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) package.</span></span> <span data-ttu-id="38a75-117">此封裝包含一組基本的類型，因此不會對其產生很大的影響。</span><span class="sxs-lookup"><span data-stu-id="38a75-117">This package contains a minimal set of types so that there is little impact in depending on it.</span></span> <span data-ttu-id="38a75-118">不過，若要完全避免根據實體類型中的任何 EF Core 套件，可以將 `ILazyLoader.Load` 方法插入為委派。</span><span class="sxs-lookup"><span data-stu-id="38a75-118">However, to completely avoid depending on any EF Core packages in the entity types, it's possible to inject the `ILazyLoader.Load` method as a delegate.</span></span> <span data-ttu-id="38a75-119">例如：</span><span class="sxs-lookup"><span data-stu-id="38a75-119">For example:</span></span>

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

<span data-ttu-id="38a75-120">上述程式碼會使用 `Load` 擴充方法，來更簡潔地使用委派：</span><span class="sxs-lookup"><span data-stu-id="38a75-120">The code above uses a `Load` extension method to make using the delegate a bit cleaner:</span></span>

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
> <span data-ttu-id="38a75-121">消極式載入委派的建構函式參數必須稱為 "lazyLoader"。</span><span class="sxs-lookup"><span data-stu-id="38a75-121">The constructor parameter for the lazy-loading delegate must be called "lazyLoader".</span></span> <span data-ttu-id="38a75-122">預計將於未來版本中推出使用與此不同之名稱的設定。</span><span class="sxs-lookup"><span data-stu-id="38a75-122">Configuration to use a different name than this is planned for a future release.</span></span>
