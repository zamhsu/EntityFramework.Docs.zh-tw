---
title: 載入相關資料 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: d3a1810599771befb451715d93454fff63949771
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238303"
---
# <a name="loading-related-data"></a>載入相關資料

Entity Framework Core 可讓您在模型中使用導覽屬性來載入相關實體。 有三種常見的 O/RM 模式可用來載入相關資料。

* **積極式載入**表示會從資料庫以初始查詢一部分的方式載入相關資料。
* **明確式載入**表示會從資料庫於稍後以明確方式載入相關資料。
* **消極式載入**表示會於存取導覽屬性時從資料庫以透明的方式載入相關資料。

> [!TIP]  
> 您可以在 GitHub 上查看這篇文章的[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying)。

## <a name="eager-loading"></a>積極式載入

您可以使用 `Include` 方法來指定要包含於查詢結果中的相關資料。 在下列範例中，於結果中所傳回部落格的 `Posts` 屬性將會填入相關文章。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> Entity Framework Core 會將導覽屬性自動修正為先前已載入至內容執行個體的任何其他實體。 因此，即使未明確包含導覽屬性的資料，如果先前已載入部分或所有相關的實體，則仍然可能會填入該屬性。

您可以將來自多個關聯性的相關資料包含至單一查詢。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a>包括多個層級

您可以使用 `ThenInclude` 方法，透過關聯性向下切入以包含多個層級的相關資料。 下列範例會載入所有部落格、其相關文章，以及每篇文章的作者。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleThenInclude)]

您可以將多個呼叫鏈結到 `ThenInclude`，以繼續包含更深層級的相關資料。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

您可以結合上述全部，以便在相同查詢中包含來自多個層級和多個根的相關資料。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#IncludeTree)]

您可能會想要針對所包括的其中一個實體包含多個相關實體。 例如，查詢 `Blogs` 時，您包括了 `Posts`，接著想要同時包含 `Posts` 的 `Author` 和 `Tags`。 若要執行此動作，您必須指定每個從根開始的包含路徑。 例如 `Blog -> Posts -> Author` 和 `Blog -> Posts -> Tags`。 這不表示您將會收到多餘的聯結，在大部分情況下，EF 會在產生 SQL 時合併聯結。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

### <a name="single-and-split-queries"></a>單一和分割查詢

> [!NOTE]
> 這項功能會在 EF Core 5.0 中引進。

在關係資料庫中，所有相關實體預設會藉由引進聯結來載入：

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

如果一般的 blog 有多個相關文章，這些文章中的資料列將會複製 blog 的資訊，導致所謂的「笛卡爆炸」問題。 隨著載入的是多對多關聯性，重複的資料量可能會成長，且會對應用程式的效能造成負面影響。

EF 可讓您指定給定的 LINQ 查詢應該*分割*成多個 SQL 查詢。 分割查詢不是聯結，而是針對每個包含的一對多導覽執行額外的 SQL 查詢：

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs?name=AsSplitQuery&highlight=5)]

這會產生下列 SQL：

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
ORDER BY [b].[BlogId]

SELECT [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title], [b].[BlogId]
FROM [Blogs] AS [b]
INNER JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId]
```

雖然這可避免與聯結和笛卡兒爆炸相關的效能問題，但它也有一些缺點：

* 雖然大部分的資料庫都保證單一查詢的資料一致性，但不會有多個查詢的保證。 這表示，如果在執行查詢時同時更新資料庫，則產生的資料可能不一致。 這可以藉由將查詢包裝在可序列化或快照集交易中來降低，不過這可能會造成本身的效能問題。 如需詳細資訊，請參閱資料庫的檔。
* 每個查詢目前都表示您的資料庫有額外的網路往返;這可能會降低效能，尤其是資料庫的延遲很高 (例如雲端服務) 。 EF Core 在未來將會藉由將查詢批次處理成單一往返來改善這項功能。
* 雖然某些資料庫允許同時使用多個查詢的結果 (以 MARS) SQL Server，但在任何指定的時間點，大部分都只允許單一查詢處於作用中狀態。 這表示在執行較新的查詢之前，必須先在應用程式的記憶體中緩衝處理先前查詢的所有結果，並以可能顯著的方式增加您的記憶體需求。

可惜的是，載入符合所有案例的相關實體並沒有一種策略。 請仔細考慮單一和分割查詢的優缺點，並選取符合您需求的各項。

> [!NOTE]
> 一對一相關實體一律透過聯結載入，因為這不會影響效能。
>
> 目前，在 SQL Server 上使用查詢分割時，需要 `MultipleActiveResultSets=true` 在連接字串中設定。 未來的預覽將移除此需求。
>
> 未來 EF Core 5.0 的預覽將允許將查詢分割指定為您內容的預設值。

### <a name="filtered-include"></a>篩選的包含

> [!NOTE]
> 這項功能會在 EF Core 5.0 中引進。

將 [包含] 套用至載入相關資料時，您可以在包含的集合導覽上套用特定的可列舉作業，以便篩選和排序結果。

支援的作業包括： `Where` 、 `OrderBy` 、 `OrderByDescending` 、、、 `ThenBy` `ThenByDescending` `Skip` 和 `Take` 。

這類作業應套用在傳遞至 Include 方法之 lambda 的集合導覽上，如下列範例所示：

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#FilteredInclude)]

每個包含的導覽只允許一組唯一的篩選作業。 在針對指定集合導覽套用多個 Include 作業的情況下 `blog.Posts` ， (在下列範例中) 只能在其中一個專案上指定篩選作業： 

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered1)]

或者，也可以針對每個包含多次的導覽套用相同的作業：

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> 在追蹤查詢的情況下，篩選包含的結果可能因為[導覽修復](tracking.md)而非預期。 先前已 querried 並儲存在變更追蹤器中的所有相關實體，都會出現在篩選的 Include 查詢結果中，即使它們不符合篩選準則的需求也一樣。 `NoTracking`在這些情況下使用已篩選的包含時，請考慮使用查詢或重新建立 DbCoNtext。

範例：

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rathat than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

### <a name="include-on-derived-types"></a>衍生類型中的 Include

您可以使用 `Include` 和 `ThenInclude`來包含只定義於衍生類型上導覽的相關資料。

假設有下列模型：

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

身分為學生之所有人員的 `School` 導覽內容可以使用多個模式進行積極式載入：

* 使用 cast

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* 使用 `as` 運算子

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* 使用 `Include` 的多載，其會接受類型 `string` 的參數

  ```csharp
  context.People.Include("School").ToList()
  ```

## <a name="explicit-loading"></a>明確式載入

您可以透過 `DbContext.Entry(...)` API 來明確地載入導覽屬性。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

您也可以透過執行會傳回相關實體的個別查詢，來明確地載入導覽屬性。 如果啟用變更追蹤，則 EF Core 在載入實體時將會自動設定新載入實體的導覽屬性來參考已經載入的任何實體，並將已載入實體的導覽屬性設定為參考新載入的實體。

### <a name="querying-related-entities"></a>查詢相關實體

您也可以取得表示導覽屬性內容的 LINQ 查詢。

這可讓您執行像是透過相關實體來執行彙總運算子，而不需將其載入至記憶體之類的動作。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

您也可以篩選要將哪些相關實體載入至記憶體。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a>消極式載入

使用消極式載入的最簡單方式是安裝 [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) \(英文\) 套件，並呼叫 `UseLazyLoadingProxies` 來啟用它。 例如：

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```

或在使用 AddDbContext 時：

```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```

EF Core 接著將針對可覆寫的所有導覽屬性 (也就是說，它必須是 `virtual` 並位於可繼承的類別上) 啟用消極式載入。 例如，在下列實體中，系統將會對 `Post.Blog` 和 `Blog.Posts` 導覽屬性進行消極式載入。

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

### <a name="lazy-loading-without-proxies"></a>沒有 Proxy 的消極式載入

消極式載入 Proxy 的運作方式是將 `ILazyLoader` 服務插入至實體，如[實體類型建構函式](../modeling/constructors.md)中所述。 例如：

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

這並不需要實體類型為可繼承的，或是導覽屬性為虛擬的，並且可讓使用 `new` 建立的實體執行個體可以在附加到內容之後進行消極式載入。 不過，它需要對 `ILazyLoader` 服務的參考，這在 [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) 套件中定義。 此套件包含最基本的型別集，因此相依於它的影響很小。 不過，若要完全避免對實體型別中任何 EF Core 套件的相依性，您可以將 `ILazyLoader.Load` 方法插入為委派。 例如：

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

上述程式碼會使用 `Load` 擴充方法，來更簡潔地使用委派：

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
> 消極式載入委派的建構函式參數必須稱為 "lazyLoader"。 預計將於未來版本中推出使用與此不同之名稱的設定。

## <a name="related-data-and-serialization"></a>相關資料和序列化

由於 EF Core 將會自動修正導覽屬性，您的物件圖形最後可能會出現循環。 例如，載入部落格與其相關文章將會產生參考文章集合的部落格物件。 那些文章都會具有針對該部落格的參考。

某些序列化架構並不允許這類循環。 例如，Json.NET 將會在遇到循環時擲回下列例外狀況。

> Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog' (針對具有類型 'MyApplication.Models.Blog' 的屬性 'Blog' 偵測到自我參考迴圈)。

如果您使用 ASP.NET Core，則可將 Json.NET 設定為略過它在物件圖形中所找到的循環。 這可在 `Startup.cs` 的 `ConfigureServices(...)` 方法中完成。

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

另一個替代方式為使用 `[JsonIgnore]` 屬性裝飾導覽屬性的其中一個，它會指示 Json.NET 序列化時不要周遊該導覽屬性。
