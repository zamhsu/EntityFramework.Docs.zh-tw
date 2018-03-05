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
# <a name="loading-related-data"></a>載入相關的資料

Entity Framework Core 可讓您在模型中使用的導覽屬性，來載入相關的實體。 有三種常見的 O/RM 模式來載入相關的資料。
* **積極式載入**表示相關的資料從資料庫載入做為初始查詢的一部分。
* **明確式載入**表示相關的資料會明確地載入從資料庫在稍後。
* **消極式載入**表示，相關的資料會以透明的方式從資料庫載入時存取導覽屬性。

> [!TIP]  
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) \(英文\)。

## <a name="eager-loading"></a>積極式載入

您可以使用`Include`方法，以指定要包含在查詢結果的相關的資料。 在下列範例中，會在結果中傳回部落格會有其`Posts`屬性填入相關的文章。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> 實體 Framework Core 將會自動修正向上導覽屬性與先前已載入至內容執行個體的任何其他實體。 因此即使沒有明確加入的資料瀏覽屬性，屬性可能仍然會填入某些或所有相關的項目先前已載入。


您可以在單一查詢中包含多個關聯性中的相關的資料。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a>包括多個層級

您可以向下鑽研到包含多個層級的相關的資料所使用的關聯性`ThenInclude`方法。 下列範例會載入所有的部落格、 其相關的文章，每次張貼的作者。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#SingleThenInclude)]

> [!NOTE]  
> 目前版本的 Visual Studio 提供不正確的程式碼完成的選項，而且可能會導致標示有語法錯誤時使用的正確運算式`ThenInclude`之後集合導覽屬性的方法。 這是在 https://github.com/dotnet/roslyn/issues/8237 追蹤 IntelliSense 問題的徵兆。 它可以安全地忽略這些假性的語法錯誤，只要程式碼正確，而且可以成功編譯。 

您可以多個呼叫鏈結到`ThenInclude`繼續進一步包括層級的相關資料。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

您可以結合這些全部都在相同查詢中包含相關的資料從多個層級和多個根節點。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#IncludeTree)]

若要包含的其中一個實體所包含的多個相關的實體。 例如，當查詢`Blog`s，包括`Posts`接著又想將同時包含`Author`和`Tags`的`Posts`。 若要這樣做，您要指定每個包含從根目錄開始的路徑。 例如，`Blog -> Posts -> Author`和`Blog -> Posts -> Tags`。 這不表示您會收到多餘的聯結，在大部分情況下，將會合併 EF 聯結產生 SQL 時。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

### <a name="include-on-derived-types"></a>在衍生類型上包含

您可以加入只在使用衍生的類型上定義導覽中的相關的資料`Include`和`ThenInclude`。 

假設下列模型：

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

內容`School`瀏覽的所有人學生可以立即載入使用的數字的模式：

- 使用 cast
```Csharp
context.People.Include(person => ((Student)person).School).ToList()
```

- 使用`as`運算子
```Csharp
context.People.Include(person => (person as Student).School).ToList()
```

- 使用的多載`Include`會接受參數的型別 `string`
```Csharp
context.People.Include("Student").ToList()
```

### <a name="ignored-includes"></a>忽略包含

如果您變更查詢，使它不會再傳回查詢開始的實體類型的執行個體時，會忽略 include 運算子。

在下列範例中，包含運算子會根據`Blog`，但然後`Select`運算子用來變更查詢以傳回匿名型別。 在此情況下，包含運算子會有任何作用。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#IgnoredInclude)]

根據預設，EF 核心會記錄警告時包含運算子都會被忽略。 請參閱[記錄](../miscellaneous/logging.md)檢視記錄輸出的詳細資訊。 對擲回或不執行任何動作，則會忽略 include 運算子時，您可以變更行為。 這是通常在設定為您的內容-選項時`DbContext.OnConfiguring`，或在`Startup.cs`如果您使用 ASP.NET Core。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/ThrowOnIgnoredInclude/BloggingContext.cs#OnConfiguring)]

## <a name="explicit-loading"></a>明確式載入

> [!NOTE]  
> 在 EF 核心 1.1 已引入此功能。

您可以明確載入的導覽屬性，透過`DbContext.Entry(...)`應用程式開發介面。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#Eager)]

您也會明確可以藉由執行不同的查詢會傳回相關的實體載入導覽屬性。 如果已啟用變更追蹤，則載入時實體，EF Core 將會自動設定來參考任何實體已經載入，新載入實體的導覽屬性並設定參考的已載入實體的導覽屬性新載入的實體。

### <a name="querying-related-entities"></a>查詢相關的實體

您也可以取得 LINQ 查詢，表示導覽屬性的內容。

這可讓您執行像是透過相關的項目執行彙總運算子，而不載入記憶體。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

您也可以篩選哪些相關的實體載入至記憶體。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a>消極式載入

> [!NOTE]  
> 在 EF 核心 2.1 中已引入此功能。

若要使用消極式載入最簡單的方式是安裝[Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/)封裝，並讓它藉由呼叫`UseLazyLoadingProxies`。 例如: 
```Csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```
或使用 AddDbContext 時：
```Csharp
    .AddDbContext<BloggingContext>(
        b => b.UseLazyLoadingProxies()
              .UseSqlServer(myConnectionString));
```
EF 核心然後會啟用消極式載入瀏覽的所有屬性會覆寫-也就是說，它必須是`virtual`和可以繼承自一個類別上。 例如，在下列的實體，`Post.Blog`和`Blog.Posts`導覽屬性將會延遲載入。
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
### <a name="lazy-loading-without-proxies"></a>沒有 proxy 的延遲載入

延遲載入 proxy 的運作方式是將，以便`ILazyLoader`中所述，服務到實體，[實體型別建構函式](../modeling/constructors.md)。 例如: 
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
這不需要用於繼承的實體類型或為虛擬的導覽屬性，並可讓實體執行個體，以建立`new`延遲載入一次附加至內容。 不過，它需要的參考`ILazyLoader`涉入 EF 核心組件的實體類型的服務。 若要避免此 EF 核心允許`ILazyLoader.Load`無法插入為委派的方法。 例如: 
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
使用上方的程式碼`Load`擴充方法，請使用 「 委派 」 的位元會清除：
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
> 消極式載入委派建構函式參數必須為"lazyLoader"。 若要使用不同的名稱，這計劃在未來的版本設定。

## <a name="related-data-and-serialization"></a>相關的資料和序列化

因為 EF 核心會自動修正向上導覽屬性，您可以得到循環物件圖形中。 例如，載入部落格和它相關的文章將會導致參考的文章集合部落格物件。 這些文章中的每個都會有部落格的參考。

某些序列化架構不允許這類循環。 例如，Json.NET 將會擲回下列例外狀況，如果發生循環。

> Newtonsoft.Json.JsonSerializationException： 自我參考型別 'MyApplication.Models.Blog' 屬性 '部落格' 偵測到迴圈。

如果您使用 ASP.NET Core，您可以設定 Json.NET 忽略它找到的物件圖形中的循環。 這是`ConfigureServices(...)`方法中的`Startup.cs`。

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
