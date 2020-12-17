---
title: EF Core 5.0 的新功能
description: EF Core 5.0 的新功能總覽
author: ajcvickers
ms.date: 09/10/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: 7737a143b9478412cdafe3c2fe5e8d7a106c9a3f
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635493"
---
# <a name="whats-new-in-ef-core-50"></a>EF Core 5.0 的新功能

所有規劃的 EF Core 5.0 功能現在都已完成。 本頁面包含每個預覽中所引進之有趣變更的總覽。

此頁面不會複製 [EF Core 5.0 的方案](xref:core/what-is-new/ef-core-5.0/plan)。 此計畫描述 EF Core 5.0 的整體主題，包括我們在出貨最終發行版本之前打算包含的所有專案。

## <a name="rc1"></a>RC1

### <a name="many-to-many"></a>多對多

EF Core 5.0 支援多對多關聯性，而不需要明確地對應聯結資料表。

例如，請考慮下列實體類型：

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

請注意，其中 `Post` 包含的集合 `Tags` ，並 `Tag` 包含的集合 `Posts` 。 EF Core 5.0 依慣例將此辨識為多對多關聯性。 這表示在中不需要任何程式碼 `OnModelCreating` ：

```csharp
public class BlogContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Tag> Tags { get; set; }
}
```

當使用 (或 `EnsureCreated`) 的遷移來建立資料庫時，EF Core 會自動建立聯結資料表。 例如，在此模型的 SQL Server 上，EF Core 會產生：

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

建立和關聯 `Tag` 和 `Post` 實體會導致聯結資料表更新自動發生。 例如：

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

插入貼文和標記之後，EF 接著會自動在聯結資料表中建立資料列。 例如，在 SQL Server：

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

針對查詢，包含和其他查詢作業的運作方式就像任何其他關聯性一樣。 例如：

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

產生的 SQL 會自動使用聯結資料表來取回所有相關的標記：

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

不同于 EF6，EF Core 允許完整自訂聯結資料表。 例如，下列程式碼會設定多對多關聯性，此關聯性也會具有對聯結實體的導覽，而且聯結實體會包含承載屬性：

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
> 尚未新增與資料庫中多對多關聯性的元件支援。 請參閱 [追蹤問題](https://github.com/dotnet/efcore/issues/22475)。

### <a name="map-entity-types-to-queries"></a>將實體類型對應至查詢

實體類型通常會對應至資料表或視圖，讓 EF Core 在查詢該型別時，將會提取資料表或視圖的內容。 EF Core 5.0 可讓實體類型對應至「定義查詢」。  (這在舊版中是部分支援的，但已經過改良，而且在 EF Core 5.0 中有不同的語法。 ) 

例如，假設有兩個數據表;其中一個包含新式文章;具有舊版文章的另一篇。 新式貼文資料表有一些額外的資料行，但基於我們的應用程式目的，我們想要結合新式和舊版文章，並將其對應至具有所有必要屬性的實體類型：

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

在 EF Core 5.0 中， `ToSqlQuery` 可以用來將此實體類型對應至提取並結合兩個數據表中資料列的查詢：

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Post>().ToSqlQuery(
        @"SELECT Id, Name, Category, BlogId FROM posts
          UNION ALL
          SELECT Id, Name, ""Legacy"", BlogId from legacy_posts");
}
```

請注意， `legacy_posts` 資料表沒有資料 `Category` 行，因此我們會改為合成所有舊版文章的預設值。

然後，您可以使用 LINQ 查詢的一般方式來使用此實體類型。 例如， LINQ 查詢：

```csharp
var posts = context.Posts.Where(e => e.Blog.Name.Contains("Unicorn")).ToList();
```

在 SQLite 上產生下列 SQL：

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

請注意，針對實體型別所設定的查詢是用來做為撰寫完整 LINQ 查詢的起點。

### <a name="event-counters"></a>事件計數器

[.Net 事件計數器](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) 是有效率地從應用程式公開效能度量的一種方式。 EF Core 5.0 包含類別下的事件計數器 `Microsoft.EntityFrameworkCore` 。 例如：

```console
dotnet counters monitor Microsoft.EntityFrameworkCore -p 49496
```

這會告訴 dotnet 計數器開始收集進程49496的 EF Core 事件。 這會產生類似主控台的輸出：

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

### <a name="property-bags"></a>屬性包

EF Core 5.0 允許相同的 CLR 類型對應到多個不同的實體類型。 這類類型稱為共用類型實體類型。 這項功能與預覽) 1 中所包含的索引子屬性結合 (，可讓屬性包作為實體類型使用。

例如，下列 DbCoNtext 會將 BCL 類型設定 `Dictionary<string, object>` 為產品和分類的共用類型實體類型。

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

字典物件 ( 「屬性包」 ) 現在可以新增至內容中做為實體實例並儲存。 例如：

```csharp
var beverages = new Dictionary<string, object>
{
    ["Name"] = "Beverages",
    ["Description"] = "Stuff to sip on"
};

context.Categories.Add(beverages);

context.SaveChanges();
```

然後，您可以透過一般方式來查詢和更新這些實體：

```csharp
var foods = context.Categories.Single(e => e["Name"] == "Foods");
var marmite = context.Products.Single(e => e["Name"] == "Marmite");

marmite["CategoryId"] = foods["Id"];
marmite["Description"] = "Yummy when spread _thinly_ on buttered Toast!";

context.SaveChanges();
```

### <a name="savechanges-interception-and-events"></a>SaveChanges 攔截和事件

EF Core 5.0 引進了 .NET 事件，以及在呼叫 SaveChanges 時觸發的 EF Core 攔截器。

事件很容易使用;例如：

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

請注意：

* 事件寄件者是 `DbContext` 實例
* 事件的引數 `SavedChanges` 包含儲存至資料庫的實體數目

攔截器是由定義 `ISaveChangesInterceptor` ，但是繼承自的是很方便的 `SaveChangesInterceptor` 方式，以避免每個方法的執行。 例如：

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

請注意：

* 攔截器同時具有同步和非同步方法。 如果您需要執行非同步 i/o （例如寫入審核伺服器），這會很有用。
* 攔截器可讓 SaveChanges 使用所有攔截器通用的機制來略過 `InterceptionResult` 。

攔截器的缺點是它們必須在正在建立時于 DbCoNtext 上註冊。 例如：

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .AddInterceptors(new MySaveChangesInterceptor())
        .UseSqlite("Data Source = test.db");
```

相反地，您可以隨時在 DbCoNtext 實例上註冊事件。

### <a name="exclude-tables-from-migrations"></a>從遷移中排除資料表

在多個 DbcoNtext 中對應單一實體類型有時很有用。 尤其是 [在使用系](https://www.martinfowler.com/bliki/BoundedContext.html)結內容時更是如此，因為每個系結內容通常會有不同的 DbCoNtext 類型。

例如， `User` 授權內容和報表內容可能需要型別。 如果對類型進行了變更 `User` ，則這兩個 dbcoNtext 的遷移將會嘗試更新資料庫。 為了避免這種情況，您可以將其中一個內容的模型設定為從其遷移中排除資料表。

在下列程式碼中， `AuthorizationContext` 將會產生資料表變更的遷移 `Users` ，但 `ReportingContext` 不會導致衝突的遷移。

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

### <a name="required-11-dependents"></a>必要的1:1 相依項

在 EF Core 3.1 中，一對一關聯性的相依 end 一律視為選擇性。 這在使用擁有的實體時最為明顯。 例如，請考慮下列模型和設定：

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

這會導致針對 SQLite 建立下表的遷移：

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

請注意，所有資料行都可為 null，即使某些 `HomeAddress` 屬性已設定為必要也是一樣。 此外，當查詢時 `Person` ，如果 home 或 work 位址的所有資料行都是 null，則 EF Core 會將 `HomeAddress` 及/或 `WorkAddress` 屬性保留為 null，而不是設定的空白實例 `Address` 。

在 EF Core 5.0 中， `HomeAddress` 現在可以將導覽設定為必要的相依。 例如：

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

遷移所建立的資料表現在會針對必要相依的必要屬性，加入不可為 null 的資料行：

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

此外，如果嘗試儲存具有 null 必要相依的擁有者，EF Core 現在將會擲回例外狀況。 在此範例中，EF Core 會在嘗試使用 null 儲存時擲回 `Person` `HomeAddress` 。

最後，即使必要相依的所有資料行都有 null 值，EF Core 仍會建立必要相依的實例。

### <a name="options-for-migration-generation"></a>產生遷移的選項

EF Core 5.0 針對不同用途導入了更多的遷移控制。 這包括了能夠：

* 知道是否正在為腳本產生遷移或立即執行
* 知道是否正在產生等冪腳本
* 知道腳本是否應該排除交易語句 (請參閱以下 _交易的遷移腳本_ 。 ) 

此行為是由列舉所指定 `MigrationsSqlGenerationOptions` ，現在可傳遞給 `IMigrator.GenerateScript` 。

這項工作中也包含了更好的等冪性腳本產生，可在 `EXEC` 需要時呼叫 SQL Server。 這項工作也可以對其他資料庫提供者（包括于 postgresql）所產生的腳本進行類似的改進。

### <a name="migrations-scripts-with-transactions"></a>使用交易來遷移腳本

從遷移產生的 SQL 腳本現在包含用來開始和認可交易的語句，適用于遷移。 例如，下列的遷移腳本是從兩個遷移產生的。 請注意，每個遷移現在都在交易內套用。

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

如上一節所述，如果交易需要以不同的方式處理，則可以停用這項交易。

### <a name="see-pending-migrations"></a>查看暫止的遷移

這項功能是由「社區」所貢獻 [@Psypher9](https://github.com/Psypher9) 。 許多人都感謝您的貢獻！

`dotnet ef migrations list`命令現在會顯示尚未套用至資料庫的遷移。 例如：

```console
ajcvickers@avickers420u:~/AllTogetherNow/Daily$ dotnet ef migrations list
Build started...
Build succeeded.
20200910201647_One
20200910201708_Two
20200910202050_Three (Pending)
ajcvickers@avickers420u:~/AllTogetherNow/Daily$
```

此外，現在有一個命令可 `Get-Migration` 讓封裝管理員主控台具有相同的功能。

### <a name="modelbuilder-api-for-value-comparers"></a>值比較子的 ModelBuilder API

自訂可變類型的 EF Core 屬性 [需要值比較子](xref:core/modeling/value-comparers) ，才能正確偵測屬性變更。 您現在可以在設定類型的值轉換時，指定此值。 例如：

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

### <a name="entityentry-trygetvalue-methods"></a>Entityentry.state TryGetValue 方法

這項功能是由「社區」所貢獻 [@m4ss1m0g](https://github.com/m4ss1m0g) 。 許多人都感謝您的貢獻！

`TryGetValue`方法已加入至 `EntityEntry.CurrentValues` 和 `EntityEntry.OriginalValues` 。 這可讓要求屬性的值，而不需要先檢查屬性是否在 EF 模型中對應。 例如：

```csharp
if (entry.CurrentValues.TryGetValue(propertyName, out var value))
{
    Console.WriteLine(value);
}
```

### <a name="default-max-batch-size-for-sql-server"></a>SQL Server 的預設批次大小上限

從 EF Core 5.0 開始，SQL Server 的 SaveChanges 的預設批次大小上限是42。 同樣地，這也是最重要的生命、Universe 和一切問題的答案。 不過，這可能是一項巧合，因為其值是透過 [分析批次處理效能](https://github.com/dotnet/efcore/issues/9270)來取得的。 我們不相信我們發現了一個終極問題，不過看起來似乎有點合理，那就是為了瞭解 SQL Server 的運作方式。

### <a name="default-environment-to-development"></a>開發的預設環境

EF Core 命令列工具現在會自動將 `ASPNETCORE_ENVIRONMENT` _和_ `DOTNET_ENVIRONMENT` 環境變數設定為「開發」。 這項功能可讓您在開發期間使用泛型主機搭配 ASP.NET Core 的體驗。 請參閱 [#19903](https://github.com/dotnet/efcore/issues/19903)。

### <a name="better-migrations-column-ordering"></a>更好的遷移資料行排序

未對應之基類的資料行現在會在對應實體類型的其他資料行之後排序。 請注意，這只會影響新建立的資料表。 現有資料表的資料行順序會維持不變。 請參閱 [#11314](https://github.com/dotnet/efcore/issues/11314)。

### <a name="query-improvements"></a>查詢改進

EF Core 5.0 RC1 包含一些額外的查詢翻譯改進：

* `is`Cosmos 上的翻譯--請參閱[#16391](https://github.com/dotnet/efcore/issues/16391)
* 使用者對應函式現在可以加上批註以控制 null 傳播--請參閱 [#19609](https://github.com/dotnet/efcore/issues/19609)
* 支援使用條件式匯總進行 GroupBy 的翻譯--請參閱 [#11711](https://github.com/dotnet/efcore/issues/11711)
* 在匯總之前將相異運算子轉換成群組元素--請參閱 [#17376](https://github.com/dotnet/efcore/issues/17376)

### <a name="model-building-for-fields"></a>欄位的模型建立

最後，在 RC1 中，EF Core 現在允許在 ModelBuilder 中使用 lambda 方法來尋找欄位以及屬性。 例如，如果您基於某些原因厭惡至屬性，並決定使用公用欄位，則這些欄位現在可以使用 lambda 產生器來對應：

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

雖然這是可行的，但我們當然不建議您這麼做。 此外，請注意，這不會將任何額外的欄位對應功能新增至 EF Core，只允許使用 lambda 方法，而不是一律需要字串方法。 這種情況很少用，因為欄位很少是公用的。

## <a name="preview-8"></a>Preview 8

### <a name="table-per-type-tpt-mapping"></a>每一類型的 (TPT) 對應的資料表

根據預設，EF Core 會將 .NET 類型的繼承階層對應到單一資料庫資料表。 這稱為每一階層的資料表 (TPH) 對應。 EF Core 5.0 也可將繼承階層架構中的每個 .NET 類型對應至不同的資料庫資料表;稱為每一類型的資料表 (TPT) 對應。

例如，請考慮使用對應階層的這個模型：

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

根據預設，EF Core 會將此對應到單一資料表：

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Species] nvarchar(max) NULL,
    [Discriminator] nvarchar(max) NOT NULL,
    [Name] nvarchar(max) NULL,
    [EducationLevel] nvarchar(max) NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);
```

不過，將每個實體類型對應至不同的資料表，會改為每個類型產生一個資料表：

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
    [EducationLevel] nvarchar(max) NULL,
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

請注意，在將 preview 8 的程式碼分支出來之後，會新增上述的外鍵條件約束的建立。

您可以使用對應屬性，將實體類型對應至不同的資料表：

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
    public string EducationLevel { get; set; }
}

[Table("Dogs")]
public class Dog : Pet
{
    public string FavoriteToy { get; set; }
}
```

或使用設定 `ModelBuilder` ：

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Animal>().ToTable("Animals");
    modelBuilder.Entity<Pet>().ToTable("Pets");
    modelBuilder.Entity<Cat>().ToTable("Cats");
    modelBuilder.Entity<Dog>().ToTable("Dogs");
}
```

檔是由問題 [#1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979)所追蹤。

### <a name="migrations-rebuild-sqlite-tables"></a>遷移：重建 SQLite 資料表

相較于其他資料庫，SQLite 的架構操作功能相對受限。 例如，從現有的資料表卸載資料行時，必須卸載並重新建立整個資料表。 EF Core 5.0 遷移現在支援自動重建資料表，以進行需要的架構變更。

例如，假設我們有一個 `Unicorns` 針對實體類型建立的資料表 `Unicorn` ：

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

接下來，我們將瞭解如何將獨角獸的年齡視為非常不一樣，因此讓我們移除該屬性、新增新的遷移，並更新資料庫。 使用 EF Core 3.1 時，此更新將會失敗，因為無法卸載資料行。 在 EF Core 5.0 中，遷移會改為重建資料表：

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

請注意：

* 使用新資料表所需的架構來建立臨時表
* 將資料從目前資料表複製到臨時表
* 外鍵強制切換關閉
* 刪除目前的資料表
* 臨時表重新命名為新的資料表

檔是由問題 [#2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583)所追蹤。

### <a name="table-valued-functions"></a>資料表值函式

這項功能是由「社區」所貢獻 [@pmiddleton](https://github.com/pmiddleton) 。 許多人都感謝您的貢獻！

EF Core 5.0 包含將 .NET 方法對應至資料表值函式 (Tvf) 的第一級支援。 然後，這些函式可以在 LINQ 查詢中使用，而函式結果的其他組合也會轉譯為 SQL。

例如，請考慮在 SQL Server 資料庫中定義的 TVF：

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

EF Core 模型需要兩個實體類型才能使用此 TVF：

* 以 `Employee` 正常方式對應至 Employees 資料表的類型
* `Report`符合 TVF 所傳回之圖形的類型

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

這些類型必須包含在 EF Core 模型中：

```csharp
modelBuilder.Entity<Employee>();
modelBuilder.Entity(typeof(Report)).HasNoKey();
```

請注意，沒有 `Report` 主鍵，因此必須設定為。

最後，.NET 方法必須對應到資料庫中的 TVF。 您可以使用新的方法，在 DbCoNtext 上定義這個方法 `FromExpression` ：

```csharp
public IQueryable<Report> GetReports(int managerId)
    => FromExpression(() => GetReports(managerId));
```

這個方法會使用符合上述定義之 TVF 的參數和傳回型別。 然後，方法會新增至 OnModelCreating 中的 EF Core 模型：

```csharp
modelBuilder.HasDbFunction(() => GetReports(default));
```

使用 lambda 的 (是將傳送至 EF Core 的簡單方法 `MethodInfo` 。 傳遞給方法的引數會被忽略。 ) 

我們現在可以撰寫 `GetReports` 在結果上呼叫和撰寫的查詢。 例如：

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

在 SQL Server 上，這會轉譯為：

```sql
SELECT [e].[Name] AS [ManagerName], [g].[Name] AS [EmployeeName]
FROM [Employees] AS [e]
CROSS APPLY [dbo].[GetReports]([e].[Id]) AS [g]
WHERE [g].[IsDeveloper] = CAST(1 AS bit)
```

請注意，SQL 的根目錄是在 `Employees` 資料表中呼叫 `GetReports` ，然後在函式的結果上加入額外的 WHERE 子句。

### <a name="flexible-queryupdate-mapping"></a>彈性查詢/更新對應

EF Core 5.0 可將相同的實體類型對應至不同的資料庫物件。 這些物件可以是資料表、views 或函數。

例如，實體類型可以對應至資料庫和資料庫資料表：

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

根據預設，EF Core 會從 view 查詢並將更新傳送至資料表。 例如，執行下列程式碼：

```csharp
var blog = context.Set<Blog>().Single(e => e.Name == "One Unicorn");

blog.Name = "1unicorn2";

context.SaveChanges();
```

針對視圖產生查詢，然後對資料表進行更新：

```sql
SELECT TOP(2) [b].[Id], [b].[Name], [b].[Url]
FROM [BlogsView] AS [b]
WHERE [b].[Name] = N'One Unicorn'

SET NOCOUNT ON;
UPDATE [Blogs] SET [Name] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="context-wide-split-query-configuration"></a>整個環境的分割查詢設定

分割查詢 (看到下面) 現在可以設定為 DbCoNtext 所執行之任何查詢的預設值。 這項設定僅適用于關聯式提供者，因此必須指定為設定的一部分 `UseProvider` 。 例如：

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(
            Your.SqlServerConnectionString,
            b => b.UseQuerySplittingBehavior(QuerySplittingBehavior.SplitQuery));
```

檔是由問題 [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407)所追蹤。

### <a name="physicaladdress-mapping"></a>PhysicalAddress 對應

這項功能是由「社區」所貢獻 [@ralmsdeveloper](https://github.com/ralmsdeveloper) 。 許多人都感謝您的貢獻！

標準的 .NET [PhysicalAddress 類別](/dotnet/api/system.net.networkinformation.physicaladdress) 現在會自動對應到尚未具有原生支援之資料庫的字串資料行。 如需詳細資訊，請參閱下面的範例 `IPAddress` 。

## <a name="preview-7"></a>Preview 7

### <a name="dbcontextfactory"></a>DbCoNtextFactory

EF Core 5.0 引進 `AddDbContextFactory` 並 `AddPooledDbContextFactory` 註冊 factory，以在應用程式的相依性插入 (D.I. ) 容器中建立 DbCoNtext 實例。 例如：

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

然後，應用程式服務（例如 ASP.NET Core 控制器）可相依于 `IDbContextFactory<TContext>` 服務的函式中。 例如：

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

然後，您可以視需要建立和使用 DbCoNtext 實例。 例如：

```csharp
public void DoSomeThing()
{
    using (var context = _contextFactory.CreateDbContext())
    {
        // ...
    }
}
```

請注意，以這種方式建立的 DbCoNtext 實例 _不_ 會由應用程式的服務提供者來管理，因此必須由應用程式處置。 這種分離功能對於 Blazor 應用程式非常有用， `IDbContextFactory` 但建議使用，但在其他情況下也可能很有用。

您可以藉由呼叫來將 DbCoNtext 實例集區 `AddPooledDbContextFactory` 。 此共用的運作方式與相同 `AddDbContextPool` ，而且也具有相同的限制。

檔是由問題 [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523)所追蹤。

### <a name="reset-dbcontext-state"></a>重設 DbCoNtext 狀態

EF Core 5.0 引進了哪些專案會 `ChangeTracker.Clear()` 清除所有已追蹤實體的 DbCoNtext。 針對每個工作單位，使用建立新的短期內容實例的最佳作法時，通常不需要這麼做。 但是，如果需要重設 DbCoNtext 實例的狀態，則使用新的 `Clear()` 方法會比大量卸離所有實體更具效能和強大。

檔是由問題 [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524)所追蹤。

### <a name="new-pattern-for-store-generated-defaults"></a>存放區產生之預設值的新模式

EF Core 允許針對可能也有預設值條件約束的資料行設定明確值。 EF Core 使用類型屬性類型的 CLR 預設值做為這個的 sentinel;如果值不是 CLR 預設值，則會將其插入，否則會使用資料庫預設值。

這會為 CLR 預設不是良好 sentinel 的型別（最值得注意的是屬性）建立問題 `bool` 。 EF Core 5.0 現在可讓支援欄位可針對像這樣的案例提供可為 null。 例如：

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

請注意，支援欄位可為 null，但公開的屬性則不是。 這可讓 sentinel 值不會 `null` 影響實體類型的公用介面。 在此情況下，如果 `IsValid` 從未設定，則會使用資料庫預設值，因為支援欄位會維持 null。 如果 `true` `false` 設定了或，則此值會明確地儲存至資料庫。

檔是由問題 [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525)所追蹤。

### <a name="cosmos-partition-keys"></a>Cosmos 分割區索引鍵

EF Core 允許 EF 模型中包含 Cosmos 分割區索引鍵。 例如：

```csharp
modelBuilder.Entity<Customer>().HasPartitionKey(b => b.AlternateKey)
```

從 preview 7 開始，資料分割索引鍵會包含在實體類型的 PK 中，用來改善某些查詢的效能。

檔是由問題 [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471)所追蹤。

### <a name="cosmos-configuration"></a>Cosmos 設定

EF Core 5.0 可改善 Cosmos 和 Cosmos 連接的設定。

先前，EF Core 需要在連接至 Cosmos 資料庫時明確指定端點和金鑰。 EF Core 5.0 允許改用連接字串。 此外，EF Core 5.0 允許明確設定 WebProxy 實例。 例如：

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.WebProxy(myProxyInstance);
            });
```

現在也可以設定許多其他的超時值、限制等等。 例如：

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

最後，預設的連接模式現在 `ConnectionMode.Gateway` 更相容。

檔是由問題 [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471)所追蹤。

### <a name="scaffold-dbcontext-now-singularizes"></a>Scaffold-DbContext 現在 singularizes

先前當從現有的資料庫 DbCoNtext 樣板時，EF Core 將會建立符合資料庫中資料表名稱的實體類型名稱。 例如，資料表 `People` 並 `Addresses` 產生名為和的實體 `People` 類型 `Addresses` 。

在先前的版本中，這項行為可透過註冊複數表示服務來設定。 現在在 EF Core 5.0 中，會使用 [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) 套件作為預設複數表示服務。 這表示資料表 `People` 和 `Addresses` 現在會對名為和的實體類型進行反向工程 `Person` `Address` 。

### <a name="savepoints"></a>點

EF Core 現在支援儲存 [點](/sql/t-sql/language-elements/save-transaction-transact-sql#remarks) ，以便更充分掌控執行多個作業的交易。

您可以手動建立、釋放和復原儲存點。 例如：

```csharp
context.Database.CreateSavepoint("MySavePoint");
```

此外，EF Core 現在會在執行失敗時復原到最後一個儲存點 `SaveChanges` 。 這可讓 SaveChanges 重新嘗試，而不需要重新嘗試整個交易。

檔是由問題 [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429)所追蹤。

## <a name="preview-6"></a>Preview 6

### <a name="split-queries-for-related-collections"></a>分割相關集合的查詢

從 EF Core 3.0 開始，EF Core 一律會為每個 LINQ 查詢產生單一 SQL 查詢。 這樣可確保在使用中交易模式的條件約束內所傳回的資料一致。 不過，當查詢使用 `Include` 或投射回多個相關的集合時，這可能會變得很慢。

EF Core 5.0 現在允許單一 LINQ 查詢，包括要分割成多個 SQL 查詢的相關集合。 這可以大幅提升效能，但如果兩個查詢之間的資料有所變更，則可能導致傳回的結果不一致。 可序列化或快照集交易可用來降低此問題，並與分割查詢達成一致性，但這可能會帶來其他效能成本和行為差異。

#### <a name="split-queries-with-include"></a>使用 Include 分割查詢

例如，假設有一個查詢會使用下列程式提取兩個層級的相關集合 `Include` ：

```csharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

根據預設，EF Core 會在使用 SQLite 提供者時產生下列 SQL：

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

新的 `AsSplitQuery` API 可以用來變更此行為。 例如：

```csharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

AsSplitQuery 適用于所有關系資料庫提供者，而且可以在查詢中的任何位置使用，就像 AsNoTracking 一樣。 EF Core 現在會產生下列三個 SQL 查詢：

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

支援查詢根目錄上的所有作業。 這包括 OrderBy/Skip/Take、聯結作業、FirstOrDefault，以及選取作業的類似單一結果。

請注意，preview 6 中不支援 OrderBy/Skip/Take 的篩選包含，但在每日組建中都有提供，而且將包含在 preview 7 中。

#### <a name="split-queries-with-collection-projections"></a>使用集合投影來分割查詢

`AsSplitQuery` 當集合在投影中載入時，也可以使用。 例如：

```csharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

使用 SQLite 提供者時，這個 LINQ 查詢會產生下列兩個 SQL 查詢：

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

請注意，只支援集合的具體化。 `e.Albums`在上述情況下的任何組合都不會產生分割查詢。 [#21234](https://github.com/dotnet/efcore/issues/21234)會追蹤此區域的改進。

### <a name="indexattribute"></a>IndexAttribute

新的 IndexAttribute 可以放在實體類型上，以指定單一資料行的索引。 例如：

```csharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(128)]
    public string FullName { get; set; }
}
```

針對 SQL Server，遷移將會產生下列 SQL：

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

IndexAttribute 也可用來指定跨越多個資料行的索引。 例如：

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

針對 SQL Server，這會導致：

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

檔是由問題 [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407)所追蹤。

### <a name="improved-query-translation-exceptions"></a>改進的查詢轉譯例外狀況

我們會持續改善查詢轉譯失敗時所產生的例外狀況訊息。 例如，此查詢會使用未對應的屬性 `IsSigned` ：

```csharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

EF Core 會擲回下列例外狀況，表示轉譯失敗，因為未 `IsSigned` 對應：

```exception
Unhandled exception. System.InvalidOperationException: The LINQ expression 'DbSet<Artist>()
   .Where(a => a.IsSigned)' could not be translated. Additional information: Translation of member 'IsSigned' on entity type 'Artist' failed. Possibly the specified member is not mapped. Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync(). See <https://go.microsoft.com/fwlink/?linkid=2101038> for more information.
```

同樣地，當您嘗試翻譯具有文化特性相依語義的字串比較時，現在會產生更好的例外狀況訊息。 例如，此查詢嘗試使用 `StringComparison.CurrentCulture` ：

```csharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

EF Core 現在會擲回下列例外狀況：

```exception
Unhandled exception. System.InvalidOperationException: The LINQ expression 'DbSet<Artist>()
     .Where(a => a.Name.Equals(
         value: "The Unicorns",
         comparisonType: CurrentCulture))' could not be translated. Additional information: Translation of 'string.Equals' method which takes 'StringComparison' argument is not supported. See <https://go.microsoft.com/fwlink/?linkid=2129535> for more information. Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync(). See <https://go.microsoft.com/fwlink/?linkid=2101038> for more information.
```

### <a name="specify-transaction-id"></a>指定交易識別碼

這項功能是由「社區」所貢獻 [@Marusyk](https://github.com/Marusyk) 。 許多人都感謝您的貢獻！

EF Core 公開交易的交易識別碼，以便跨呼叫進行交易的相互關聯。 此識別碼通常會在交易開始時由 EF Core 設定。 如果應用程式改為啟動交易，則這項功能可讓應用程式明確地設定交易識別碼，使其在使用它的任何地方都能正確地相互關聯。 例如：

```csharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a>IPAddress 對應

這項功能是由「社區」所貢獻 [@ralmsdeveloper](https://github.com/ralmsdeveloper) 。 許多人都感謝您的貢獻！

標準 .NET [IPAddress 類別](/dotnet/api/system.net.ipaddress) 現在會自動對應至尚未具有原生支援之資料庫的字串資料行。 例如，請考慮對應此實體類型：

```csharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

在 SQL Server 上，這會導致建立下表：

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
```

然後可以用一般方式新增實體：

```csharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
```

而產生的 SQL 會插入正規化的 IPv4 或 IPv6 位址：

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a>當樣板時排除 OnConfiguring

從現有的資料庫 scaffold DbCoNtext 時，EF Core 預設會使用連接字串建立 OnConfiguring 多載，讓內容可以立即使用。 但是，如果您已經有部分類別具有 OnConfiguring，或如果您以其他方式設定內容，這就不會很有用。

若要解決此情況，現在可以指示可忽略 OnConfiguring 產生的基本程式命令。 例如：

```console
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

或者，在封裝管理員主控台中：

```console
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring
```

請注意，我們建議使用 [已命名的連接字串和安全的儲存體，例如使用者密碼](xref:core/managing-schemas/scaffolding#configuration-and-user-secrets)。

### <a name="translations-for-firstordefault-on-strings"></a>字串的 FirstOrDefault 翻譯

這項功能是由「社區」所貢獻 [@dvoreckyaa](https://github.com/dvoreckyaa) 。 許多人都感謝您的貢獻！

字串中字元的 FirstOrDefault 和類似運算子現在已轉譯。 例如，這個 LINQ 查詢：

```csharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

使用 SQL Server 時，將會轉譯為下列 SQL：

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a>簡化案例區塊

EF Core 現在會以案例區塊產生更好的查詢。 例如，這個 LINQ 查詢：

```csharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

Was SQL Server 正式轉譯為：

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

但現在已轉譯為：

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

## <a name="preview-5"></a>Preview 5

### <a name="database-collations"></a>資料庫定序

您現在可以在 EF 模型中指定資料庫的預設定序。 這會流經產生的遷移，以在建立資料庫時設定定序。 例如：

```csharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

然後，遷移會產生下列各項，以在 SQL Server 上建立資料庫：

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

您也可以指定要用於特定資料庫資料行的定序。 例如：

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.Name)
    .UseCollation("German_PhoneBook_CI_AS");
```

若未使用遷移，則在 DbCoNtext 架構時，定序現在會從資料庫進行反向工程。

最後， `EF.Functions.Collate()` 可以使用不同的定序來進行特定查詢。 例如：

```csharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

這會產生 SQL Server 的下列查詢：

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

請注意，特定定序應謹慎使用，因為它們可能會對資料庫效能造成負面影響。

檔是由問題 [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273)所追蹤。

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a>IDesignTimeDbCoNtextFactory 的流程引數

引數現在會從命令列流向 `CreateDbContext` [IDesignTimeDbCoNtextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1)方法。 例如，若要表示這是開發組建，您 `dev` 可以在命令列上傳遞自訂引數 (例如) ：

```console
dotnet ef migrations add two --verbose --dev
```

然後，這個引數會流入處理站，可用來控制內容的建立與初始化方式。 例如：

```csharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args)
        => new SomeDbContext(args.Contains("--dev"));
}
```

檔是由問題 [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419)所追蹤。

### <a name="no-tracking-queries-with-identity-resolution"></a>沒有識別解析的追蹤查詢

現在可以設定無追蹤查詢來執行識別解析。 例如，下列查詢會為每個貼文建立新的 Blog 實例，即使每個 Blog 都有相同的主要金鑰。

```csharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

不過，代價通常會稍微慢一點，且一律使用更多記憶體，因此可以變更此查詢，以確保只會建立單一的 Blog 實例：

```csharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

請注意，這只適用于無追蹤查詢，因為所有追蹤查詢都已展示此行為。 此外，在遵循 API 審核之後，將會 `PerformIdentityResolution` 變更語法。 請參閱 [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073)。

檔是由問題 [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895)所追蹤。

### <a name="stored-persisted-computed-columns"></a>儲存 (保存) 計算資料行

大部分的資料庫允許在計算後儲存計算資料行值。 雖然這會佔用磁碟空間，但計算資料行只會在更新時計算一次，而不是每次抓取其值時。 這也可讓某些資料庫的資料行編制索引。

EF Core 5.0 允許將計算資料行設定為已儲存。 例如：

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a>SQLite 計算資料行

EF Core 現在支援 SQLite 資料庫中的計算資料行。

## <a name="preview-4"></a>Preview 4

### <a name="configure-database-precisionscale-in-model"></a>在模型中設定資料庫 precision/scale

您現在可以使用模型產生器來指定屬性的有效位數和小數位數。 例如：

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

精確度和小數位數仍然可以透過完整的資料庫類型來設定，例如 "decimal (16，4) "。

檔是由問題 [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527)所追蹤。

### <a name="specify-sql-server-index-fill-factor"></a>指定 SQL Server 索引填滿因數

您現在可以在 SQL Server 上建立索引時指定填滿因數。 例如：

```csharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a>Preview 3

### <a name="filtered-include"></a>篩選的包含

Include 方法現在支援篩選包含的實體。 例如：

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

此查詢將會連同每個相關聯的貼文傳回 blog，但只有在貼文標題包含「乳酪」時。

Skip 和 Take 也可以用來減少包含的實體數目。 例如：

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```

此查詢會傳回包含每個 blog 最多五篇文章的 blog。

如需完整詳細資料，請參閱 [包含檔](xref:core/querying/related-data#filtered-include) 。

### <a name="new-modelbuilder-api-for-navigation-properties"></a>適用于導覽屬性的新 ModelBuilder API

導覽屬性主要是在 [定義關聯](xref:core/modeling/relationships)性時進行設定。 不過，在 `Navigation` 導覽屬性需要額外設定的情況下，可以使用新的方法。 例如，當依據慣例找不到欄位時，設定導覽的支援欄位：

```csharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

請注意， `Navigation` API 不會取代關聯性設定。 相反地，它允許在已探索或已定義的關聯性中，進行其他的導覽屬性設定。

請參閱設定 [導覽屬性檔](xref:core/modeling/relationships#configuring-navigation-properties)。

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a>命名空間和連接字串的新命令列參數

現在可讓您在命令列上指定命名空間。 例如，若要對資料庫進行反向工程，將內容和模型類別放在不同的命名空間中：

```dotnetcli
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

如需完整的詳細資料，請參閱「 [遷移](xref:core/managing-schemas/migrations/index#namespaces) 和 [反向工程](xref:core/managing-schemas/scaffolding#directories-and-namespaces) 」檔。

---
此外，現在可以將連接字串傳遞至 `database-update` 命令：

```dotnetcli
dotnet ef database update --connection "connection string"
```

如需完整的詳細資訊，請參閱 [工具檔](xref:core/cli/dotnet#dotnet-ef-database-update) 。

對應的參數也已加入至 VS 封裝管理員主控台中使用的 PowerShell 命令。

### <a name="enabledetailederrors-has-returned"></a>EnableDetailedErrors 已傳回

基於效能考慮，從資料庫讀取值時，EF 不會執行額外的 null 檢查。 這可能會導致在遇到未預期的 null 時，難以造成根本原因的例外狀況。

使用 `EnableDetailedErrors` 會將額外的 null 檢查新增至查詢，如此一來，若要降低效能，您可以更輕鬆地追蹤至根本原因。

例如：

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors
        .UseSqlServer(Your.SqlServerConnectionString);
```

檔是由問題 [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955)所追蹤。

### <a name="cosmos-partition-keys"></a>Cosmos 分割區索引鍵

您現在可以在查詢中指定要用於指定之查詢的資料分割索引鍵。 例如：

```csharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

檔是由問題 [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199)所追蹤。

### <a name="support-for-the-sql-server-datalength-function"></a>支援 SQL Server DATALENGTH 函數

這可以使用新的方法來存取 `EF.Functions.DataLength` 。 例如：

```csharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
```

## <a name="preview-2"></a>Preview 2

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a>使用 c # 屬性來指定屬性支援欄位

C # 屬性現在可用來指定屬性的支援欄位。 即使無法自動找到支援欄位，這個屬性仍可讓 EF Core 繼續寫入和讀取支援欄位。 例如：

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

檔是由問題 [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230)所追蹤。

### <a name="complete-discriminator-mapping"></a>完成鑒別子對應

EF Core 使用鑒別子資料行來進行 [繼承階層的 TPH 對應](xref:core/modeling/inheritance)。 只要 EF Core 知道鑒別子的所有可能值，就可能會有一些效能增強功能。 EF Core 5.0 現在會實行這些增強功能。

例如，舊版的 EF Core 一律會為傳回階層中所有類型的查詢產生此 SQL：

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

當設定完整的鑒別子對應時，EF Core 5.0 現在會產生下列內容：

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

這會是從 preview 3 開始的預設行為。

### <a name="performance-improvements-in-microsoftdatasqlite"></a>Microsoft 的效能改進

我們對 SQLIte 進行了兩項效能改進：

* 使用 SqliteBlob 和資料流程，以 GetBytes、GetChars 和 GetTextReader 來取得二進位和字串資料，現在更有效率。
* >sqliteconnection 的初始化現在是延遲的。

這些改良功能位於 ADO.NET 的 Microsoft. Sqlite 提供者中，因此也可改善 EF Core 之外的效能。

## <a name="preview-1"></a>Preview 1

### <a name="simple-logging"></a>簡單記錄

這項功能 `Database.Log` 在 EF6 中新增了類似的功能。 也就是說，它會提供簡單的方法來取得 EF Core 的記錄，而不需要設定任何種類的外部記錄架構。

[2019 年12月5日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)包含初稿檔。

其他檔是由問題 [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085)所追蹤。

### <a name="simple-way-to-get-generated-sql"></a>取得所產生 SQL 的簡單方式

EF Core 5.0 引進了 `ToQueryString` 擴充方法，此方法會傳回 EF Core 將在執行 LINQ 查詢時產生的 SQL。

[2020 年1月9日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)包含初稿檔。

其他檔是由問題 [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331)所追蹤。

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a>使用 c # 屬性工作表示實體沒有索引鍵

實體類型現在可以設定為沒有使用新的索引鍵 `KeylessAttribute` 。 例如：

```csharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

檔是由問題 [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186)所追蹤。

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a>可以在初始化的 DbCoNtext 上變更連接或連接字串

現在，您可以更輕鬆地建立沒有任何連接或連接字串的 DbCoNtext 實例。 此外，連接或連接字串現在可以在內容實例上進行變化。 這項功能可讓相同的內容實例動態連接到不同的資料庫。

檔是由問題 [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075)所追蹤。

### <a name="change-tracking-proxies"></a>變更追蹤 proxy

EF Core 現在可以產生自動執行 [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) 和 [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged)的執行時間 proxy。 然後，這些會將實體屬性的值變更直接回報給 EF Core，以避免需要掃描變更。 不過，proxy 有自己的一組限制，因此不適合所有人。

檔是由問題 [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076)所追蹤。

### <a name="enhanced-debug-views"></a>增強的調試視圖

偵錯工具是一種簡單的方法，可讓您在偵測到問題時，查看 EF Core 的內部。 模型的偵錯工具是在一段時間前執行。 針對 EF Core 5.0，我們讓模型視圖更容易讀取，並在狀態管理員中為追蹤的實體新增了新的偵錯工具。

[2019 年12月12日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)包含初稿檔。

其他檔是由問題 [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086)所追蹤。

### <a name="improved-handling-of-database-null-semantics"></a>改進資料庫 null 語義的處理

關係資料庫通常會將 Null 視為未知值，因此不等於任何其他 Null。 雖然 c # 會將 null 視為已定義的值，但會比較是否等於任何其他 null。 EF Core 預設會轉譯查詢，使其使用 c # null 語義。 EF Core 5.0 可大幅提升這些翻譯的效率。

檔是由問題 [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612)所追蹤。

### <a name="indexer-properties"></a>索引子屬性

EF Core 5.0 支援 c # 索引子屬性的對應。 這些屬性可讓實體當做屬性包，其中的資料行會對應至包中的命名屬性。

檔是由問題 [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018)所追蹤。

### <a name="generation-of-check-constraints-for-enum-mappings"></a>列舉對應的檢查條件約束產生

EF Core 5.0 遷移現在可以產生列舉屬性對應的檢查條件約束。 例如：

```sql
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

檔是由問題 [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082)所追蹤。

### <a name="isrelational"></a>IsRelational

`IsRelational`除了現有的、和之外，還加入了新的方法 `IsSqlServer` `IsSqlite` `IsInMemory` 。 這個方法可以用來測試 DbCoNtext 是否使用任何關係資料庫提供者。 例如：

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

檔是由問題 [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185)所追蹤。

### <a name="cosmos-optimistic-concurrency-with-etags"></a>使用 Etag Cosmos 開放式平行存取

Azure Cosmos DB 資料庫提供者現在支援使用 Etag 的開放式平行存取。 使用 OnModelCreating 中的模型產生器來設定 ETag：

```csharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

然後 SaveChanges 會擲回 `DbUpdateConcurrencyException` 並行衝突，以進行 [處理](xref:core/saving/concurrency) 以執行重試等。

檔是由問題 [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099)所追蹤。

### <a name="query-translations-for-more-datetime-constructs"></a>更多日期時間結構的查詢翻譯

現在已翻譯包含新日期時間結構的查詢。

此外，現在已對應下列 SQL Server 函式：

* DateDiffWeek
* DateFromParts

例如：

```csharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

檔是由問題 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)所追蹤。

### <a name="query-translations-for-more-byte-array-constructs"></a>更多位元組陣列結構的查詢翻譯

使用 Contains、Length、Enumerable.sequenceequal 等的查詢現在會轉譯為 SQL。

[2019 年12月5日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)包含初稿檔。

其他檔是由問題 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)所追蹤。

### <a name="query-translation-for-reverse"></a>反向的查詢轉譯

`Reverse`現在會轉譯使用的查詢。 例如：

```csharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

檔是由問題 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)所追蹤。

### <a name="query-translation-for-bitwise-operators"></a>位運算子的查詢轉譯

使用位運算子的查詢現在會在更多情況下轉譯，例如：

```csharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

檔是由問題 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)所追蹤。

### <a name="query-translation-for-strings-on-cosmos"></a>Cosmos 上字串的查詢轉譯

使用字串方法的查詢現在會在使用 Azure Cosmos DB 提供者時轉譯。

檔是由問題 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)所追蹤。
