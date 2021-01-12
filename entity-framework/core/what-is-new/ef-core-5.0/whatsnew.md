---
title: EF Core 5.0 的新功能
description: EF Core 5.0 的新功能總覽
author: ajcvickers
ms.date: 09/10/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: 64b72ba2a6f752b9d71ea9b64ab08f4cf92ef03d
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129274"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="8fff4-103">EF Core 5.0 的新功能</span><span class="sxs-lookup"><span data-stu-id="8fff4-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="8fff4-104">下列清單包含 EF Core 5.0 中的主要新功能。</span><span class="sxs-lookup"><span data-stu-id="8fff4-104">The following list includes the major new features in EF Core 5.0.</span></span> <span data-ttu-id="8fff4-105">如需發行中問題的完整清單，請參閱我們的 [問題追蹤](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A5.0.0)程式。</span><span class="sxs-lookup"><span data-stu-id="8fff4-105">For the full list of issues in the release, see our [issue tracker](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A5.0.0).</span></span>

<span data-ttu-id="8fff4-106">作為主要版本，EF Core 5.0 也包含幾項 [重大變更](xref:core/what-is-new/ef-core-5.0/breaking-changes)，也就是 API 改進或可能對現有應用程式造成負面影響的行為變更。</span><span class="sxs-lookup"><span data-stu-id="8fff4-106">As a major release, EF Core 5.0 also contains several [breaking changes](xref:core/what-is-new/ef-core-5.0/breaking-changes), which are API improvements or behavioral changes that may have negative impact on existing applications.</span></span>

## <a name="many-to-many"></a><span data-ttu-id="8fff4-107">多對多</span><span class="sxs-lookup"><span data-stu-id="8fff4-107">Many-to-many</span></span>

<span data-ttu-id="8fff4-108">EF Core 5.0 支援多對多關聯性，而不需要明確地對應聯結資料表。</span><span class="sxs-lookup"><span data-stu-id="8fff4-108">EF Core 5.0 supports many-to-many relationships without explicitly mapping the join table.</span></span>

<span data-ttu-id="8fff4-109">例如，請考慮下列實體類型：</span><span class="sxs-lookup"><span data-stu-id="8fff4-109">For example, consider these entity types:</span></span>

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

<span data-ttu-id="8fff4-110">EF Core 5.0 依慣例將此辨識為多對多關聯性，並 `PostTag` 在資料庫中自動建立聯結資料表。</span><span class="sxs-lookup"><span data-stu-id="8fff4-110">EF Core 5.0 recognizes this as a many-to-many relationship by convention, and automatically creates a `PostTag` join table in the database.</span></span> <span data-ttu-id="8fff4-111">您可以查詢和更新資料，而不需要明確參考聯結資料表，以大幅簡化程式碼。</span><span class="sxs-lookup"><span data-stu-id="8fff4-111">Data can be queried and updated without explicitly referencing the join table, considerably simplifying code.</span></span> <span data-ttu-id="8fff4-112">您仍然可以視需要明確地自訂和查詢聯結資料表。</span><span class="sxs-lookup"><span data-stu-id="8fff4-112">The join table can still be customized and queried explicitly if needed.</span></span>

<span data-ttu-id="8fff4-113">如需詳細資訊，請 [參閱多對多的完整檔](xref:core/modeling/relationships#many-to-many)。</span><span class="sxs-lookup"><span data-stu-id="8fff4-113">For further information, [see the full documentation on many-to-many](xref:core/modeling/relationships#many-to-many).</span></span>

## <a name="split-queries"></a><span data-ttu-id="8fff4-114">分割查詢</span><span class="sxs-lookup"><span data-stu-id="8fff4-114">Split queries</span></span>

<span data-ttu-id="8fff4-115">從 EF Core 3.0 開始，EF Core 一律會為每個 LINQ 查詢產生單一 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="8fff4-115">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="8fff4-116">這樣可確保在使用中交易模式的條件約束內所傳回的資料一致。</span><span class="sxs-lookup"><span data-stu-id="8fff4-116">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="8fff4-117">不過，當查詢使用 `Include` 或投射回多個相關的集合時，這可能會變得很慢。</span><span class="sxs-lookup"><span data-stu-id="8fff4-117">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="8fff4-118">EF Core 5.0 現在允許單一 LINQ 查詢，包括要分割成多個 SQL 查詢的相關集合。</span><span class="sxs-lookup"><span data-stu-id="8fff4-118">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="8fff4-119">這可以大幅提升效能，但如果兩個查詢之間的資料有所變更，則可能導致傳回的結果不一致。</span><span class="sxs-lookup"><span data-stu-id="8fff4-119">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="8fff4-120">可序列化或快照集交易可用來降低此問題，並與分割查詢達成一致性，但這可能會帶來其他效能成本和行為差異。</span><span class="sxs-lookup"><span data-stu-id="8fff4-120">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

<span data-ttu-id="8fff4-121">例如，假設有一個查詢會使用下列程式提取兩個層級的相關集合 `Include` ：</span><span class="sxs-lookup"><span data-stu-id="8fff4-121">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```csharp
var artists = context.Artists
    .Include(e => e.Albums)
    .ToList();
```

<span data-ttu-id="8fff4-122">根據預設，EF Core 會在使用 SQLite 提供者時產生下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="8fff4-122">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

```sql
SELECT a."Id", a."Name", a0."Id", a0."ArtistId", a0."Title"
FROM "Artists" AS a
LEFT JOIN "Album" AS a0 ON a."Id" = a0."ArtistId"
ORDER BY a."Id", a0."Id"
```

<span data-ttu-id="8fff4-123">使用分割查詢時，會改為產生下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="8fff4-123">With split queries, the following SQL is generated instead:</span></span>

```sql
SELECT a."Id", a."Name"
FROM "Artists" AS a
ORDER BY a."Id"

SELECT a0."Id", a0."ArtistId", a0."Title", a."Id"
FROM "Artists" AS a
INNER JOIN "Album" AS a0 ON a."Id" = a0."ArtistId"
ORDER BY a."Id"
```

<span data-ttu-id="8fff4-124">您可以藉由將新的運算子放 `AsSplitQuery` 在 LINQ 查詢中的任何位置，或在模型的全域位置來啟用分割查詢 `OnConfiguring` 。</span><span class="sxs-lookup"><span data-stu-id="8fff4-124">Split queries can be enabled by placing the new `AsSplitQuery` operator anywhere in your LINQ query, or globally in your model's `OnConfiguring`.</span></span> <span data-ttu-id="8fff4-125">如需詳細資訊，請 [參閱分割查詢的完整檔](xref:core/querying/single-split-queries)集。</span><span class="sxs-lookup"><span data-stu-id="8fff4-125">For further information, [see the full documentation on split queries](xref:core/querying/single-split-queries).</span></span>

## <a name="simple-logging-and-improved-diagnostics"></a><span data-ttu-id="8fff4-126">簡單的記錄和改善的診斷</span><span class="sxs-lookup"><span data-stu-id="8fff4-126">Simple logging and improved diagnostics</span></span>

<span data-ttu-id="8fff4-127">EF Core 5.0 引進了一種簡單的方式，可透過新的方法設定記錄 `LogTo` 。</span><span class="sxs-lookup"><span data-stu-id="8fff4-127">EF Core 5.0 introduces a simple way to set up logging via the new `LogTo` method.</span></span> <span data-ttu-id="8fff4-128">下列程式會將記錄訊息寫入主控台，包括 EF Core 產生的所有 SQL：</span><span class="sxs-lookup"><span data-stu-id="8fff4-128">The following will cause logging messages to be written to the console, including all SQL generated by EF Core:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder.LogTo(Console.WriteLine);
```

<span data-ttu-id="8fff4-129">此外，現在可以對 `ToQueryString` 任何 LINQ 查詢進行呼叫，以抓取查詢會執行的 SQL：</span><span class="sxs-lookup"><span data-stu-id="8fff4-129">In addition, it is now possible to call `ToQueryString` on any LINQ query, retrieving the SQL that the query would execute:</span></span>

```csharp
Console.WriteLine(
    ctx.Artists
    .Where(a => a.Name == "Pink Floyd")
    .ToQueryString());
```

<span data-ttu-id="8fff4-130">最後，各種 EF Core 類型都是以增強型屬性來調整，以 `DebugView` 提供內部的詳細觀點。</span><span class="sxs-lookup"><span data-stu-id="8fff4-130">Finally, various EF Core types have been fitted with an enhanced `DebugView` property which provides a detailed view into the internals.</span></span> <span data-ttu-id="8fff4-131">例如，您 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType> 可以參考以查看在指定時間內所追蹤的實體。</span><span class="sxs-lookup"><span data-stu-id="8fff4-131">For example, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType> can be consulted to see exactly which entities are being tracked in a given moment.</span></span>

<span data-ttu-id="8fff4-132">如需詳細資訊，請 [參閱記錄和攔截的相關檔](xref:core/logging-events-diagnostics/index)。</span><span class="sxs-lookup"><span data-stu-id="8fff4-132">For further information, [see the documentation on logging and interception](xref:core/logging-events-diagnostics/index).</span></span>

## <a name="filtered-include"></a><span data-ttu-id="8fff4-133">篩選的包含</span><span class="sxs-lookup"><span data-stu-id="8fff4-133">Filtered include</span></span>

<span data-ttu-id="8fff4-134">`Include`方法現在支援篩選包含的實體：</span><span class="sxs-lookup"><span data-stu-id="8fff4-134">The `Include` method now supports filtering of the entities included:</span></span>

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="8fff4-135">此查詢將會連同每個相關聯的貼文傳回 blog，但只有在貼文標題包含「乳酪」時。</span><span class="sxs-lookup"><span data-stu-id="8fff4-135">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="8fff4-136">如需詳細資訊，請 [參閱分割查詢的完整檔](xref:core/querying/related-data/eager#filtered-include)集。</span><span class="sxs-lookup"><span data-stu-id="8fff4-136">For further information, [see the full documentation on split queries](xref:core/querying/related-data/eager#filtered-include).</span></span>

## <a name="table-per-type-tpt-mapping"></a><span data-ttu-id="8fff4-137">每一類型的 (TPT) 對應的資料表</span><span class="sxs-lookup"><span data-stu-id="8fff4-137">Table-per-type (TPT) mapping</span></span>

<span data-ttu-id="8fff4-138">根據預設，EF Core 會將 .NET 類型的繼承階層對應到單一資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="8fff4-138">By default, EF Core maps an inheritance hierarchy of .NET types to a single database table.</span></span> <span data-ttu-id="8fff4-139">這稱為每一階層的資料表 (TPH) 對應。</span><span class="sxs-lookup"><span data-stu-id="8fff4-139">This is known as table-per-hierarchy (TPH) mapping.</span></span> <span data-ttu-id="8fff4-140">EF Core 5.0 也可將繼承階層架構中的每個 .NET 類型對應至不同的資料庫資料表;稱為每一類型的資料表 (TPT) 對應。</span><span class="sxs-lookup"><span data-stu-id="8fff4-140">EF Core 5.0 also allows mapping each .NET type in an inheritance hierarchy to a different database table; known as table-per-type (TPT) mapping.</span></span>

<span data-ttu-id="8fff4-141">例如，請考慮使用對應階層的這個模型：</span><span class="sxs-lookup"><span data-stu-id="8fff4-141">For example, consider this model with a mapped hierarchy:</span></span>

```csharp
public class Animal
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Cat : Animal
{
    public string EducationLevel { get; set; }
}

public class Dog : Animal
{
    public string FavoriteToy { get; set; }
}
```

<span data-ttu-id="8fff4-142">使用 TPT 時，會針對階層中的每個類型建立資料庫資料表：</span><span class="sxs-lookup"><span data-stu-id="8fff4-142">With TPT, a database table is created for each type in the hierarchy:</span></span>

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);

CREATE TABLE [Cats] (
    [Id] int NOT NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    CONSTRAINT [PK_Cats] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Cats_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
);

CREATE TABLE [Dogs] (
    [Id] int NOT NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Dogs] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Dogs_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
);
```

<span data-ttu-id="8fff4-143">如需詳細資訊，請 [參閱 TPT 的完整檔](xref:core/modeling/inheritance)。</span><span class="sxs-lookup"><span data-stu-id="8fff4-143">For further information, [see the full documentation on TPT](xref:core/modeling/inheritance).</span></span>

## <a name="flexible-entity-mapping"></a><span data-ttu-id="8fff4-144">彈性實體對應</span><span class="sxs-lookup"><span data-stu-id="8fff4-144">Flexible entity mapping</span></span>

<span data-ttu-id="8fff4-145">實體類型通常會對應至資料表或視圖，讓 EF Core 在查詢該型別時，將會提取資料表或視圖的內容。</span><span class="sxs-lookup"><span data-stu-id="8fff4-145">Entity types are commonly mapped to tables or views such that EF Core will pull back the contents of the table or view when querying for that type.</span></span> <span data-ttu-id="8fff4-146">EF Core 5.0 會新增額外的對應選項，其中實體可以對應至 SQL 查詢 (稱為「定義查詢」 ) ，或對應至資料表值函數 (TVF) ：</span><span class="sxs-lookup"><span data-stu-id="8fff4-146">EF Core 5.0 adds additional mapping options, where an entity can be mapped to a SQL query (called a "defining query"), or to a table-valued function (TVF):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Post>().ToSqlQuery(
        @"SELECT Id, Name, Category, BlogId FROM posts
          UNION ALL
          SELECT Id, Name, ""Legacy"", BlogId from legacy_posts");

    modelBuilder.Entity<Blog>().ToFunction("BlogsReturningFunction");
}
```

<span data-ttu-id="8fff4-147">資料表值函式也可以對應至 .NET 方法，而不是對應至 DbSet，以允許傳遞參數;您可以使用來設定對應 <xref:Microsoft.EntityFrameworkCore.RelationalModelBuilderExtensions.HasDbFunction%2A> 。</span><span class="sxs-lookup"><span data-stu-id="8fff4-147">Table-valued functions can also be mapped to a .NET method rather than to a DbSet, allowing parameters to be passed; the mapping can be set up with <xref:Microsoft.EntityFrameworkCore.RelationalModelBuilderExtensions.HasDbFunction%2A>.</span></span>

<span data-ttu-id="8fff4-148">最後，您現在可以在查詢 (或函數或定義查詢) 時，將實體對應至 view，但是在更新時，會將其對應至資料表：</span><span class="sxs-lookup"><span data-stu-id="8fff4-148">Finally, it is now possible to map an entity to a view when querying (or to a function or defining query), but to a table when updating:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

## <a name="shared-type-entity-types-and-property-bags"></a><span data-ttu-id="8fff4-149">共用類型的實體類型和屬性包</span><span class="sxs-lookup"><span data-stu-id="8fff4-149">Shared-type entity types and property bags</span></span>

<span data-ttu-id="8fff4-150">EF Core 5.0 允許相同的 CLR 類型對應到多個不同的實體類型;這類類型稱為共用類型實體類型。</span><span class="sxs-lookup"><span data-stu-id="8fff4-150">EF Core 5.0 allows the same CLR type to be mapped to multiple different entity types; such types are known as shared-type entity types.</span></span> <span data-ttu-id="8fff4-151">雖然任何 CLR 型別都可以搭配這項功能使用，但 .NET `Dictionary` 提供了一個特別吸引人的使用案例，我們稱之為「屬性包」：</span><span class="sxs-lookup"><span data-stu-id="8fff4-151">While any CLR type can be used with this feature, .NET `Dictionary` offers a particularly compelling use-case which we call "property bags":</span></span>

```csharp
public class ProductsContext : DbContext
{
    public DbSet<Dictionary<string, object>> Products => Set<Dictionary<string, object>>("Product");

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.SharedTypeEntity<Dictionary<string, object>>("Product", b =>
        {
            b.IndexerProperty<int>("Id");
            b.IndexerProperty<string>("Name").IsRequired();
            b.IndexerProperty<decimal>("Price");
        });
    }
}
```

<span data-ttu-id="8fff4-152">然後，您可以查詢和更新這些實體，就像一般實體型別一樣，也可以使用專屬的 CLR 型別。</span><span class="sxs-lookup"><span data-stu-id="8fff4-152">These entities can then be queried and updated just like normal entity types with their own, dedicated CLR type.</span></span> <span data-ttu-id="8fff4-153">您可以在 [屬性包](xref:core/modeling/shadow-properties)的檔中找到詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="8fff4-153">More information can be found in the documentation on [property bags](xref:core/modeling/shadow-properties).</span></span>

## <a name="required-11-dependents"></a><span data-ttu-id="8fff4-154">必要的1:1 相依項</span><span class="sxs-lookup"><span data-stu-id="8fff4-154">Required 1:1 dependents</span></span>

<span data-ttu-id="8fff4-155">在 EF Core 3.1 中，一對一關聯性的相依 end 一律視為選擇性。</span><span class="sxs-lookup"><span data-stu-id="8fff4-155">In EF Core 3.1, the dependent end of a one-to-one relationship was always considered optional.</span></span> <span data-ttu-id="8fff4-156">這在使用擁有的實體時最為明顯，因為所有擁有實體的資料行在資料庫中建立為可為 null，即使它們已在模型中設定為必要。</span><span class="sxs-lookup"><span data-stu-id="8fff4-156">This was most apparent when using owned entities, as all the owned entity's column were created as nullable in the database, even if they were configured as required in the model.</span></span>

<span data-ttu-id="8fff4-157">在 EF Core 5.0 中，可將所擁有實體的導覽設定為必要的相依。</span><span class="sxs-lookup"><span data-stu-id="8fff4-157">In EF Core 5.0, a navigation to an owned entity can be configured as as a required dependent.</span></span> <span data-ttu-id="8fff4-158">例如：</span><span class="sxs-lookup"><span data-stu-id="8fff4-158">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Person>(b =>
    {
        b.OwnsOne(e => e.HomeAddress,
            b =>
            {
                b.Property(e => e.City).IsRequired();
                b.Property(e => e.Postcode).IsRequired();
            });
        b.Navigation(e => e.HomeAddress).IsRequired();
    });
}
```

## <a name="dbcontextfactory"></a><span data-ttu-id="8fff4-159">DbCoNtextFactory</span><span class="sxs-lookup"><span data-stu-id="8fff4-159">DbContextFactory</span></span>

<span data-ttu-id="8fff4-160">EF Core 5.0 引進 `AddDbContextFactory` 並 `AddPooledDbContextFactory` 註冊在應用程式的相依性插入 (D.I. ) 容器中建立 DbCoNtext 實例的 factory; 當應用程式程式碼需要手動建立和處置內容實例時，這會很有用。</span><span class="sxs-lookup"><span data-stu-id="8fff4-160">EF Core 5.0 introduces `AddDbContextFactory` and `AddPooledDbContextFactory` to register a factory for creating DbContext instances in the application's dependency injection (D.I.) container; this can be useful when application code needs to create and dispose context instances manually.</span></span>

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

<span data-ttu-id="8fff4-161">此時，應用程式服務（例如 ASP.NET Core 控制器）可以插入 `IDbContextFactory<TContext>` ，然後用它來具現化內容實例：</span><span class="sxs-lookup"><span data-stu-id="8fff4-161">At this point, application services such as ASP.NET Core controllers can then be injected with `IDbContextFactory<TContext>`, and use it to instantiate context instances:</span></span>

```csharp
public class MyController
{
    private readonly IDbContextFactory<SomeDbContext> _contextFactory;

    public MyController(IDbContextFactory<SomeDbContext> contextFactory)
        => _contextFactory = contextFactory;

    public void DoSomeThing()
    {
        using (var context = _contextFactory.CreateDbContext())
        {
            // ...
        }
    }
}
```

<span data-ttu-id="8fff4-162">如需詳細資訊，請 [參閱 DbCoNtextFactory 的完整檔](xref:core/dbcontext-configuration/index#using-a-dbcontext-factory-eg-for-blazor)。</span><span class="sxs-lookup"><span data-stu-id="8fff4-162">For further information, [see the full documentation on DbContextFactory](xref:core/dbcontext-configuration/index#using-a-dbcontext-factory-eg-for-blazor).</span></span>

## <a name="sqlite-table-rebuilds"></a><span data-ttu-id="8fff4-163">SQLite 資料表重建</span><span class="sxs-lookup"><span data-stu-id="8fff4-163">SQLite table rebuilds</span></span>

<span data-ttu-id="8fff4-164">相較于其他資料庫，SQLite 在其架構操作功能方面相當有限;例如，不支援從現有的資料表卸載資料行。</span><span class="sxs-lookup"><span data-stu-id="8fff4-164">Compared to other databases, SQLite is relatively limited in its schema manipulation capabilities; for example, dropping a column from an existing table is not supported.</span></span> <span data-ttu-id="8fff4-165">EF Core 5.0 解決這些限制的方法，是自動建立新的資料表、從舊的資料表複製資料、卸載舊的資料表，然後重新命名新的資料表。</span><span class="sxs-lookup"><span data-stu-id="8fff4-165">EF Core 5.0 works around these limitations by automatically creating a new table, copying the data from the old table, dropping the old table and renaming the new one.</span></span> <span data-ttu-id="8fff4-166">這會「重建」資料表，並允許安全地套用先前不支援的遷移作業。</span><span class="sxs-lookup"><span data-stu-id="8fff4-166">This "rebuilds" the table, and allows previously unsupported migration operations to be safely applied.</span></span>

<span data-ttu-id="8fff4-167">如需現在可透過資料表重建支援哪些遷移作業的詳細資訊， [請參閱此檔頁面](xref:core/providers/sqlite/limitations#migrations-limitations)。</span><span class="sxs-lookup"><span data-stu-id="8fff4-167">For details on which migration operations are now supported via table rebuilds, [see this documentation page](xref:core/providers/sqlite/limitations#migrations-limitations).</span></span>

## <a name="database-collations"></a><span data-ttu-id="8fff4-168">資料庫定序</span><span class="sxs-lookup"><span data-stu-id="8fff4-168">Database collations</span></span>

<span data-ttu-id="8fff4-169">EF Core 5.0 引進了在資料庫、資料行或查詢層級指定文字定序的支援。</span><span class="sxs-lookup"><span data-stu-id="8fff4-169">EF Core 5.0 introduces support for specifying text collations at the database, column or query level.</span></span> <span data-ttu-id="8fff4-170">這可讓您以具彈性且不會危害查詢效能的方式設定區分大小寫和其他文字部分。</span><span class="sxs-lookup"><span data-stu-id="8fff4-170">This allows case sensitivity and other textual aspects to be configured in a way that is both flexible and does not compromise query performance.</span></span>

<span data-ttu-id="8fff4-171">例如，下列範例會將資料 `Name` 行設定為在 SQL Server 上區分大小寫，而且在資料行上建立的任何索引都會適當地運作：</span><span class="sxs-lookup"><span data-stu-id="8fff4-171">For example, the following will configure the `Name` column to be case-sensitive on SQL Server, and any indexes created on the column will function accordingly:</span></span>

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.Name)
    .UseCollation("SQL_Latin1_General_CP1_CS_AS");
```

<span data-ttu-id="8fff4-172">如需詳細資訊，請參閱定序 [和區分大小寫的完整檔](xref:core/miscellaneous/collations-and-case-sensitivity)。</span><span class="sxs-lookup"><span data-stu-id="8fff4-172">For further information, [see the full documentation on collations and case sensitivity](xref:core/miscellaneous/collations-and-case-sensitivity).</span></span>

## <a name="event-counters"></a><span data-ttu-id="8fff4-173">事件計數器</span><span class="sxs-lookup"><span data-stu-id="8fff4-173">Event counters</span></span>

<span data-ttu-id="8fff4-174">EF Core 5.0 會公開 [事件計數器](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0) ，可用來追蹤應用程式的效能並找出各種異常。</span><span class="sxs-lookup"><span data-stu-id="8fff4-174">EF Core 5.0 exposes [event counters](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0) which can be used to track your application's performance and spot various anomalies.</span></span> <span data-ttu-id="8fff4-175">只要使用 [dotnet 計數器](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) 工具附加到執行 EF 的進程：</span><span class="sxs-lookup"><span data-stu-id="8fff4-175">Simply attach to a process running EF with the [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) tool:</span></span>

```console
> dotnet counters monitor Microsoft.EntityFrameworkCore -p 49496

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

<span data-ttu-id="8fff4-176">如需詳細資訊，請 [參閱事件計數器的完整檔](xref:core/logging-events-diagnostics/event-counters)。</span><span class="sxs-lookup"><span data-stu-id="8fff4-176">For further information, [see the full documentation on event counters](xref:core/logging-events-diagnostics/event-counters).</span></span>

## <a name="other-features"></a><span data-ttu-id="8fff4-177">其他功能</span><span class="sxs-lookup"><span data-stu-id="8fff4-177">Other features</span></span>

### <a name="model-building"></a><span data-ttu-id="8fff4-178">模型建立</span><span class="sxs-lookup"><span data-stu-id="8fff4-178">Model building</span></span>

* <span data-ttu-id="8fff4-179">為了更輕鬆地設定 [值](xref:core/modeling/value-comparers)比較子，我們引進了模型建立 api。</span><span class="sxs-lookup"><span data-stu-id="8fff4-179">Model building APIs have been introduced for easier configuration of [value comparers](xref:core/modeling/value-comparers).</span></span>
* <span data-ttu-id="8fff4-180">計算資料行現在可以設定為已 [*儲存* 或 *虛擬*](xref:core/modeling/generated-properties#computed-columns)。</span><span class="sxs-lookup"><span data-stu-id="8fff4-180">Computed columns can now be configured as [*stored* or *virtual*](xref:core/modeling/generated-properties#computed-columns).</span></span>
* <span data-ttu-id="8fff4-181">精確度和小數位數現在可透過 [流暢的 API](xref:core/modeling/entity-properties#precision-and-scale)進行設定。</span><span class="sxs-lookup"><span data-stu-id="8fff4-181">Precision and scale can now be configured [via the Fluent API](xref:core/modeling/entity-properties#precision-and-scale).</span></span>
* <span data-ttu-id="8fff4-182">針對 [導覽屬性](xref:core/modeling/relationships#configuring-navigation-properties)引進了新的模型建立 api。</span><span class="sxs-lookup"><span data-stu-id="8fff4-182">New model building APIs have been introduced for [navigation properties](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>
* <span data-ttu-id="8fff4-183">已針對欄位引進新的模型建立 Api，類似于屬性。</span><span class="sxs-lookup"><span data-stu-id="8fff4-183">New model building APIs have been introduced for fields, similar to properties.</span></span>
* <span data-ttu-id="8fff4-184">.NET [PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) 和 [IPAddress](/dotnet/api/system.net.ipaddress) 類型現在可以對應到資料庫字串資料行。</span><span class="sxs-lookup"><span data-stu-id="8fff4-184">The .NET [PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) and [IPAddress](/dotnet/api/system.net.ipaddress) types can now be mapped to database string columns.</span></span>
* <span data-ttu-id="8fff4-185">現在可以透過 [新的 `[BackingField]` 屬性](xref:core/modeling/backing-field)來設定支援欄位。</span><span class="sxs-lookup"><span data-stu-id="8fff4-185">A backing field can now be configured via [the new `[BackingField]` attribute](xref:core/modeling/backing-field).</span></span>
* <span data-ttu-id="8fff4-186">現在允許可為 null 的支援欄位，為存放區產生的預設值提供更好的支援，其中 CLR 預設值不是良好的 sentinel 值 (值得注意的 `bool`) 。</span><span class="sxs-lookup"><span data-stu-id="8fff4-186">Nullable backing fields are now allowed, providing better support for store-generated defaults where the CLR default isn't a good sentinel value (notable `bool`).</span></span>
* <span data-ttu-id="8fff4-187">您 `[Index]` 可以在實體類型上使用新的屬性來指定索引，而不是使用流暢的 API。</span><span class="sxs-lookup"><span data-stu-id="8fff4-187">A new `[Index]` attribute can be used on an entity type to specify an index, instead of using the Fluent API.</span></span>
* <span data-ttu-id="8fff4-188">新 `[Keyless]` 屬性可以用來將實體類型設定 [為沒有索引鍵](xref:core/modeling/keyless-entity-types)。</span><span class="sxs-lookup"><span data-stu-id="8fff4-188">A new `[Keyless]` attribute can be used to configure an entity type [as having no key](xref:core/modeling/keyless-entity-types).</span></span>
* <span data-ttu-id="8fff4-189">根據預設， [EF Core 現在會將鑒別子視為 *完成*](xref:core/modeling/inheritance#table-per-hierarchy-and-discriminator-configuration)，這表示它預期不會看到模型中的應用程式未設定的鑒別子值。</span><span class="sxs-lookup"><span data-stu-id="8fff4-189">By default, [EF Core now regards discriminators as *complete*](xref:core/modeling/inheritance#table-per-hierarchy-and-discriminator-configuration), meaning that it expects to never see discriminator values not configured by the application in the model.</span></span> <span data-ttu-id="8fff4-190">這可讓您進行一些效能改進，如果您的鑒別子資料行可能保存未知值，就可以停用此功能。</span><span class="sxs-lookup"><span data-stu-id="8fff4-190">This allows for some performance improvements, and can be disabled if your discriminator column might hold unknown values.</span></span>

### <a name="query"></a><span data-ttu-id="8fff4-191">查詢</span><span class="sxs-lookup"><span data-stu-id="8fff4-191">Query</span></span>

* <span data-ttu-id="8fff4-192">查詢轉譯失敗例外狀況現在包含更明確的失敗原因原因，以協助找出問題。</span><span class="sxs-lookup"><span data-stu-id="8fff4-192">Query translation failure exceptions now contain more explicit reasons about the reasons for the failure, to help pinpoint the issue.</span></span>
* <span data-ttu-id="8fff4-193">無追蹤查詢現在可以執行身分 [識別解析](xref:core/querying/tracking#identity-resolution)，避免針對相同資料庫物件傳回多個實體實例。</span><span class="sxs-lookup"><span data-stu-id="8fff4-193">No-tracking queries can now perform [identity resolution](xref:core/querying/tracking#identity-resolution), avoiding multiple entity instances being returned for the same database object.</span></span>
* <span data-ttu-id="8fff4-194">新增了具有條件式匯總之 GroupBy 的支援 (例如 `GroupBy(o => o.OrderDate).Select(g => g.Count(i => i.OrderDate != null))`) 。</span><span class="sxs-lookup"><span data-stu-id="8fff4-194">Added support for GroupBy with conditional aggregates (e.g. `GroupBy(o => o.OrderDate).Select(g => g.Count(i => i.OrderDate != null))`).</span></span>
* <span data-ttu-id="8fff4-195">已新增在匯總之前將 Distinct 運算子轉換成群組元素的支援。</span><span class="sxs-lookup"><span data-stu-id="8fff4-195">Added support for translating the Distinct operator over group elements before aggregate.</span></span>
* <span data-ttu-id="8fff4-196">的翻譯 [`Reverse`](/dotnet/api/system.linq.queryable.reverse) 。</span><span class="sxs-lookup"><span data-stu-id="8fff4-196">Translation of [`Reverse`](/dotnet/api/system.linq.queryable.reverse).</span></span>
* <span data-ttu-id="8fff4-197">改善 `DateTime` SQL Server (例如) 的翻譯 [`DateDiffWeek`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateDiffWeek%2A) [`DateFromParts`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateFromParts%2A) 。</span><span class="sxs-lookup"><span data-stu-id="8fff4-197">Improved translation around `DateTime` for SQL Server (e.g. [`DateDiffWeek`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateDiffWeek%2A), [`DateFromParts`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateFromParts%2A)).</span></span>
* <span data-ttu-id="8fff4-198">在位元組陣列上進行新方法的轉譯 (例如、 [`Contains`](/dotnet/api/system.linq.enumerable.contains) [`Length`](/dotnet/api/system.array.length) 、 [`SequenceEqual`](/dotnet/api/system.linq.enumerable.sequenceequal)) 。</span><span class="sxs-lookup"><span data-stu-id="8fff4-198">Translation of new methods on byte arrays (e.g. [`Contains`](/dotnet/api/system.linq.enumerable.contains), [`Length`](/dotnet/api/system.array.length), [`SequenceEqual`](/dotnet/api/system.linq.enumerable.sequenceequal)).</span></span>
* <span data-ttu-id="8fff4-199">轉譯一些額外的位運算子，例如二補數。</span><span class="sxs-lookup"><span data-stu-id="8fff4-199">Translation of some additional bitwise operators, such as two's complement.</span></span>
* <span data-ttu-id="8fff4-200">字串的轉譯 `FirstOrDefault` 。</span><span class="sxs-lookup"><span data-stu-id="8fff4-200">Translation of `FirstOrDefault` over strings.</span></span>
* <span data-ttu-id="8fff4-201">改進了針對 null 語義的查詢轉譯，以產生更緊密、更有效率的查詢。</span><span class="sxs-lookup"><span data-stu-id="8fff4-201">Improved query translation around null semantics, resulting in tighter, more efficient queries.</span></span>
* <span data-ttu-id="8fff4-202">使用者對應函式現在可以加上批註來控制 null 傳播，進而產生更緊密、更有效率的查詢。</span><span class="sxs-lookup"><span data-stu-id="8fff4-202">User-mapped functions can now be annotated to control null propagation, again resulting in tighter, more efficient queries.</span></span>
* <span data-ttu-id="8fff4-203">包含案例區塊的 SQL 現在更簡潔。</span><span class="sxs-lookup"><span data-stu-id="8fff4-203">SQL containing CASE blocks is now considerably more concise.</span></span>
* <span data-ttu-id="8fff4-204">您 [`DATELENGTH`](https://docs.microsoft.com/sql/t-sql/functions/datalength-transact-sql) 現在可以透過新的方法，在查詢中呼叫 SQL Server 函數 [`EF.Functions.DataLength`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DataLength%2A) 。</span><span class="sxs-lookup"><span data-stu-id="8fff4-204">The SQL Server [`DATELENGTH`](https://docs.microsoft.com/sql/t-sql/functions/datalength-transact-sql) function can now be called in queries via the new [`EF.Functions.DataLength`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DataLength%2A) method.</span></span>
* <span data-ttu-id="8fff4-205">`EnableDetailedErrors` 將 [其他詳細資料新增至例外](xref:core/logging-events-diagnostics/simple-logging#detailed-query-exceptions)狀況。</span><span class="sxs-lookup"><span data-stu-id="8fff4-205">`EnableDetailedErrors` adds [additional details to exceptions](xref:core/logging-events-diagnostics/simple-logging#detailed-query-exceptions).</span></span>

### <a name="saving"></a><span data-ttu-id="8fff4-206">儲存中</span><span class="sxs-lookup"><span data-stu-id="8fff4-206">Saving</span></span>

* <span data-ttu-id="8fff4-207">SaveChanges [攔截](xref:core/logging-events-diagnostics/interceptors#savechanges-interception) 和 [事件](xref:core/logging-events-diagnostics/events)。</span><span class="sxs-lookup"><span data-stu-id="8fff4-207">SaveChanges [interception](xref:core/logging-events-diagnostics/interceptors#savechanges-interception) and [events](xref:core/logging-events-diagnostics/events).</span></span>
* <span data-ttu-id="8fff4-208">已引進 Api 來控制 [交易儲存點](xref:core/saving/transactions#savepoints)。</span><span class="sxs-lookup"><span data-stu-id="8fff4-208">APIs have been introduced for controlling [transaction savepoints](xref:core/saving/transactions#savepoints).</span></span> <span data-ttu-id="8fff4-209">此外，EF Core 會在 `SaveChanges` 呼叫且交易已在進行時自動建立儲存點，並在失敗時回復。</span><span class="sxs-lookup"><span data-stu-id="8fff4-209">In addition, EF Core will automatically create a savepoint when `SaveChanges` is called and a transaction is already in progress, and roll back to it in case of failure.</span></span>
* <span data-ttu-id="8fff4-210">交易識別碼可以由應用程式明確設定，讓記錄和其他地方的交易事件更容易相互關聯。</span><span class="sxs-lookup"><span data-stu-id="8fff4-210">A transaction ID can be explicitly set by the application, allowing for easier correlation of transaction events in logging and elsewhere.</span></span>
* <span data-ttu-id="8fff4-211">根據批次處理效能的分析，SQL Server 的預設批次大小上限已變更為42。</span><span class="sxs-lookup"><span data-stu-id="8fff4-211">The default maximum batch size for SQL Server has been changed to 42 based on an analysis of batching performance.</span></span>

### <a name="migrations-and-scaffolding"></a><span data-ttu-id="8fff4-212">遷移和樣板</span><span class="sxs-lookup"><span data-stu-id="8fff4-212">Migrations and scaffolding</span></span>

* <span data-ttu-id="8fff4-213">您現在可以 [從遷移中排除](xref:core/modeling/entity-types#excluding-from-migrations)資料表。</span><span class="sxs-lookup"><span data-stu-id="8fff4-213">Tables can now be [excluded from migrations](xref:core/modeling/entity-types#excluding-from-migrations).</span></span>
* <span data-ttu-id="8fff4-214">新的 [`dotnet ef migrations list`](xref:core/cli/dotnet#dotnet-ef-migrations-list) 命令現在會顯示尚未套用至資料庫的遷移 ([`Get-Migration`](xref:core/cli/powershell#get-migration) 在套件管理主控台) 中相同。</span><span class="sxs-lookup"><span data-stu-id="8fff4-214">A new [`dotnet ef migrations list`](xref:core/cli/dotnet#dotnet-ef-migrations-list) command now shows which migrations have not yet been applied to the database ([`Get-Migration`](xref:core/cli/powershell#get-migration) does the same in the Package Management Console).</span></span>
* <span data-ttu-id="8fff4-215">遷移腳本現在包含適當的交易語句，可改善遷移應用程式失敗的處理案例。</span><span class="sxs-lookup"><span data-stu-id="8fff4-215">Migrations scripts now contain transaction statements where appropriate to improve handling cases where migration application fails.</span></span>
* <span data-ttu-id="8fff4-216">未對應之基類的資料行現在會在對應實體類型的其他資料行之後排序。</span><span class="sxs-lookup"><span data-stu-id="8fff4-216">The columns for unmapped base classes are now ordered after other columns for mapped entity types.</span></span> <span data-ttu-id="8fff4-217">請注意，這只會影響新建立的資料表;現有資料表的資料行順序會維持不變。</span><span class="sxs-lookup"><span data-stu-id="8fff4-217">Note this only impacts newly created tables; the column order for existing tables remains unchanged.</span></span>
* <span data-ttu-id="8fff4-218">如果所產生的遷移具有等冪性，而且輸出將會立即執行，還是以腳本的形式來執行，現在就可以得知遷移產生。</span><span class="sxs-lookup"><span data-stu-id="8fff4-218">Migration generation can now be aware if the migration being generated is idempotent, and whether the output will be executed immediately or generated as a script.</span></span>
* <span data-ttu-id="8fff4-219">已新增新的命令列[參數，以](xref:core/managing-schemas/scaffolding#directories-and-namespaces)在[遷移](xref:core/managing-schemas/migrations/index#namespaces)和架構中指定命名空間。</span><span class="sxs-lookup"><span data-stu-id="8fff4-219">New command-line parameters have been added for specifying namespaces in [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [scaffolding](xref:core/managing-schemas/scaffolding#directories-and-namespaces).</span></span>
* <span data-ttu-id="8fff4-220">[Dotnet ef database update](xref:core/cli/dotnet#dotnet-ef-database-update)命令現在接受 `--connection` 用於指定連接字串的新參數。</span><span class="sxs-lookup"><span data-stu-id="8fff4-220">The [dotnet ef database update](xref:core/cli/dotnet#dotnet-ef-database-update) command now accepts a new `--connection` parameter for specifying the connection string.</span></span>
* <span data-ttu-id="8fff4-221">現化現有的資料庫現在會 singularizes 資料表名稱，因此，名為和的資料表 `People` `Addresses` 將會 scaffold 至稱為和的實體類型 `Person` `Address` 。</span><span class="sxs-lookup"><span data-stu-id="8fff4-221">Scaffolding existing databases now singularizes table names, so tables named `People` and `Addresses` will be scaffolded to entity types called `Person` and `Address`.</span></span> <span data-ttu-id="8fff4-222">[仍然可以保留原始的資料庫名稱](xref:core/managing-schemas/scaffolding#preserving-names)。</span><span class="sxs-lookup"><span data-stu-id="8fff4-222">[Original database names can still be preserved](xref:core/managing-schemas/scaffolding#preserving-names).</span></span>
* <span data-ttu-id="8fff4-223">新 [`--no-onconfiguring`](xref:core/cli/dotnet#dotnet-ef-dbcontext-scaffold) 選項可以指示 `OnModelConfiguring` 在建立模型的 EF Core 時排除。</span><span class="sxs-lookup"><span data-stu-id="8fff4-223">The new [`--no-onconfiguring`](xref:core/cli/dotnet#dotnet-ef-dbcontext-scaffold) option can instruct EF Core to exclude `OnModelConfiguring` when scaffolding a model.</span></span>

### <a name="cosmos"></a><span data-ttu-id="8fff4-224">Cosmos</span><span class="sxs-lookup"><span data-stu-id="8fff4-224">Cosmos</span></span>

* <span data-ttu-id="8fff4-225">已展開[Cosmos 連接設定](xref:core/providers/cosmos/index#cosmos-options)。</span><span class="sxs-lookup"><span data-stu-id="8fff4-225">[Cosmos connection settings](xref:core/providers/cosmos/index#cosmos-options) have been expanded.</span></span>
* <span data-ttu-id="8fff4-226">Cosmos 現在透過 [使用 etag 支援](xref:core/providers/cosmos/index#optimistic-concurrency-with-etags)開放式平行存取。</span><span class="sxs-lookup"><span data-stu-id="8fff4-226">Optimistic concurrency is now [supported on Cosmos via the use of ETags](xref:core/providers/cosmos/index#optimistic-concurrency-with-etags).</span></span>
* <span data-ttu-id="8fff4-227">新 `WithPartitionKey` 方法可讓 Cosmos 分割區索引 [鍵](xref:core/providers/cosmos/index#partition-keys) 同時包含在模型和查詢中。</span><span class="sxs-lookup"><span data-stu-id="8fff4-227">The new `WithPartitionKey` method allows the Cosmos [partition key](xref:core/providers/cosmos/index#partition-keys) to be included both in the model and in queries.</span></span>
* <span data-ttu-id="8fff4-228">字串方法 [`Contains`](/dotnet/api/system.string.contains) [`StartsWith`](/dotnet/api/system.string.startswith) 和 [`EndsWith`](/dotnet/api/system.string.endswith) 現在會針對 Cosmos 轉譯。</span><span class="sxs-lookup"><span data-stu-id="8fff4-228">The string methods [`Contains`](/dotnet/api/system.string.contains), [`StartsWith`](/dotnet/api/system.string.startswith) and [`EndsWith`](/dotnet/api/system.string.endswith) are now translated for Cosmos.</span></span>
* <span data-ttu-id="8fff4-229">C # `is` 運算子現在會在 Cosmos 上轉譯。</span><span class="sxs-lookup"><span data-stu-id="8fff4-229">The C# `is` operator is now translated on Cosmos.</span></span>

### <a name="sqlite"></a><span data-ttu-id="8fff4-230">Sqlite</span><span class="sxs-lookup"><span data-stu-id="8fff4-230">Sqlite</span></span>

* <span data-ttu-id="8fff4-231">現在支援計算資料行。</span><span class="sxs-lookup"><span data-stu-id="8fff4-231">Computed columns are now supported.</span></span>
* <span data-ttu-id="8fff4-232">使用 SqliteBlob 和資料流程，以 GetBytes、GetChars 和 GetTextReader 來取得二進位和字串資料，現在更有效率。</span><span class="sxs-lookup"><span data-stu-id="8fff4-232">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="8fff4-233">>sqliteconnection 的初始化現在是延遲的。</span><span class="sxs-lookup"><span data-stu-id="8fff4-233">Initialization of SqliteConnection is now lazy.</span></span>

### <a name="other"></a><span data-ttu-id="8fff4-234">其他</span><span class="sxs-lookup"><span data-stu-id="8fff4-234">Other</span></span>

* <span data-ttu-id="8fff4-235">您可以產生可自動執行 [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) 和 [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged)的變更追蹤 proxy。</span><span class="sxs-lookup"><span data-stu-id="8fff4-235">Change-tracking proxies can be generated that automatically implement [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) and [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged).</span></span> <span data-ttu-id="8fff4-236">這提供了變更追蹤的替代方法，此方法不會在呼叫時掃描變更 `SaveChanges` 。</span><span class="sxs-lookup"><span data-stu-id="8fff4-236">This provides an alternative approach to change-tracking that doesn't scan for changes when `SaveChanges` is called.</span></span>
* <span data-ttu-id="8fff4-237">您 <xref:System.Data.Common.DbConnection> 現在可以在已初始化的 DbCoNtext 上變更或連接字串。</span><span class="sxs-lookup"><span data-stu-id="8fff4-237">A <xref:System.Data.Common.DbConnection> or connection string can now be changed on an already-initialized DbContext.</span></span>
* <span data-ttu-id="8fff4-238">新的 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType> .0 # Microsoft_EntityFrameworkCore_ChangeTracking_ChangeTracker_Clear) 方法會清除所有已追蹤實體的 DbCoNtext。</span><span class="sxs-lookup"><span data-stu-id="8fff4-238">The new <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType>.0#Microsoft_EntityFrameworkCore_ChangeTracking_ChangeTracker_Clear) method clears the DbContext of all tracked entities.</span></span> <span data-ttu-id="8fff4-239">針對每個工作單位，使用建立新的短期內容實例的最佳作法時，通常不需要這麼做。</span><span class="sxs-lookup"><span data-stu-id="8fff4-239">This should usually not be needed when using the best practice of creating a new, short-lived context instance for each unit-of-work.</span></span> <span data-ttu-id="8fff4-240">但是，如果需要重設 DbCoNtext 實例的狀態，則使用新的 `Clear()` 方法比大量卸離所有實體更有效率且穩固。</span><span class="sxs-lookup"><span data-stu-id="8fff4-240">However, if there is a need to reset the state of a DbContext instance, then using the new `Clear()` method is more efficient and robust than mass-detaching all entities.</span></span>
* <span data-ttu-id="8fff4-241">EF Core 命令列工具現在會自動將 `ASPNETCORE_ENVIRONMENT` _和_ `DOTNET_ENVIRONMENT` 環境變數設定為「開發」。</span><span class="sxs-lookup"><span data-stu-id="8fff4-241">The EF Core command line tools now automatically configure the `ASPNETCORE_ENVIRONMENT` _and_ `DOTNET_ENVIRONMENT` environment variables to "Development".</span></span> <span data-ttu-id="8fff4-242">這項功能可讓您在開發期間使用泛型主機搭配 ASP.NET Core 的體驗。</span><span class="sxs-lookup"><span data-stu-id="8fff4-242">This brings the experience when using the generic host in line with the experience for ASP.NET Core during development.</span></span>
* <span data-ttu-id="8fff4-243">自訂命令列引數可流入 <xref:Microsoft.EntityFrameworkCore.Design.IDesignTimeDbContextFactory%601> ，讓應用程式控制內容的建立與初始化方式。</span><span class="sxs-lookup"><span data-stu-id="8fff4-243">Custom command-line arguments can be flowed into <xref:Microsoft.EntityFrameworkCore.Design.IDesignTimeDbContextFactory%601>, allowing applications to control how the context is created and initialized.</span></span>
* <span data-ttu-id="8fff4-244">您現在可以 [在 SQL Server 上設定](xref:core/providers/sql-server/indexes#fill-factor)索引填滿因數。</span><span class="sxs-lookup"><span data-stu-id="8fff4-244">The index fill factor can now be [configured on SQL Server](xref:core/providers/sql-server/indexes#fill-factor).</span></span>
* <span data-ttu-id="8fff4-245"><xref:Microsoft.EntityFrameworkCore.RelationalDatabaseFacadeExtensions.IsRelational%2A>當使用關聯式提供者和非關聯提供者 (（例如 InMemory) ）時，可以使用新的屬性來區別。</span><span class="sxs-lookup"><span data-stu-id="8fff4-245">The new <xref:Microsoft.EntityFrameworkCore.RelationalDatabaseFacadeExtensions.IsRelational%2A> property can be used to distinguish when using a relational provider and a non-relation provider (such as InMemory).</span></span>
