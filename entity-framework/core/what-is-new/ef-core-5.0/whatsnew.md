---
title: 5.0 EF Core 的新功能
description: EF Core 5.0 中的新功能總覽
author: ajcvickers
ms.date: 03/30/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: c902988920e3b1a6039808fe0658fc19dee2728a
ms.sourcegitcommit: 387cbd8109c0fc5ce6bdc85d0dec1aed72ad4c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103070"
---
# <a name="whats-new-in-ef-core-50"></a>5.0 EF Core 的新功能

EF Core 5.0 目前正在開發中。
此頁面將包含每個預覽中所引進之有趣變更的總覽。

此頁面不會複製[EF Core 5.0 的計畫](plan.md)。
此計畫描述 EF Core 5.0 的整體主題，包括我們打算在交付最終版本之前包含的所有專案。

我們會將這裡的連結新增至正式檔，因為它已發佈。

## <a name="preview-3"></a>Preview 3

### <a name="filtered-include"></a>篩選的包含

Include 方法現在支援篩選包含的實體。
例如：

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

此查詢將會連同每個相關聯的貼文一起傳回 blog，但只有在貼文標題包含「乳酪」時。

Skip 和 Take 也可以用來減少包含的實體數目。
例如：
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
此查詢將會傳回包含每個 blog 中最多五篇文章的 blog。

如需完整詳細資料，請參閱[包含檔](xref:core/querying/related-data#filtered-include)。

### <a name="new-modelbuilder-api-for-navigation-properties"></a>導覽屬性的新 ModelBuilder API

導覽屬性主要是在[定義關聯](xref:core/modeling/relationships)性時設定。
不過，在導覽`Navigation`屬性需要其他設定的情況下，可以使用新的方法。
例如，當依照慣例找不到欄位時，設定導覽的支援欄位：

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

請注意， `Navigation` API 不會取代關聯性設定。
相反地，它允許在已經探索或定義的關聯性中，額外設定導覽屬性。

檔是由問題[#2302](https://github.com/dotnet/EntityFramework.Docs/issues/2302)追蹤。

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a>命名空間和連接字串的新命令列參數 

現在，遷移和架構可讓您在命令列上指定命名空間。
例如，若要對資料庫進行反向工程，將內容和模型類別放在不同的命名空間中： 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

此外，連接字串現在可以傳遞至`database-update`命令：

```
dotnet ef database update --connection "connection string"
```

對等的參數也已新增至 VS 套件管理員主控台中使用的 PowerShell 命令。

檔是由問題[#2303](https://github.com/dotnet/EntityFramework.Docs/issues/2303)追蹤。

### <a name="enabledetailederrors-has-returned"></a>EnableDetailedErrors 已傳回

基於效能考慮，從資料庫讀取值時，EF 不會執行額外的 null 檢查。
當遇到未預期的 null 時，這可能會導致難以造成根本原因的例外狀況。

使用`EnableDetailedErrors`會在查詢中加入額外的 null 檢查，如此一來，在小型的效能額外負荷下，這些錯誤就會更容易追蹤到根本原因。  

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

您現在可以在查詢中指定要用於給定查詢的分割區索引鍵。
例如：

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

檔是由問題[#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199)追蹤。

### <a name="support-for-the-sql-server-datalength-function"></a>支援 SQL Server DATALENGTH 函數

這可使用新`EF.Functions.DataLength`的方法來存取。
例如：
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a>Preview 2

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a>使用 c # 屬性指定屬性支援欄位

C # 屬性現在可以用來指定屬性的支援欄位。
即使無法自動找到支援欄位，此屬性仍然可以讓 EF Core 仍會如往常般寫入和讀取支援欄位。
例如：

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

EF Core 針對[繼承階層的 TPH 對應](/ef/core/modeling/inheritance)使用鑒別子資料行。
只要 EF Core 知道鑒別子的所有可能值，就可能會有一些效能增強功能。
EF Core 5.0 現在會實行這些增強功能。

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

這項功能會`Database.Log`在 EF6 中新增類似的功能。
也就是說，它提供簡單的方法，讓您不需要設定任何類型的外部記錄架構，即可從 EF Core 取得記錄。

[2019 年12月5日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)會包含初稿檔。

其他檔則是由問題[#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085)追蹤。

### <a name="simple-way-to-get-generated-sql"></a>取得產生之 SQL 的簡單方式

EF Core 5.0 引進了`ToQueryString`擴充方法，這會傳回在執行 LINQ 查詢時，EF Core 將產生的 SQL。

初稿檔包含在[2020 年1月9日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)。

其他檔則是由問題[#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331)追蹤。

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a>使用 c # 屬性來表示實體沒有索引鍵

現在可以使用新`KeylessAttribute`的，將實體類型設定為沒有任何索引鍵。
例如：

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

現在您可以更輕鬆地建立 DbCoNtext 實例，而不需要任何連接或連接字串。
此外，連接或連接字串現在可以在內容實例上進行變化。
這項功能可讓相同的內容實例動態連接到不同的資料庫。

檔是由問題[#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075)追蹤。

### <a name="change-tracking-proxies"></a>變更追蹤 proxy

EF Core 現在可以產生自動執行[INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1)和[INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1)的執行時間 proxy。
這些之後，實體屬性上的這些值會直接變更為 EF Core，避免需要掃描變更。
不過，proxy 會有自己的一組限制，因此不適合所有人。

檔是由問題[#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076)追蹤。

### <a name="enhanced-debug-views"></a>增強的調試視圖

「偵錯工具」是一種簡單的方式，可在偵錯工具發生問題時查看 EF Core 的內部。
模型的「偵錯工具」視圖已在一段時間前完成。
針對 EF Core 5.0，我們讓模型視圖更容易閱讀，並在狀態管理員中為追蹤的實體新增新的 [偵錯工具]。

[2019 年12月12日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)會包含初稿檔。

其他檔則是由問題[#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086)追蹤。

### <a name="improved-handling-of-database-null-semantics"></a>改善資料庫 null 語義的處理

關係資料庫通常會將 Null 視為未知的值，因此不等於任何其他 Null。
雖然 c # 會將 null 視為已定義的值，但其比較會等於任何其他 null。
EF Core 預設會轉譯查詢，使其使用 c # null 語義。
EF Core 5.0 大幅提升了這些翻譯的效率。

檔是由問題[#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612)追蹤。

### <a name="indexer-properties"></a>索引子屬性

EF Core 5.0 支援 c # 索引子屬性的對應。
這些屬性可讓實體作為屬性包，其中的資料行會對應到包中的命名屬性。

檔是由問題[#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018)追蹤。

### <a name="generation-of-check-constraints-for-enum-mappings"></a>列舉對應的檢查條件約束產生

EF Core 5.0 遷移現在可以產生 enum 屬性對應的檢查條件約束。
例如：

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

檔是由問題[#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082)追蹤。

### <a name="isrelational"></a>IsRelational

除了現有`IsRelational` `IsSqlServer`的、和`IsSqlite` `IsInMemory`之外，還加入了新的方法。
這個方法可以用來測試 DbCoNtext 是否使用任何關係資料庫提供者。
例如：

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

Azure Cosmos DB 資料庫提供者現在支援使用 Etag 的開放式平行存取。
使用 OnModelCreating 中的模型產生器來設定 ETag：

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

接著，SaveChanges 會擲`DbUpdateConcurrencyException`回並行衝突，以[處理](https://docs.microsoft.com/ef/core/saving/concurrency)以執行重試等。

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

使用`Reverse`的查詢現在已轉譯。
例如：

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
