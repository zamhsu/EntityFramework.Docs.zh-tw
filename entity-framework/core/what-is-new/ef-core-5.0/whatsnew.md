---
title: EF Core 5.0 的新功能
description: EF Core 5.0 的新功能總覽
author: ajcvickers
ms.date: 09/10/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: 3efa883cdfac1ecd412112ef06c7763f1a7e12f1
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429243"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="5ea9b-103">EF Core 5.0 的新功能</span><span class="sxs-lookup"><span data-stu-id="5ea9b-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="5ea9b-104">所有規劃的 EF Core 5.0 功能現在都已完成。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-104">All features planned for EF Core 5.0 have now been completed.</span></span> <span data-ttu-id="5ea9b-105">本頁面包含每個預覽中所引進之有趣變更的總覽。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-105">This page contains an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="5ea9b-106">此頁面不會複製 [EF Core 5.0 的方案](xref:core/what-is-new/ef-core-5.0/plan)。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-106">This page does not duplicate the [plan for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span></span> <span data-ttu-id="5ea9b-107">此計畫描述 EF Core 5.0 的整體主題，包括我們在出貨最終發行版本之前打算包含的所有專案。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

## <a name="rc1"></a><span data-ttu-id="5ea9b-108">RC1</span><span class="sxs-lookup"><span data-stu-id="5ea9b-108">RC1</span></span>

### <a name="many-to-many"></a><span data-ttu-id="5ea9b-109">多對多</span><span class="sxs-lookup"><span data-stu-id="5ea9b-109">Many-to-many</span></span>

<span data-ttu-id="5ea9b-110">EF Core 5.0 支援多對多關聯性，而不需要明確地對應聯結資料表。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-110">EF Core 5.0 supports many-to-many relationships without explicitly mapping the join table.</span></span>

<span data-ttu-id="5ea9b-111">例如，請考慮下列實體類型：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-111">For example, consider these entity types:</span></span>

```csharp
public class Post
{
    public int Id { get; set; }
    public string Name { get; set; }
    public ICollection<Tag> Tags { get; set; }
}

public class Tag
{
    public int Id { get; set; }
    public string Text { get; set; }
    public ICollection<Post> Posts { get; set; }
}
```

<span data-ttu-id="5ea9b-112">請注意，其中 `Post` 包含的集合 `Tags` ，並 `Tag` 包含的集合 `Posts` 。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-112">Notice that `Post` contains a collection of `Tags`, and `Tag` contains a collection of `Posts`.</span></span> <span data-ttu-id="5ea9b-113">EF Core 5.0 依慣例將此辨識為多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-113">EF Core 5.0 recognizes this as a many-to-many relationship by convention.</span></span> <span data-ttu-id="5ea9b-114">這表示在中不需要任何程式碼 `OnModelCreating` ：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-114">This means no code is required in `OnModelCreating`:</span></span>

```csharp
public class BlogContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Tag> Tags { get; set; }
}
```

<span data-ttu-id="5ea9b-115">當使用 (或 `EnsureCreated`) 的遷移來建立資料庫時，EF Core 會自動建立聯結資料表。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-115">When Migrations (or `EnsureCreated`) are used to create the database, EF Core will automatically create the join table.</span></span> <span data-ttu-id="5ea9b-116">例如，在此模型的 SQL Server 上，EF Core 會產生：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-116">For example, on SQL Server for this model, EF Core generates:</span></span>

```sql
CREATE TABLE [Posts] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Posts] PRIMARY KEY ([Id])
);

CREATE TABLE [Tag] (
    [Id] int NOT NULL IDENTITY,
    [Text] nvarchar(max) NULL,
    CONSTRAINT [PK_Tag] PRIMARY KEY ([Id])
);

CREATE TABLE [PostTag] (
    [PostsId] int NOT NULL,
    [TagsId] int NOT NULL,
    CONSTRAINT [PK_PostTag] PRIMARY KEY ([PostsId], [TagsId]),
    CONSTRAINT [FK_PostTag_Posts_PostsId] FOREIGN KEY ([PostsId]) REFERENCES [Posts] ([Id]) ON DELETE CASCADE,
    CONSTRAINT [FK_PostTag_Tag_TagsId] FOREIGN KEY ([TagsId]) REFERENCES [Tag] ([Id]) ON DELETE CASCADE
);

CREATE INDEX [IX_PostTag_TagsId] ON [PostTag] ([TagsId]);
```

<span data-ttu-id="5ea9b-117">建立和關聯 `Tag` 和 `Post` 實體會導致聯結資料表更新自動發生。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-117">Creating and associating `Tag` and `Post` entities results in join table updates happening automatically.</span></span> <span data-ttu-id="5ea9b-118">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-118">For example:</span></span>

```csharp
var beginnerTag = new Tag {Text = "Beginner"};
var advancedTag = new Tag {Text = "Advanced"};
var efCoreTag = new Tag {Text = "EF Core"};

context.AddRange(
    new Post {Name = "EF Core 101", Tags = new List<Tag> {beginnerTag, efCoreTag}},
    new Post {Name = "Writing an EF database provider", Tags = new List<Tag> {advancedTag, efCoreTag}},
    new Post {Name = "Savepoints in EF Core", Tags = new List<Tag> {beginnerTag, efCoreTag}});

context.SaveChanges();
```

<span data-ttu-id="5ea9b-119">插入貼文和標記之後，EF 接著會自動在聯結資料表中建立資料列。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-119">After inserting the Posts and Tags, EF will then automatically create rows in the join table.</span></span> <span data-ttu-id="5ea9b-120">例如，在 SQL Server：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-120">For example, on SQL Server:</span></span>

```sql
SET NOCOUNT ON;
INSERT INTO [PostTag] ([PostsId], [TagsId])
VALUES (@p6, @p7),
(@p8, @p9),
(@p10, @p11),
(@p12, @p13),
(@p14, @p15),
(@p16, @p17);
```

<span data-ttu-id="5ea9b-121">針對查詢，包含和其他查詢作業的運作方式就像任何其他關聯性一樣。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-121">For queries, Include and other query operations work just like for any other relationship.</span></span> <span data-ttu-id="5ea9b-122">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-122">For example:</span></span>

```csharp
foreach (var post in context.Posts.Include(e => e.Tags))
{
    Console.Write($"Post \"{post.Name}\" has tags");

    foreach (var tag in post.Tags)
    {
        Console.Write($" '{tag.Text}'");
    }
}
```

<span data-ttu-id="5ea9b-123">產生的 SQL 會自動使用聯結資料表來取回所有相關的標記：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-123">The SQL generated uses the join table automatically to bring back all related Tags:</span></span>

```sql
SELECT [p].[Id], [p].[Name], [t0].[PostsId], [t0].[TagsId], [t0].[Id], [t0].[Text]
FROM [Posts] AS [p]
LEFT JOIN (
    SELECT [p0].[PostsId], [p0].[TagsId], [t].[Id], [t].[Text]
    FROM [PostTag] AS [p0]
    INNER JOIN [Tag] AS [t] ON [p0].[TagsId] = [t].[Id]
) AS [t0] ON [p].[Id] = [t0].[PostsId]
ORDER BY [p].[Id], [t0].[PostsId], [t0].[TagsId], [t0].[Id]
```

<span data-ttu-id="5ea9b-124">不同于 EF6，EF Core 允許完整自訂聯結資料表。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-124">Unlike EF6, EF Core allows full customization of the join table.</span></span> <span data-ttu-id="5ea9b-125">例如，下列程式碼會設定多對多關聯性，此關聯性也會具有對聯結實體的導覽，而且聯結實體會包含承載屬性：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-125">For example, the code below configures a many-to-many relationship that also has navigations to the join entity, and in which the join entity contains a payload property:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Post>()
        .HasMany(p => p.Tags)
        .WithMany(p => p.Posts)
        .UsingEntity<PostTag>(
            j => j
                .HasOne(pt => pt.Tag)
                .WithMany()
                .HasForeignKey(pt => pt.TagId),
            j => j
                .HasOne(pt => pt.Post)
                .WithMany()
                .HasForeignKey(pt => pt.PostId),
            j =>
            {
                j.Property(pt => pt.PublicationDate).HasDefaultValueSql("CURRENT_TIMESTAMP");
                j.HasKey(t => new { t.PostId, t.TagId });
            });
}
```

> [!NOTE]
> <span data-ttu-id="5ea9b-126">尚未新增與資料庫中多對多關聯性的元件支援。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-126">Support for scaffolding many-to-many relationships from the database is not yet added.</span></span> <span data-ttu-id="5ea9b-127">請參閱 [追蹤問題](https://github.com/dotnet/efcore/issues/22475)。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-127">See [tracking issue](https://github.com/dotnet/efcore/issues/22475).</span></span>

### <a name="map-entity-types-to-queries"></a><span data-ttu-id="5ea9b-128">將實體類型對應至查詢</span><span class="sxs-lookup"><span data-stu-id="5ea9b-128">Map entity types to queries</span></span>

<span data-ttu-id="5ea9b-129">實體類型通常會對應至資料表或視圖，讓 EF Core 在查詢該型別時，將會提取資料表或視圖的內容。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-129">Entity types are commonly mapped to tables or views such that EF Core will pull back the contents of the table or view when querying for that type.</span></span> <span data-ttu-id="5ea9b-130">EF Core 5.0 可讓實體類型對應至「定義查詢」。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-130">EF Core 5.0 allows an entity type to mapped to a "defining query".</span></span> <span data-ttu-id="5ea9b-131"> (這在舊版中是部分支援的，但已經過改良，而且在 EF Core 5.0 中有不同的語法。 ) </span><span class="sxs-lookup"><span data-stu-id="5ea9b-131">(This was partially supported in previous versions, but is much improved and has different syntax in EF Core 5.0.)</span></span>

<span data-ttu-id="5ea9b-132">例如，假設有兩個數據表;其中一個包含新式文章;具有舊版文章的另一篇。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-132">For example, consider two tables; one with modern posts; the other with legacy posts.</span></span> <span data-ttu-id="5ea9b-133">新式貼文資料表有一些額外的資料行，但基於我們的應用程式目的，我們想要結合新式和舊版文章，並將其對應至具有所有必要屬性的實體類型：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-133">The modern posts table has some additional columns, but for the purpose of our application we want both modern and legacy posts to be combined and mapped to an entity type with all necessary properties:</span></span>

```csharp
public class Post
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Category { get; set; }
    public int BlogId { get; set; }
    public Blog Blog { get; set; }
}
```

<span data-ttu-id="5ea9b-134">在 EF Core 5.0 中， `ToSqlQuery` 可以用來將此實體類型對應至提取並結合兩個數據表中資料列的查詢：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-134">In EF Core 5.0, `ToSqlQuery` can be used to map this entity type to a query that pulls and combines rows from both tables:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Post>().ToSqlQuery(
        @"SELECT Id, Name, Category, BlogId FROM posts
          UNION ALL
          SELECT Id, Name, ""Legacy"", BlogId from legacy_posts");
}
```

<span data-ttu-id="5ea9b-135">請注意， `legacy_posts` 資料表沒有資料 `Category` 行，因此我們會改為合成所有舊版文章的預設值。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-135">Notice that the `legacy_posts` table does not have a `Category` column, so we instead synthesize a default value for all legacy posts.</span></span>

<span data-ttu-id="5ea9b-136">然後，您可以使用 LINQ 查詢的一般方式來使用此實體類型。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-136">This entity type can then be used in the normal way for LINQ queries.</span></span> <span data-ttu-id="5ea9b-137">例如，</span><span class="sxs-lookup"><span data-stu-id="5ea9b-137">For example.</span></span> <span data-ttu-id="5ea9b-138">LINQ 查詢：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-138">the LINQ query:</span></span>

```csharp
var posts = context.Posts.Where(e => e.Blog.Name.Contains("Unicorn")).ToList();
```

<span data-ttu-id="5ea9b-139">在 SQLite 上產生下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-139">Generates the following SQL on SQLite:</span></span>

```sql
SELECT "p"."Id", "p"."BlogId", "p"."Category", "p"."Name"
FROM (
    SELECT Id, Name, Category, BlogId FROM posts
    UNION ALL
    SELECT Id, Name, "Legacy", BlogId from legacy_posts
) AS "p"
INNER JOIN "Blogs" AS "b" ON "p"."BlogId" = "b"."Id"
WHERE ('Unicorn' = '') OR (instr("b"."Name", 'Unicorn') > 0)
```

<span data-ttu-id="5ea9b-140">請注意，針對實體型別所設定的查詢是用來做為撰寫完整 LINQ 查詢的起點。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-140">Notice that the query configured for the entity type is used as a starting for composing the full LINQ query.</span></span>

### <a name="event-counters"></a><span data-ttu-id="5ea9b-141">事件計數器</span><span class="sxs-lookup"><span data-stu-id="5ea9b-141">Event counters</span></span>

<span data-ttu-id="5ea9b-142">[.Net 事件計數器](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) 是有效率地從應用程式公開效能度量的一種方式。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-142">[.NET event counters](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) are a way to efficiently expose performance metrics from an application.</span></span> <span data-ttu-id="5ea9b-143">EF Core 5.0 包含類別下的事件計數器 `Microsoft.EntityFrameworkCore` 。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-143">EF Core 5.0 includes event counters under the `Microsoft.EntityFrameworkCore` category.</span></span> <span data-ttu-id="5ea9b-144">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-144">For example:</span></span>

```console
dotnet counters monitor Microsoft.EntityFrameworkCore -p 49496
```

<span data-ttu-id="5ea9b-145">這會告訴 dotnet 計數器開始收集進程49496的 EF Core 事件。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-145">This tells dotnet counters to start collecting EF Core events for process 49496.</span></span> <span data-ttu-id="5ea9b-146">這會產生類似主控台的輸出：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-146">This generates output like this in the console:</span></span>

```console
[Microsoft.EntityFrameworkCore]
    Active DbContexts                                               1
    Execution Strategy Operation Failures (Count / 1 sec)           0
    Execution Strategy Operation Failures (Total)                   0
    Optimistic Concurrency Failures (Count / 1 sec)                 0
    Optimistic Concurrency Failures (Total)                         0
    Queries (Count / 1 sec)                                     1,755
    Queries (Total)                                            98,402
    Query Cache Hit Rate (%)                                      100
    SaveChanges (Count / 1 sec)                                     0
    SaveChanges (Total)                                             1
```

### <a name="property-bags"></a><span data-ttu-id="5ea9b-147">屬性包</span><span class="sxs-lookup"><span data-stu-id="5ea9b-147">Property bags</span></span>

<span data-ttu-id="5ea9b-148">EF Core 5.0 允許相同的 CLR 類型對應到多個不同的實體類型。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-148">EF Core 5.0 allows the same CLR type to be mapped to multiple different entity types.</span></span> <span data-ttu-id="5ea9b-149">這類類型稱為共用類型實體類型。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-149">Such types are known as shared-type entity types.</span></span> <span data-ttu-id="5ea9b-150">這項功能與預覽) 1 中所包含的索引子屬性結合 (，可讓屬性包作為實體類型使用。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-150">This feature combined with indexer properties (included in preview 1) allows property bags to be used as entity type.</span></span>

<span data-ttu-id="5ea9b-151">例如，下列 DbCoNtext 會將 BCL 類型設定 `Dictionary<string, object>` 為產品和分類的共用類型實體類型。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-151">For example, the DbContext below configures the BCL type `Dictionary<string, object>` as a shared-type entity type for both products and categories.</span></span>

```csharp
public class ProductsContext : DbContext
{
    public DbSet<Dictionary<string, object>> Products => Set<Dictionary<string, object>>("Product");
    public DbSet<Dictionary<string, object>> Categories => Set<Dictionary<string, object>>("Category");

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.SharedTypeEntity<Dictionary<string, object>>("Category", b =>
        {
            b.IndexerProperty<string>("Description");
            b.IndexerProperty<int>("Id");
            b.IndexerProperty<string>("Name").IsRequired();
        });

        modelBuilder.SharedTypeEntity<Dictionary<string, object>>("Product", b =>
        {
            b.IndexerProperty<int>("Id");
            b.IndexerProperty<string>("Name").IsRequired();
            b.IndexerProperty<string>("Description");
            b.IndexerProperty<decimal>("Price");
            b.IndexerProperty<int?>("CategoryId");

            b.HasOne("Category", null).WithMany();
        });
    }
}
```

<span data-ttu-id="5ea9b-152">字典物件 ( 「屬性包」 ) 現在可以新增至內容中做為實體實例並儲存。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-152">Dictionary objects ("property bags") can now be added to the context as entity instances and saved.</span></span> <span data-ttu-id="5ea9b-153">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-153">For example:</span></span>

```csharp
var beverages = new Dictionary<string, object>
{
    ["Name"] = "Beverages",
    ["Description"] = "Stuff to sip on"
};

context.Categories.Add(beverages);

context.SaveChanges();
```

<span data-ttu-id="5ea9b-154">然後，您可以透過一般方式來查詢和更新這些實體：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-154">These entities can then be queried and updated in the normal way:</span></span>

```csharp
var foods = context.Categories.Single(e => e["Name"] == "Foods");
var marmite = context.Products.Single(e => e["Name"] == "Marmite");

marmite["CategoryId"] = foods["Id"];
marmite["Description"] = "Yummy when spread _thinly_ on buttered Toast!";

context.SaveChanges();
```

### <a name="savechanges-interception-and-events"></a><span data-ttu-id="5ea9b-155">SaveChanges 攔截和事件</span><span class="sxs-lookup"><span data-stu-id="5ea9b-155">SaveChanges interception and events</span></span>

<span data-ttu-id="5ea9b-156">EF Core 5.0 引進了 .NET 事件，以及在呼叫 SaveChanges 時觸發的 EF Core 攔截器。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-156">EF Core 5.0 introduces both .NET events and an EF Core interceptor triggered when SaveChanges is called.</span></span>

<span data-ttu-id="5ea9b-157">事件很容易使用;例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-157">The events are simple to use; for example:</span></span>

```csharp
context.SavingChanges += (sender, args) =>
{
    Console.WriteLine($"Saving changes for {((DbContext)sender).Database.GetConnectionString()}");
};

context.SavedChanges += (sender, args) =>
{
    Console.WriteLine($"Saved {args.EntitiesSavedCount} changes for {((DbContext)sender).Database.GetConnectionString()}");
};
```

<span data-ttu-id="5ea9b-158">請注意：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-158">Notice that:</span></span>

* <span data-ttu-id="5ea9b-159">事件寄件者是 `DbContext` 實例</span><span class="sxs-lookup"><span data-stu-id="5ea9b-159">The event sender is the `DbContext` instance</span></span>
* <span data-ttu-id="5ea9b-160">事件的引數 `SavedChanges` 包含儲存至資料庫的實體數目</span><span class="sxs-lookup"><span data-stu-id="5ea9b-160">The args for the `SavedChanges` event contains the number of entities saved to the database</span></span>

<span data-ttu-id="5ea9b-161">攔截器是由定義 `ISaveChangesInterceptor` ，但通常會 convienient 繼承， `SaveChangesInterceptor` 以避免執行每個方法。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-161">The interceptor is defined by `ISaveChangesInterceptor`, but it is often convienient to inherit from `SaveChangesInterceptor` to avoid implementing every method.</span></span> <span data-ttu-id="5ea9b-162">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-162">For example:</span></span>

```csharp
public class MySaveChangesInterceptor : SaveChangesInterceptor
{
    public override InterceptionResult<int> SavingChanges(
        DbContextEventData eventData,
        InterceptionResult<int> result)
    {
        Console.WriteLine($"Saving changes for {eventData.Context.Database.GetConnectionString()}");

        return result;
    }

    public override ValueTask<InterceptionResult<int>> SavingChangesAsync(
        DbContextEventData eventData,
        InterceptionResult<int> result,
        CancellationToken cancellationToken = new CancellationToken())
    {
        Console.WriteLine($"Saving changes asynchronously for {eventData.Context.Database.GetConnectionString()}");

        return new ValueTask<InterceptionResult<int>>(result);
    }
}
```

<span data-ttu-id="5ea9b-163">請注意：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-163">Notice that:</span></span>

* <span data-ttu-id="5ea9b-164">攔截器同時具有同步和非同步方法。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-164">The interceptor has both sync and async methods.</span></span> <span data-ttu-id="5ea9b-165">如果您需要執行非同步 i/o （例如寫入審核伺服器），這會很有用。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-165">This can be useful if you need to perform async I/O, such as writing to an audit server.</span></span>
* <span data-ttu-id="5ea9b-166">攔截器可讓 SaveChanges 使用所有攔截器通用的機制來略過 `InterceptionResult` 。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-166">The interceptor allows SaveChanges to be skipped using the `InterceptionResult` mechanism common to all interceptors.</span></span>

<span data-ttu-id="5ea9b-167">攔截器的缺點是它們必須在正在建立時于 DbCoNtext 上註冊。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-167">The downside of interceptors is that they must be registered on the DbContext when it is being constructed.</span></span> <span data-ttu-id="5ea9b-168">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-168">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .AddInterceptors(new MySaveChangesInterceptor())
        .UseSqlite("Data Source = test.db");
```

<span data-ttu-id="5ea9b-169">相反地，您可以隨時在 DbCoNtext 實例上註冊事件。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-169">In contrast, the events can be registered on the DbContext instance at any time.</span></span>

### <a name="exclude-tables-from-migrations"></a><span data-ttu-id="5ea9b-170">從遷移中排除資料表</span><span class="sxs-lookup"><span data-stu-id="5ea9b-170">Exclude tables from migrations</span></span>

<span data-ttu-id="5ea9b-171">在多個 DbcoNtext 中對應單一實體類型有時很有用。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-171">It is sometimes useful to have a single entity type mapped in multiple DbContexts.</span></span> <span data-ttu-id="5ea9b-172">尤其是 [在使用系](https://www.martinfowler.com/bliki/BoundedContext.html)結內容時更是如此，因為每個系結內容通常會有不同的 DbCoNtext 類型。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-172">This is especially true when using [bounded contexts](https://www.martinfowler.com/bliki/BoundedContext.html), for which it is common to have a different DbContext type for each bounded context.</span></span>

<span data-ttu-id="5ea9b-173">例如， `User` 授權內容和報表內容可能需要型別。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-173">For example, a `User` type may be needed by both an authorization context and a reporting context.</span></span> <span data-ttu-id="5ea9b-174">如果對類型進行了變更 `User` ，則這兩個 dbcoNtext 的遷移將會嘗試更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-174">If a change is made to the `User` type, then migrations for both DbContexts will attempt to update the database.</span></span> <span data-ttu-id="5ea9b-175">為了避免這種情況，您可以將其中一個內容的模型設定為從其遷移中排除資料表。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-175">To prevent this, the model for one of the contexts can be configured to exclude the table from its migrations.</span></span>

<span data-ttu-id="5ea9b-176">在下列程式碼中， `AuthorizationContext` 將會產生資料表變更的遷移 `Users` ，但 `ReportingContext` 不會導致衝突的遷移。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-176">In the code below, the `AuthorizationContext` will generate migrations for changes to the `Users` table, but the `ReportingContext` will not, preventing the migrations from clashing.</span></span>

```csharp
public class AuthorizationContext : DbContext
{
    public DbSet<User> Users { get; set; }
}

public class ReportingContext : DbContext
{
    public DbSet<User> Users { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<User>().ToTable("Users", t => t.ExcludeFromMigrations());
    }
}
```

### <a name="required-11-dependents"></a><span data-ttu-id="5ea9b-177">必要的1:1 相依項</span><span class="sxs-lookup"><span data-stu-id="5ea9b-177">Required 1:1 dependents</span></span>

<span data-ttu-id="5ea9b-178">在 EF Core 3.1 中，一對一關聯性的相依 end 一律視為選擇性。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-178">In EF Core 3.1, the dependent end of a one-to-one relationship was always considered optional.</span></span> <span data-ttu-id="5ea9b-179">這在使用擁有的實體時最為明顯。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-179">This was most apparent when using owned entities.</span></span> <span data-ttu-id="5ea9b-180">例如，請考慮下列模型和設定：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-180">For example, consider the following model and configuration:</span></span>

```csharp
public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }

    public Address HomeAddress { get; set; }
    public Address WorkAddress { get; set; }
}

public class Address
{
    public string Line1 { get; set; }
    public string Line2 { get; set; }
    public string City { get; set; }
    public string Region { get; set; }
    public string Country { get; set; }
    public string Postcode { get; set; }
}
```

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Person>(b =>
    {
        b.OwnsOne(e => e.HomeAddress,
            b =>
            {
                b.Property(e => e.Line1).IsRequired();
                b.Property(e => e.City).IsRequired();
                b.Property(e => e.Region).IsRequired();
                b.Property(e => e.Postcode).IsRequired();
            });

        b.OwnsOne(e => e.WorkAddress);
    });
}
```

<span data-ttu-id="5ea9b-181">這會導致針對 SQLite 建立下表的遷移：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-181">This results in Migrations creating the following table for SQLite:</span></span>

```sql
CREATE TABLE "People" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_People" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL,
    "HomeAddress_Line1" TEXT NULL,
    "HomeAddress_Line2" TEXT NULL,
    "HomeAddress_City" TEXT NULL,
    "HomeAddress_Region" TEXT NULL,
    "HomeAddress_Country" TEXT NULL,
    "HomeAddress_Postcode" TEXT NULL,
    "WorkAddress_Line1" TEXT NULL,
    "WorkAddress_Line2" TEXT NULL,
    "WorkAddress_City" TEXT NULL,
    "WorkAddress_Region" TEXT NULL,
    "WorkAddress_Country" TEXT NULL,
    "WorkAddress_Postcode" TEXT NULL
);
```

<span data-ttu-id="5ea9b-182">請注意，所有資料行都可為 null，即使某些 `HomeAddress` 屬性已設定為必要也是一樣。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-182">Notice that all the columns are nullable, even though some of the `HomeAddress` properties have been configured as required.</span></span> <span data-ttu-id="5ea9b-183">此外，當查詢時 `Person` ，如果 home 或 work 位址的所有資料行都是 null，則 EF Core 會將 `HomeAddress` 及/或 `WorkAddress` 屬性保留為 null，而不是設定的空白實例 `Address` 。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-183">Also, when querying for a `Person`, if all the columns for either the home or work address are null, then EF Core will leave the `HomeAddress` and/or `WorkAddress` properties as null, rather than setting an empty instance of `Address`.</span></span>

<span data-ttu-id="5ea9b-184">在 EF Core 5.0 中， `HomeAddress` 現在可以將導覽設定為必要的相依。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-184">In EF Core 5.0, the `HomeAddress` navigation can now be configured as as a required dependent.</span></span> <span data-ttu-id="5ea9b-185">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-185">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Person>(b =>
    {
        b.OwnsOne(e => e.HomeAddress,
            b =>
            {
                b.Property(e => e.Line1).IsRequired();
                b.Property(e => e.City).IsRequired();
                b.Property(e => e.Region).IsRequired();
                b.Property(e => e.Postcode).IsRequired();
            });
        b.Navigation(e => e.HomeAddress).IsRequired();

        b.OwnsOne(e => e.WorkAddress);
    });
}
```

<span data-ttu-id="5ea9b-186">遷移所建立的資料表現在會針對必要相依的必要屬性，加入不可為 null 的資料行：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-186">The table created by Migrations will now included non-nullable columns for the required properties of the required dependent:</span></span>

```sql
CREATE TABLE "People" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_People" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL,
    "HomeAddress_Line1" TEXT NOT NULL,
    "HomeAddress_Line2" TEXT NULL,
    "HomeAddress_City" TEXT NOT NULL,
    "HomeAddress_Region" TEXT NOT NULL,
    "HomeAddress_Country" TEXT NULL,
    "HomeAddress_Postcode" TEXT NOT NULL,
    "WorkAddress_Line1" TEXT NULL,
    "WorkAddress_Line2" TEXT NULL,
    "WorkAddress_City" TEXT NULL,
    "WorkAddress_Region" TEXT NULL,
    "WorkAddress_Country" TEXT NULL,
    "WorkAddress_Postcode" TEXT NULL
);
```

<span data-ttu-id="5ea9b-187">此外，如果嘗試儲存具有 null 必要相依的擁有者，EF Core 現在將會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-187">In addition, EF Core will now throw an exception if an attempt is made to save an owner which has a null required dependent.</span></span> <span data-ttu-id="5ea9b-188">在此範例中，EF Core 會在嘗試使用 null 儲存時擲回 `Person` `HomeAddress` 。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-188">In this example, EF Core will throw when attempting to save a `Person` with a null `HomeAddress`.</span></span>

<span data-ttu-id="5ea9b-189">最後，即使必要相依的所有資料行都有 null 值，EF Core 仍會建立必要相依的實例。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-189">Finally, EF Core will still create an instance of a required dependent even when all the columns for the required dependent have null values.</span></span>

### <a name="options-for-migration-generation"></a><span data-ttu-id="5ea9b-190">產生遷移的選項</span><span class="sxs-lookup"><span data-stu-id="5ea9b-190">Options for migration generation</span></span>

<span data-ttu-id="5ea9b-191">EF Core 5.0 針對不同用途導入了更多的遷移控制。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-191">EF Core 5.0 introduces greater control over generation of migrations for different purposes.</span></span> <span data-ttu-id="5ea9b-192">這包括了能夠：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-192">This includes the ability to:</span></span>

* <span data-ttu-id="5ea9b-193">知道是否正在為腳本產生遷移或立即執行</span><span class="sxs-lookup"><span data-stu-id="5ea9b-193">Know if the migration is being generated for a script or for immediate execution</span></span>
* <span data-ttu-id="5ea9b-194">知道是否正在產生等冪腳本</span><span class="sxs-lookup"><span data-stu-id="5ea9b-194">Know if an idempotent script is being generated</span></span>
* <span data-ttu-id="5ea9b-195">知道腳本是否應該排除交易語句 (請參閱以下 _交易的遷移腳本_ 。 ) </span><span class="sxs-lookup"><span data-stu-id="5ea9b-195">Know if the script should exclude transaction statements (See _Migrations scripts with transactions_ below.)</span></span>

<span data-ttu-id="5ea9b-196">此行為是由列舉所指定 `MigrationsSqlGenerationOptions` ，現在可傳遞給 `IMigrator.GenerateScript` 。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-196">This behavior is specified by an the `MigrationsSqlGenerationOptions` enum, which can now be passed to `IMigrator.GenerateScript`.</span></span>

<span data-ttu-id="5ea9b-197">這項工作中也包含了更好的等冪性腳本產生，可在 `EXEC` 需要時呼叫 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-197">Also included in this work is better generation of idempotent scripts with calls to `EXEC` on SQL Server when needed.</span></span> <span data-ttu-id="5ea9b-198">這項工作也可以對其他資料庫提供者（包括于 postgresql）所產生的腳本進行類似的改進。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-198">This work also enables similar improvements to the scripts generated by other database providers, including PostgreSQL.</span></span>

### <a name="migrations-scripts-with-transactions"></a><span data-ttu-id="5ea9b-199">使用交易來遷移腳本</span><span class="sxs-lookup"><span data-stu-id="5ea9b-199">Migrations scripts with transactions</span></span>

<span data-ttu-id="5ea9b-200">從遷移產生的 SQL 腳本現在包含用來開始和認可交易的語句，適用于遷移。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-200">SQL scripts generated from migrations now contain statements to begin and commit transactions as appropriate for the migration.</span></span> <span data-ttu-id="5ea9b-201">例如，下列的遷移腳本是從兩個遷移產生的。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-201">For example, the migration script below was generated from two migrations.</span></span> <span data-ttu-id="5ea9b-202">請注意，每個遷移現在都在交易內套用。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-202">Notice that each migration is now applied inside a transaction.</span></span>

```sql
BEGIN TRANSACTION;
GO

CREATE TABLE [Groups] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Groups] PRIMARY KEY ([Id])
);
GO

CREATE TABLE [Members] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NULL,
    [GroupId] int NULL,
    CONSTRAINT [PK_Members] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Members_Groups_GroupId] FOREIGN KEY ([GroupId]) REFERENCES [Groups] ([Id]) ON DELETE NO ACTION
);
GO

CREATE INDEX [IX_Members_GroupId] ON [Members] ([GroupId]);
GO

INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
VALUES (N'20200910194835_One', N'6.0.0-alpha.1.20460.2');
GO

COMMIT;
GO

BEGIN TRANSACTION;
GO

EXEC sp_rename N'[Groups].[Name]', N'GroupName', N'COLUMN';
GO

INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
VALUES (N'20200910195234_Two', N'6.0.0-alpha.1.20460.2');
GO

COMMIT;
```

<span data-ttu-id="5ea9b-203">如上一節所述，如果交易需要以不同的方式處理，則可以停用這項交易。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-203">As mentioned in the previous section, this use of transactions can be disabled if transactions need to be handled differently.</span></span>

### <a name="see-pending-migrations"></a><span data-ttu-id="5ea9b-204">查看暫止的遷移</span><span class="sxs-lookup"><span data-stu-id="5ea9b-204">See pending migrations</span></span>

<span data-ttu-id="5ea9b-205">這項功能是由「社區」所貢獻 [@Psypher9](https://github.com/Psypher9) 。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-205">This feature was contributed from the community by [@Psypher9](https://github.com/Psypher9).</span></span> <span data-ttu-id="5ea9b-206">許多人都感謝您的貢獻！</span><span class="sxs-lookup"><span data-stu-id="5ea9b-206">Many thanks for the contribution!</span></span>

<span data-ttu-id="5ea9b-207">`dotnet ef migrations list`命令現在會顯示尚未套用至資料庫的遷移。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-207">The `dotnet ef migrations list` command now shows which migrations have not yet been applied to the database.</span></span> <span data-ttu-id="5ea9b-208">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-208">For example:</span></span>

```console
ajcvickers@avickers420u:~/AllTogetherNow/Daily$ dotnet ef migrations list
Build started...
Build succeeded.
20200910201647_One
20200910201708_Two
20200910202050_Three (Pending)
ajcvickers@avickers420u:~/AllTogetherNow/Daily$
```

<span data-ttu-id="5ea9b-209">此外，現在有一個命令可 `Get-Migration` 讓封裝管理員主控台具有相同的功能。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-209">In addition, there is now a `Get-Migration` command for the Package Manager Console with the same functionality.</span></span>

### <a name="modelbuilder-api-for-value-comparers"></a><span data-ttu-id="5ea9b-210">值比較子的 ModelBuilder API</span><span class="sxs-lookup"><span data-stu-id="5ea9b-210">ModelBuilder API for value comparers</span></span>

<span data-ttu-id="5ea9b-211">自訂可變類型的 EF Core 屬性 [需要值比較子](xref:core/modeling/value-comparers) ，才能正確偵測屬性變更。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-211">EF Core properties for custom mutable types [require a value comparer](xref:core/modeling/value-comparers) for property changes to be detected correctly.</span></span> <span data-ttu-id="5ea9b-212">您現在可以在設定類型的值轉換時，指定此值。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-212">This can now be specified as part of configuring the value conversion for the type.</span></span> <span data-ttu-id="5ea9b-213">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-213">For example:</span></span>

```csharp
modelBuilder
    .Entity<EntityType>()
    .Property(e => e.MyProperty)
    .HasConversion(
        v => JsonSerializer.Serialize(v, null),
        v => JsonSerializer.Deserialize<List<int>>(v, null),
        new ValueComparer<List<int>>(
            (c1, c2) => c1.SequenceEqual(c2),
            c => c.Aggregate(0, (a, v) => HashCode.Combine(a, v.GetHashCode())),
            c => c.ToList()));
```

### <a name="entityentry-trygetvalue-methods"></a><span data-ttu-id="5ea9b-214">Entityentry.state TryGetValue 方法</span><span class="sxs-lookup"><span data-stu-id="5ea9b-214">EntityEntry TryGetValue methods</span></span>

<span data-ttu-id="5ea9b-215">這項功能是由「社區」所貢獻 [@m4ss1m0g](https://github.com/m4ss1m0g) 。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-215">This feature was contributed from the community by [@m4ss1m0g](https://github.com/m4ss1m0g).</span></span> <span data-ttu-id="5ea9b-216">許多人都感謝您的貢獻！</span><span class="sxs-lookup"><span data-stu-id="5ea9b-216">Many thanks for the contribution!</span></span>

<span data-ttu-id="5ea9b-217">`TryGetValue`方法已加入至 `EntityEntry.CurrentValues` 和 `EntityEntry.OriginalValues` 。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-217">A `TryGetValue` method has been added to `EntityEntry.CurrentValues` and `EntityEntry.OriginalValues`.</span></span> <span data-ttu-id="5ea9b-218">這可讓要求屬性的值，而不需要先檢查屬性是否在 EF 模型中對應。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-218">This allows the value of a property to be requested without first checking if the property is mapped in the EF model.</span></span> <span data-ttu-id="5ea9b-219">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-219">For example:</span></span>

```csharp
if (entry.CurrentValues.TryGetValue(propertyName, out var value))
{
    Console.WriteLine(value);
}
```

### <a name="default-max-batch-size-for-sql-server"></a><span data-ttu-id="5ea9b-220">SQL Server 的預設批次大小上限</span><span class="sxs-lookup"><span data-stu-id="5ea9b-220">Default max batch size for SQL Server</span></span>

<span data-ttu-id="5ea9b-221">從 EF Core 5.0 開始，SQL Server 的 SaveChanges 的預設批次大小上限是42。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-221">Starting with EF Core 5.0, the default maximum batch size for SaveChanges on SQL Server is now 42.</span></span> <span data-ttu-id="5ea9b-222">同樣地，這也是最重要的生命、Universe 和一切問題的答案。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-222">As is well known, this is also the answer to the Ultimate Question of Life, the Universe, and Everything.</span></span> <span data-ttu-id="5ea9b-223">不過，這可能是一項巧合，因為其值是透過 [分析批次處理效能](https://github.com/dotnet/efcore/issues/9270)來取得的。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-223">However, this is probably a coincidence, since the value was obtained through [analysis of batching performance](https://github.com/dotnet/efcore/issues/9270).</span></span> <span data-ttu-id="5ea9b-224">我們不相信我們發現了一個終極問題，不過看起來似乎有點合理，那就是為了瞭解 SQL Server 的運作方式。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-224">We do not believe that we have discovered a form of the Ultimate Question, although it does seem somewhat plausible that the Earth was created to understand why SQL Server works the way it does.</span></span>

### <a name="default-environment-to-development"></a><span data-ttu-id="5ea9b-225">開發的預設環境</span><span class="sxs-lookup"><span data-stu-id="5ea9b-225">Default environment to Development</span></span>

<span data-ttu-id="5ea9b-226">EF Core 命令列工具現在會自動將 `ASPNETCORE_ENVIRONMENT` _和_ `DOTNET_ENVIRONMENT` 環境變數設定為「開發」。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-226">The EF Core command line tools now automatically configure the `ASPNETCORE_ENVIRONMENT` _and_ `DOTNET_ENVIRONMENT` environment variables to "Development".</span></span> <span data-ttu-id="5ea9b-227">這項功能可讓您在開發期間使用泛型主機搭配 ASP.NET Core 的體驗。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-227">This brings the experience when using the generic host in line with the experience for ASP.NET Core during development.</span></span> <span data-ttu-id="5ea9b-228">請參閱 [#19903](https://github.com/dotnet/efcore/issues/19903)。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-228">See [#19903](https://github.com/dotnet/efcore/issues/19903).</span></span>

### <a name="better-migrations-column-ordering"></a><span data-ttu-id="5ea9b-229">更好的遷移資料行排序</span><span class="sxs-lookup"><span data-stu-id="5ea9b-229">Better migrations column ordering</span></span>

<span data-ttu-id="5ea9b-230">未對應之基類的資料行現在會在對應實體類型的其他資料行之後排序。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-230">The columns for unmapped base classes are now ordered after other columns for mapped entity types.</span></span> <span data-ttu-id="5ea9b-231">請注意，這只會影響新建立的資料表。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-231">Note this only impacts newly created tables.</span></span> <span data-ttu-id="5ea9b-232">現有資料表的資料行順序會維持不變。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-232">The column order for existing tables remains unchanged.</span></span> <span data-ttu-id="5ea9b-233">請參閱 [#11314](https://github.com/dotnet/efcore/issues/11314)。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-233">See [#11314](https://github.com/dotnet/efcore/issues/11314).</span></span>

### <a name="query-improvements"></a><span data-ttu-id="5ea9b-234">查詢改進</span><span class="sxs-lookup"><span data-stu-id="5ea9b-234">Query improvements</span></span>

<span data-ttu-id="5ea9b-235">EF Core 5.0 RC1 包含一些額外的查詢翻譯改進：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-235">EF Core 5.0 RC1 contains some additional query translation improvements:</span></span>

* <span data-ttu-id="5ea9b-236">`is`Cosmos 上的翻譯--請參閱[#16391](https://github.com/dotnet/efcore/issues/16391)</span><span class="sxs-lookup"><span data-stu-id="5ea9b-236">Translation of `is` on Cosmos--see [#16391](https://github.com/dotnet/efcore/issues/16391)</span></span>
* <span data-ttu-id="5ea9b-237">使用者對應函式現在可以加上批註以控制 null 傳播--請參閱 [#19609](https://github.com/dotnet/efcore/issues/19609)</span><span class="sxs-lookup"><span data-stu-id="5ea9b-237">User-mapped functions can now be annotated to control null propagation--see [#19609](https://github.com/dotnet/efcore/issues/19609)</span></span>
* <span data-ttu-id="5ea9b-238">支援使用條件式匯總進行 GroupBy 的翻譯--請參閱 [#11711](https://github.com/dotnet/efcore/issues/11711)</span><span class="sxs-lookup"><span data-stu-id="5ea9b-238">Support for translation of GroupBy with conditional aggregates--see [#11711](https://github.com/dotnet/efcore/issues/11711)</span></span>
* <span data-ttu-id="5ea9b-239">在匯總之前將相異運算子轉換成群組元素--請參閱 [#17376](https://github.com/dotnet/efcore/issues/17376)</span><span class="sxs-lookup"><span data-stu-id="5ea9b-239">Translation of Distinct operator over group element before aggregate--see [#17376](https://github.com/dotnet/efcore/issues/17376)</span></span>

### <a name="model-building-for-fields"></a><span data-ttu-id="5ea9b-240">欄位的模型建立</span><span class="sxs-lookup"><span data-stu-id="5ea9b-240">Model building for fields</span></span>

<span data-ttu-id="5ea9b-241">最後，在 RC1 中，EF Core 現在允許在 ModelBuilder 中使用 lambda 方法來尋找欄位以及屬性。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-241">Finally for RC1, EF Core now allows use of the lambda methods in the ModelBuilder for fields as well as properties.</span></span> <span data-ttu-id="5ea9b-242">例如，如果您基於某些原因厭惡至屬性，並決定使用公用欄位，則這些欄位現在可以使用 lambda 產生器來對應：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-242">For example, if you are averse to properties for some reason and decide to use public fields, then these fields can now be mapped using the lambda builders:</span></span>

```csharp
public class Post
{
    public int Id;
    public string Name;
    public string Category;
    public int BlogId;
    public Blog Blog;
}

public class Blog
{
    public int Id;
    public string Name;
    public ICollection<Post> Posts;
}
```

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>(b =>
    {
        b.Property(e => e.Id);
        b.Property(e => e.Name);
    });

    modelBuilder.Entity<Post>(b =>
    {
        b.Property(e => e.Id);
        b.Property(e => e.Name);
        b.Property(e => e.Category);
        b.Property(e => e.BlogId);
        b.HasOne(e => e.Blog).WithMany(e => e.Posts);
    });
}
```

<span data-ttu-id="5ea9b-243">雖然這是可行的，但我們當然不建議您這麼做。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-243">While this is now possible, we are certainly not recommending that you do this.</span></span> <span data-ttu-id="5ea9b-244">此外，請注意，這不會將任何額外的欄位對應功能新增至 EF Core，只允許使用 lambda 方法，而不是一律需要字串方法。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-244">Also, note that this does not add any additional field mapping capabilities to EF Core, it only allows the lambda methods to be used instead of always requiring the string methods.</span></span> <span data-ttu-id="5ea9b-245">這種情況很少用，因為欄位很少是公用的。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-245">This is seldom useful since fields are rarely public.</span></span>

## <a name="preview-8"></a><span data-ttu-id="5ea9b-246">Preview 8</span><span class="sxs-lookup"><span data-stu-id="5ea9b-246">Preview 8</span></span>

### <a name="table-per-type-tpt-mapping"></a><span data-ttu-id="5ea9b-247">每一類型的 (TPT) 對應的資料表</span><span class="sxs-lookup"><span data-stu-id="5ea9b-247">Table-per-type (TPT) mapping</span></span>

<span data-ttu-id="5ea9b-248">根據預設，EF Core 會將 .NET 類型的繼承階層對應到單一資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-248">By default, EF Core maps an inheritance hierarchy of .NET types to a single database table.</span></span> <span data-ttu-id="5ea9b-249">這稱為每一階層的資料表 (TPH) 對應。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-249">This is known as table-per-hierarchy (TPH) mapping.</span></span> <span data-ttu-id="5ea9b-250">EF Core 5.0 也可將繼承階層架構中的每個 .NET 類型對應至不同的資料庫資料表;稱為每一類型的資料表 (TPT) 對應。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-250">EF Core 5.0 also allows mapping each .NET type in an inheritance hierarchy to a different database table; known as table-per-type (TPT) mapping.</span></span>

<span data-ttu-id="5ea9b-251">例如，請考慮使用對應階層的這個模型：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-251">For example, consider this model with a mapped hierarchy:</span></span>

```csharp
public class Animal
{
    public int Id { get; set; }
    public string Species { get; set; }
}

public class Pet : Animal
{
    public string Name { get; set; }
}

public class Cat : Pet
{
    public string EducationLevel { get; set; }
}

public class Dog : Pet
{
    public string FavoriteToy { get; set; }
}
```

<span data-ttu-id="5ea9b-252">根據預設，EF Core 會將此對應到單一資料表：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-252">By default, EF Core will map this to a single table:</span></span>

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Species] nvarchar(max) NULL,
    [Discriminator] nvarchar(max) NOT NULL,
    [Name] nvarchar(max) NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);
```

<span data-ttu-id="5ea9b-253">不過，將每個實體類型對應至不同的資料表，會改為每個類型產生一個資料表：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-253">However, mapping each entity type to a different table will instead result in one table per type:</span></span>

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Species] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);

CREATE TABLE [Pets] (
    [Id] int NOT NULL,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Pets] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Pets_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION
);

CREATE TABLE [Cats] (
    [Id] int NOT NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    CONSTRAINT [PK_Cats] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Cats_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
    CONSTRAINT [FK_Cats_Pets_Id] FOREIGN KEY ([Id]) REFERENCES [Pets] ([Id]) ON DELETE NO ACTION
);

CREATE TABLE [Dogs] (
    [Id] int NOT NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Dogs] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Dogs_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
    CONSTRAINT [FK_Dogs_Pets_Id] FOREIGN KEY ([Id]) REFERENCES [Pets] ([Id]) ON DELETE NO ACTION
);
```

<span data-ttu-id="5ea9b-254">請注意，在將 preview 8 的程式碼分支出來之後，會新增上述的外鍵條件約束的建立。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-254">Note that creation of the foreign key constraints shown above were added after branching the code for preview 8.</span></span>

<span data-ttu-id="5ea9b-255">您可以使用對應屬性，將實體類型對應至不同的資料表：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-255">Entity types can be mapped to different tables using mapping attributes:</span></span>

```csharp
[Table("Animals")]
public class Animal
{
    public int Id { get; set; }
    public string Species { get; set; }
}

[Table("Pets")]
public class Pet : Animal
{
    public string Name { get; set; }
}

[Table("Cats")]
public class Cat : Pet
{
    public string EdcuationLevel { get; set; }
}

[Table("Dogs")]
public class Dog : Pet
{
    public string FavoriteToy { get; set; }
}
```

<span data-ttu-id="5ea9b-256">或使用設定 `ModelBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-256">Or using `ModelBuilder` configuration:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Animal>().ToTable("Animals");
    modelBuilder.Entity<Pet>().ToTable("Pets");
    modelBuilder.Entity<Cat>().ToTable("Cats");
    modelBuilder.Entity<Dog>().ToTable("Dogs");
}
```

<span data-ttu-id="5ea9b-257">檔是由問題 [#1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-257">Documentation is tracked by issue [#1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979).</span></span>

### <a name="migrations-rebuild-sqlite-tables"></a><span data-ttu-id="5ea9b-258">遷移：重建 SQLite 資料表</span><span class="sxs-lookup"><span data-stu-id="5ea9b-258">Migrations: Rebuild SQLite tables</span></span>

<span data-ttu-id="5ea9b-259">相較于其他資料庫，SQLite 的架構操作功能相對受限。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-259">Compared to other database, SQLite is relatively limited in its schema manipulation capabilities.</span></span> <span data-ttu-id="5ea9b-260">例如，從現有的資料表卸載資料行時，必須卸載並重新建立整個資料表。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-260">For example, dropping a column from an existing table requires that the entire table be dropped and re-created.</span></span> <span data-ttu-id="5ea9b-261">EF Core 5.0 遷移現在支援自動重建資料表，以進行需要的架構變更。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-261">EF Core 5.0 Migrations now supports automatic rebuilding the table for schema changes that require it.</span></span>

<span data-ttu-id="5ea9b-262">例如，假設我們有一個 `Unicorns` 針對實體類型建立的資料表 `Unicorn` ：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-262">For example, imagine we have a `Unicorns` table created for a `Unicorn` entity type:</span></span>

```csharp
public class Unicorn
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
}
```

```sql
CREATE TABLE "Unicorns" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_Unicorns" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL,
    "Age" INTEGER NOT NULL
);
```

<span data-ttu-id="5ea9b-263">接下來，我們將瞭解如何將獨角獸的年齡視為非常不一樣，因此讓我們移除該屬性、新增新的遷移，並更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-263">We then learn that storing the age of a unicorn is considered very rude, so let's remove that property, add a new migration, and update the database.</span></span> <span data-ttu-id="5ea9b-264">使用 EF Core 3.1 時，此更新將會失敗，因為無法卸載資料行。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-264">This update will fail when using EF Core 3.1 because the column cannot be dropped.</span></span> <span data-ttu-id="5ea9b-265">在 EF Core 5.0 中，遷移會改為重建資料表：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-265">In EF Core 5.0, Migrations will instead rebuild the table:</span></span>

```sql
CREATE TABLE "ef_temp_Unicorns" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_Unicorns" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL
);

INSERT INTO "ef_temp_Unicorns" ("Id", "Name")
SELECT "Id", "Name"
FROM Unicorns;

PRAGMA foreign_keys = 0;

DROP TABLE "Unicorns";

ALTER TABLE "ef_temp_Unicorns" RENAME TO "Unicorns";

PRAGMA foreign_keys = 1;
```

<span data-ttu-id="5ea9b-266">請注意：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-266">Notice that:</span></span>

* <span data-ttu-id="5ea9b-267">使用新資料表所需的架構來建立臨時表</span><span class="sxs-lookup"><span data-stu-id="5ea9b-267">A temporary table is created with the desired schema for the new table</span></span>
* <span data-ttu-id="5ea9b-268">將資料從目前資料表複製到臨時表</span><span class="sxs-lookup"><span data-stu-id="5ea9b-268">Data is copied from the current table into the temporary table</span></span>
* <span data-ttu-id="5ea9b-269">外鍵強制切換關閉</span><span class="sxs-lookup"><span data-stu-id="5ea9b-269">Foreign key enforcement is switched off</span></span>
* <span data-ttu-id="5ea9b-270">刪除目前的資料表</span><span class="sxs-lookup"><span data-stu-id="5ea9b-270">The current table is dropped</span></span>
* <span data-ttu-id="5ea9b-271">臨時表重新命名為新的資料表</span><span class="sxs-lookup"><span data-stu-id="5ea9b-271">The temporary table is renamed to be the new table</span></span>

<span data-ttu-id="5ea9b-272">檔是由問題 [#2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-272">Documentation is tracked by issue [#2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583).</span></span>

### <a name="table-valued-functions"></a><span data-ttu-id="5ea9b-273">資料表值函式</span><span class="sxs-lookup"><span data-stu-id="5ea9b-273">Table-valued functions</span></span>

<span data-ttu-id="5ea9b-274">這項功能是由「社區」所貢獻 [@pmiddleton](https://github.com/pmiddleton) 。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-274">This feature was contributed from the community by [@pmiddleton](https://github.com/pmiddleton).</span></span> <span data-ttu-id="5ea9b-275">許多人都感謝您的貢獻！</span><span class="sxs-lookup"><span data-stu-id="5ea9b-275">Many thanks for the contribution!</span></span>

<span data-ttu-id="5ea9b-276">EF Core 5.0 包含將 .NET 方法對應至資料表值函式 (Tvf) 的第一級支援。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-276">EF Core 5.0 includes first-class support for mapping .NET methods to table-valued functions (TVFs).</span></span> <span data-ttu-id="5ea9b-277">然後，這些函式可以在 LINQ 查詢中使用，而函式結果的其他組合也會轉譯為 SQL。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-277">These functions can then be used in LINQ queries where additional composition on the results of the function will also be translated to SQL.</span></span>

<span data-ttu-id="5ea9b-278">例如，請考慮在 SQL Server 資料庫中定義的 TVF：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-278">For example, consider this TVF defined in a SQL Server database:</span></span>

```sql
CREATE FUNCTION GetReports(@employeeId int)
RETURNS @reports TABLE
(
    Name nvarchar(50) NOT NULL,
    IsDeveloper bit NOT NULL
)
AS
BEGIN
    WITH cteEmployees AS
    (
        SELECT Id, Name, ManagerId, IsDeveloper
        FROM Employees
        WHERE Id = @employeeId
        UNION ALL
        SELECT e.Id, e.Name, e.ManagerId, e.IsDeveloper
        FROM Employees e
        INNER JOIN cteEmployees cteEmp ON cteEmp.Id = e.ManagerId
    )
    INSERT INTO @reports
    SELECT Name, IsDeveloper
    FROM cteEmployees
    WHERE Id != @employeeId

    RETURN
END
```

<span data-ttu-id="5ea9b-279">EF Core 模型需要兩個實體類型才能使用此 TVF：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-279">The EF Core model requires two entity types to use this TVF:</span></span>

* <span data-ttu-id="5ea9b-280">以 `Employee` 正常方式對應至 Employees 資料表的類型</span><span class="sxs-lookup"><span data-stu-id="5ea9b-280">An `Employee` type that maps to the Employees table in the normal way</span></span>
* <span data-ttu-id="5ea9b-281">`Report`符合 TVF 所傳回之圖形的類型</span><span class="sxs-lookup"><span data-stu-id="5ea9b-281">A `Report` type that matches the shape returned by the TVF</span></span>

```csharp
public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }

    public int? ManagerId { get; set; }
    public virtual Employee Manager { get; set; }
}
```

```csharp
public class Report
{
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }
}
```

<span data-ttu-id="5ea9b-282">這些類型必須包含在 EF Core 模型中：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-282">These types must be included in the EF Core model:</span></span>

```csharp
modelBuilder.Entity<Employee>();
modelBuilder.Entity(typeof(Report)).HasNoKey();
```

<span data-ttu-id="5ea9b-283">請注意，沒有 `Report` 主鍵，因此必須設定為。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-283">Notice that `Report` has no primary key and so must be configured as such.</span></span>

<span data-ttu-id="5ea9b-284">最後，.NET 方法必須對應到資料庫中的 TVF。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-284">Finally, a .NET method must be mapped to the TVF in the database.</span></span> <span data-ttu-id="5ea9b-285">您可以使用新的方法，在 DbCoNtext 上定義這個方法 `FromExpression` ：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-285">This method can be defined on the DbContext using the new `FromExpression` method:</span></span>

```csharp
public IQueryable<Report> GetReports(int managerId)
    => FromExpression(() => GetReports(managerId));
```

<span data-ttu-id="5ea9b-286">這個方法會使用符合上述定義之 TVF 的參數和傳回型別。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-286">This method uses a parameter and return type that match the TVF defined above.</span></span> <span data-ttu-id="5ea9b-287">然後，方法會新增至 OnModelCreating 中的 EF Core 模型：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-287">The method is then added to the EF Core model in OnModelCreating:</span></span>

```csharp
modelBuilder.HasDbFunction(() => GetReports(default));
```

<span data-ttu-id="5ea9b-288">使用 lambda 的 (是將傳送至 EF Core 的簡單方法 `MethodInfo` 。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-288">(Using a lambda here is an easy way to pass the `MethodInfo` to EF Core.</span></span> <span data-ttu-id="5ea9b-289">傳遞給方法的引數會被忽略。 ) </span><span class="sxs-lookup"><span data-stu-id="5ea9b-289">The arguments passed to the method are ignored.)</span></span>

<span data-ttu-id="5ea9b-290">我們現在可以撰寫 `GetReports` 在結果上呼叫和撰寫的查詢。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-290">We can now write queries that call `GetReports` and compose over the results.</span></span> <span data-ttu-id="5ea9b-291">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-291">For example:</span></span>

```csharp
from e in context.Employees
from rc in context.GetReports(e.Id)
where rc.IsDeveloper == true
select new
{
  ManagerName = e.Name,
  EmployeeName = rc.Name,
})
```

<span data-ttu-id="5ea9b-292">在 SQL Server 上，這會轉譯為：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-292">On SQL Server, this translates to:</span></span>

```sql
SELECT [e].[Name] AS [ManagerName], [g].[Name] AS [EmployeeName]
FROM [Employees] AS [e]
CROSS APPLY [dbo].[GetReports]([e].[Id]) AS [g]
WHERE [g].[IsDeveloper] = CAST(1 AS bit)
```

<span data-ttu-id="5ea9b-293">請注意，SQL 的根目錄是在 `Employees` 資料表中呼叫 `GetReports` ，然後在函式的結果上加入額外的 WHERE 子句。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-293">Notice that the SQL is rooted in the `Employees` table, calls `GetReports`, and then adds an additional WHERE clause on the results of the function.</span></span>

### <a name="flexible-queryupdate-mapping"></a><span data-ttu-id="5ea9b-294">彈性查詢/更新對應</span><span class="sxs-lookup"><span data-stu-id="5ea9b-294">Flexible query/update mapping</span></span>

<span data-ttu-id="5ea9b-295">EF Core 5.0 可將相同的實體類型對應至不同的資料庫物件。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-295">EF Core 5.0 allows mapping the same entity type to different database objects.</span></span> <span data-ttu-id="5ea9b-296">這些物件可以是資料表、views 或函數。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-296">These objects may be tables, views, or functions.</span></span>

<span data-ttu-id="5ea9b-297">例如，實體類型可以對應至資料庫和資料庫資料表：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-297">For example, an entity type can be mapped to both a database view and a database table:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

<span data-ttu-id="5ea9b-298">根據預設，EF Core 會從 view 查詢並將更新傳送至資料表。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-298">By default, EF Core will then query from the view and send updates to the table.</span></span> <span data-ttu-id="5ea9b-299">例如，執行下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-299">For example, executing the following code:</span></span>

```csharp
var blog = context.Set<Blog>().Single(e => e.Name == "One Unicorn");

blog.Name = "1unicorn2";

context.SaveChanges();
```

<span data-ttu-id="5ea9b-300">針對視圖產生查詢，然後對資料表進行更新：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-300">Results in a query against the view, and then an update to the table:</span></span>

```sql
SELECT TOP(2) [b].[Id], [b].[Name], [b].[Url]
FROM [BlogsView] AS [b]
WHERE [b].[Name] = N'One Unicorn'

SET NOCOUNT ON;
UPDATE [Blogs] SET [Name] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="context-wide-split-query-configuration"></a><span data-ttu-id="5ea9b-301">整個環境的分割查詢設定</span><span class="sxs-lookup"><span data-stu-id="5ea9b-301">Context-wide split-query configuration</span></span>

<span data-ttu-id="5ea9b-302">分割查詢 (看到下面) 現在可以設定為 DbCoNtext 所執行之任何查詢的預設值。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-302">Split queries (see below) can now be configured as the default for any query executed by the DbContext.</span></span> <span data-ttu-id="5ea9b-303">這項設定僅適用于關聯式提供者，因此必須指定為設定的一部分 `UseProvider` 。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-303">This configuration is only available for relational providers, and so must be specified as part of the `UseProvider` configuration.</span></span> <span data-ttu-id="5ea9b-304">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-304">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(
            Your.SqlServerConnectionString,
            b => b.UseQuerySplittingBehavior(QuerySplittingBehavior.SplitQuery));
```

<span data-ttu-id="5ea9b-305">檔是由問題 [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-305">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="physicaladdress-mapping"></a><span data-ttu-id="5ea9b-306">PhysicalAddress 對應</span><span class="sxs-lookup"><span data-stu-id="5ea9b-306">PhysicalAddress mapping</span></span>

<span data-ttu-id="5ea9b-307">這項功能是由「社區」所貢獻 [@ralmsdeveloper](https://github.com/ralmsdeveloper) 。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-307">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="5ea9b-308">許多人都感謝您的貢獻！</span><span class="sxs-lookup"><span data-stu-id="5ea9b-308">Many thanks for the contribution!</span></span>

<span data-ttu-id="5ea9b-309">標準的 .NET [PhysicalAddress 類別](/dotnet/api/system.net.networkinformation.physicaladdress) 現在會自動對應到尚未具有原生支援之資料庫的字串資料行。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-309">The standard .NET [PhysicalAddress class](/dotnet/api/system.net.networkinformation.physicaladdress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="5ea9b-310">如需詳細資訊，請參閱下面的範例 `IPAddress` 。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-310">For more information, see the examples for `IPAddress` below.</span></span>

## <a name="preview-7"></a><span data-ttu-id="5ea9b-311">Preview 7</span><span class="sxs-lookup"><span data-stu-id="5ea9b-311">Preview 7</span></span>

### <a name="dbcontextfactory"></a><span data-ttu-id="5ea9b-312">DbCoNtextFactory</span><span class="sxs-lookup"><span data-stu-id="5ea9b-312">DbContextFactory</span></span>

<span data-ttu-id="5ea9b-313">EF Core 5.0 引進 `AddDbContextFactory` 並 `AddPooledDbContextFactory` 註冊 factory，以在應用程式的相依性插入 (D.I. ) 容器中建立 DbCoNtext 實例。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-313">EF Core 5.0 introduces `AddDbContextFactory` and `AddPooledDbContextFactory` to register a factory for creating DbContext instances in the application's dependency injection (D.I.) container.</span></span> <span data-ttu-id="5ea9b-314">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-314">For example:</span></span>

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

<span data-ttu-id="5ea9b-315">然後，應用程式服務（例如 ASP.NET Core 控制器）可相依于 `IDbContextFactory<TContext>` 服務的函式中。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-315">Application services such as ASP.NET Core controllers can then depend on `IDbContextFactory<TContext>` in the service constructor.</span></span> <span data-ttu-id="5ea9b-316">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-316">For example:</span></span>

```csharp
public class MyController
{
    private readonly IDbContextFactory<SomeDbContext> _contextFactory;

    public MyController(IDbContextFactory<SomeDbContext> contextFactory)
    {
        _contextFactory = contextFactory;
    }
}
```

<span data-ttu-id="5ea9b-317">然後，您可以視需要建立和使用 DbCoNtext 實例。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-317">DbContext instances can then be created and used as needed.</span></span> <span data-ttu-id="5ea9b-318">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-318">For example:</span></span>

```csharp
public void DoSomeThing()
{
    using (var context = _contextFactory.CreateDbContext())
    {
        // ...
    }
}
```

<span data-ttu-id="5ea9b-319">請注意，以這種方式建立的 DbCoNtext 實例 _不_ 會由應用程式的服務提供者來管理，因此必須由應用程式處置。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-319">Note that the DbContext instances created in this way are _not_ managed by the application's service provider and therefore must be disposed by the application.</span></span> <span data-ttu-id="5ea9b-320">這種分離功能對於 Blazor 應用程式非常有用， `IDbContextFactory` 但建議使用，但在其他情況下也可能很有用。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-320">This decoupling is very useful for Blazor applications, where using `IDbContextFactory` is recommended, but may also be useful in other scenarios.</span></span>

<span data-ttu-id="5ea9b-321">您可以藉由呼叫來將 DbCoNtext 實例集區 `AddPooledDbContextFactory` 。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-321">DbContext instances can be pooled by calling `AddPooledDbContextFactory`.</span></span> <span data-ttu-id="5ea9b-322">此共用的運作方式與相同 `AddDbContextPool` ，而且也具有相同的限制。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-322">This pooling works the same way as for `AddDbContextPool`, and also has the same limitations.</span></span>

<span data-ttu-id="5ea9b-323">檔是由問題 [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-323">Documentation is tracked by issue [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).</span></span>

### <a name="reset-dbcontext-state"></a><span data-ttu-id="5ea9b-324">重設 DbCoNtext 狀態</span><span class="sxs-lookup"><span data-stu-id="5ea9b-324">Reset DbContext state</span></span>

<span data-ttu-id="5ea9b-325">EF Core 5.0 引進了哪些專案會 `ChangeTracker.Clear()` 清除所有已追蹤實體的 DbCoNtext。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-325">EF Core 5.0 introduces `ChangeTracker.Clear()` which clears the DbContext of all tracked entities.</span></span> <span data-ttu-id="5ea9b-326">針對每個工作單位，使用建立新的短期內容實例的最佳作法時，通常不需要這麼做。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-326">This should usually not be needed when using the best practice of creating a new, short-lived context instance for each unit-of-work.</span></span> <span data-ttu-id="5ea9b-327">但是，如果需要重設 DbCoNtext 實例的狀態，則使用新的 `Clear()` 方法會比大量卸離所有實體更具效能和強大。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-327">However, if there is a need to reset the state of a DbContext instance, then using the new `Clear()` method is more performant and robust than mass-detaching all entities.</span></span>

<span data-ttu-id="5ea9b-328">檔是由問題 [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-328">Documentation is tracked by issue [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span></span>

### <a name="new-pattern-for-store-generated-defaults"></a><span data-ttu-id="5ea9b-329">存放區產生之預設值的新模式</span><span class="sxs-lookup"><span data-stu-id="5ea9b-329">New pattern for store-generated defaults</span></span>

<span data-ttu-id="5ea9b-330">EF Core 允許針對可能也有預設值條件約束的資料行設定明確值。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-330">EF Core allows an explicit value to be set for a column that may also have default value constraint.</span></span> <span data-ttu-id="5ea9b-331">EF Core 使用類型屬性類型的 CLR 預設值做為這個的 sentinel;如果值不是 CLR 預設值，則會將其插入，否則會使用資料庫預設值。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-331">EF Core uses the CLR default of type property type as a sentinel for this; if the value is not the CLR default, then it is inserted, otherwise the database default is used.</span></span>

<span data-ttu-id="5ea9b-332">這會為 CLR 預設不是良好 sentinel 的型別（最值得注意的是屬性）建立問題 `bool` 。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-332">This creates problems for types where the CLR default is not a good sentinel--most notably, `bool` properties.</span></span> <span data-ttu-id="5ea9b-333">EF Core 5.0 現在可讓支援欄位可針對像這樣的案例提供可為 null。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-333">EF Core 5.0 now allows the backing field to be nullable for cases like this.</span></span> <span data-ttu-id="5ea9b-334">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-334">For example:</span></span>

```csharp
public class Blog
{
    private bool? _isValid;

    public bool IsValid
    {
        get => _isValid ?? false;
        set => _isValid = value;
    }
}
```

<span data-ttu-id="5ea9b-335">請注意，支援欄位可為 null，但公開的屬性則不是。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-335">Note that the backing field is nullable, but the publicly exposed property is not.</span></span> <span data-ttu-id="5ea9b-336">這可讓 sentinel 值不會 `null` 影響實體類型的公用介面。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-336">This allows the sentinel value to be `null` without impacting the public surface of the entity type.</span></span> <span data-ttu-id="5ea9b-337">在此情況下，如果 `IsValid` 從未設定，則會使用資料庫預設值，因為支援欄位會維持 null。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-337">In this case, if the `IsValid` is never set, then the database default will be used since the backing field remains null.</span></span> <span data-ttu-id="5ea9b-338">如果 `true` `false` 設定了或，則此值會明確地儲存至資料庫。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-338">If either `true` or `false` are set, then this value is saved explicitly to the database.</span></span>

<span data-ttu-id="5ea9b-339">檔是由問題 [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-339">Documentation is tracked by issue [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="5ea9b-340">Cosmos 分割區索引鍵</span><span class="sxs-lookup"><span data-stu-id="5ea9b-340">Cosmos partition keys</span></span>

<span data-ttu-id="5ea9b-341">EF Core 允許 EF 模型中包含 Cosmos 分割區索引鍵。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-341">EF Core allows the Cosmos partition key is included in the EF model.</span></span> <span data-ttu-id="5ea9b-342">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-342">For example:</span></span>

```csharp
modelBuilder.Entity<Customer>().HasPartitionKey(b => b.AlternateKey)
```

<span data-ttu-id="5ea9b-343">從 preview 7 開始，資料分割索引鍵會包含在實體類型的 PK 中，用來改善某些查詢的效能。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-343">Starting with preview 7, the partition key is included in the entity type's PK and is used to improved performance in some queries.</span></span>

<span data-ttu-id="5ea9b-344">檔是由問題 [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-344">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="cosmos-configuration"></a><span data-ttu-id="5ea9b-345">Cosmos 設定</span><span class="sxs-lookup"><span data-stu-id="5ea9b-345">Cosmos configuration</span></span>

<span data-ttu-id="5ea9b-346">EF Core 5.0 可改善 Cosmos 和 Cosmos 連接的設定。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-346">EF Core 5.0 improves configuration of Cosmos and Cosmos connections.</span></span>

<span data-ttu-id="5ea9b-347">先前，EF Core 需要在連接至 Cosmos 資料庫時明確指定端點和金鑰。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-347">Previously, EF Core required the end-point and key to be specified explicitly when connecting to a Cosmos database.</span></span> <span data-ttu-id="5ea9b-348">EF Core 5.0 允許改用連接字串。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-348">EF Core 5.0 allows use of a connection string instead.</span></span> <span data-ttu-id="5ea9b-349">此外，EF Core 5.0 允許明確設定 WebProxy 實例。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-349">In addition, EF Core 5.0 allows the WebProxy instance to be explicitly set.</span></span> <span data-ttu-id="5ea9b-350">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-350">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.WebProxy(myProxyInstance);
            });
```

<span data-ttu-id="5ea9b-351">現在也可以設定許多其他的超時值、限制等等。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-351">Many other timeout values, limits, etc. can now also be configured.</span></span> <span data-ttu-id="5ea9b-352">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-352">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.LimitToEndpoint();
                cosmosOptionsBuilder.RequestTimeout(requestTimeout);
                cosmosOptionsBuilder.OpenTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.IdleTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.GatewayModeMaxConnectionLimit(connectionLimit);
                cosmosOptionsBuilder.MaxTcpConnectionsPerEndpoint(connectionLimit);
                cosmosOptionsBuilder.MaxRequestsPerTcpConnection(requestLimit);
            });
```

<span data-ttu-id="5ea9b-353">最後，預設的連接模式現在 `ConnectionMode.Gateway` 更相容。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-353">Finally, the default connection mode is now `ConnectionMode.Gateway`, which is generally more compatible.</span></span>

<span data-ttu-id="5ea9b-354">檔是由問題 [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-354">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="scaffold-dbcontext-now-singularizes"></a><span data-ttu-id="5ea9b-355">Scaffold-DbContext 現在 singularizes</span><span class="sxs-lookup"><span data-stu-id="5ea9b-355">Scaffold-DbContext now singularizes</span></span>

<span data-ttu-id="5ea9b-356">先前當從現有的資料庫 DbCoNtext 樣板時，EF Core 將會建立符合資料庫中資料表名稱的實體類型名稱。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-356">Previously when scaffolding a DbContext from an existing database, EF Core will create entity type names that match the table names in the database.</span></span> <span data-ttu-id="5ea9b-357">例如，資料表 `People` 並 `Addresses` 產生名為和的實體 `People` 類型 `Addresses` 。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-357">For example, tables `People` and `Addresses` resulted in entity types named `People` and `Addresses`.</span></span>

<span data-ttu-id="5ea9b-358">在先前的版本中，這項行為可透過註冊複數表示服務來設定。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-358">In previous releases, this behavior was configurable through registration of a pluralization service.</span></span> <span data-ttu-id="5ea9b-359">現在在 EF Core 5.0 中，會使用 [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) 套件作為預設複數表示服務。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-359">Now in EF Core 5.0, the [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) package is used as a default pluralization service.</span></span> <span data-ttu-id="5ea9b-360">這表示資料表 `People` 和 `Addresses` 現在會對名為和的實體類型進行反向工程 `Person` `Address` 。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-360">This means tables `People` and `Addresses` will now be reverse engineered to entity types named `Person` and `Address`.</span></span>

### <a name="savepoints"></a><span data-ttu-id="5ea9b-361">點</span><span class="sxs-lookup"><span data-stu-id="5ea9b-361">Savepoints</span></span>

<span data-ttu-id="5ea9b-362">EF Core 現在支援儲存 [點](/sql/t-sql/language-elements/save-transaction-transact-sql#remarks) ，以便更充分掌控執行多個作業的交易。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-362">EF Core now supports [savepoints](/sql/t-sql/language-elements/save-transaction-transact-sql#remarks) for greater control over transactions that execute multiple operations.</span></span>

<span data-ttu-id="5ea9b-363">您可以手動建立、釋放和復原儲存點。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-363">Savepoints can be manually created, released, and rolled back.</span></span> <span data-ttu-id="5ea9b-364">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-364">For example:</span></span>

```csharp
context.Database.CreateSavepoint("MySavePoint");
```

<span data-ttu-id="5ea9b-365">此外，EF Core 現在會在執行失敗時復原到最後一個儲存點 `SaveChanges` 。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-365">In addition, EF Core will now roll back to the last savepoint when executing `SaveChanges` fails.</span></span> <span data-ttu-id="5ea9b-366">這可讓 SaveChanges 重新嘗試，而不需要重新嘗試整個交易。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-366">This allows SaveChanges to be re-tried without re-trying the entire transaction.</span></span>

<span data-ttu-id="5ea9b-367">檔是由問題 [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-367">Documentation is tracked by issue [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span></span>

## <a name="preview-6"></a><span data-ttu-id="5ea9b-368">Preview 6</span><span class="sxs-lookup"><span data-stu-id="5ea9b-368">Preview 6</span></span>

### <a name="split-queries-for-related-collections"></a><span data-ttu-id="5ea9b-369">分割相關集合的查詢</span><span class="sxs-lookup"><span data-stu-id="5ea9b-369">Split queries for related collections</span></span>

<span data-ttu-id="5ea9b-370">從 EF Core 3.0 開始，EF Core 一律會為每個 LINQ 查詢產生單一 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-370">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="5ea9b-371">這樣可確保在使用中交易模式的條件約束內所傳回的資料一致。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-371">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="5ea9b-372">不過，當查詢使用 `Include` 或投射回多個相關的集合時，這可能會變得很慢。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-372">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="5ea9b-373">EF Core 5.0 現在允許單一 LINQ 查詢，包括要分割成多個 SQL 查詢的相關集合。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-373">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="5ea9b-374">這可以大幅提升效能，但如果兩個查詢之間的資料有所變更，則可能導致傳回的結果不一致。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-374">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="5ea9b-375">可序列化或快照集交易可用來降低此問題，並與分割查詢達成一致性，但這可能會帶來其他效能成本和行為差異。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-375">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

#### <a name="split-queries-with-include"></a><span data-ttu-id="5ea9b-376">使用 Include 分割查詢</span><span class="sxs-lookup"><span data-stu-id="5ea9b-376">Split queries with Include</span></span>

<span data-ttu-id="5ea9b-377">例如，假設有一個查詢會使用下列程式提取兩個層級的相關集合 `Include` ：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-377">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```csharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="5ea9b-378">根據預設，EF Core 會在使用 SQLite 提供者時產生下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-378">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name", "t0"."Id", "t0"."ArtistId", "t0"."Title", "t0"."Id0", "t0"."AlbumId", "t0"."Name"
FROM "Artists" AS "a"
LEFT JOIN (
    SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "t"."Id" AS "Id0", "t"."AlbumId", "t"."Name"
    FROM "Album" AS "a0"
    LEFT JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
) AS "t0" ON "a"."Id" = "t0"."ArtistId"
ORDER BY "a"."Id", "t0"."Id", "t0"."Id0"
```

<span data-ttu-id="5ea9b-379">新的 `AsSplitQuery` API 可以用來變更此行為。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-379">The new `AsSplitQuery` API can be used to change this behavior.</span></span> <span data-ttu-id="5ea9b-380">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-380">For example:</span></span>

```csharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="5ea9b-381">AsSplitQuery 適用于所有關系資料庫提供者，而且可以在查詢中的任何位置使用，就像 AsNoTracking 一樣。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-381">AsSplitQuery is available for all relational database providers and can be used anywhere in the query, just like AsNoTracking.</span></span> <span data-ttu-id="5ea9b-382">EF Core 現在會產生下列三個 SQL 查詢：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-382">EF Core will now generate the following three SQL queries:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id", "a0"."Id"

SELECT "t"."Id", "t"."AlbumId", "t"."Name", "a"."Id", "a0"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
INNER JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
ORDER BY "a"."Id", "a0"."Id"
```

<span data-ttu-id="5ea9b-383">支援查詢根目錄上的所有作業。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-383">All operations on the query root are supported.</span></span> <span data-ttu-id="5ea9b-384">這包括 OrderBy/Skip/Take、聯結作業、FirstOrDefault，以及選取作業的類似單一結果。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-384">This includes OrderBy/Skip/Take, Join operations, FirstOrDefault and similar single result selecting operations.</span></span>

<span data-ttu-id="5ea9b-385">請注意，preview 6 中不支援 OrderBy/Skip/Take 的篩選包含，但在每日組建中都有提供，而且將包含在 preview 7 中。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-385">Note that filtered Includes with OrderBy/Skip/Take are not supported in preview 6, but are available in the daily builds and will be included in preview 7.</span></span>

#### <a name="split-queries-with-collection-projections"></a><span data-ttu-id="5ea9b-386">使用集合投影來分割查詢</span><span class="sxs-lookup"><span data-stu-id="5ea9b-386">Split queries with collection projections</span></span>

<span data-ttu-id="5ea9b-387">`AsSplitQuery` 當集合在投影中載入時，也可以使用。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-387">`AsSplitQuery` can also be used when collections are loaded in projections.</span></span> <span data-ttu-id="5ea9b-388">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-388">For example:</span></span>

```csharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

<span data-ttu-id="5ea9b-389">使用 SQLite 提供者時，這個 LINQ 查詢會產生下列兩個 SQL 查詢：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-389">This LINQ query generates the following two SQL queries when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

<span data-ttu-id="5ea9b-390">請注意，只支援集合的具體化。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-390">Note that only materialization of the collection is supported.</span></span> <span data-ttu-id="5ea9b-391">`e.Albums`在上述情況下的任何組合都不會產生分割查詢。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-391">Any composition after `e.Albums` in above case won't result in a split query.</span></span> <span data-ttu-id="5ea9b-392">[#21234](https://github.com/dotnet/efcore/issues/21234)會追蹤此區域的改進。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-392">Improvements in this area are tracked by [#21234](https://github.com/dotnet/efcore/issues/21234).</span></span>

### <a name="indexattribute"></a><span data-ttu-id="5ea9b-393">IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="5ea9b-393">IndexAttribute</span></span>

<span data-ttu-id="5ea9b-394">新的 IndexAttribute 可以放在實體類型上，以指定單一資料行的索引。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-394">The new IndexAttribute can be placed on an entity type to specify an index for a single column.</span></span> <span data-ttu-id="5ea9b-395">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-395">For example:</span></span>

```csharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(128)]
    public string FullName { get; set; }
}
```

<span data-ttu-id="5ea9b-396">針對 SQL Server，遷移將會產生下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-396">For SQL Server, Migrations will then generate the following SQL:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

<span data-ttu-id="5ea9b-397">IndexAttribute 也可用來指定跨越多個資料行的索引。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-397">IndexAttribute can also be used to specify an index spanning multiple columns.</span></span> <span data-ttu-id="5ea9b-398">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-398">For example:</span></span>

```csharp
[Index(nameof(FirstName), nameof(LastName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(64)]
    public string FirstName { get; set; }

    [MaxLength(64)]
    public string LastName { get; set; }
}
```

<span data-ttu-id="5ea9b-399">針對 SQL Server，這會導致：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-399">For SQL Server, this results in:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

<span data-ttu-id="5ea9b-400">檔是由問題 [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-400">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="improved-query-translation-exceptions"></a><span data-ttu-id="5ea9b-401">改進的查詢轉譯例外狀況</span><span class="sxs-lookup"><span data-stu-id="5ea9b-401">Improved query translation exceptions</span></span>

<span data-ttu-id="5ea9b-402">我們會持續改善查詢轉譯失敗時所產生的例外狀況訊息。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-402">We are continuing to improve the exception messages generated when query translation fails.</span></span> <span data-ttu-id="5ea9b-403">例如，此查詢會使用未對應的屬性 `IsSigned` ：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-403">For example, this query uses the unmapped property `IsSigned`:</span></span>

```csharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

<span data-ttu-id="5ea9b-404">EF Core 會擲回下列例外狀況，表示轉譯失敗，因為未 `IsSigned` 對應：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-404">EF Core will throw the following exception indicating that translation failed because `IsSigned` is not mapped:</span></span>

```exception
Unhandled exception. System.InvalidOperationException: The LINQ expression 'DbSet<Artist>()
   .Where(a => a.IsSigned)' could not be translated. Additional information: Translation of member 'IsSigned' on entity type 'Artist' failed. Possibly the specified member is not mapped. Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync(). See <https://go.microsoft.com/fwlink/?linkid=2101038> for more information.
```

<span data-ttu-id="5ea9b-405">同樣地，當您嘗試翻譯具有文化特性相依語義的字串比較時，現在會產生更好的例外狀況訊息。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-405">Similarly, better exception messages are now generated when attempting to translate string comparisons with culture-dependent semantics.</span></span> <span data-ttu-id="5ea9b-406">例如，此查詢嘗試使用 `StringComparison.CurrentCulture` ：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-406">For example, this query attempts to use `StringComparison.CurrentCulture`:</span></span>

```csharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

<span data-ttu-id="5ea9b-407">EF Core 現在會擲回下列例外狀況：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-407">EF Core will now throw the following exception:</span></span>

```exception
Unhandled exception. System.InvalidOperationException: The LINQ expression 'DbSet<Artist>()
     .Where(a => a.Name.Equals(
         value: "The Unicorns",
         comparisonType: CurrentCulture))' could not be translated. Additional information: Translation of 'string.Equals' method which takes 'StringComparison' argument is not supported. See <https://go.microsoft.com/fwlink/?linkid=2129535> for more information. Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync(). See <https://go.microsoft.com/fwlink/?linkid=2101038> for more information.
```

### <a name="specify-transaction-id"></a><span data-ttu-id="5ea9b-408">指定交易識別碼</span><span class="sxs-lookup"><span data-stu-id="5ea9b-408">Specify transaction ID</span></span>

<span data-ttu-id="5ea9b-409">這項功能是由「社區」所貢獻 [@Marusyk](https://github.com/Marusyk) 。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-409">This feature was contributed from the community by [@Marusyk](https://github.com/Marusyk).</span></span> <span data-ttu-id="5ea9b-410">許多人都感謝您的貢獻！</span><span class="sxs-lookup"><span data-stu-id="5ea9b-410">Many thanks for the contribution!</span></span>

<span data-ttu-id="5ea9b-411">EF Core 公開交易的交易識別碼，以便跨呼叫進行交易的相互關聯。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-411">EF Core exposes a transaction ID for correlation of transactions across calls.</span></span> <span data-ttu-id="5ea9b-412">此識別碼通常會在交易開始時由 EF Core 設定。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-412">This ID typically set by EF Core when a transaction is started.</span></span> <span data-ttu-id="5ea9b-413">如果應用程式改為啟動交易，則這項功能可讓應用程式明確地設定交易識別碼，使其在使用它的任何地方都能正確地相互關聯。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-413">If the application starts the transaction instead, then this feature allows the application to explicitly set the transaction ID so it is correlated correctly everywhere it is used.</span></span> <span data-ttu-id="5ea9b-414">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-414">For example:</span></span>

```csharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a><span data-ttu-id="5ea9b-415">IPAddress 對應</span><span class="sxs-lookup"><span data-stu-id="5ea9b-415">IPAddress mapping</span></span>

<span data-ttu-id="5ea9b-416">這項功能是由「社區」所貢獻 [@ralmsdeveloper](https://github.com/ralmsdeveloper) 。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-416">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="5ea9b-417">許多人都感謝您的貢獻！</span><span class="sxs-lookup"><span data-stu-id="5ea9b-417">Many thanks for the contribution!</span></span>

<span data-ttu-id="5ea9b-418">標準 .NET [IPAddress 類別](/dotnet/api/system.net.ipaddress) 現在會自動對應至尚未具有原生支援之資料庫的字串資料行。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-418">The standard .NET [IPAddress class](/dotnet/api/system.net.ipaddress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="5ea9b-419">例如，請考慮對應此實體類型：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-419">For example, consider mapping this entity type:</span></span>

```csharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

<span data-ttu-id="5ea9b-420">在 SQL Server 上，這會導致建立下表：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-420">On SQL Server, this will result in Migrations creating the following table:</span></span>

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
```

<span data-ttu-id="5ea9b-421">然後可以用一般方式新增實體：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-421">Entities can then be added in the normal way:</span></span>

```csharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
```

<span data-ttu-id="5ea9b-422">而產生的 SQL 會插入正規化的 IPv4 或 IPv6 位址：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-422">And the resulting SQL will insert the normalized IPv4 or IPv6 address:</span></span>

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a><span data-ttu-id="5ea9b-423">當樣板時排除 OnConfiguring</span><span class="sxs-lookup"><span data-stu-id="5ea9b-423">Exclude OnConfiguring when scaffolding</span></span>

<span data-ttu-id="5ea9b-424">從現有的資料庫 scaffold DbCoNtext 時，EF Core 預設會使用連接字串建立 OnConfiguring 多載，讓內容可以立即使用。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-424">When a DbContext is scaffolded from an existing database, EF Core by default creates an OnConfiguring overload with a connection string so that the context is immediately usable.</span></span> <span data-ttu-id="5ea9b-425">但是，如果您已經有部分類別具有 OnConfiguring，或如果您以其他方式設定內容，這就不會很有用。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-425">However, this is not useful if you already have a partial class with OnConfiguring, or if you are configuring the context some other way.</span></span>

<span data-ttu-id="5ea9b-426">若要解決此情況，現在可以指示可忽略 OnConfiguring 產生的基本程式命令。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-426">To address this, the scaffolding commands can now be instructed to omit generation of OnConfiguring.</span></span> <span data-ttu-id="5ea9b-427">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-427">For example:</span></span>

```console
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

<span data-ttu-id="5ea9b-428">或者，在封裝管理員主控台中：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-428">Or in the Package Manager Console:</span></span>

```console
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring
```

<span data-ttu-id="5ea9b-429">請注意，我們建議使用 [已命名的連接字串和安全的儲存體，例如使用者密碼](xref:core/managing-schemas/scaffolding#configuration-and-user-secrets)。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-429">Note that we recommend using [a named connection string and secure storage like User Secrets](xref:core/managing-schemas/scaffolding#configuration-and-user-secrets).</span></span>

### <a name="translations-for-firstordefault-on-strings"></a><span data-ttu-id="5ea9b-430">字串的 FirstOrDefault 翻譯</span><span class="sxs-lookup"><span data-stu-id="5ea9b-430">Translations for FirstOrDefault on strings</span></span>

<span data-ttu-id="5ea9b-431">這項功能是由「社區」所貢獻 [@dvoreckyaa](https://github.com/dvoreckyaa) 。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-431">This feature was contributed from the community by [@dvoreckyaa](https://github.com/dvoreckyaa).</span></span> <span data-ttu-id="5ea9b-432">許多人都感謝您的貢獻！</span><span class="sxs-lookup"><span data-stu-id="5ea9b-432">Many thanks for the contribution!</span></span>

<span data-ttu-id="5ea9b-433">字串中字元的 FirstOrDefault 和類似運算子現在已轉譯。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-433">FirstOrDefault and similar operators for characters in strings are now translated.</span></span> <span data-ttu-id="5ea9b-434">例如，這個 LINQ 查詢：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-434">For example, this LINQ query:</span></span>

```csharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

<span data-ttu-id="5ea9b-435">使用 SQL Server 時，將會轉譯為下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-435">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a><span data-ttu-id="5ea9b-436">簡化案例區塊</span><span class="sxs-lookup"><span data-stu-id="5ea9b-436">Simplify case blocks</span></span>

<span data-ttu-id="5ea9b-437">EF Core 現在會以案例區塊產生更好的查詢。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-437">EF Core now generates better queries with CASE blocks.</span></span> <span data-ttu-id="5ea9b-438">例如，這個 LINQ 查詢：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-438">For example, this LINQ query:</span></span>

```csharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

<span data-ttu-id="5ea9b-439">Was SQL Server 正式轉譯為：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-439">Was on SQL Server formally translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN (CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END = 0) AND CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

<span data-ttu-id="5ea9b-440">但現在已轉譯為：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-440">But is now translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

## <a name="preview-5"></a><span data-ttu-id="5ea9b-441">Preview 5</span><span class="sxs-lookup"><span data-stu-id="5ea9b-441">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="5ea9b-442">資料庫定序</span><span class="sxs-lookup"><span data-stu-id="5ea9b-442">Database collations</span></span>

<span data-ttu-id="5ea9b-443">您現在可以在 EF 模型中指定資料庫的預設定序。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-443">The default collation for a database can now be specified in the EF model.</span></span> <span data-ttu-id="5ea9b-444">這會流經產生的遷移，以在建立資料庫時設定定序。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-444">This will flow through to generated migrations to set the collation when the database is created.</span></span> <span data-ttu-id="5ea9b-445">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-445">For example:</span></span>

```csharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="5ea9b-446">然後，遷移會產生下列各項，以在 SQL Server 上建立資料庫：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-446">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="5ea9b-447">您也可以指定要用於特定資料庫資料行的定序。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-447">The collation to use for specific database columns can also be specified.</span></span> <span data-ttu-id="5ea9b-448">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-448">For example:</span></span>

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.Name)
    .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="5ea9b-449">若未使用遷移，則在 DbCoNtext 架構時，定序現在會從資料庫進行反向工程。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-449">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="5ea9b-450">最後， `EF.Functions.Collate()` 可以使用不同的定序來進行特定查詢。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-450">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span> <span data-ttu-id="5ea9b-451">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-451">For example:</span></span>

```csharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="5ea9b-452">這會產生 SQL Server 的下列查詢：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-452">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="5ea9b-453">請注意，特定定序應謹慎使用，因為它們可能會對資料庫效能造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-453">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="5ea9b-454">檔是由問題 [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-454">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="5ea9b-455">IDesignTimeDbCoNtextFactory 的流程引數</span><span class="sxs-lookup"><span data-stu-id="5ea9b-455">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="5ea9b-456">引數現在會從命令列流向 `CreateDbContext` [IDesignTimeDbCoNtextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1)方法。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-456">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1).</span></span> <span data-ttu-id="5ea9b-457">例如，若要表示這是開發組建，您 `dev` 可以在命令列上傳遞自訂引數 (例如) ：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-457">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can be passed on the command line:</span></span>

```console
dotnet ef migrations add two --verbose --dev
```

<span data-ttu-id="5ea9b-458">然後，這個引數會流入處理站，可用來控制內容的建立與初始化方式。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-458">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span> <span data-ttu-id="5ea9b-459">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-459">For example:</span></span>

```csharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args)
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="5ea9b-460">檔是由問題 [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-460">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="5ea9b-461">沒有識別解析的追蹤查詢</span><span class="sxs-lookup"><span data-stu-id="5ea9b-461">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="5ea9b-462">現在可以設定無追蹤查詢來執行識別解析。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-462">No-tracking queries can now be configured to perform identity resolution.</span></span> <span data-ttu-id="5ea9b-463">例如，下列查詢會為每個貼文建立新的 Blog 實例，即使每個 Blog 都有相同的主要金鑰。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-463">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span>

```csharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="5ea9b-464">不過，代價通常會稍微慢一點，且一律使用更多記憶體，因此可以變更此查詢，以確保只會建立單一的 Blog 實例：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-464">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```csharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="5ea9b-465">請注意，這只適用于無追蹤查詢，因為所有追蹤查詢都已展示此行為。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-465">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="5ea9b-466">此外，在遵循 API 審核之後，將會 `PerformIdentityResolution` 變更語法。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-466">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span> <span data-ttu-id="5ea9b-467">請參閱 [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073)。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-467">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="5ea9b-468">檔是由問題 [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-468">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="5ea9b-469">儲存 (保存) 計算資料行</span><span class="sxs-lookup"><span data-stu-id="5ea9b-469">Stored (persisted) computed columns</span></span>

<span data-ttu-id="5ea9b-470">大部分的資料庫允許在計算後儲存計算資料行值。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-470">Most databases allow computed column values to be stored after computation.</span></span> <span data-ttu-id="5ea9b-471">雖然這會佔用磁碟空間，但計算資料行只會在更新時計算一次，而不是每次抓取其值時。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-471">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span> <span data-ttu-id="5ea9b-472">這也可讓某些資料庫的資料行編制索引。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-472">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="5ea9b-473">EF Core 5.0 允許將計算資料行設定為已儲存。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-473">EF Core 5.0 allows computed columns to be configured as stored.</span></span> <span data-ttu-id="5ea9b-474">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-474">For example:</span></span>

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="5ea9b-475">SQLite 計算資料行</span><span class="sxs-lookup"><span data-stu-id="5ea9b-475">SQLite computed columns</span></span>

<span data-ttu-id="5ea9b-476">EF Core 現在支援 SQLite 資料庫中的計算資料行。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-476">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="5ea9b-477">Preview 4</span><span class="sxs-lookup"><span data-stu-id="5ea9b-477">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="5ea9b-478">在模型中設定資料庫 precision/scale</span><span class="sxs-lookup"><span data-stu-id="5ea9b-478">Configure database precision/scale in model</span></span>

<span data-ttu-id="5ea9b-479">您現在可以使用模型產生器來指定屬性的有效位數和小數位數。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-479">Precision and scale for a property can now be specified using the model builder.</span></span> <span data-ttu-id="5ea9b-480">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-480">For example:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="5ea9b-481">精確度和小數位數仍然可以透過完整的資料庫類型來設定，例如 "decimal (16，4) "。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-481">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span>

<span data-ttu-id="5ea9b-482">檔是由問題 [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-482">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="5ea9b-483">指定 SQL Server 索引填滿因數</span><span class="sxs-lookup"><span data-stu-id="5ea9b-483">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="5ea9b-484">您現在可以在 SQL Server 上建立索引時指定填滿因數。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-484">The fill factor can now be specified when creating an index on SQL Server.</span></span> <span data-ttu-id="5ea9b-485">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-485">For example:</span></span>

```csharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="5ea9b-486">Preview 3</span><span class="sxs-lookup"><span data-stu-id="5ea9b-486">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="5ea9b-487">篩選的包含</span><span class="sxs-lookup"><span data-stu-id="5ea9b-487">Filtered Include</span></span>

<span data-ttu-id="5ea9b-488">Include 方法現在支援篩選包含的實體。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-488">The Include method now supports filtering of the entities included.</span></span> <span data-ttu-id="5ea9b-489">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-489">For example:</span></span>

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="5ea9b-490">此查詢將會連同每個相關聯的貼文傳回 blog，但只有在貼文標題包含「乳酪」時。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-490">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="5ea9b-491">Skip 和 Take 也可以用來減少包含的實體數目。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-491">Skip and Take can also be used to reduce the number of included entities.</span></span> <span data-ttu-id="5ea9b-492">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-492">For example:</span></span>

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```

<span data-ttu-id="5ea9b-493">此查詢會傳回包含每個 blog 最多五篇文章的 blog。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-493">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="5ea9b-494">如需完整詳細資料，請參閱 [包含檔](xref:core/querying/related-data#filtered-include) 。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-494">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="5ea9b-495">適用于導覽屬性的新 ModelBuilder API</span><span class="sxs-lookup"><span data-stu-id="5ea9b-495">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="5ea9b-496">導覽屬性主要是在 [定義關聯](xref:core/modeling/relationships)性時進行設定。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-496">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span> <span data-ttu-id="5ea9b-497">不過，在 `Navigation` 導覽屬性需要額外設定的情況下，可以使用新的方法。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-497">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span> <span data-ttu-id="5ea9b-498">例如，當依據慣例找不到欄位時，設定導覽的支援欄位：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-498">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```csharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="5ea9b-499">請注意， `Navigation` API 不會取代關聯性設定。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-499">Note that the `Navigation` API does not replace relationship configuration.</span></span> <span data-ttu-id="5ea9b-500">相反地，它允許在已探索或已定義的關聯性中，進行其他的導覽屬性設定。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-500">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="5ea9b-501">請參閱設定 [導覽屬性檔](xref:core/modeling/relationships#configuring-navigation-properties)。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-501">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="5ea9b-502">命名空間和連接字串的新命令列參數</span><span class="sxs-lookup"><span data-stu-id="5ea9b-502">New command-line parameters for namespaces and connection strings</span></span>

<span data-ttu-id="5ea9b-503">現在可讓您在命令列上指定命名空間。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-503">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span> <span data-ttu-id="5ea9b-504">例如，若要對資料庫進行反向工程，將內容和模型類別放在不同的命名空間中：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-504">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span>

```dotnetcli
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="5ea9b-505">如需完整的詳細資料，請參閱「 [遷移](xref:core/managing-schemas/migrations/index#namespaces) 和 [反向工程](xref:core/managing-schemas/scaffolding#directories-and-namespaces) 」檔。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-505">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="5ea9b-506">此外，現在可以將連接字串傳遞至 `database-update` 命令：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-506">Also, a connection string can now be passed to the `database-update` command:</span></span>

```dotnetcli
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="5ea9b-507">如需完整的詳細資訊，請參閱 [工具檔](xref:core/cli/dotnet#dotnet-ef-database-update) 。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-507">See the [Tools documentation](xref:core/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="5ea9b-508">對應的參數也已加入至 VS 封裝管理員主控台中使用的 PowerShell 命令。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-508">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="5ea9b-509">EnableDetailedErrors 已傳回</span><span class="sxs-lookup"><span data-stu-id="5ea9b-509">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="5ea9b-510">基於效能考慮，從資料庫讀取值時，EF 不會執行額外的 null 檢查。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-510">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span> <span data-ttu-id="5ea9b-511">這可能會導致在遇到未預期的 null 時，難以造成根本原因的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-511">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="5ea9b-512">使用 `EnableDetailedErrors` 會將額外的 null 檢查新增至查詢，如此一來，若要降低效能，您可以更輕鬆地追蹤至根本原因。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-512">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>

<span data-ttu-id="5ea9b-513">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-513">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="5ea9b-514">檔是由問題 [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-514">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="5ea9b-515">Cosmos 分割區索引鍵</span><span class="sxs-lookup"><span data-stu-id="5ea9b-515">Cosmos partition keys</span></span>

<span data-ttu-id="5ea9b-516">您現在可以在查詢中指定要用於指定之查詢的資料分割索引鍵。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-516">The partition key to use for a given query can now be specified in the query.</span></span> <span data-ttu-id="5ea9b-517">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-517">For example:</span></span>

```csharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="5ea9b-518">檔是由問題 [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-518">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="5ea9b-519">支援 SQL Server DATALENGTH 函數</span><span class="sxs-lookup"><span data-stu-id="5ea9b-519">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="5ea9b-520">這可以使用新的方法來存取 `EF.Functions.DataLength` 。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-520">This can be accessed using the new `EF.Functions.DataLength` method.</span></span> <span data-ttu-id="5ea9b-521">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-521">For example:</span></span>

```csharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
```

## <a name="preview-2"></a><span data-ttu-id="5ea9b-522">Preview 2</span><span class="sxs-lookup"><span data-stu-id="5ea9b-522">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="5ea9b-523">使用 c # 屬性來指定屬性支援欄位</span><span class="sxs-lookup"><span data-stu-id="5ea9b-523">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="5ea9b-524">C # 屬性現在可用來指定屬性的支援欄位。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-524">A C# attribute can now be used to specify the backing field for a property.</span></span> <span data-ttu-id="5ea9b-525">即使無法自動找到支援欄位，這個屬性仍可讓 EF Core 繼續寫入和讀取支援欄位。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-525">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span> <span data-ttu-id="5ea9b-526">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-526">For example:</span></span>

```csharp
public class Blog
{
    private string _mainTitle;

    public int Id { get; set; }

    [BackingField(nameof(_mainTitle))]
    public string Title
    {
        get => _mainTitle;
        set => _mainTitle = value;
    }
}
```

<span data-ttu-id="5ea9b-527">檔是由問題 [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-527">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="5ea9b-528">完成鑒別子對應</span><span class="sxs-lookup"><span data-stu-id="5ea9b-528">Complete discriminator mapping</span></span>

<span data-ttu-id="5ea9b-529">EF Core 使用鑒別子資料行來進行 [繼承階層的 TPH 對應](xref:core/modeling/inheritance)。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-529">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](xref:core/modeling/inheritance).</span></span> <span data-ttu-id="5ea9b-530">只要 EF Core 知道鑒別子的所有可能值，就可能會有一些效能增強功能。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-530">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span> <span data-ttu-id="5ea9b-531">EF Core 5.0 現在會實行這些增強功能。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-531">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="5ea9b-532">例如，舊版的 EF Core 一律會為傳回階層中所有類型的查詢產生此 SQL：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-532">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="5ea9b-533">當設定完整的鑒別子對應時，EF Core 5.0 現在會產生下列內容：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-533">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="5ea9b-534">這會是從 preview 3 開始的預設行為。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-534">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="5ea9b-535">Microsoft 的效能改進</span><span class="sxs-lookup"><span data-stu-id="5ea9b-535">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="5ea9b-536">我們對 SQLIte 進行了兩項效能改進：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-536">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="5ea9b-537">使用 SqliteBlob 和資料流程，以 GetBytes、GetChars 和 GetTextReader 來取得二進位和字串資料，現在更有效率。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-537">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="5ea9b-538">>sqliteconnection 的初始化現在是延遲的。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-538">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="5ea9b-539">這些改良功能位於 ADO.NET 的 Microsoft. Sqlite 提供者中，因此也可改善 EF Core 之外的效能。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-539">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="5ea9b-540">Preview 1</span><span class="sxs-lookup"><span data-stu-id="5ea9b-540">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="5ea9b-541">簡單記錄</span><span class="sxs-lookup"><span data-stu-id="5ea9b-541">Simple logging</span></span>

<span data-ttu-id="5ea9b-542">這項功能 `Database.Log` 在 EF6 中新增了類似的功能。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-542">This feature adds functionality similar to `Database.Log` in EF6.</span></span> <span data-ttu-id="5ea9b-543">也就是說，它會提供簡單的方法來取得 EF Core 的記錄，而不需要設定任何種類的外部記錄架構。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-543">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="5ea9b-544">[2019 年12月5日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)包含初稿檔。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-544">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="5ea9b-545">其他檔是由問題 [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-545">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="5ea9b-546">取得所產生 SQL 的簡單方式</span><span class="sxs-lookup"><span data-stu-id="5ea9b-546">Simple way to get generated SQL</span></span>

<span data-ttu-id="5ea9b-547">EF Core 5.0 引進了 `ToQueryString` 擴充方法，此方法會傳回 EF Core 將在執行 LINQ 查詢時產生的 SQL。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-547">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="5ea9b-548">[2020 年1月9日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)包含初稿檔。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-548">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="5ea9b-549">其他檔是由問題 [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-549">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="5ea9b-550">使用 c # 屬性工作表示實體沒有索引鍵</span><span class="sxs-lookup"><span data-stu-id="5ea9b-550">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="5ea9b-551">實體類型現在可以設定為沒有使用新的索引鍵 `KeylessAttribute` 。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-551">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span> <span data-ttu-id="5ea9b-552">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-552">For example:</span></span>

```csharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="5ea9b-553">檔是由問題 [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-553">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="5ea9b-554">可以在初始化的 DbCoNtext 上變更連接或連接字串</span><span class="sxs-lookup"><span data-stu-id="5ea9b-554">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="5ea9b-555">現在，您可以更輕鬆地建立沒有任何連接或連接字串的 DbCoNtext 實例。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-555">It is now easier to create a DbContext instance without any connection or connection string.</span></span> <span data-ttu-id="5ea9b-556">此外，連接或連接字串現在可以在內容實例上進行變化。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-556">Also, the connection or connection string can now be mutated on the context instance.</span></span> <span data-ttu-id="5ea9b-557">這項功能可讓相同的內容實例動態連接到不同的資料庫。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-557">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="5ea9b-558">檔是由問題 [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-558">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="5ea9b-559">變更追蹤 proxy</span><span class="sxs-lookup"><span data-stu-id="5ea9b-559">Change-tracking proxies</span></span>

<span data-ttu-id="5ea9b-560">EF Core 現在可以產生自動執行 [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) 和 [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged)的執行時間 proxy。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-560">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) and [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged).</span></span> <span data-ttu-id="5ea9b-561">然後，這些會將實體屬性的值變更直接回報給 EF Core，以避免需要掃描變更。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-561">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span> <span data-ttu-id="5ea9b-562">不過，proxy 有自己的一組限制，因此不適合所有人。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-562">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="5ea9b-563">檔是由問題 [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-563">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="5ea9b-564">增強的調試視圖</span><span class="sxs-lookup"><span data-stu-id="5ea9b-564">Enhanced debug views</span></span>

<span data-ttu-id="5ea9b-565">偵錯工具是一種簡單的方法，可讓您在偵測到問題時，查看 EF Core 的內部。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-565">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span> <span data-ttu-id="5ea9b-566">模型的偵錯工具是在一段時間前執行。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-566">A debug view for the Model was implemented some time ago.</span></span> <span data-ttu-id="5ea9b-567">針對 EF Core 5.0，我們讓模型視圖更容易讀取，並在狀態管理員中為追蹤的實體新增了新的偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-567">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="5ea9b-568">[2019 年12月12日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)包含初稿檔。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-568">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="5ea9b-569">其他檔是由問題 [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-569">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="5ea9b-570">改進資料庫 null 語義的處理</span><span class="sxs-lookup"><span data-stu-id="5ea9b-570">Improved handling of database null semantics</span></span>

<span data-ttu-id="5ea9b-571">關係資料庫通常會將 Null 視為未知值，因此不等於任何其他 Null。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-571">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span> <span data-ttu-id="5ea9b-572">雖然 c # 會將 null 視為已定義的值，但會比較是否等於任何其他 null。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-572">While C# treats null as a defined value, which compares equal to any other null.</span></span> <span data-ttu-id="5ea9b-573">EF Core 預設會轉譯查詢，使其使用 c # null 語義。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-573">EF Core by default translates queries so that they use C# null semantics.</span></span> <span data-ttu-id="5ea9b-574">EF Core 5.0 可大幅提升這些翻譯的效率。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-574">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="5ea9b-575">檔是由問題 [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-575">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="5ea9b-576">索引子屬性</span><span class="sxs-lookup"><span data-stu-id="5ea9b-576">Indexer properties</span></span>

<span data-ttu-id="5ea9b-577">EF Core 5.0 支援 c # 索引子屬性的對應。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-577">EF Core 5.0 supports mapping of C# indexer properties.</span></span> <span data-ttu-id="5ea9b-578">這些屬性可讓實體當做屬性包，其中的資料行會對應至包中的命名屬性。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-578">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="5ea9b-579">檔是由問題 [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-579">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="5ea9b-580">列舉對應的檢查條件約束產生</span><span class="sxs-lookup"><span data-stu-id="5ea9b-580">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="5ea9b-581">EF Core 5.0 遷移現在可以產生列舉屬性對應的檢查條件約束。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-581">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span> <span data-ttu-id="5ea9b-582">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-582">For example:</span></span>

```sql
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="5ea9b-583">檔是由問題 [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-583">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="5ea9b-584">IsRelational</span><span class="sxs-lookup"><span data-stu-id="5ea9b-584">IsRelational</span></span>

<span data-ttu-id="5ea9b-585">`IsRelational`除了現有的、和之外，還加入了新的方法 `IsSqlServer` `IsSqlite` `IsInMemory` 。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-585">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span> <span data-ttu-id="5ea9b-586">這個方法可以用來測試 DbCoNtext 是否使用任何關係資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-586">This method can be used to test if the DbContext is using any relational database provider.</span></span> <span data-ttu-id="5ea9b-587">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-587">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="5ea9b-588">檔是由問題 [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-588">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="5ea9b-589">使用 Etag Cosmos 開放式平行存取</span><span class="sxs-lookup"><span data-stu-id="5ea9b-589">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="5ea9b-590">Azure Cosmos DB 資料庫提供者現在支援使用 Etag 的開放式平行存取。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-590">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span> <span data-ttu-id="5ea9b-591">使用 OnModelCreating 中的模型產生器來設定 ETag：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-591">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```csharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="5ea9b-592">然後 SaveChanges 會擲回 `DbUpdateConcurrencyException` 並行衝突，以進行 [處理](xref:core/saving/concurrency) 以執行重試等。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-592">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](xref:core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="5ea9b-593">檔是由問題 [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-593">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="5ea9b-594">更多日期時間結構的查詢翻譯</span><span class="sxs-lookup"><span data-stu-id="5ea9b-594">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="5ea9b-595">現在已翻譯包含新日期時間結構的查詢。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-595">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="5ea9b-596">此外，現在已對應下列 SQL Server 函式：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-596">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="5ea9b-597">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="5ea9b-597">DateDiffWeek</span></span>
* <span data-ttu-id="5ea9b-598">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="5ea9b-598">DateFromParts</span></span>

<span data-ttu-id="5ea9b-599">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-599">For example:</span></span>

```csharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="5ea9b-600">檔是由問題 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-600">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="5ea9b-601">更多位元組陣列結構的查詢翻譯</span><span class="sxs-lookup"><span data-stu-id="5ea9b-601">Query translations for more byte array constructs</span></span>

<span data-ttu-id="5ea9b-602">使用 Contains、Length、Enumerable.sequenceequal 等的查詢現在會轉譯為 SQL。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-602">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="5ea9b-603">[2019 年12月5日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)包含初稿檔。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-603">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="5ea9b-604">其他檔是由問題 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-604">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="5ea9b-605">反向的查詢轉譯</span><span class="sxs-lookup"><span data-stu-id="5ea9b-605">Query translation for Reverse</span></span>

<span data-ttu-id="5ea9b-606">`Reverse`現在會轉譯使用的查詢。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-606">Queries using `Reverse` are now translated.</span></span> <span data-ttu-id="5ea9b-607">例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-607">For example:</span></span>

```csharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="5ea9b-608">檔是由問題 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-608">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="5ea9b-609">位運算子的查詢轉譯</span><span class="sxs-lookup"><span data-stu-id="5ea9b-609">Query translation for bitwise operators</span></span>

<span data-ttu-id="5ea9b-610">使用位運算子的查詢現在會在更多情況下轉譯，例如：</span><span class="sxs-lookup"><span data-stu-id="5ea9b-610">Queries using bitwise operators are now translated in more cases For example:</span></span>

```csharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="5ea9b-611">檔是由問題 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-611">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="5ea9b-612">Cosmos 上字串的查詢轉譯</span><span class="sxs-lookup"><span data-stu-id="5ea9b-612">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="5ea9b-613">使用字串方法的查詢現在會在使用 Azure Cosmos DB 提供者時轉譯。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-613">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="5ea9b-614">檔是由問題 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea9b-614">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
