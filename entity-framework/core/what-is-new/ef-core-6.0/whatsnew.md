---
title: EF Core 6.0 的新功能
description: EF Core 6.0 中的新功能總覽
author: ajcvickers
ms.date: 03/08/2021
uid: core/what-is-new/ef-core-6.0/whatsnew
ms.openlocfilehash: 15ab49f60d8831c60e599d8c06b3700aace74bda
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023519"
---
# <a name="whats-new-in-ef-core-60"></a>EF Core 6.0 的新功能

EF Core 6.0 目前正在開發中。 這包含每個預覽中所引進之有趣變更的總覽。

此頁面不會複製 [EF Core 6.0 的方案](xref:core/what-is-new/ef-core-6.0/plan)。 此計畫描述 EF Core 6.0 的整體主題，包括我們在出貨最終發行版本之前打算包含的所有專案。

> [!TIP]
> 您可以 [從 GitHub 下載範例程式碼](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/NewInEFCore6)，以執行並偵測到下列顯示的所有 preview 1 範例。

## <a name="ef-core-60-preview-2"></a>EF Core 6.0 Preview 2

### <a name="preserve-synchronization-context-in-savechangesasync"></a>保留 SaveChangesAsync 中的同步處理內容

GitHub 問題： [#23971](https://github.com/dotnet/efcore/issues/23971)。

我們 [已將5.0 版中的 EF Core 程式碼變更](https://github.com/dotnet/efcore/issues/10164) 為 <xref:System.Threading.Tasks.Task.ConfigureAwait%2A?displayProperty=nameWithType> `false` 在我們非同步程式碼的所有位置中設定為 `await` 。 這通常是 EF Core 使用方式的較佳選擇。 不過，這 <xref:System.Data.Entity.DbContext.SaveChangesAsync%2A> 是特殊案例，因為 EF Core 會在非同步資料庫作業完成之後，將產生的值設定為追蹤的實體。 這些變更可能會觸發通知，例如，可能必須在 U.I. 上執行 執行緒。 因此，我們只會在此方法的 EF Core 6.0 中還原這項變更 <xref:System.Data.Entity.DbContext.SaveChangesAsync%2A> 。

### <a name="translate-stringconcat-with-multiple-arguments"></a>使用多個引數來轉譯 String. Concat

GitHub 問題： [#23859](https://github.com/dotnet/efcore/issues/23859)。 這項功能是由所貢獻 [@wmeints](https://github.com/wmeints) 。

從 EF Core 6.0 開始， <xref:System.String.Concat%2A?displayProperty=nameWithType> 具有多個引數的呼叫現在會轉譯為 SQL。 例如，下列查詢：

<!--
        var shards = context.Shards
            .Where(e => string.Concat(e.Token1, e.Token2, e.Token3) != e.TokensProcessed).ToList();
-->
[!code-csharp[StringConcat](../../../../samples/core/Miscellaneous/NewInEFCore6/StringConcatSample.cs?name=StringConcat)]

使用 SQL Server 時，將會轉譯為下列 SQL：

```sql
SELECT [s].[Id], [s].[Token1], [s].[Token2], [s].[Token3], [s].[TokensProcessed]
FROM [Shards] AS [s]
WHERE ((COALESCE([s].[Token1], N'') + (COALESCE([s].[Token2], N'') + COALESCE([s].[Token3], N''))) <> [s].[TokensProcessed]) OR [s].[TokensProcessed] IS NULL
```

### <a name="smoother-integration-with-systemlinqasync"></a>與 System.object 的整合更加順暢

GitHub 問題： [#24041](https://github.com/dotnet/efcore/issues/24041)。

System.string [封裝會](https://www.nuget.org/packages/System.Linq.Async/) 加入用戶端非同步 Linq 處理。 將此套件與舊版 EF Core 搭配使用，會因為非同步 LINQ 方法的命名空間衝突而繁瑣。 在 EF Core 6.0 中，我們已利用 c # 模式比對，讓 <xref:System.Collections.Generic.IAsyncEnumerable%601> 公開的 EF Core 不 <xref:Microsoft.EntityFrameworkCore.DbSet%601> 需要直接執行介面。

請注意，大部分的應用程式不需要使用 system.string，因為 EF Core 查詢通常會在伺服器上完整轉譯。

### <a name="more-flexible-free-text-search"></a>更具彈性的自由文字搜尋

GitHub 問題： [#23921](https://github.com/dotnet/efcore/issues/23921)。

在 EF Core 6.0 中，我們已放寬和的參數 <xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.FreeText(Microsoft.EntityFrameworkCore.DbFunctions,System.String,System.String)> 需求 <xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.Contains%2A> 。 這可讓這些函式搭配二進位資料行使用，或與使用值轉換器對應的資料行搭配使用。 例如，假設某個實體類型的 `Name` 屬性定義為值物件：

<!--
    public class Customer
    {
        public int Id { get; set; }

        public Name Name{ get; set; }
    }

    public class Name
    {
        public string First { get; set; }
        public string MiddleInitial { get; set; }
        public string Last { get; set; }
    }
-->
[!code-csharp[EntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/ContainsFreeTextSample.cs?name=EntityType)]

這會對應到資料庫中的 JSON：

<!--
            modelBuilder.Entity<Customer>()
                .Property(e => e.Name)
                .HasConversion(
                    v => JsonSerializer.Serialize(v, null),
                    v => JsonSerializer.Deserialize<Name>(v, null));
-->
[!code-csharp[ConfigureCompositeValueObject](../../../../samples/core/Miscellaneous/NewInEFCore6/ContainsFreeTextSample.cs?name=ConfigureCompositeValueObject)]

您現在可以使用 `Contains` 或， `FreeText` 即使屬性的類型不是，也可以執行 `Name` 查詢 `string` 。 例如：

<!--
        var result = context.Customers.Where(e => EF.Functions.Contains(e.Name, "Martin")).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/ContainsFreeTextSample.cs?name=Query)]

使用 SQL Server 時，這會產生下列 SQL：

```sql
SELECT [c].[Id], [c].[Name]
FROM [Customers] AS [c]
WHERE CONTAINS([c].[Name], N'Martin')
```

## <a name="ef-core-60-preview-1"></a>EF Core 6.0 Preview 1

### <a name="unicodeattribute"></a>UnicodeAttribute

GitHub 問題： [#19794](https://github.com/dotnet/efcore/issues/19794)。 這項功能是由所貢獻 [@RaymondHuy](https://github.com/RaymondHuy) 。

從 EF Core 6.0 開始，您現在可以使用對應屬性，將字串屬性對應到非 Unicode 資料行， _而不需要直接指定資料庫類型_。 例如，假設有一個 `Book` 實體類型具有 [國際標準書籍編號的屬性， (isbn) ](https://en.wikipedia.org/wiki/International_Standard_Book_Number) 格式為 "isbn 978-3-16-148410-0"：

<!--
    public class Book
    {
        public int Id { get; set; }
        public string Title { get; set; }

        [Unicode(false)]
        [MaxLength(22)]
        public string Isbn { get; set; }
    }
-->
[!code-csharp[BookEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/UnicodeAttributeSample.cs?name=BookEntityType)]

由於 ISBNs 不能包含任何非 unicode 字元，因此 `Unicode` 屬性會導致使用非 unicode 字串類型。 此外， `MaxLength` 也會用來限制資料庫資料行的大小。 例如，使用 SQL Server 時，這會產生的資料庫資料行 `varchar(22)` ：

```sql
CREATE TABLE [Book] (
    [Id] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Isbn] varchar(22) NULL,
    CONSTRAINT [PK_Book] PRIMARY KEY ([Id]));
```

> [!NOTE]
> EF Core 預設會將字串屬性對應至 Unicode 資料行。 `UnicodeAttribute` 當資料庫系統只支援 Unicode 類型時，會忽略。

### <a name="precisionattribute"></a>PrecisionAttribute

GitHub 問題： [#17914](https://github.com/dotnet/efcore/issues/17914)。 這項功能是由所貢獻 [@RaymondHuy](https://github.com/RaymondHuy) 。

您現在可以使用對應屬性來設定資料庫資料行的有效位數和小數位數， _而不需要直接指定資料庫類型_。 例如，假設有一個 `Product` 具有 decimal 屬性的實體類型 `Price` ：

<!--
    public class Product
    {
        public int Id { get; set; }

        [Precision(precision: 10, scale: 2)]
        public decimal Price { get; set; }
    }
-->
[!code-csharp[ProductEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/PrecisionAttributeSample.cs?name=ProductEntityType)]

EF Core 會將此屬性對應至精確度為10且小數位數為2的資料庫資料行。 例如，在 SQL Server 上：

```sql
CREATE TABLE [Product] (
    [Id] int NOT NULL IDENTITY,
    [Price] decimal(10,2) NOT NULL,
    CONSTRAINT [PK_Product] PRIMARY KEY ([Id]));
```

### <a name="entitytypeconfigurationattribute"></a>EntityTypeConfigurationAttribute

GitHub 問題： [#23163](https://github.com/dotnet/efcore/issues/23163)。 這項功能是由所貢獻 [@KaloyanIT](https://github.com/KaloyanIT) 。

<xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> 實例可讓 <xref:Microsoft.EntityFrameworkCore.ModelBuilder> 每個實體類型的設定都包含在自己的設定類別中。 例如：

<!--
public class BookConfiguration : IEntityTypeConfiguration<Book>
{
    public void Configure(EntityTypeBuilder<Book> builder)
    {
        builder
            .Property(e => e.Isbn)
            .IsUnicode(false)
            .HasMaxLength(22);
    }
}
-->
[!code-csharp[BookConfiguration](../../../../samples/core/Miscellaneous/NewInEFCore6/EntityTypeConfigurationAttributeSample.cs?name=BookConfiguration)]

一般來說，此設定類別必須具現化，並從中呼叫 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> 。 例如：

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    new BookConfiguration().Configure(modelBuilder.Entity<Book>());
}
```

從 EF Core 6.0 開始， `EntityTypeConfigurationAttribute` 可以在實體類型上放置，讓 EF Core 可以尋找並使用適當的設定。 例如：

<!--
[EntityTypeConfiguration(typeof(BookConfiguration))]
public class Book
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Isbn { get; set; }
}
-->
[!code-csharp[BookEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/EntityTypeConfigurationAttributeSample.cs?name=BookEntityType)]

這個屬性工作表示 `IEntityTypeConfiguration` 當 `Book` 模型中包含實體類型時，EF Core 將會使用指定的實作為。 實體類型會包含在模型中，使用其中一個一般機制。 例如，藉由建立 <xref:Microsoft.EntityFrameworkCore.DbSet%601> 實體類型的屬性：

<!--
public class BooksContext : DbContext
{
    public DbSet<Book> Books { get; set; }

    //...
-->
[!code-csharp[DbContext](../../../../samples/core/Miscellaneous/NewInEFCore6/EntityTypeConfigurationAttributeSample.cs?name=DbContext)]

或者，在中註冊它 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> ：

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Book>();
}
```

> [!NOTE]
> `EntityTypeConfigurationAttribute` 元件中將不會自動探索類型。 實體類型必須加入至模型，然後才會在該實體類型上探索屬性。

### <a name="translate-tostring-on-sqlite"></a>在 SQLite 上轉譯 ToString

GitHub 問題： [#17223](https://github.com/dotnet/efcore/issues/17223)。 這項功能是由所貢獻 [@ralmsdeveloper](https://github.com/ralmsdeveloper) 。

<xref:System.Object.ToString>使用 SQLite 資料庫提供者時，的呼叫現在會轉譯為 SQL。 這適用于涉及非字串資料行的文字搜尋。 例如，假設將 `User` 電話號碼儲存為數值的實體類型：

<!--
    public class User
    {
        public int Id { get; set; }
        public string Username { get; set; }
        public long PhoneNumber { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/ToStringTranslationSample.cs?name=UserEntityType)]

`ToString` 可以用來將數位轉換成資料庫中的字串。 接著，我們可以使用此字串搭配函式（例如） `LIKE` 來尋找符合模式的數位。 例如，若要尋找包含555的所有數位：

<!--
var users = context.Users.Where(u => EF.Functions.Like(u.PhoneNumber.ToString(), "%555%")).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/ToStringTranslationSample.cs?name=Query)]

使用 SQLite 資料庫時，這會轉譯成下列 SQL：

```sql
SELECT COUNT(*)
FROM "Users" AS "u"
WHERE CAST("u"."PhoneNumber" AS TEXT) LIKE '%555%'
```

請注意， <xref:System.Object.ToString> EF Core 5.0 已支援 FOR SQL Server 的翻譯，其他資料庫提供者也可能支援此功能。

### <a name="effunctionsrandom"></a>英 孚。函數。 Random

GitHub 問題： [#16141](https://github.com/dotnet/efcore/issues/16141)。 這項功能是由所貢獻 [@RaymondHuy](https://github.com/RaymondHuy) 。

`EF.Functions.Random` 對應至資料庫函式，其會傳回介於0和1之間的虛擬亂數。 適用于 SQL Server、SQLite 和 Cosmos 的 EF Core 存放庫中已執行翻譯。 例如，假設有一個 `User` 具有屬性的實體類型 `Popularity` ：

<!--
    public class User
    {
        public int Id { get; set; }
        public string Username { get; set; }
        public int Popularity { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/RandomFunctionSample.cs?name=UserEntityType)]

`Popularity` 可以有1到5（含）的值。 使用 `EF.Functions.Random` 我們可以撰寫查詢，以傳回具有隨機播放熱門程度的所有使用者：

<!--
var users = context.Users.Where(u => u.Popularity == (int)(EF.Functions.Random() * 5.0) + 1).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/RandomFunctionSample.cs?name=Query)]

使用 SQL Server 資料庫時，這會轉換成下列 SQL：

```sql
SELECT [u].[Id], [u].[Popularity], [u].[Username]
FROM [Users] AS [u]
WHERE [u].[Popularity] = (CAST((RAND() * 5.0E0) AS int) + 1)
```

### <a name="support-for-sql-server-sparse-columns"></a>支援 SQL Server sparse 資料行

GitHub 問題： [#8023](https://github.com/dotnet/efcore/issues/8023)。

SQL Server [稀疏資料行](/sql/relational-databases/tables/use-sparse-columns) 是經過優化以儲存 null 值的一般資料行。 這在使用 [TPH 繼承對應](xref:core/modeling/inheritance) 時很有用，因為很少使用的子類型屬性會導致資料表中大部分資料列的 null 資料行值。 例如，假設有一個 `ForumModerator` 延伸自的類別 `ForumUser` ：

<!--
    public class ForumUser
    {
        public int Id { get; set; }
        public string Username { get; set; }
    }

    public class ForumModerator : ForumUser
    {
        public string ForumName { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/SparseColumnsSample.cs?name=UserEntityType)]

可能有數百萬個使用者，只有少數的使用者是仲裁者。 這表示在這裡將對應 `ForumName` 為稀疏可能有意義。 這現在可以使用 `IsSparse` 中的來設定 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> 。 例如：

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<ForumModerator>()
                .Property(e => e.ForumName)
                .IsSparse();
        }
-->
[!code-csharp[OnModelCreating](../../../../samples/core/Miscellaneous/NewInEFCore6/SparseColumnsSample.cs?name=OnModelCreating)]

EF Core 遷移會將資料行標示為「稀疏」。 例如：

```sql
CREATE TABLE [ForumUser] (
    [Id] int NOT NULL IDENTITY,
    [Username] nvarchar(max) NULL,
    [Discriminator] nvarchar(max) NOT NULL,
    [ForumName] nvarchar(max) SPARSE NULL,
    CONSTRAINT [PK_ForumUser] PRIMARY KEY ([Id]));
```

> [!NOTE]
> 稀疏資料行有限制。 請務必閱讀 [SQL Server sparse 資料行檔](/sql/relational-databases/tables/use-sparse-columns) ，以確保稀疏資料行是您案例的正確選擇。

### <a name="in-memory-database-validate-required-properties-are-not-null"></a>記憶體內部資料庫：驗證必要的屬性不是 null

GitHub 問題： [#10613](https://github.com/dotnet/efcore/issues/10613)。 這項功能是由所貢獻 [@fagnercarvalho](https://github.com/fagnercarvalho) 。

如果嘗試針對標示為必要的屬性儲存 null 值，則 EF Core 記憶體內部資料庫現在會擲回例外狀況。 例如，假設有一個 `User` 具有必要屬性的型別 `Username` ：

<!--
    public class User
    {
        public int Id { get; set; }

        [Required]
        public string Username { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/InMemoryRequiredPropertiesSample.cs?name=UserEntityType)]

嘗試以 null 儲存實體 `Username` 將會導致下列例外狀況：

> Microsoft.entityframeworkcore. 就 dbupdateexception：具有索引鍵值 ' {Id： 1} ' 的實體類型 ' User ' 實例缺少必要屬性 ' {' Username '} '。

如有必要，可以停用此驗證。 例如：

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder
                .LogTo(Console.WriteLine, new[] { InMemoryEventId.ChangesSaved })
                .UseInMemoryDatabase("UserContextWithNullCheckingDisabled")
                .EnableNullabilityCheck(false);
        }
-->
[!code-csharp[OnConfiguring](../../../../samples/core/Miscellaneous/NewInEFCore6/InMemoryRequiredPropertiesSample.cs?name=OnConfiguring)]

### <a name="improved-sql-server-translation-for-isnullorwhitespace"></a>改進適用于 IsNullOrWhitespace 的 SQL Server 轉譯

GitHub 問題： [#22916](https://github.com/dotnet/efcore/issues/22916)。 這項功能是由所貢獻 [@Marusyk](https://github.com/Marusyk) 。

請考慮下列查詢：

<!--
        var users = context.Users.Where(
            e => string.IsNullOrWhiteSpace(e.FirstName)
                 || string.IsNullOrWhiteSpace(e.LastName)).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/IsNullOrWhitespaceSample.cs?name=Query)]

在 EF Core 6.0 之前，這會在 SQL Server 上轉譯為下列內容：

```sql
SELECT [u].[Id], [u].[FirstName], [u].[LastName]
FROM [Users] AS [u]
WHERE ([u].[FirstName] IS NULL OR (LTRIM(RTRIM([u].[FirstName])) = N'')) OR ([u].[LastName] IS NULL OR (LTRIM(RTRIM([u].[LastName])) = N''))
```

EF Core 6.0 的這種轉譯已經過改善，可讓您：

```sql
SELECT [u].[Id], [u].[FirstName], [u].[LastName]
FROM [Users] AS [u]
WHERE ([u].[FirstName] IS NULL OR ([u].[FirstName] = N'')) OR ([u].[LastName] IS NULL OR ([u].[LastName] = N''))
```

### <a name="database-comments-are-scaffolded-to-code-comments"></a>資料庫批註 scaffold 至程式碼批註

GitHub 問題： [#19113](https://github.com/dotnet/efcore/issues/19113)。 這項功能是由所貢獻 [@ErikEJ](https://github.com/ErikEJ) 。

SQL 資料表和資料行的批註現在 scaffold 至從現有 SQL Server 資料庫 [對 EF Core 模型進行反向工程](xref:core/managing-schemas/scaffolding) 時所建立的實體類型。 例如：

```csharp
/// <summary>
/// The Blog table.
/// </summary>
public partial class Blog
{
    /// <summary>
    /// The primary key.
    /// </summary>
    [Key]
    public int Id { get; set; }
}
```

## <a name="microsoftdatasqlite-60-preview-1"></a>Microsoft. Sqlite 6.0 Preview 1

> [!TIP]
> 您可以 [從 GitHub 下載範例程式碼](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/NewInEFCore6)，以執行並偵測到下列顯示的所有 preview 1 範例。

### <a name="savepoints-api"></a>儲存點 API

GitHub 問題： [#20228](https://github.com/dotnet/efcore/issues/20228)。

我們已在 [ADO.NET 提供者中對儲存點的通用 API 進行](https://github.com/dotnet/runtime/issues/33397)標準化。 Microsoft Data Sqlite 現在支援此 API，包括：

- <xref:System.Data.Common.DbTransaction.Save(System.String)> 在交易中建立儲存點
- <xref:System.Data.Common.DbTransaction.Rollback(System.String)> 復原至上一個儲存點
- <xref:System.Data.Common.DbTransaction.Release(System.String)> 釋放儲存點

使用儲存點可讓交易的一部分回復，而不會回復整個交易。 例如，下列程式碼：

- 建立交易
- 將更新傳送至資料庫
- 建立儲存點
- 將其他更新傳送至資料庫
- 復原至先前建立的儲存點
- 認可交易

<!--
        using var connection = new SqliteConnection("DataSource=test.db");
        connection.Open();

        using var transaction = connection.BeginTransaction();

        using (var command = connection.CreateCommand())
        {
            command.CommandText = @"UPDATE Users SET Username = 'ajcvickers' WHERE Id = 1";
            command.ExecuteNonQuery();
        }

        transaction.Save("MySavepoint");

        using (var command = connection.CreateCommand())
        {
            command.CommandText = @"UPDATE Users SET Username = 'wfvickers' WHERE Id = 2";
            command.ExecuteNonQuery();
        }

        transaction.Rollback("MySavepoint");

        transaction.Commit();
-->
[!code-csharp[PerformUpdates](../../../../samples/core/Miscellaneous/NewInEFCore6/SqliteSamples.cs?name=PerformUpdates)]

這會導致第一個更新認可至資料庫，而第二個更新則不會認可，因為儲存點是在認可交易之前復原。

### <a name="command-timeout-in-the-connection-string"></a>連接字串中的命令逾時

GitHub 問題： [#22505](https://github.com/dotnet/efcore/issues/22505)。 這項功能是由所貢獻 [@nmichels](https://github.com/nmichels) 。

ADO.NET 提供者支援兩種不同的超時：

- 連接逾時值，決定建立資料庫連接時等待的最長時間。
- 命令逾時，決定等候命令完成執行的最長時間。

您可以使用程式碼設定命令逾時 <xref:System.Data.Common.DbCommand.CommandTimeout?displayProperty=nameWithType> 。 許多提供者現在也會在連接字串中公開此命令逾時。 請使用 `Command Timeout` 連接字串關鍵字來遵循此趨勢。 例如， `"Command Timeout=60;DataSource=test.db"` 會使用60秒作為連接所建立命令的預設超時。

> [!TIP]
> Sqlite `Default Timeout` 會視為的同義字 `Command Timeout` ，因此可以改為使用（如果偏好的話）。
