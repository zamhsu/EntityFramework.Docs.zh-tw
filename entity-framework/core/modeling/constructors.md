---
title: "建構函式 EF 核心與實體類型"
author: ajcvickers
ms.author: divega
ms.date: 02/23/2018
ms.assetid: 420AFFE7-B709-4A68-9149-F06F8746FB33
ms.technology: entity-framework-core
uid: core/modeling/constructors
ms.openlocfilehash: 38ab0c1c3cd8c490875abf30b8478c99bc58630f
ms.sourcegitcommit: 60b831318c4f5ec99061e8af6a7c9e7c03b3469c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2018
---
# <a name="entity-types-with-constructors"></a><span data-ttu-id="8770e-102">使用建構函式的實體類型</span><span class="sxs-lookup"><span data-stu-id="8770e-102">Entity types with constructors</span></span>

> [!NOTE]  
> <span data-ttu-id="8770e-103">這項功能是在 EF 核心 2.1 中新功能。</span><span class="sxs-lookup"><span data-stu-id="8770e-103">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="8770e-104">從開始 EF 核心 2.1，您就可以定義參數的建構函式，並已建立實體的執行個體時呼叫這個建構函式的 EF 核心。</span><span class="sxs-lookup"><span data-stu-id="8770e-104">Starting with EF Core 2.1, it is now possible to define a constructor with parameters and have EF Core call this constructor when creating an instance of the entity.</span></span> <span data-ttu-id="8770e-105">建構函式參數可以繫結至對應的屬性，或為各種類型的服務，以促進行為類似消極式載入。</span><span class="sxs-lookup"><span data-stu-id="8770e-105">The constructor parameters can be bound to mapped properties, or to various kinds of services to facilitate behaviors like lazy-loading.</span></span>

> [!NOTE]  
> <span data-ttu-id="8770e-106">EF 核心 2.1 中，為所有的建構函式繫結的慣例。</span><span class="sxs-lookup"><span data-stu-id="8770e-106">As of EF Core 2.1, all constructor binding is by convention.</span></span> <span data-ttu-id="8770e-107">若要使用的特定建構函式的組態已規劃在未來的版本。</span><span class="sxs-lookup"><span data-stu-id="8770e-107">Configuration of specific constructors to use is planned for a future release.</span></span>

## <a name="binding-to-mapped-properties"></a><span data-ttu-id="8770e-108">繫結至對應的屬性</span><span class="sxs-lookup"><span data-stu-id="8770e-108">Binding to mapped properties</span></span>

<span data-ttu-id="8770e-109">請考量一般的部落格文章/模型：</span><span class="sxs-lookup"><span data-stu-id="8770e-109">Consider a typical Blog/Post model:</span></span>

```Csharp
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

<span data-ttu-id="8770e-110">當 EF 核心建立這些類型的執行個體時，例如結果的查詢時，它會先呼叫預設的無參數建構函式，然後設定每一個屬性的值從資料庫。</span><span class="sxs-lookup"><span data-stu-id="8770e-110">When EF Core creates instances of these types, such as for the results of a query, it will first call the default parameterless constructor and then set each property to the value from the database.</span></span> <span data-ttu-id="8770e-111">不過，如果 EF 核心找到的參數化建構函式參數名稱和相符的型別對應屬性，則它會改為呼叫這些屬性值的參數化建構函式，並將未明確設定每個屬性。</span><span class="sxs-lookup"><span data-stu-id="8770e-111">However, if EF Core finds a parameterized constructor with parameter names and types that match those of mapped properties, then it will instead call the parameterized constructor with values for those properties and will not set each property explicitly.</span></span> <span data-ttu-id="8770e-112">例如: </span><span class="sxs-lookup"><span data-stu-id="8770e-112">For example:</span></span>

```Csharp
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
<span data-ttu-id="8770e-113">請注意一些事項：</span><span class="sxs-lookup"><span data-stu-id="8770e-113">Some things to note:</span></span>
* <span data-ttu-id="8770e-114">並非所有屬性都必須具有建構函式參數。</span><span class="sxs-lookup"><span data-stu-id="8770e-114">Not all properties need to have constructor parameters.</span></span> <span data-ttu-id="8770e-115">比方說，Post.Content 是未設定屬性的任何建構函式參數，因此 EF 核心之後呼叫建構函式以一般方式將設定。</span><span class="sxs-lookup"><span data-stu-id="8770e-115">For example, the Post.Content property is not set by any constructor parameter, so EF Core will set it after calling the constructor in the normal way.</span></span>
* <span data-ttu-id="8770e-116">參數型別和名稱必須符合屬性類型和名稱，不同之處在於屬性可以是依照 pascal 命名法的大小寫慣例而參數是依照 camel 命名法的大小寫慣例。</span><span class="sxs-lookup"><span data-stu-id="8770e-116">The parameter types and names must match property types and names, except that properties can be Pascal-cased while the parameters are camel-cased.</span></span>
* <span data-ttu-id="8770e-117">EF 核心無法設定 （例如部落格或上述文章） 的導覽屬性使用建構函式。</span><span class="sxs-lookup"><span data-stu-id="8770e-117">EF Core cannot set navigation properties (such as Blog or Posts above) using a constructor.</span></span>
* <span data-ttu-id="8770e-118">建構函式可以是公用、 私用，或有任何其他協助工具。</span><span class="sxs-lookup"><span data-stu-id="8770e-118">The constructor can be public, private, or have any other accessibility.</span></span>

### <a name="read-only-properties"></a><span data-ttu-id="8770e-119">唯讀屬性</span><span class="sxs-lookup"><span data-stu-id="8770e-119">Read-only properties</span></span>

<span data-ttu-id="8770e-120">一旦透過建構函式設定屬性之後將可以有意義將其中部分為唯讀。</span><span class="sxs-lookup"><span data-stu-id="8770e-120">Once properties are being set via the constructor it can make sense to make some of them read-only.</span></span> <span data-ttu-id="8770e-121">EF 核心支援，但要留意的一些事項：</span><span class="sxs-lookup"><span data-stu-id="8770e-121">EF Core supports this, but there are some things to look out for:</span></span>
* <span data-ttu-id="8770e-122">沒有 setter 的屬性未對應的慣例。</span><span class="sxs-lookup"><span data-stu-id="8770e-122">Properties without setters are not mapped by convention.</span></span> <span data-ttu-id="8770e-123">（這樣通常會以對應不應對應，例如計算屬性的屬性。）</span><span class="sxs-lookup"><span data-stu-id="8770e-123">(Doing so tends to map properties that should not be mapped, such as computed properties.)</span></span>
* <span data-ttu-id="8770e-124">使用自動產生索引鍵的值必須是讀寫，因為插入新的實體時，金鑰產生器所要設定需要的索引鍵值的索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="8770e-124">Using automatically generated key values requires a key property that is read-write, since the key value needs to be set by the key generator when inserting new entities.</span></span>

<span data-ttu-id="8770e-125">避免這些作業的簡單方法是使用私用 setter。</span><span class="sxs-lookup"><span data-stu-id="8770e-125">An easy way to avoid these things is to use private setters.</span></span> <span data-ttu-id="8770e-126">例如: </span><span class="sxs-lookup"><span data-stu-id="8770e-126">For example:</span></span>
```Csharp
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
<span data-ttu-id="8770e-127">EF 核心看見為讀寫，也就是說，所有的屬性對應與之前，以及索引鍵可能仍會存放區產生的私用 setter 的屬性。</span><span class="sxs-lookup"><span data-stu-id="8770e-127">EF Core sees a property with a private setter as read-write, which means that all properties are mapped as before and the key can still be store-generated.</span></span>

<span data-ttu-id="8770e-128">使用私用 setter 的替代方式是將屬性實際上是唯讀，並新增 OnModelCreating 更明確的對應。</span><span class="sxs-lookup"><span data-stu-id="8770e-128">An alternative to using private setters is to make properties really read-only and add more explicit mapping in OnModelCreating.</span></span> <span data-ttu-id="8770e-129">同樣地，某些屬性可以完全移除並取代欄位。</span><span class="sxs-lookup"><span data-stu-id="8770e-129">Likewise, some properties can be removed completely and replaced with only fields.</span></span> <span data-ttu-id="8770e-130">例如，請考慮這些實體類型：</span><span class="sxs-lookup"><span data-stu-id="8770e-130">For example, consider these entity types:</span></span>

```Csharp
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
<span data-ttu-id="8770e-131">和 OnModelCreating 中的此設定：</span><span class="sxs-lookup"><span data-stu-id="8770e-131">And this configuration in OnModelCreating:</span></span>
```Csharp
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
<span data-ttu-id="8770e-132">必須注意的事項：</span><span class="sxs-lookup"><span data-stu-id="8770e-132">Things to note:</span></span>
* <span data-ttu-id="8770e-133">金鑰 「 屬性 」 現在是欄位。</span><span class="sxs-lookup"><span data-stu-id="8770e-133">The key "property" is now a field.</span></span> <span data-ttu-id="8770e-134">它不是`readonly`欄位，以便可以使用存放區產生的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="8770e-134">It is not a `readonly` field so that store-generated keys can be used.</span></span> 
* <span data-ttu-id="8770e-135">其他屬性是唯讀屬性只在建構函式中設定。</span><span class="sxs-lookup"><span data-stu-id="8770e-135">The other properties are read-only properties set only in the constructor.</span></span>
* <span data-ttu-id="8770e-136">如果只有 EF 來設定或讀取資料庫的主索引鍵值，就不需要包含於建構函式。</span><span class="sxs-lookup"><span data-stu-id="8770e-136">If the primary key value is only ever set by EF or read from the database, then there is no need to include it in the constructor.</span></span> <span data-ttu-id="8770e-137">這讓 「 屬性 」 的索引鍵保持為簡單欄位，並使其清除，它應該不明確設定時建立新的部落格或文章。</span><span class="sxs-lookup"><span data-stu-id="8770e-137">This leaves the key "property" as a simple field and makes it clear that it should not be set explicitly when creating new blogs or posts.</span></span>

> [!NOTE]  
> <span data-ttu-id="8770e-138">此程式碼會產生編譯器警告 '169' 表示永遠不會使用此欄位。</span><span class="sxs-lookup"><span data-stu-id="8770e-138">This code will result in compiler warning '169' indicating that the field is never used.</span></span> <span data-ttu-id="8770e-139">因為事實上 EF 核心使用的欄位 extralinguistic 的方式可以忽略。</span><span class="sxs-lookup"><span data-stu-id="8770e-139">This can be ignored since in reality EF Core is using the field in an extralinguistic manner.</span></span>

## <a name="injecting-services"></a><span data-ttu-id="8770e-140">插入服務</span><span class="sxs-lookup"><span data-stu-id="8770e-140">Injecting services</span></span>

<span data-ttu-id="8770e-141">EF 核心也可以將 「 服務 」 插入到之實體類型的建構函式。</span><span class="sxs-lookup"><span data-stu-id="8770e-141">EF Core can also inject "services" into an entity type's constructor.</span></span> <span data-ttu-id="8770e-142">例如，下列可插入：</span><span class="sxs-lookup"><span data-stu-id="8770e-142">For example, the following can be injected:</span></span>
* <span data-ttu-id="8770e-143">`DbContext` -目前的內容執行個體，這也可輸入為衍生的 DbContext 類型</span><span class="sxs-lookup"><span data-stu-id="8770e-143">`DbContext` - the current context instance, which can also be typed as your derived DbContext type</span></span>
* <span data-ttu-id="8770e-144">`ILazyLoader` -消極式載入服務-請參閱[消極式載入文件](../querying/related-data.md)如需詳細資訊</span><span class="sxs-lookup"><span data-stu-id="8770e-144">`ILazyLoader` - the lazy-loading service--see the [lazy-loading documentation](../querying/related-data.md) for more details</span></span>
* <span data-ttu-id="8770e-145">`Action<object, string>` -消極式載入委派-請參閱[消極式載入文件](../querying/related-data.md)如需詳細資訊</span><span class="sxs-lookup"><span data-stu-id="8770e-145">`Action<object, string>` - a lazy-loading delegate--see the [lazy-loading documentation](../querying/related-data.md) for more details</span></span>
* <span data-ttu-id="8770e-146">`IEntityType` -此實體類型相關聯的 EF 核心中繼資料</span><span class="sxs-lookup"><span data-stu-id="8770e-146">`IEntityType` - the EF Core metadata associated with this entity type</span></span>

> [!NOTE]  
> <span data-ttu-id="8770e-147">為準，EF 核心 2.1 中，可插入 EF Core 的已知的服務。</span><span class="sxs-lookup"><span data-stu-id="8770e-147">As of EF Core 2.1, only services known by EF Core can be injected.</span></span> <span data-ttu-id="8770e-148">用於將應用程式服務的支援是正在考慮未來的版本。</span><span class="sxs-lookup"><span data-stu-id="8770e-148">Support for injecting application services is being considered for a future release.</span></span>

<span data-ttu-id="8770e-149">例如，插入的 DbContext 可用來選擇性地存取資料庫，以取得相關實體的相關資訊，而不會載入所有它們。</span><span class="sxs-lookup"><span data-stu-id="8770e-149">For example, an injected DbContext can be used to selectively access the database to obtain information about related entities without loading them all.</span></span> <span data-ttu-id="8770e-150">在下列範例中，這用來取得而不必載入張貼的部落格文章的數目：</span><span class="sxs-lookup"><span data-stu-id="8770e-150">In the example below this is used to obtain the number of posts in a blog without loading the posts:</span></span>

```Csharp
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
<span data-ttu-id="8770e-151">請注意相關的幾件事：</span><span class="sxs-lookup"><span data-stu-id="8770e-151">A few things to notice about this:</span></span>
* <span data-ttu-id="8770e-152">因為才打電話 EF 核心，而且沒有其他公用建構函式一般用途，是私人的建構函式。</span><span class="sxs-lookup"><span data-stu-id="8770e-152">The constructor is private, since it is only ever call by EF Core, and there is another public constructor for general use.</span></span>
* <span data-ttu-id="8770e-153">使用插入的服務的程式碼 （也就是內容） 會針對它防禦正在`null`來處理一些情況其中 EF 核心不會建立執行個體。</span><span class="sxs-lookup"><span data-stu-id="8770e-153">The code using the injected service (i.e. the context) is defensive against it being `null` to handle cases where EF Core is not creating the instance.</span></span>
* <span data-ttu-id="8770e-154">因為服務儲存在讀/寫屬性，如果實體附加到新的內容執行個體，它會被重設。</span><span class="sxs-lookup"><span data-stu-id="8770e-154">Because service is stored in a read/write property it will be reset when the entity is attached to a new context instance.</span></span>

> [!WARNING]  
> <span data-ttu-id="8770e-155">插入如下 DbContext 是通常會視為反面模式，因為直接到 EF Core 涉入實體類型。</span><span class="sxs-lookup"><span data-stu-id="8770e-155">Injecting the DbContext like this is often considered an anti-pattern since it couples your entity types directly to EF Core.</span></span> <span data-ttu-id="8770e-156">使用像這樣的服務資料隱碼之前，請審慎考慮所有選項。</span><span class="sxs-lookup"><span data-stu-id="8770e-156">Carefully consider all options before using service injection like this.</span></span>
