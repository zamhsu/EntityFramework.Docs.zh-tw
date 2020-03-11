---
title: 具有函數的實體類型-EF Core
author: ajcvickers
ms.date: 02/23/2018
ms.assetid: 420AFFE7-B709-4A68-9149-F06F8746FB33
uid: core/modeling/constructors
ms.openlocfilehash: ddfaa8eebde388a9d3309f21b8891de593077956
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417326"
---
# <a name="entity-types-with-constructors"></a><span data-ttu-id="ccc93-102">具有函數的實體類型</span><span class="sxs-lookup"><span data-stu-id="ccc93-102">Entity types with constructors</span></span>

> [!NOTE]  
> <span data-ttu-id="ccc93-103">此功能是 EF Core 2.1 中的新功能。</span><span class="sxs-lookup"><span data-stu-id="ccc93-103">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="ccc93-104">從 EF Core 2.1 開始，現在可以定義具有參數的處理常式，並在建立實體的實例時，讓 EF Core 呼叫此函式。</span><span class="sxs-lookup"><span data-stu-id="ccc93-104">Starting with EF Core 2.1, it is now possible to define a constructor with parameters and have EF Core call this constructor when creating an instance of the entity.</span></span> <span data-ttu-id="ccc93-105">此函式參數可以系結至對應的屬性，或系結至各種不同的服務，以促進延遲載入之類的行為。</span><span class="sxs-lookup"><span data-stu-id="ccc93-105">The constructor parameters can be bound to mapped properties, or to various kinds of services to facilitate behaviors like lazy-loading.</span></span>

> [!NOTE]  
> <span data-ttu-id="ccc93-106">從 EF Core 2.1，所有的函式系結都是依照慣例。</span><span class="sxs-lookup"><span data-stu-id="ccc93-106">As of EF Core 2.1, all constructor binding is by convention.</span></span> <span data-ttu-id="ccc93-107">未來的版本已規劃要使用的特定構造函式設定。</span><span class="sxs-lookup"><span data-stu-id="ccc93-107">Configuration of specific constructors to use is planned for a future release.</span></span>

## <a name="binding-to-mapped-properties"></a><span data-ttu-id="ccc93-108">系結至對應的屬性</span><span class="sxs-lookup"><span data-stu-id="ccc93-108">Binding to mapped properties</span></span>

<span data-ttu-id="ccc93-109">請考慮一般的 Blog/Post 模型：</span><span class="sxs-lookup"><span data-stu-id="ccc93-109">Consider a typical Blog/Post model:</span></span>

``` csharp
public class Blog
{
    public int Id { get; set; }

    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```

<span data-ttu-id="ccc93-110">當 EF Core 建立這些類型的實例時（例如，針對查詢的結果），它會先呼叫預設的無參數的函式，然後將每個屬性設定為資料庫中的值。</span><span class="sxs-lookup"><span data-stu-id="ccc93-110">When EF Core creates instances of these types, such as for the results of a query, it will first call the default parameterless constructor and then set each property to the value from the database.</span></span> <span data-ttu-id="ccc93-111">不過，如果 EF Core 使用符合對應屬性之參數名稱和類型來尋找參數化的函式，則會改為使用這些屬性的值來呼叫參數化的函式，而且不會明確設定每個屬性。</span><span class="sxs-lookup"><span data-stu-id="ccc93-111">However, if EF Core finds a parameterized constructor with parameter names and types that match those of mapped properties, then it will instead call the parameterized constructor with values for those properties and will not set each property explicitly.</span></span> <span data-ttu-id="ccc93-112">例如：</span><span class="sxs-lookup"><span data-stu-id="ccc93-112">For example:</span></span>

``` csharp
public class Blog
{
    public Blog(int id, string name, string author)
    {
        Id = id;
        Name = name;
        Author = author;
    }

    public int Id { get; set; }

    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public Post(int id, string title, DateTime postedOn)
    {
        Id = id;
        Title = title;
        PostedOn = postedOn;
    }

    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```

<span data-ttu-id="ccc93-113">注意事項如下：</span><span class="sxs-lookup"><span data-stu-id="ccc93-113">Some things to note:</span></span>

* <span data-ttu-id="ccc93-114">並非所有屬性都必須具有函數參數。</span><span class="sxs-lookup"><span data-stu-id="ccc93-114">Not all properties need to have constructor parameters.</span></span> <span data-ttu-id="ccc93-115">例如，不是由任何的「函數參數」設定「內容」屬性，因此 EF Core 會在以一般方式呼叫「函式」之後設定它。</span><span class="sxs-lookup"><span data-stu-id="ccc93-115">For example, the Post.Content property is not set by any constructor parameter, so EF Core will set it after calling the constructor in the normal way.</span></span>
* <span data-ttu-id="ccc93-116">參數類型和名稱必須符合屬性類型和名稱，但屬性可以是 Pascal 大小寫，而參數則是 camel 大小寫。</span><span class="sxs-lookup"><span data-stu-id="ccc93-116">The parameter types and names must match property types and names, except that properties can be Pascal-cased while the parameters are camel-cased.</span></span>
* <span data-ttu-id="ccc93-117">EF Core 無法使用函式來設定導覽屬性（例如上述的 Blog 或 Post）。</span><span class="sxs-lookup"><span data-stu-id="ccc93-117">EF Core cannot set navigation properties (such as Blog or Posts above) using a constructor.</span></span>
* <span data-ttu-id="ccc93-118">此函式可以是公用、私用或具有任何其他存取範圍。</span><span class="sxs-lookup"><span data-stu-id="ccc93-118">The constructor can be public, private, or have any other accessibility.</span></span> <span data-ttu-id="ccc93-119">不過，消極式載入 proxy 需要可從繼承 proxy 類別存取的函式。</span><span class="sxs-lookup"><span data-stu-id="ccc93-119">However, lazy-loading proxies require that the constructor is accessible from the inheriting proxy class.</span></span> <span data-ttu-id="ccc93-120">這通常表示將它設為公用或受保護。</span><span class="sxs-lookup"><span data-stu-id="ccc93-120">Usually this means making it either public or protected.</span></span>

### <a name="read-only-properties"></a><span data-ttu-id="ccc93-121">唯讀屬性</span><span class="sxs-lookup"><span data-stu-id="ccc93-121">Read-only properties</span></span>

<span data-ttu-id="ccc93-122">透過函式設定屬性之後，將其部分設為唯讀可能是合理的。</span><span class="sxs-lookup"><span data-stu-id="ccc93-122">Once properties are being set via the constructor it can make sense to make some of them read-only.</span></span> <span data-ttu-id="ccc93-123">EF Core 支援這種情況，但有一些要注意的事項：</span><span class="sxs-lookup"><span data-stu-id="ccc93-123">EF Core supports this, but there are some things to look out for:</span></span>

* <span data-ttu-id="ccc93-124">沒有 setter 的屬性不會依照慣例來對應。</span><span class="sxs-lookup"><span data-stu-id="ccc93-124">Properties without setters are not mapped by convention.</span></span> <span data-ttu-id="ccc93-125">（這麼做通常會對應不應對應的屬性，例如計算屬性）。</span><span class="sxs-lookup"><span data-stu-id="ccc93-125">(Doing so tends to map properties that should not be mapped, such as computed properties.)</span></span>
* <span data-ttu-id="ccc93-126">使用自動產生的索引鍵值需要具有讀寫的索引鍵屬性，因為在插入新的實體時，索引鍵的值必須由金鑰產生器設定。</span><span class="sxs-lookup"><span data-stu-id="ccc93-126">Using automatically generated key values requires a key property that is read-write, since the key value needs to be set by the key generator when inserting new entities.</span></span>

<span data-ttu-id="ccc93-127">避免這些問題的簡單方法是使用私用 setter。</span><span class="sxs-lookup"><span data-stu-id="ccc93-127">An easy way to avoid these things is to use private setters.</span></span> <span data-ttu-id="ccc93-128">例如：</span><span class="sxs-lookup"><span data-stu-id="ccc93-128">For example:</span></span>

``` csharp
public class Blog
{
    public Blog(int id, string name, string author)
    {
        Id = id;
        Name = name;
        Author = author;
    }

    public int Id { get; private set; }

    public string Name { get; private set; }
    public string Author { get; private set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public Post(int id, string title, DateTime postedOn)
    {
        Id = id;
        Title = title;
        PostedOn = postedOn;
    }

    public int Id { get; private set; }

    public string Title { get; private set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; private set; }

    public Blog Blog { get; set; }
}
```

<span data-ttu-id="ccc93-129">EF Core 看到具有私用 setter 的屬性為讀寫，這表示所有屬性都對應于前面，而且金鑰仍然可以存放區產生。</span><span class="sxs-lookup"><span data-stu-id="ccc93-129">EF Core sees a property with a private setter as read-write, which means that all properties are mapped as before and the key can still be store-generated.</span></span>

<span data-ttu-id="ccc93-130">使用私用 setter 的替代方法是讓屬性成為唯讀的，並在 OnModelCreating 中新增更明確的對應。</span><span class="sxs-lookup"><span data-stu-id="ccc93-130">An alternative to using private setters is to make properties really read-only and add more explicit mapping in OnModelCreating.</span></span> <span data-ttu-id="ccc93-131">同樣地，某些屬性可以完全移除，而且只會以欄位取代。</span><span class="sxs-lookup"><span data-stu-id="ccc93-131">Likewise, some properties can be removed completely and replaced with only fields.</span></span> <span data-ttu-id="ccc93-132">例如，請考慮下列實體類型：</span><span class="sxs-lookup"><span data-stu-id="ccc93-132">For example, consider these entity types:</span></span>

``` csharp
public class Blog
{
    private int _id;

    public Blog(string name, string author)
    {
        Name = name;
        Author = author;
    }

    public string Name { get; }
    public string Author { get; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    private int _id;

    public Post(string title, DateTime postedOn)
    {
        Title = title;
        PostedOn = postedOn;
    }

    public string Title { get; }
    public string Content { get; set; }
    public DateTime PostedOn { get; }

    public Blog Blog { get; set; }
}
```

<span data-ttu-id="ccc93-133">在 OnModelCreating 中的這項設定：</span><span class="sxs-lookup"><span data-stu-id="ccc93-133">And this configuration in OnModelCreating:</span></span>

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>(
        b =>
        {
            b.HasKey("_id");
            b.Property(e => e.Author);
            b.Property(e => e.Name);
        });

    modelBuilder.Entity<Post>(
        b =>
        {
            b.HasKey("_id");
            b.Property(e => e.Title);
            b.Property(e => e.PostedOn);
        });
}
```

<span data-ttu-id="ccc93-134">注意事項：</span><span class="sxs-lookup"><span data-stu-id="ccc93-134">Things to note:</span></span>

* <span data-ttu-id="ccc93-135">索引鍵 "property" 現在是欄位。</span><span class="sxs-lookup"><span data-stu-id="ccc93-135">The key "property" is now a field.</span></span> <span data-ttu-id="ccc93-136">這不是 `readonly` 欄位，因此可以使用存放區產生的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="ccc93-136">It is not a `readonly` field so that store-generated keys can be used.</span></span>
* <span data-ttu-id="ccc93-137">其他屬性是唯讀屬性，只會在此函式中設定。</span><span class="sxs-lookup"><span data-stu-id="ccc93-137">The other properties are read-only properties set only in the constructor.</span></span>
* <span data-ttu-id="ccc93-138">如果主要金鑰值只是由 EF 設定或從資料庫讀取，則不需要將它包含在此函式中。</span><span class="sxs-lookup"><span data-stu-id="ccc93-138">If the primary key value is only ever set by EF or read from the database, then there is no need to include it in the constructor.</span></span> <span data-ttu-id="ccc93-139">這會將金鑰「屬性」保留為簡單的欄位，並清楚指出不應該在建立新的 blog 或文章時明確設定。</span><span class="sxs-lookup"><span data-stu-id="ccc93-139">This leaves the key "property" as a simple field and makes it clear that it should not be set explicitly when creating new blogs or posts.</span></span>

> [!NOTE]  
> <span data-ttu-id="ccc93-140">這段程式碼會產生編譯器警告 ' 169 '，表示從未使用過此欄位。</span><span class="sxs-lookup"><span data-stu-id="ccc93-140">This code will result in compiler warning '169' indicating that the field is never used.</span></span> <span data-ttu-id="ccc93-141">這可能會被忽略，因為事實上，EF Core 以 extralinguistic 的方式使用欄位。</span><span class="sxs-lookup"><span data-stu-id="ccc93-141">This can be ignored since in reality EF Core is using the field in an extralinguistic manner.</span></span>

## <a name="injecting-services"></a><span data-ttu-id="ccc93-142">插入服務</span><span class="sxs-lookup"><span data-stu-id="ccc93-142">Injecting services</span></span>

<span data-ttu-id="ccc93-143">EF Core 也可以在實體類型的函式中插入「服務」。</span><span class="sxs-lookup"><span data-stu-id="ccc93-143">EF Core can also inject "services" into an entity type's constructor.</span></span> <span data-ttu-id="ccc93-144">例如，您可以插入下列內容：</span><span class="sxs-lookup"><span data-stu-id="ccc93-144">For example, the following can be injected:</span></span>

* <span data-ttu-id="ccc93-145">`DbContext`-目前的內容實例，也可以輸入為衍生的 DbCoNtext 類型</span><span class="sxs-lookup"><span data-stu-id="ccc93-145">`DbContext` - the current context instance, which can also be typed as your derived DbContext type</span></span>
* <span data-ttu-id="ccc93-146">`ILazyLoader`-延遲載入服務--如需詳細資訊，請參閱消極[式載入檔](../querying/related-data.md)</span><span class="sxs-lookup"><span data-stu-id="ccc93-146">`ILazyLoader` - the lazy-loading service--see the [lazy-loading documentation](../querying/related-data.md) for more details</span></span>
* <span data-ttu-id="ccc93-147">`Action<object, string>`-延遲載入委派--如需詳細資料，請參閱消極[式載入檔](../querying/related-data.md)</span><span class="sxs-lookup"><span data-stu-id="ccc93-147">`Action<object, string>` - a lazy-loading delegate--see the [lazy-loading documentation](../querying/related-data.md) for more details</span></span>
* <span data-ttu-id="ccc93-148">`IEntityType`-與此實體類型相關聯的 EF Core 中繼資料</span><span class="sxs-lookup"><span data-stu-id="ccc93-148">`IEntityType` - the EF Core metadata associated with this entity type</span></span>

> [!NOTE]  
> <span data-ttu-id="ccc93-149">從 EF Core 2.1，只有 EF Core 已知的服務可以插入。</span><span class="sxs-lookup"><span data-stu-id="ccc93-149">As of EF Core 2.1, only services known by EF Core can be injected.</span></span> <span data-ttu-id="ccc93-150">未來版本將會考慮插入應用程式服務的支援。</span><span class="sxs-lookup"><span data-stu-id="ccc93-150">Support for injecting application services is being considered for a future release.</span></span>

<span data-ttu-id="ccc93-151">例如，插入的 DbCoNtext 可以用來選擇性地存取資料庫，以取得相關實體的資訊，而不需全部載入。</span><span class="sxs-lookup"><span data-stu-id="ccc93-151">For example, an injected DbContext can be used to selectively access the database to obtain information about related entities without loading them all.</span></span> <span data-ttu-id="ccc93-152">在下列範例中，這是用來取得 blog 中的貼文數目，而不需要載入文章：</span><span class="sxs-lookup"><span data-stu-id="ccc93-152">In the example below this is used to obtain the number of posts in a blog without loading the posts:</span></span>

``` csharp
public class Blog
{
    public Blog()
    {
    }

    private Blog(BloggingContext context)
    {
        Context = context;
    }

    private BloggingContext Context { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; set; }

    public int PostsCount
        => Posts?.Count
           ?? Context?.Set<Post>().Count(p => Id == EF.Property<int?>(p, "BlogId"))
           ?? 0;
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```

<span data-ttu-id="ccc93-153">以下是一些要注意的事項：</span><span class="sxs-lookup"><span data-stu-id="ccc93-153">A few things to notice about this:</span></span>

* <span data-ttu-id="ccc93-154">此函式是私用的，因為它只會由 EF Core 呼叫，而且有另一個一般使用的公用函數。</span><span class="sxs-lookup"><span data-stu-id="ccc93-154">The constructor is private, since it is only ever called by EF Core, and there is another public constructor for general use.</span></span>
* <span data-ttu-id="ccc93-155">使用插入的服務（也就是內容）的程式碼，可以防禦它 `null`，以處理 EF Core 不會建立實例的情況。</span><span class="sxs-lookup"><span data-stu-id="ccc93-155">The code using the injected service (that is, the context) is defensive against it being `null` to handle cases where EF Core is not creating the instance.</span></span>
* <span data-ttu-id="ccc93-156">因為服務是儲存在讀取/寫入屬性中，所以當實體附加至新的內容實例時，將會重設它。</span><span class="sxs-lookup"><span data-stu-id="ccc93-156">Because service is stored in a read/write property it will be reset when the entity is attached to a new context instance.</span></span>

> [!WARNING]  
> <span data-ttu-id="ccc93-157">插入這類 DbCoNtext 通常會被視為反模式，因為它會直接將您的實體類型結合 EF Core。</span><span class="sxs-lookup"><span data-stu-id="ccc93-157">Injecting the DbContext like this is often considered an anti-pattern since it couples your entity types directly to EF Core.</span></span> <span data-ttu-id="ccc93-158">在使用這類服務插入之前，請先仔細考慮所有選項。</span><span class="sxs-lookup"><span data-stu-id="ccc93-158">Carefully consider all options before using service injection like this.</span></span>
