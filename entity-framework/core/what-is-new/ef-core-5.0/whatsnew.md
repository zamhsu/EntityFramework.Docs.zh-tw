---
title: 5.0 EF Core 的新功能
description: EF Core 5.0 中的新功能總覽
author: ajcvickers
ms.date: 06/02/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: 0a2ba5b804cc6636b321edcc48feeb76ad60560b
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370366"
---
# <a name="whats-new-in-ef-core-50"></a>5.0 EF Core 的新功能

EF Core 5.0 目前正在開發中。 此頁面將包含每個預覽中所引進之有趣變更的總覽。

此頁面不會複製[EF Core 5.0 的計畫](xref:core/what-is-new/ef-core-5.0/plan)。 此計畫描述 EF Core 5.0 的整體主題，包括我們打算在交付最終版本之前包含的所有專案。

我們會將這裡的連結新增至正式檔，因為它已發佈。

## <a name="preview-6"></a>Preview 6

### <a name="split-queries-for-related-collections"></a>相關集合的分割查詢

從 EF Core 3.0 開始，EF Core 一律會針對每個 LINQ 查詢產生單一 SQL 查詢。 這可確保在使用中交易模式的條件約束內傳回的資料一致。 不過，當查詢使用 `Include` 或預測來重新顯示多個相關的集合時，這可能會變得非常緩慢。

EF Core 5.0 現在允許單一 LINQ 查詢，包括要分割成多個 SQL 查詢的相關集合。 這可以大幅提升效能，但如果兩個查詢之間的資料有所變更，可能會導致傳回的結果不一致。 可序列化或快照集交易可以用來減輕這個問題，並利用分割查詢達成一致性，但這可能會帶來其他效能成本和行為上的差異。

#### <a name="split-queries-with-include"></a>使用 Include 分割查詢

例如，假設有一個查詢會使用提取兩個層級的相關集合 `Include` ：

```CSharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

根據預設，當使用 SQLite 提供者時，EF Core 將會產生下列 SQL：

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

新的 `AsSplitQuery` API 可用於變更此行為。 例如：

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

支援查詢根目錄上的所有作業。 這包括 OrderBy/Skip/Take、Join 作業、FirstOrDefault 和類似的單一結果選取作業。

請注意，preview 6 不支援具有 OrderBy/Skip/Take 的篩選包含，但在每日組建中都有提供，而且將包含在 preview 7 中。

#### <a name="split-queries-with-collection-projections"></a>使用集合投射分割查詢

`AsSplitQuery`在投影中載入集合時，也可以使用。 例如：

```CSharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

使用 SQLite 提供者時，此 LINQ 查詢會產生下列兩個 SQL 查詢：

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

請注意，只支援集合的具體化。 `e.Albums`在上述情況下的任何組合都不會產生分割查詢。 此區域中的改進功能會由[#21234](https://github.com/dotnet/efcore/issues/21234)追蹤。

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

IndexAttribute 也可以用來指定跨越多個資料行的索引。 例如：

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

檔是由問題[#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407)追蹤。

### <a name="improved-query-translation-exceptions"></a>改善的查詢轉譯例外狀況

我們會繼續改善查詢轉譯失敗時所產生的例外狀況訊息。 例如，此查詢會使用未對應的屬性 `IsSigned` ：

```CSharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

EF Core 將會擲回下列例外狀況，指出轉譯失敗，因為未 `IsSigned` 對應：

> 未處理的例外狀況。 InvalidOperationException： LINQ 運算式的 DbSet <Artist> （）。Where （a => IsSigned） ' 無法轉譯。 其他資訊：實體類型 ' 演出者 ' 上成員 ' IsSigned ' 的轉譯失敗。 可能是指定的成員未對應。 請以可翻譯的形式重寫查詢，或將呼叫插入 Enumerable.asenumerable （）、AsAsyncEnumerable （）、ToList （）或 ToListAsync （），以明確地切換至用戶端評估。 如需相關資訊，請參閱 https://go.microsoft.com/fwlink/?linkid=2101038 。

同樣地，當嘗試使用與文化特性相關的語義來轉譯字串比較時，現在會產生更好的例外狀況訊息。 例如，此查詢會嘗試使用 `StringComparison.CurrentCulture` ：

```CSharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

EF Core 現在會擲回下列例外狀況：

> 未處理的例外狀況。 InvalidOperationException： LINQ 運算式的 DbSet <Artist> （）。其中（a =>. Name. Equals （值： "the 獨角獸"，comparisonType： CurrentCulture）） ' 無法轉譯。 其他資訊： ' string ' 的轉譯。不支援採用 ' StringComparison ' 引數的 Equals ' 方法。 如需相關資訊，請參閱 https://go.microsoft.com/fwlink/?linkid=2129535 。 請以可翻譯的形式重寫查詢，或將呼叫插入 Enumerable.asenumerable （）、AsAsyncEnumerable （）、ToList （）或 ToListAsync （），以明確地切換至用戶端評估。 如需相關資訊，請參閱 https://go.microsoft.com/fwlink/?linkid=2101038 。

### <a name="specify-transaction-id"></a>指定交易識別碼

這項功能是由的「社區」提供 [@Marusyk](https://github.com/Marusyk) 。 感謝您的貢獻！

EF Core 會公開交易識別碼，以便在呼叫之間相互關聯。 此識別碼通常會在交易啟動時由 EF Core 設定。 如果應用程式改為啟動交易，則此功能可讓應用程式明確設定交易識別碼，讓它在使用的任何地方都能正確地相互關聯。 例如：

```CSharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a>IPAddress 對應

這項功能是由的「社區」提供 [@ralmsdeveloper](https://github.com/ralmsdeveloper) 。 感謝您的貢獻！

標準 .NET [IPAddress 類別](/dotnet/api/system.net.ipaddress)現在會自動對應至尚未具備原生支援之資料庫的字串資料行。 例如，請考慮對應此實體類型：

```CSharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

在 SQL Server 上，這會導致遷移建立下列資料表：

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
``` 

然後，實體可以透過正常方式新增：

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

### <a name="exclude-onconfiguring-when-scaffolding"></a>在架構時排除 OnConfiguring

從現有的資料庫 scaffold DbCoNtext 時，EF Core 預設會使用連接字串建立 OnConfiguring 多載，以便立即使用內容。 不過，如果您已經有部分類別具有 OnConfiguring，或如果您要以其他方式設定內容，這就不會很有用。

為了解決這個情況，現在可以指示樣板命令省略產生 OnConfiguring。 例如：

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

或在 [套件管理員主控台] 中：

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring 
``` 

請注意，我們建議使用[已命名的連接字串，並保護像是使用者秘密的儲存體](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets)。

### <a name="translations-for-firstordefault-on-strings"></a>字串上 FirstOrDefault 的翻譯

這項功能是由的「社區」提供 [@dvoreckyaa](https://github.com/dvoreckyaa) 。 感謝您的貢獻！

現在會轉譯字串中字元的 FirstOrDefault 和類似運算子。 例如，此 LINQ 查詢：

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

EF Core 現在會以案例區塊產生更佳的查詢。 例如，此 LINQ 查詢： 

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

現在，您可以在 EF 模型中指定資料庫的預設定序。 這會傳送至產生的遷移，以便在建立資料庫時設定定序。 例如：

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

接著，遷移會產生下列內容，以在 SQL Server 上建立資料庫：

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

您也可以指定用於特定資料庫資料行的定序。 例如：

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

對於未使用遷移的人員，定序現在會在 DbCoNtext 架構時，從資料庫進行反向工程。

最後， `EF.Functions.Collate()` 允許使用不同定序的特定查詢。 例如：

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

檔是由問題[#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273)追蹤。

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a>IDesignTimeDbCoNtextFactory 中的流程引數

引數現在會從命令列流動到 `CreateDbContext` [IDesignTimeDbCoNtextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1)的方法中。 例如，若要指出這是開發組建， `dev` 可以在命令列上傳遞自訂引數（例如）：

```
dotnet ef migrations add two --verbose --dev
``` 

這個引數接著會流入處理站，它可以用來控制內容的建立和初始化方式。 例如：

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args) 
        => new SomeDbContext(args.Contains("--dev"));
}
```

檔是由問題[#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419)追蹤。

### <a name="no-tracking-queries-with-identity-resolution"></a>沒有識別解析的追蹤查詢

現在沒有任何追蹤查詢可以設定為執行識別解析。 例如，下列查詢會針對每個貼文建立新的 Blog 實例，即使每個 Blog 都有相同的主要金鑰。 

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

不過，通常會稍微變慢，而且一定會使用更多的記憶體，因此可以變更此查詢，以確保只會建立單一的 Blog 實例：

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

請注意，這只適用于沒有追蹤的查詢，因為所有追蹤查詢都已經呈現此行為。 此外，在遵循 API 審查之後， `PerformIdentityResolution` 語法將會變更。 請參閱[#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073)。

檔是由問題[#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895)追蹤。

### <a name="stored-persisted-computed-columns"></a>儲存的（保存）計算資料行

大部分的資料庫都允許在計算後儲存計算的資料行值。 雖然這會佔用磁碟空間，但計算的資料行只會在 update 上計算一次，而不是每次抓取它的值。 這也可讓某些資料庫的資料行編制索引。

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

### <a name="configure-database-precisionscale-in-model"></a>在模型中設定資料庫精確度/規模

屬性的有效位數和小數位數現在可以使用模型產生器來指定。 例如：

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

精確度和小數位數仍然可以透過完整資料庫類型來設定，例如 "decimal （16，4）"。 

檔是由問題[#527](https://github.com/dotnet/EntityFramework.Docs/issues/527)追蹤。

### <a name="specify-sql-server-index-fill-factor"></a>指定 SQL Server 索引填滿因數

在 SQL Server 上建立索引時，現在可以指定填滿因數。 例如：

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

此查詢將會連同每個相關聯的貼文一起傳回 blog，但只有在貼文標題包含「乳酪」時。

Skip 和 Take 也可以用來減少包含的實體數目。 例如：
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
此查詢將會傳回包含每個 blog 中最多五篇文章的 blog。

如需完整詳細資料，請參閱[包含檔](xref:core/querying/related-data#filtered-include)。

### <a name="new-modelbuilder-api-for-navigation-properties"></a>導覽屬性的新 ModelBuilder API

導覽屬性主要是在[定義關聯](xref:core/modeling/relationships)性時設定。 不過，在 `Navigation` 導覽屬性需要其他設定的情況下，可以使用新的方法。 例如，當依照慣例找不到欄位時，設定導覽的支援欄位：

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

請注意， `Navigation` API 不會取代關聯性設定。 相反地，它允許在已經探索或定義的關聯性中，額外設定導覽屬性。

請參閱設定[導覽屬性檔](xref:core/modeling/relationships#configuring-navigation-properties)。

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a>命名空間和連接字串的新命令列參數 

現在，遷移和架構可讓您在命令列上指定命名空間。 例如，若要對資料庫進行反向工程，將內容和模型類別放在不同的命名空間中： 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

如需完整詳細資料，請參閱[遷移](xref:core/managing-schemas/migrations/index#namespaces)和[還原工程](xref:core/managing-schemas/scaffolding#directories-and-namespaces)檔。

---
此外，連接字串現在可以傳遞至 `database-update` 命令：

```
dotnet ef database update --connection "connection string"
```

如需完整詳細資料，請參閱[工具檔](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update)。

對等的參數也已新增至 VS 套件管理員主控台中使用的 PowerShell 命令。

### <a name="enabledetailederrors-has-returned"></a>EnableDetailedErrors 已傳回

基於效能考慮，從資料庫讀取值時，EF 不會執行額外的 null 檢查。 當遇到未預期的 null 時，這可能會導致難以造成根本原因的例外狀況。

使用 `EnableDetailedErrors` 會在查詢中加入額外的 null 檢查，如此一來，在小型的效能額外負荷下，這些錯誤就會更容易追蹤到根本原因。  

例如：
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

檔是由問題[#955](https://github.com/dotnet/EntityFramework.Docs/issues/955)追蹤。

### <a name="cosmos-partition-keys"></a>Cosmos 分割區索引鍵

您現在可以在查詢中指定要用於給定查詢的分割區索引鍵。 例如：

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

檔是由問題[#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199)追蹤。

### <a name="support-for-the-sql-server-datalength-function"></a>支援 SQL Server DATALENGTH 函數

這可使用新的方法來存取 `EF.Functions.DataLength` 。 例如：
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a>Preview 2

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a>使用 c # 屬性指定屬性支援欄位

C # 屬性現在可以用來指定屬性的支援欄位。 即使無法自動找到支援欄位，此屬性仍然可以讓 EF Core 仍會如往常般寫入和讀取支援欄位。 例如：

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

檔是由問題[#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230)追蹤。

### <a name="complete-discriminator-mapping"></a>完成鑒別子對應

EF Core 針對[繼承階層的 TPH 對應](/ef/core/modeling/inheritance)使用鑒別子資料行。 只要 EF Core 知道鑒別子的所有可能值，就可能會有一些效能增強功能。 EF Core 5.0 現在會實行這些增強功能。

例如，舊版的 EF Core 一律會針對傳回階層中所有類型的查詢產生此 SQL：

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

### <a name="performance-improvements-in-microsoftdatasqlite"></a>Microsoft 中的效能改進。 Sqlite

我們對 SQLIte 進行了兩項效能改善：

* 藉由使用 SqliteBlob 和資料流程，以 GetBytes、GetChars 和 GetTextReader 來抓取二進位和字串資料現在更有效率。
* SqliteConnection 的初始化現在是延遲的。

這些改良功能位於 ADO.NET 的 Microsoft. Sqlite 提供者，因此也會改善 EF Core 之外的效能。

## <a name="preview-1"></a>Preview 1

### <a name="simple-logging"></a>簡單記錄

這項功能會 `Database.Log` 在 EF6 中新增類似的功能。 也就是說，它提供簡單的方法，讓您不需要設定任何類型的外部記錄架構，即可從 EF Core 取得記錄。

[2019 年12月5日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)會包含初稿檔。

其他檔則是由問題[#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085)追蹤。

### <a name="simple-way-to-get-generated-sql"></a>取得產生之 SQL 的簡單方式

EF Core 5.0 引進了 `ToQueryString` 擴充方法，這會傳回在執行 LINQ 查詢時，EF Core 將產生的 SQL。

初稿檔包含在[2020 年1月9日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)。

其他檔則是由問題[#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331)追蹤。

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a>使用 c # 屬性來表示實體沒有索引鍵

現在可以使用新的，將實體類型設定為沒有任何索引鍵 `KeylessAttribute` 。 例如：

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

檔是由問題[#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186)追蹤。

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a>連接或連接字串可以在初始化的 DbCoNtext 上變更

現在您可以更輕鬆地建立 DbCoNtext 實例，而不需要任何連接或連接字串。 此外，連接或連接字串現在可以在內容實例上進行變化。 這項功能可讓相同的內容實例動態連接到不同的資料庫。

檔是由問題[#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075)追蹤。

### <a name="change-tracking-proxies"></a>變更追蹤 proxy

EF Core 現在可以產生自動執行[INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1)和[INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1)的執行時間 proxy。 這些之後，實體屬性上的這些值會直接變更為 EF Core，避免需要掃描變更。 不過，proxy 會有自己的一組限制，因此不適合所有人。

檔是由問題[#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076)追蹤。

### <a name="enhanced-debug-views"></a>增強的調試視圖

「偵錯工具」是一種簡單的方式，可在偵錯工具發生問題時查看 EF Core 的內部。 模型的「偵錯工具」視圖已在一段時間前完成。 針對 EF Core 5.0，我們讓模型視圖更容易閱讀，並在狀態管理員中為追蹤的實體新增新的 [偵錯工具]。

[2019 年12月12日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)會包含初稿檔。

其他檔則是由問題[#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086)追蹤。

### <a name="improved-handling-of-database-null-semantics"></a>改善資料庫 null 語義的處理

關係資料庫通常會將 Null 視為未知的值，因此不等於任何其他 Null。 雖然 c # 會將 null 視為已定義的值，但其比較會等於任何其他 null。 EF Core 預設會轉譯查詢，使其使用 c # null 語義。 EF Core 5.0 大幅提升了這些翻譯的效率。

檔是由問題[#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612)追蹤。

### <a name="indexer-properties"></a>索引子屬性

EF Core 5.0 支援 c # 索引子屬性的對應。 這些屬性可讓實體作為屬性包，其中的資料行會對應到包中的命名屬性。

檔是由問題[#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018)追蹤。

### <a name="generation-of-check-constraints-for-enum-mappings"></a>列舉對應的檢查條件約束產生

EF Core 5.0 遷移現在可以產生 enum 屬性對應的檢查條件約束。 例如：

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

檔是由問題[#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082)追蹤。

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

檔是由問題[#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185)追蹤。

### <a name="cosmos-optimistic-concurrency-with-etags"></a>使用 Etag Cosmos 開放式平行存取

Azure Cosmos DB 資料庫提供者現在支援使用 Etag 的開放式平行存取。 使用 OnModelCreating 中的模型產生器來設定 ETag：

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

接著，SaveChanges 會擲回 `DbUpdateConcurrencyException` 並行衝突，以[處理](https://docs.microsoft.com/ef/core/saving/concurrency)以執行重試等。

檔是由問題[#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099)追蹤。

### <a name="query-translations-for-more-datetime-constructs"></a>更多日期時間結構的查詢翻譯

包含新日期時間結構的查詢現在已轉譯。

此外，現在已對應下列 SQL Server 函式：

* DateDiffWeek
* DateFromParts

例如：

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。

### <a name="query-translations-for-more-byte-array-constructs"></a>更多位元組陣列結構的查詢翻譯

在 byte [] 屬性上使用 Contains、Length、Enumerable.sequenceequal 等的查詢現在會轉譯為 SQL。

[2019 年12月5日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)會包含初稿檔。

其他檔則是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。

### <a name="query-translation-for-reverse"></a>反向的查詢轉譯

使用 `Reverse` 的查詢現在已轉譯。 例如：

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。

### <a name="query-translation-for-bitwise-operators"></a>位運算子的查詢轉譯

使用位運算子的查詢現在會在更多情況下轉譯，例如：

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。

### <a name="query-translation-for-strings-on-cosmos"></a>Cosmos 上字串的查詢轉譯

使用 Azure Cosmos DB 提供者時，現在會轉譯使用字串方法 Contains、StartsWith 和 EndsWith 的查詢。

檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。
