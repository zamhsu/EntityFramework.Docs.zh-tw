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
# <a name="whats-new-in-ef-core-50"></a>EF Core 5.0 的新功能

下列清單包含 EF Core 5.0 中的主要新功能。 如需發行中問題的完整清單，請參閱我們的 [問題追蹤](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A5.0.0)程式。

作為主要版本，EF Core 5.0 也包含幾項 [重大變更](xref:core/what-is-new/ef-core-5.0/breaking-changes)，也就是 API 改進或可能對現有應用程式造成負面影響的行為變更。

## <a name="many-to-many"></a>多對多

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

EF Core 5.0 依慣例將此辨識為多對多關聯性，並 `PostTag` 在資料庫中自動建立聯結資料表。 您可以查詢和更新資料，而不需要明確參考聯結資料表，以大幅簡化程式碼。 您仍然可以視需要明確地自訂和查詢聯結資料表。

如需詳細資訊，請 [參閱多對多的完整檔](xref:core/modeling/relationships#many-to-many)。

## <a name="split-queries"></a>分割查詢

從 EF Core 3.0 開始，EF Core 一律會為每個 LINQ 查詢產生單一 SQL 查詢。 這樣可確保在使用中交易模式的條件約束內所傳回的資料一致。 不過，當查詢使用 `Include` 或投射回多個相關的集合時，這可能會變得很慢。

EF Core 5.0 現在允許單一 LINQ 查詢，包括要分割成多個 SQL 查詢的相關集合。 這可以大幅提升效能，但如果兩個查詢之間的資料有所變更，則可能導致傳回的結果不一致。 可序列化或快照集交易可用來降低此問題，並與分割查詢達成一致性，但這可能會帶來其他效能成本和行為差異。

例如，假設有一個查詢會使用下列程式提取兩個層級的相關集合 `Include` ：

```csharp
var artists = context.Artists
    .Include(e => e.Albums)
    .ToList();
```

根據預設，EF Core 會在使用 SQLite 提供者時產生下列 SQL：

```sql
SELECT a."Id", a."Name", a0."Id", a0."ArtistId", a0."Title"
FROM "Artists" AS a
LEFT JOIN "Album" AS a0 ON a."Id" = a0."ArtistId"
ORDER BY a."Id", a0."Id"
```

使用分割查詢時，會改為產生下列 SQL：

```sql
SELECT a."Id", a."Name"
FROM "Artists" AS a
ORDER BY a."Id"

SELECT a0."Id", a0."ArtistId", a0."Title", a."Id"
FROM "Artists" AS a
INNER JOIN "Album" AS a0 ON a."Id" = a0."ArtistId"
ORDER BY a."Id"
```

您可以藉由將新的運算子放 `AsSplitQuery` 在 LINQ 查詢中的任何位置，或在模型的全域位置來啟用分割查詢 `OnConfiguring` 。 如需詳細資訊，請 [參閱分割查詢的完整檔](xref:core/querying/single-split-queries)集。

## <a name="simple-logging-and-improved-diagnostics"></a>簡單的記錄和改善的診斷

EF Core 5.0 引進了一種簡單的方式，可透過新的方法設定記錄 `LogTo` 。 下列程式會將記錄訊息寫入主控台，包括 EF Core 產生的所有 SQL：

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder.LogTo(Console.WriteLine);
```

此外，現在可以對 `ToQueryString` 任何 LINQ 查詢進行呼叫，以抓取查詢會執行的 SQL：

```csharp
Console.WriteLine(
    ctx.Artists
    .Where(a => a.Name == "Pink Floyd")
    .ToQueryString());
```

最後，各種 EF Core 類型都是以增強型屬性來調整，以 `DebugView` 提供內部的詳細觀點。 例如，您 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType> 可以參考以查看在指定時間內所追蹤的實體。

如需詳細資訊，請 [參閱記錄和攔截的相關檔](xref:core/logging-events-diagnostics/index)。

## <a name="filtered-include"></a>篩選的包含

`Include`方法現在支援篩選包含的實體：

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

此查詢將會連同每個相關聯的貼文傳回 blog，但只有在貼文標題包含「乳酪」時。

如需詳細資訊，請 [參閱分割查詢的完整檔](xref:core/querying/related-data/eager#filtered-include)集。

## <a name="table-per-type-tpt-mapping"></a>每一類型的 (TPT) 對應的資料表

根據預設，EF Core 會將 .NET 類型的繼承階層對應到單一資料庫資料表。 這稱為每一階層的資料表 (TPH) 對應。 EF Core 5.0 也可將繼承階層架構中的每個 .NET 類型對應至不同的資料庫資料表;稱為每一類型的資料表 (TPT) 對應。

例如，請考慮使用對應階層的這個模型：

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

使用 TPT 時，會針對階層中的每個類型建立資料庫資料表：

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

如需詳細資訊，請 [參閱 TPT 的完整檔](xref:core/modeling/inheritance)。

## <a name="flexible-entity-mapping"></a>彈性實體對應

實體類型通常會對應至資料表或視圖，讓 EF Core 在查詢該型別時，將會提取資料表或視圖的內容。 EF Core 5.0 會新增額外的對應選項，其中實體可以對應至 SQL 查詢 (稱為「定義查詢」 ) ，或對應至資料表值函數 (TVF) ：

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

資料表值函式也可以對應至 .NET 方法，而不是對應至 DbSet，以允許傳遞參數;您可以使用來設定對應 <xref:Microsoft.EntityFrameworkCore.RelationalModelBuilderExtensions.HasDbFunction%2A> 。

最後，您現在可以在查詢 (或函數或定義查詢) 時，將實體對應至 view，但是在更新時，會將其對應至資料表：

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

## <a name="shared-type-entity-types-and-property-bags"></a>共用類型的實體類型和屬性包

EF Core 5.0 允許相同的 CLR 類型對應到多個不同的實體類型;這類類型稱為共用類型實體類型。 雖然任何 CLR 型別都可以搭配這項功能使用，但 .NET `Dictionary` 提供了一個特別吸引人的使用案例，我們稱之為「屬性包」：

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

然後，您可以查詢和更新這些實體，就像一般實體型別一樣，也可以使用專屬的 CLR 型別。 您可以在 [屬性包](xref:core/modeling/shadow-properties)的檔中找到詳細資訊。

## <a name="required-11-dependents"></a>必要的1:1 相依項

在 EF Core 3.1 中，一對一關聯性的相依 end 一律視為選擇性。 這在使用擁有的實體時最為明顯，因為所有擁有實體的資料行在資料庫中建立為可為 null，即使它們已在模型中設定為必要。

在 EF Core 5.0 中，可將所擁有實體的導覽設定為必要的相依。 例如：

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

## <a name="dbcontextfactory"></a>DbCoNtextFactory

EF Core 5.0 引進 `AddDbContextFactory` 並 `AddPooledDbContextFactory` 註冊在應用程式的相依性插入 (D.I. ) 容器中建立 DbCoNtext 實例的 factory; 當應用程式程式碼需要手動建立和處置內容實例時，這會很有用。

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

此時，應用程式服務（例如 ASP.NET Core 控制器）可以插入 `IDbContextFactory<TContext>` ，然後用它來具現化內容實例：

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

如需詳細資訊，請 [參閱 DbCoNtextFactory 的完整檔](xref:core/dbcontext-configuration/index#using-a-dbcontext-factory-eg-for-blazor)。

## <a name="sqlite-table-rebuilds"></a>SQLite 資料表重建

相較于其他資料庫，SQLite 在其架構操作功能方面相當有限;例如，不支援從現有的資料表卸載資料行。 EF Core 5.0 解決這些限制的方法，是自動建立新的資料表、從舊的資料表複製資料、卸載舊的資料表，然後重新命名新的資料表。 這會「重建」資料表，並允許安全地套用先前不支援的遷移作業。

如需現在可透過資料表重建支援哪些遷移作業的詳細資訊， [請參閱此檔頁面](xref:core/providers/sqlite/limitations#migrations-limitations)。

## <a name="database-collations"></a>資料庫定序

EF Core 5.0 引進了在資料庫、資料行或查詢層級指定文字定序的支援。 這可讓您以具彈性且不會危害查詢效能的方式設定區分大小寫和其他文字部分。

例如，下列範例會將資料 `Name` 行設定為在 SQL Server 上區分大小寫，而且在資料行上建立的任何索引都會適當地運作：

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.Name)
    .UseCollation("SQL_Latin1_General_CP1_CS_AS");
```

如需詳細資訊，請參閱定序 [和區分大小寫的完整檔](xref:core/miscellaneous/collations-and-case-sensitivity)。

## <a name="event-counters"></a>事件計數器

EF Core 5.0 會公開 [事件計數器](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0) ，可用來追蹤應用程式的效能並找出各種異常。 只要使用 [dotnet 計數器](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) 工具附加到執行 EF 的進程：

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

如需詳細資訊，請 [參閱事件計數器的完整檔](xref:core/logging-events-diagnostics/event-counters)。

## <a name="other-features"></a>其他功能

### <a name="model-building"></a>模型建立

* 為了更輕鬆地設定 [值](xref:core/modeling/value-comparers)比較子，我們引進了模型建立 api。
* 計算資料行現在可以設定為已 [*儲存* 或 *虛擬*](xref:core/modeling/generated-properties#computed-columns)。
* 精確度和小數位數現在可透過 [流暢的 API](xref:core/modeling/entity-properties#precision-and-scale)進行設定。
* 針對 [導覽屬性](xref:core/modeling/relationships#configuring-navigation-properties)引進了新的模型建立 api。
* 已針對欄位引進新的模型建立 Api，類似于屬性。
* .NET [PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) 和 [IPAddress](/dotnet/api/system.net.ipaddress) 類型現在可以對應到資料庫字串資料行。
* 現在可以透過 [新的 `[BackingField]` 屬性](xref:core/modeling/backing-field)來設定支援欄位。
* 現在允許可為 null 的支援欄位，為存放區產生的預設值提供更好的支援，其中 CLR 預設值不是良好的 sentinel 值 (值得注意的 `bool`) 。
* 您 `[Index]` 可以在實體類型上使用新的屬性來指定索引，而不是使用流暢的 API。
* 新 `[Keyless]` 屬性可以用來將實體類型設定 [為沒有索引鍵](xref:core/modeling/keyless-entity-types)。
* 根據預設， [EF Core 現在會將鑒別子視為 *完成*](xref:core/modeling/inheritance#table-per-hierarchy-and-discriminator-configuration)，這表示它預期不會看到模型中的應用程式未設定的鑒別子值。 這可讓您進行一些效能改進，如果您的鑒別子資料行可能保存未知值，就可以停用此功能。

### <a name="query"></a>查詢

* 查詢轉譯失敗例外狀況現在包含更明確的失敗原因原因，以協助找出問題。
* 無追蹤查詢現在可以執行身分 [識別解析](xref:core/querying/tracking#identity-resolution)，避免針對相同資料庫物件傳回多個實體實例。
* 新增了具有條件式匯總之 GroupBy 的支援 (例如 `GroupBy(o => o.OrderDate).Select(g => g.Count(i => i.OrderDate != null))`) 。
* 已新增在匯總之前將 Distinct 運算子轉換成群組元素的支援。
* 的翻譯 [`Reverse`](/dotnet/api/system.linq.queryable.reverse) 。
* 改善 `DateTime` SQL Server (例如) 的翻譯 [`DateDiffWeek`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateDiffWeek%2A) [`DateFromParts`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateFromParts%2A) 。
* 在位元組陣列上進行新方法的轉譯 (例如、 [`Contains`](/dotnet/api/system.linq.enumerable.contains) [`Length`](/dotnet/api/system.array.length) 、 [`SequenceEqual`](/dotnet/api/system.linq.enumerable.sequenceequal)) 。
* 轉譯一些額外的位運算子，例如二補數。
* 字串的轉譯 `FirstOrDefault` 。
* 改進了針對 null 語義的查詢轉譯，以產生更緊密、更有效率的查詢。
* 使用者對應函式現在可以加上批註來控制 null 傳播，進而產生更緊密、更有效率的查詢。
* 包含案例區塊的 SQL 現在更簡潔。
* 您 [`DATELENGTH`](https://docs.microsoft.com/sql/t-sql/functions/datalength-transact-sql) 現在可以透過新的方法，在查詢中呼叫 SQL Server 函數 [`EF.Functions.DataLength`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DataLength%2A) 。
* `EnableDetailedErrors` 將 [其他詳細資料新增至例外](xref:core/logging-events-diagnostics/simple-logging#detailed-query-exceptions)狀況。

### <a name="saving"></a>儲存中

* SaveChanges [攔截](xref:core/logging-events-diagnostics/interceptors#savechanges-interception) 和 [事件](xref:core/logging-events-diagnostics/events)。
* 已引進 Api 來控制 [交易儲存點](xref:core/saving/transactions#savepoints)。 此外，EF Core 會在 `SaveChanges` 呼叫且交易已在進行時自動建立儲存點，並在失敗時回復。
* 交易識別碼可以由應用程式明確設定，讓記錄和其他地方的交易事件更容易相互關聯。
* 根據批次處理效能的分析，SQL Server 的預設批次大小上限已變更為42。

### <a name="migrations-and-scaffolding"></a>遷移和樣板

* 您現在可以 [從遷移中排除](xref:core/modeling/entity-types#excluding-from-migrations)資料表。
* 新的 [`dotnet ef migrations list`](xref:core/cli/dotnet#dotnet-ef-migrations-list) 命令現在會顯示尚未套用至資料庫的遷移 ([`Get-Migration`](xref:core/cli/powershell#get-migration) 在套件管理主控台) 中相同。
* 遷移腳本現在包含適當的交易語句，可改善遷移應用程式失敗的處理案例。
* 未對應之基類的資料行現在會在對應實體類型的其他資料行之後排序。 請注意，這只會影響新建立的資料表;現有資料表的資料行順序會維持不變。
* 如果所產生的遷移具有等冪性，而且輸出將會立即執行，還是以腳本的形式來執行，現在就可以得知遷移產生。
* 已新增新的命令列[參數，以](xref:core/managing-schemas/scaffolding#directories-and-namespaces)在[遷移](xref:core/managing-schemas/migrations/index#namespaces)和架構中指定命名空間。
* [Dotnet ef database update](xref:core/cli/dotnet#dotnet-ef-database-update)命令現在接受 `--connection` 用於指定連接字串的新參數。
* 現化現有的資料庫現在會 singularizes 資料表名稱，因此，名為和的資料表 `People` `Addresses` 將會 scaffold 至稱為和的實體類型 `Person` `Address` 。 [仍然可以保留原始的資料庫名稱](xref:core/managing-schemas/scaffolding#preserving-names)。
* 新 [`--no-onconfiguring`](xref:core/cli/dotnet#dotnet-ef-dbcontext-scaffold) 選項可以指示 `OnModelConfiguring` 在建立模型的 EF Core 時排除。

### <a name="cosmos"></a>Cosmos

* 已展開[Cosmos 連接設定](xref:core/providers/cosmos/index#cosmos-options)。
* Cosmos 現在透過 [使用 etag 支援](xref:core/providers/cosmos/index#optimistic-concurrency-with-etags)開放式平行存取。
* 新 `WithPartitionKey` 方法可讓 Cosmos 分割區索引 [鍵](xref:core/providers/cosmos/index#partition-keys) 同時包含在模型和查詢中。
* 字串方法 [`Contains`](/dotnet/api/system.string.contains) [`StartsWith`](/dotnet/api/system.string.startswith) 和 [`EndsWith`](/dotnet/api/system.string.endswith) 現在會針對 Cosmos 轉譯。
* C # `is` 運算子現在會在 Cosmos 上轉譯。

### <a name="sqlite"></a>Sqlite

* 現在支援計算資料行。
* 使用 SqliteBlob 和資料流程，以 GetBytes、GetChars 和 GetTextReader 來取得二進位和字串資料，現在更有效率。
* >sqliteconnection 的初始化現在是延遲的。

### <a name="other"></a>其他

* 您可以產生可自動執行 [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) 和 [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged)的變更追蹤 proxy。 這提供了變更追蹤的替代方法，此方法不會在呼叫時掃描變更 `SaveChanges` 。
* 您 <xref:System.Data.Common.DbConnection> 現在可以在已初始化的 DbCoNtext 上變更或連接字串。
* 新的 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType> .0 # Microsoft_EntityFrameworkCore_ChangeTracking_ChangeTracker_Clear) 方法會清除所有已追蹤實體的 DbCoNtext。 針對每個工作單位，使用建立新的短期內容實例的最佳作法時，通常不需要這麼做。 但是，如果需要重設 DbCoNtext 實例的狀態，則使用新的 `Clear()` 方法比大量卸離所有實體更有效率且穩固。
* EF Core 命令列工具現在會自動將 `ASPNETCORE_ENVIRONMENT` _和_ `DOTNET_ENVIRONMENT` 環境變數設定為「開發」。 這項功能可讓您在開發期間使用泛型主機搭配 ASP.NET Core 的體驗。
* 自訂命令列引數可流入 <xref:Microsoft.EntityFrameworkCore.Design.IDesignTimeDbContextFactory%601> ，讓應用程式控制內容的建立與初始化方式。
* 您現在可以 [在 SQL Server 上設定](xref:core/providers/sql-server/indexes#fill-factor)索引填滿因數。
* <xref:Microsoft.EntityFrameworkCore.RelationalDatabaseFacadeExtensions.IsRelational%2A>當使用關聯式提供者和非關聯提供者 (（例如 InMemory) ）時，可以使用新的屬性來區別。
