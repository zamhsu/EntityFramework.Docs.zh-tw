---
title: 具有函式的實體類型-EF Core
description: 使用函式搭配 Entity Framework Core 模型來系結資料
author: ajcvickers
ms.date: 02/23/2018
uid: core/modeling/constructors
ms.openlocfilehash: 9502d75072eebb80c37cf1805e21f7d112269ba1
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063708"
---
# <a name="entity-types-with-constructors"></a><span data-ttu-id="90d83-103">具有函式的實體類型</span><span class="sxs-lookup"><span data-stu-id="90d83-103">Entity types with constructors</span></span>

<span data-ttu-id="90d83-104">您可以定義具有參數的函式，並在建立實體的實例時，EF Core 呼叫這個函式。</span><span class="sxs-lookup"><span data-stu-id="90d83-104">It's possible to define a constructor with parameters and have EF Core call this constructor when creating an instance of the entity.</span></span> <span data-ttu-id="90d83-105">這些函式參數可以系結至對應的屬性，或系結至各種不同的服務，以促進延遲載入等行為。</span><span class="sxs-lookup"><span data-stu-id="90d83-105">The constructor parameters can be bound to mapped properties, or to various kinds of services to facilitate behaviors like lazy-loading.</span></span>

> [!NOTE]
> <span data-ttu-id="90d83-106">目前，所有的函式系結都是依照慣例。</span><span class="sxs-lookup"><span data-stu-id="90d83-106">Currently, all constructor binding is by convention.</span></span> <span data-ttu-id="90d83-107">未來的版本計畫會設定要使用的特定函式。</span><span class="sxs-lookup"><span data-stu-id="90d83-107">Configuration of specific constructors to use is planned for a future release.</span></span>

## <a name="binding-to-mapped-properties"></a><span data-ttu-id="90d83-108">系結至對應的屬性</span><span class="sxs-lookup"><span data-stu-id="90d83-108">Binding to mapped properties</span></span>

<span data-ttu-id="90d83-109">請考慮一般的 Blog/Post 模型：</span><span class="sxs-lookup"><span data-stu-id="90d83-109">Consider a typical Blog/Post model:</span></span>

```csharp
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

<span data-ttu-id="90d83-110">當 EF Core 建立這些型別的實例時（例如查詢結果），它會先呼叫預設的無參數的函式，然後將每個屬性設定為資料庫中的值。</span><span class="sxs-lookup"><span data-stu-id="90d83-110">When EF Core creates instances of these types, such as for the results of a query, it will first call the default parameterless constructor and then set each property to the value from the database.</span></span> <span data-ttu-id="90d83-111">但是，如果 EF Core 找到參數名稱和類型符合對應屬性的參數化的函式，則會改為使用這些屬性的值來呼叫參數化的函式，而不會明確地設定每個屬性。</span><span class="sxs-lookup"><span data-stu-id="90d83-111">However, if EF Core finds a parameterized constructor with parameter names and types that match those of mapped properties, then it will instead call the parameterized constructor with values for those properties and will not set each property explicitly.</span></span> <span data-ttu-id="90d83-112">例如︰</span><span class="sxs-lookup"><span data-stu-id="90d83-112">For example:</span></span>

```csharp
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

<span data-ttu-id="90d83-113">注意事項如下：</span><span class="sxs-lookup"><span data-stu-id="90d83-113">Some things to note:</span></span>

* <span data-ttu-id="90d83-114">並非所有屬性都必須有函式參數。</span><span class="sxs-lookup"><span data-stu-id="90d83-114">Not all properties need to have constructor parameters.</span></span> <span data-ttu-id="90d83-115">例如，任何的函式參數都不會設定 Content-type 屬性，因此 EF Core 會在以正常方式呼叫該函式之後進行設定。</span><span class="sxs-lookup"><span data-stu-id="90d83-115">For example, the Post.Content property is not set by any constructor parameter, so EF Core will set it after calling the constructor in the normal way.</span></span>
* <span data-ttu-id="90d83-116">參數型別和名稱必須符合屬性型別和名稱，不同之處在于屬性可以是 Pascal 大小寫，而參數的大小寫則是 camel。</span><span class="sxs-lookup"><span data-stu-id="90d83-116">The parameter types and names must match property types and names, except that properties can be Pascal-cased while the parameters are camel-cased.</span></span>
* <span data-ttu-id="90d83-117">EF Core 無法使用「函式」來設定導覽屬性 (例如) 的 Blog 或貼文。</span><span class="sxs-lookup"><span data-stu-id="90d83-117">EF Core cannot set navigation properties (such as Blog or Posts above) using a constructor.</span></span>
* <span data-ttu-id="90d83-118">此函數可以是公用、私用或具有任何其他存取範圍。</span><span class="sxs-lookup"><span data-stu-id="90d83-118">The constructor can be public, private, or have any other accessibility.</span></span> <span data-ttu-id="90d83-119">不過，消極式載入 proxy 需要可從繼承 proxy 類別存取的函式。</span><span class="sxs-lookup"><span data-stu-id="90d83-119">However, lazy-loading proxies require that the constructor is accessible from the inheriting proxy class.</span></span> <span data-ttu-id="90d83-120">這通常表示它是公開或受保護的。</span><span class="sxs-lookup"><span data-stu-id="90d83-120">Usually this means making it either public or protected.</span></span>

### <a name="read-only-properties"></a><span data-ttu-id="90d83-121">唯讀屬性</span><span class="sxs-lookup"><span data-stu-id="90d83-121">Read-only properties</span></span>

<span data-ttu-id="90d83-122">一旦屬性是透過函式進行設定，就有可能讓某些屬性成為唯讀。</span><span class="sxs-lookup"><span data-stu-id="90d83-122">Once properties are being set via the constructor it can make sense to make some of them read-only.</span></span> <span data-ttu-id="90d83-123">EF Core 支援這項功能，但有一些需要注意的事項：</span><span class="sxs-lookup"><span data-stu-id="90d83-123">EF Core supports this, but there are some things to look out for:</span></span>

* <span data-ttu-id="90d83-124">沒有 setter 的屬性不會依照慣例進行對應。</span><span class="sxs-lookup"><span data-stu-id="90d83-124">Properties without setters are not mapped by convention.</span></span> <span data-ttu-id="90d83-125"> (這麼做，通常會對應不應該對應的屬性，例如計算的屬性。 ) </span><span class="sxs-lookup"><span data-stu-id="90d83-125">(Doing so tends to map properties that should not be mapped, such as computed properties.)</span></span>
* <span data-ttu-id="90d83-126">使用自動產生的索引鍵值需要可讀寫的索引鍵屬性，因為在插入新的實體時，金鑰產生器需要設定金鑰值。</span><span class="sxs-lookup"><span data-stu-id="90d83-126">Using automatically generated key values requires a key property that is read-write, since the key value needs to be set by the key generator when inserting new entities.</span></span>

<span data-ttu-id="90d83-127">避免這些事的簡單方法是使用私用 setter。</span><span class="sxs-lookup"><span data-stu-id="90d83-127">An easy way to avoid these things is to use private setters.</span></span> <span data-ttu-id="90d83-128">例如︰</span><span class="sxs-lookup"><span data-stu-id="90d83-128">For example:</span></span>

```csharp
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

<span data-ttu-id="90d83-129">EF Core 看到具有私用 setter 的屬性為讀寫，這表示所有屬性都與之前對應，而且索引鍵仍可以存放區產生。</span><span class="sxs-lookup"><span data-stu-id="90d83-129">EF Core sees a property with a private setter as read-write, which means that all properties are mapped as before and the key can still be store-generated.</span></span>

<span data-ttu-id="90d83-130">使用私用 setter 的另一種方式，是讓屬性真正是唯讀的，並在 OnModelCreating 中新增更明確的對應。</span><span class="sxs-lookup"><span data-stu-id="90d83-130">An alternative to using private setters is to make properties really read-only and add more explicit mapping in OnModelCreating.</span></span> <span data-ttu-id="90d83-131">同樣地，某些屬性可以完全移除，並且只以欄位取代。</span><span class="sxs-lookup"><span data-stu-id="90d83-131">Likewise, some properties can be removed completely and replaced with only fields.</span></span> <span data-ttu-id="90d83-132">例如，請考慮下列實體類型：</span><span class="sxs-lookup"><span data-stu-id="90d83-132">For example, consider these entity types:</span></span>

```csharp
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

<span data-ttu-id="90d83-133">此外，OnModelCreating 中的這項設定：</span><span class="sxs-lookup"><span data-stu-id="90d83-133">And this configuration in OnModelCreating:</span></span>

```csharp
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

<span data-ttu-id="90d83-134">注意事項：</span><span class="sxs-lookup"><span data-stu-id="90d83-134">Things to note:</span></span>

* <span data-ttu-id="90d83-135">索引鍵 "property" 現在是一個欄位。</span><span class="sxs-lookup"><span data-stu-id="90d83-135">The key "property" is now a field.</span></span> <span data-ttu-id="90d83-136">它不是 `readonly` 欄位，因此可以使用存放區產生的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="90d83-136">It is not a `readonly` field so that store-generated keys can be used.</span></span>
* <span data-ttu-id="90d83-137">其他屬性則是唯讀屬性，只會在此函式中設定。</span><span class="sxs-lookup"><span data-stu-id="90d83-137">The other properties are read-only properties set only in the constructor.</span></span>
* <span data-ttu-id="90d83-138">如果主要金鑰值只是由 EF 設定或從資料庫讀取，則不需要將它包含在函式中。</span><span class="sxs-lookup"><span data-stu-id="90d83-138">If the primary key value is only ever set by EF or read from the database, then there is no need to include it in the constructor.</span></span> <span data-ttu-id="90d83-139">這會將機碼 "property" 保留為簡單的欄位，並清楚指出在建立新的 blog 或 post 時，不應該明確地設定它。</span><span class="sxs-lookup"><span data-stu-id="90d83-139">This leaves the key "property" as a simple field and makes it clear that it should not be set explicitly when creating new blogs or posts.</span></span>

> [!NOTE]
> <span data-ttu-id="90d83-140">這段程式碼會產生編譯器警告 ' 169 '，指出從未使用過此欄位。</span><span class="sxs-lookup"><span data-stu-id="90d83-140">This code will result in compiler warning '169' indicating that the field is never used.</span></span> <span data-ttu-id="90d83-141">這可以被忽略，因為在現實中 EF Core 會以 extralinguistic 的方式使用欄位。</span><span class="sxs-lookup"><span data-stu-id="90d83-141">This can be ignored since in reality EF Core is using the field in an extralinguistic manner.</span></span>

## <a name="injecting-services"></a><span data-ttu-id="90d83-142">插入服務</span><span class="sxs-lookup"><span data-stu-id="90d83-142">Injecting services</span></span>

<span data-ttu-id="90d83-143">EF Core 也可以在實體類型的函式中插入 "services"。</span><span class="sxs-lookup"><span data-stu-id="90d83-143">EF Core can also inject "services" into an entity type's constructor.</span></span> <span data-ttu-id="90d83-144">例如，您可以插入下列內容：</span><span class="sxs-lookup"><span data-stu-id="90d83-144">For example, the following can be injected:</span></span>

* <span data-ttu-id="90d83-145">`DbContext` -目前的內容實例，也可以輸入為您的衍生 DbCoNtext 型別</span><span class="sxs-lookup"><span data-stu-id="90d83-145">`DbContext` - the current context instance, which can also be typed as your derived DbContext type</span></span>
* <span data-ttu-id="90d83-146">`ILazyLoader`-延遲載入服務--如需詳細資料，請參閱消極[式載入檔](xref:core/querying/related-data)</span><span class="sxs-lookup"><span data-stu-id="90d83-146">`ILazyLoader` - the lazy-loading service--see the [lazy-loading documentation](xref:core/querying/related-data) for more details</span></span>
* <span data-ttu-id="90d83-147">`Action<object, string>`-消極式載入委派--如需詳細資料，請參閱消極[式載入檔](xref:core/querying/related-data)</span><span class="sxs-lookup"><span data-stu-id="90d83-147">`Action<object, string>` - a lazy-loading delegate--see the [lazy-loading documentation](xref:core/querying/related-data) for more details</span></span>
* <span data-ttu-id="90d83-148">`IEntityType` -與此實體類型相關聯的 EF Core 中繼資料</span><span class="sxs-lookup"><span data-stu-id="90d83-148">`IEntityType` - the EF Core metadata associated with this entity type</span></span>

> [!NOTE]
> <span data-ttu-id="90d83-149">目前只能插入 EF Core 已知的服務。</span><span class="sxs-lookup"><span data-stu-id="90d83-149">Currently, only services known by EF Core can be injected.</span></span> <span data-ttu-id="90d83-150">未來版本會考慮插入應用程式服務的支援。</span><span class="sxs-lookup"><span data-stu-id="90d83-150">Support for injecting application services is being considered for a future release.</span></span>

<span data-ttu-id="90d83-151">例如，插入的 DbCoNtext 可以用來選擇性地存取資料庫，以取得相關實體的相關資訊，而不將它們全部載入。</span><span class="sxs-lookup"><span data-stu-id="90d83-151">For example, an injected DbContext can be used to selectively access the database to obtain information about related entities without loading them all.</span></span> <span data-ttu-id="90d83-152">在下列範例中，這是用來取得 blog 中的貼文數目，而不需要載入文章：</span><span class="sxs-lookup"><span data-stu-id="90d83-152">In the example below this is used to obtain the number of posts in a blog without loading the posts:</span></span>

```csharp
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

<span data-ttu-id="90d83-153">以下是一些要注意的事項：</span><span class="sxs-lookup"><span data-stu-id="90d83-153">A few things to notice about this:</span></span>

* <span data-ttu-id="90d83-154">此函式是私用的，因為它只會由 EF Core 呼叫，而且有另一個公用的函式可供一般用途使用。</span><span class="sxs-lookup"><span data-stu-id="90d83-154">The constructor is private, since it is only ever called by EF Core, and there is another public constructor for general use.</span></span>
* <span data-ttu-id="90d83-155">使用插入服務的程式碼 (也就是，內容) 是防禦的，因為它會 `null` 處理 EF Core 不是建立實例的情況。</span><span class="sxs-lookup"><span data-stu-id="90d83-155">The code using the injected service (that is, the context) is defensive against it being `null` to handle cases where EF Core is not creating the instance.</span></span>
* <span data-ttu-id="90d83-156">由於服務是儲存在讀取/寫入屬性中，因此當實體附加至新的內容實例時，將會重設。</span><span class="sxs-lookup"><span data-stu-id="90d83-156">Because service is stored in a read/write property it will be reset when the entity is attached to a new context instance.</span></span>

> [!WARNING]
> <span data-ttu-id="90d83-157">像這樣插入 DbCoNtext 通常會被視為反模式，因為它會將您的實體類型直接結合 EF Core。</span><span class="sxs-lookup"><span data-stu-id="90d83-157">Injecting the DbContext like this is often considered an anti-pattern since it couples your entity types directly to EF Core.</span></span> <span data-ttu-id="90d83-158">使用像這樣的服務插入之前，請仔細考慮所有選項。</span><span class="sxs-lookup"><span data-stu-id="90d83-158">Carefully consider all options before using service injection like this.</span></span>
