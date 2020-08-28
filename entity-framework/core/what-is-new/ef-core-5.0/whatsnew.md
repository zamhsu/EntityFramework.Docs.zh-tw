---
title: EF Core 5.0 的新功能
description: EF Core 5.0 的新功能總覽
author: ajcvickers
ms.date: 07/20/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: f822e3ae776778749a654377cbd9d9814ca40972
ms.sourcegitcommit: 12d257db4786487a0c28e9ddd79f176f7cf6edb1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89043593"
---
# <a name="whats-new-in-ef-core-50"></a>EF Core 5.0 的新功能

EF Core 5.0 目前正在開發中。 此頁面將包含每個預覽中所引進之有趣變更的總覽。

此頁面不會複製 [EF Core 5.0 的方案](xref:core/what-is-new/ef-core-5.0/plan)。 此計畫描述 EF Core 5.0 的整體主題，包括我們在出貨最終發行版本之前打算包含的所有專案。

我們會在發行時，將連結從這裡新增至官方檔。

## <a name="preview-8"></a>Preview 8

### <a name="table-per-type-tpt-mapping"></a>每一類型的 (TPT) 對應的資料表

根據預設，EF Core 會將 .NET 類型的繼承階層對應到單一資料庫資料表。 這稱為每一階層的資料表 (TPH) 對應。 EF Core 5.0 也可將繼承階層架構中的每個 .NET 類型對應至不同的資料庫資料表;稱為每一類型的資料表 (TPT) 對應。

例如，請考慮使用對應階層的這個模型：

```c#
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
    public string EdcuationLevel { get; set; }
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
    [EdcuationLevel] nvarchar(max) NULL,
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

請注意，在將 preview 8 的程式碼分支出來之後，會新增上述的外鍵條件約束的建立。

您可以使用對應屬性，將實體類型對應至不同的資料表：

```c#
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

或使用設定 `ModelBuilder` ：

```c#
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

```c#
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

```c#
public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }

    public int? ManagerId { get; set; }
    public virtual Employee Manager { get; set; }
}
```

```c#
public class Report
{
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }
}
```

這些類型必須包含在 EF Core 模型中：

```c#
modelBuilder.Entity<Employee>();
modelBuilder.Entity(typeof(Report)).HasNoKey();
```

請注意，沒有 `Report` 主鍵，因此必須設定為。

最後，.NET 方法必須對應到資料庫中的 TVF。 您可以使用新的方法，在 DbCoNtext 上定義這個方法 `FromExpression` ：

```c#
public IQueryable<Report> GetReports(int managerId)
    => FromExpression(() => GetReports(managerId));
```

這個方法會使用符合上述定義之 TVF 的參數和傳回型別。 然後，方法會新增至 OnModelCreating 中的 EF Core 模型：

```c#
modelBuilder.HasDbFunction(() => GetReports(default));
```

使用 lambda 的 (是將傳送至 EF Core 的簡單方法 `MethodInfo` 。 傳遞給方法的引數會被忽略。 ) 

我們現在可以撰寫 `GetReports` 在結果上呼叫和撰寫的查詢。 例如：

```c#
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

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

根據預設，EF Core 會從 view 查詢並將更新傳送至資料表。 例如，執行下列程式碼：

```c#
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

```c#
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

### <a name="scaffold-dbcontext-now-singularizes"></a>Scaffold-DbCoNtext now singularizes

先前當從現有的資料庫 DbCoNtext 樣板時，EF Core 將會建立符合資料庫中資料表名稱的實體類型名稱。 例如，資料表 `People` 並 `Addresses` 產生名為和的實體 `People` 類型 `Addresses` 。

在先前的版本中，這項行為可透過註冊複數表示服務來設定。 現在在 EF Core 5.0 中，會使用 [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) 套件作為預設複數表示服務。 這表示資料表 `People` 和 `Addresses` 現在會對名為和的實體類型進行反向工程 `Person` `Address` 。

### <a name="savepoints"></a>點

EF Core 現在支援儲存 [點](/SQL/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15#remarks) ，以便更充分掌控執行多個作業的交易。

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

```CSharp
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

```CSharp
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

```CSharp
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

```CSharp
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

```CSharp
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

```CSharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

EF Core 會擲回下列例外狀況，表示轉譯失敗，因為未 `IsSigned` 對應：

> 未處理的例外狀況。 InvalidOperationException： LINQ 運算式 ' DbSet <Artist> ( # A2。無法轉譯 (a =>. IsSigned) '。 其他資訊：在實體類型 ' 演出者 ' 上轉譯成員 ' IsSigned ' 失敗。 可能是指定的成員未對應。 請在可轉譯的表單中重寫查詢，或插入對 Enumerable.asenumerable ( # A1、AsAsyncEnumerable ( # A3、ToList ( # A5 或 ToListAsync ( # A7 的呼叫，以明確地切換至用戶端評估。 如需相關資訊，請參閱 https://go.microsoft.com/fwlink/?linkid=2101038 。

同樣地，當您嘗試翻譯具有文化特性相依語義的字串比較時，現在會產生更好的例外狀況訊息。 例如，此查詢嘗試使用 `StringComparison.CurrentCulture` ：

```CSharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

EF Core 現在會擲回下列例外狀況：

> 未處理的例外狀況。 InvalidOperationException： LINQ 運算式 ' DbSet <Artist> ( # A2。其中 (a => a. Equals ( 值： "獨角獸"，comparisonType： CurrentCulture) # A6 ' 無法轉譯。 其他資訊： ' string ' 的翻譯。不支援採用 ' StringComparison ' 引數的 Equals ' 方法。 如需相關資訊，請參閱 https://go.microsoft.com/fwlink/?linkid=2129535 。 請在可轉譯的表單中重寫查詢，或插入對 Enumerable.asenumerable ( # A1、AsAsyncEnumerable ( # A3、ToList ( # A5 或 ToListAsync ( # A7 的呼叫，以明確地切換至用戶端評估。 如需相關資訊，請參閱 https://go.microsoft.com/fwlink/?linkid=2101038 。

### <a name="specify-transaction-id"></a>指定交易識別碼

這項功能是由「社區」所貢獻 [@Marusyk](https://github.com/Marusyk) 。 許多人都感謝您的貢獻！

EF Core 公開交易的交易識別碼，以便跨呼叫進行交易的相互關聯。 此識別碼通常會在交易開始時由 EF Core 設定。 如果應用程式改為啟動交易，則這項功能可讓應用程式明確地設定交易識別碼，使其在使用它的任何地方都能正確地相互關聯。 例如：

```CSharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a>IPAddress 對應

這項功能是由「社區」所貢獻 [@ralmsdeveloper](https://github.com/ralmsdeveloper) 。 許多人都感謝您的貢獻！

標準 .NET [IPAddress 類別](/dotnet/api/system.net.ipaddress) 現在會自動對應至尚未具有原生支援之資料庫的字串資料行。 例如，請考慮對應此實體類型：

```CSharp
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

```CSharp
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

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

或者，在封裝管理員主控台中：

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring
```

請注意，我們建議使用 [已命名的連接字串和安全的儲存體，例如使用者密碼](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets)。

### <a name="translations-for-firstordefault-on-strings"></a>字串的 FirstOrDefault 翻譯

這項功能是由「社區」所貢獻 [@dvoreckyaa](https://github.com/dvoreckyaa) 。 許多人都感謝您的貢獻！

字串中字元的 FirstOrDefault 和類似運算子現在已轉譯。 例如，這個 LINQ 查詢：

```CSharp
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

```CSharp
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

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

然後，遷移會產生下列各項，以在 SQL Server 上建立資料庫：

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

您也可以指定要用於特定資料庫資料行的定序。 例如：

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

若未使用遷移，則在 DbCoNtext 架構時，定序現在會從資料庫進行反向工程。

最後， `EF.Functions.Collate()` 可以使用不同的定序來進行特定查詢。 例如：

```CSharp
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

引數現在會從命令列流向 `CreateDbContext` [IDesignTimeDbCoNtextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1)方法。 例如，若要表示這是開發組建，您 `dev` 可以在命令列上傳遞自訂引數 (例如) ：

```
dotnet ef migrations add two --verbose --dev
```

然後，這個引數會流入處理站，可用來控制內容的建立與初始化方式。 例如：

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args)
        => new SomeDbContext(args.Contains("--dev"));
}
```

檔是由問題 [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419)所追蹤。

### <a name="no-tracking-queries-with-identity-resolution"></a>沒有識別解析的追蹤查詢

現在可以設定無追蹤查詢來執行識別解析。 例如，下列查詢會為每個貼文建立新的 Blog 實例，即使每個 Blog 都有相同的主要金鑰。

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

不過，代價通常會稍微慢一點，且一律使用更多記憶體，因此可以變更此查詢，以確保只會建立單一的 Blog 實例：

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

請注意，這只適用于無追蹤查詢，因為所有追蹤查詢都已展示此行為。 此外，在遵循 API 審核之後，將會 `PerformIdentityResolution` 變更語法。 請參閱 [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073)。

檔是由問題 [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895)所追蹤。

### <a name="stored-persisted-computed-columns"></a>儲存 (保存) 計算資料行

大部分的資料庫允許在計算後儲存計算資料行值。 雖然這會佔用磁碟空間，但計算資料行只會在更新時計算一次，而不是每次抓取其值時。 這也可讓某些資料庫的資料行編制索引。

EF Core 5.0 允許將計算資料行設定為已儲存。 例如：

```CSharp
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

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

精確度和小數位數仍然可以透過完整的資料庫類型來設定，例如 "decimal (16，4) "。

檔是由問題 [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527)所追蹤。

### <a name="specify-sql-server-index-fill-factor"></a>指定 SQL Server 索引填滿因數

您現在可以在 SQL Server 上建立索引時指定填滿因數。 例如：

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a>Preview 3

### <a name="filtered-include"></a>篩選的包含

Include 方法現在支援篩選包含的實體。 例如：

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

此查詢將會連同每個相關聯的貼文傳回 blog，但只有在貼文標題包含「乳酪」時。

Skip 和 Take 也可以用來減少包含的實體數目。 例如：

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
此查詢會傳回包含每個 blog 最多五篇文章的 blog。

如需完整詳細資料，請參閱 [包含檔](xref:core/querying/related-data#filtered-include) 。

### <a name="new-modelbuilder-api-for-navigation-properties"></a>適用于導覽屬性的新 ModelBuilder API

導覽屬性主要是在 [定義關聯](xref:core/modeling/relationships)性時進行設定。 不過，在 `Navigation` 導覽屬性需要額外設定的情況下，可以使用新的方法。 例如，當依據慣例找不到欄位時，設定導覽的支援欄位：

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

請注意， `Navigation` API 不會取代關聯性設定。 相反地，它允許在已探索或已定義的關聯性中，進行其他的導覽屬性設定。

請參閱設定 [導覽屬性檔](xref:core/modeling/relationships#configuring-navigation-properties)。

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a>命名空間和連接字串的新命令列參數

現在可讓您在命令列上指定命名空間。 例如，若要對資料庫進行反向工程，將內容和模型類別放在不同的命名空間中：

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

如需完整的詳細資料，請參閱「 [遷移](xref:core/managing-schemas/migrations/index#namespaces) 和 [反向工程](xref:core/managing-schemas/scaffolding#directories-and-namespaces) 」檔。

---
此外，現在可以將連接字串傳遞至 `database-update` 命令：

```
dotnet ef database update --connection "connection string"
```

如需完整的詳細資訊，請參閱 [工具檔](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) 。

對應的參數也已加入至 VS 封裝管理員主控台中使用的 PowerShell 命令。

### <a name="enabledetailederrors-has-returned"></a>EnableDetailedErrors 已傳回

基於效能考慮，從資料庫讀取值時，EF 不會執行額外的 null 檢查。 這可能會導致在遇到未預期的 null 時，難以造成根本原因的例外狀況。

使用 `EnableDetailedErrors` 會將額外的 null 檢查新增至查詢，如此一來，若要降低效能，您可以更輕鬆地追蹤至根本原因。

例如：
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors
        .UseSqlServer(Your.SqlServerConnectionString);
```

檔是由問題 [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955)所追蹤。

### <a name="cosmos-partition-keys"></a>Cosmos 分割區索引鍵

您現在可以在查詢中指定要用於指定之查詢的資料分割索引鍵。 例如：

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

檔是由問題 [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199)所追蹤。

### <a name="support-for-the-sql-server-datalength-function"></a>支援 SQL Server DATALENGTH 函數

這可以使用新的方法來存取 `EF.Functions.DataLength` 。 例如：
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
```

## <a name="preview-2"></a>Preview 2

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a>使用 c # 屬性來指定屬性支援欄位

C # 屬性現在可用來指定屬性的支援欄位。 即使無法自動找到支援欄位，這個屬性仍可讓 EF Core 繼續寫入和讀取支援欄位。 例如：

```CSharp
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

EF Core 使用鑒別子資料行來進行 [繼承階層的 TPH 對應](/ef/core/modeling/inheritance)。 只要 EF Core 知道鑒別子的所有可能值，就可能會有一些效能增強功能。 EF Core 5.0 現在會實行這些增強功能。

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

```CSharp
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

EF Core 現在可以產生自動執行 [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) 和 [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1)的執行時間 proxy。 然後，這些會將實體屬性的值變更直接回報給 EF Core，以避免需要掃描變更。 不過，proxy 有自己的一組限制，因此不適合所有人。

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

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

檔是由問題 [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082)所追蹤。

### <a name="isrelational"></a>IsRelational

`IsRelational`除了現有的、和之外，還加入了新的方法 `IsSqlServer` `IsSqlite` `IsInMemory` 。 這個方法可以用來測試 DbCoNtext 是否使用任何關係資料庫提供者。 例如：

```CSharp
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

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

然後 SaveChanges 會擲回 `DbUpdateConcurrencyException` 並行衝突，以進行 [處理](/ef/core/saving/concurrency) 以執行重試等。

檔是由問題 [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099)所追蹤。

### <a name="query-translations-for-more-datetime-constructs"></a>更多日期時間結構的查詢翻譯

現在已翻譯包含新日期時間結構的查詢。

此外，現在已對應下列 SQL Server 函式：

* DateDiffWeek
* DateFromParts

例如：

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

檔是由問題 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)所追蹤。

### <a name="query-translations-for-more-byte-array-constructs"></a>更多位元組陣列結構的查詢翻譯

使用 Contains、Length、Enumerable.sequenceequal 等的查詢現在會轉譯為 SQL。

[2019 年12月5日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)包含初稿檔。

其他檔是由問題 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)所追蹤。

### <a name="query-translation-for-reverse"></a>反向的查詢轉譯

`Reverse`現在會轉譯使用的查詢。 例如：

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

檔是由問題 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)所追蹤。

### <a name="query-translation-for-bitwise-operators"></a>位運算子的查詢轉譯

使用位運算子的查詢現在會在更多情況下轉譯，例如：

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

檔是由問題 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)所追蹤。

### <a name="query-translation-for-strings-on-cosmos"></a>Cosmos 上字串的查詢轉譯

使用字串方法的查詢現在會在使用 Azure Cosmos DB 提供者時轉譯。

檔是由問題 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)所追蹤。
