---
title: Entity Framework Core 3.0 中的新功能 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: 2EBE2CCC-E52D-483F-834C-8877F5EB0C0C
uid: core/what-is-new/ef-core-3.0/index
ms.openlocfilehash: ccfb8259c70cf8706a06eb3b22b9541224c3b9bb
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182077"
---
# <a name="new-features-in-entity-framework-core-30"></a>Entity Framework Core 3.0 中的新功能

下列清單包含 EF Core 3.0 中的主要新功能。

作為主要版本，EF Core 3.0 也包含數個[中斷性變更](xref:core/what-is-new/ef-core-3.0/breaking-changes)，也就是可能對現有應用程式造成負面影響的 API 改進。  

## <a name="linq-overhaul"></a>LINQ 檢修 

LINQ 可讓您以偏好的 .NET 語言撰寫資料庫查詢，進而利用豐富的型別資訊優勢來提供 IntelliSense 與編譯時間型別檢查。
但是 LINQ 也可以讓您撰寫不限數目的複雜查詢，其中包含任意運算式 (方法呼叫或作業)。
如何處理所有這些組合是 LINQ 提供者的主要挑戰。

在 EF Core 3.0 中，我們重新架構了 LINQ 提供者，讓您能夠將更多查詢模式轉譯為 SQL、在更多情況下產生有效率的查詢，以及防止無法偵測到無效率的查詢。 以新的 LINQ 提供者為基礎，我們將在未來的版本中提供新的查詢功能和效能改進，而不會中斷現有的應用程式和資料提供者。

### <a name="restricted-client-evaluation"></a>受限制的用戶端評估
最重要的設計變更，就是如何處理無法轉換成參數或轉譯為 SQL 的 LINQ 運算式。

在舊版中，EF Core 會識別查詢的哪些部分可以轉譯成 SQL，並在用戶端上執行查詢的其餘部分。
在某些情況下，這種類型的用戶端執行是必要的，但在其他許多情況下，它可能會導致無效率的查詢。

例如，如果 EF Core 2.2 無法轉譯 `Where()` 呼叫中的述詞，它會不使用篩選執行 SQL 陳述式、從資料庫傳輸所有資料列，然後在記憶體內進行篩選：

``` csharp
var specialCustomers = 
  context.Customers
    .Where(c => c.Name.StartsWith(n) && IsSpecialCustomer(c));
```

如果資料庫包含少量的資料列，那是可接受的，但是如果資料庫包含大量資料列，則可能導致明顯的效能問題，甚至導致應用程式失敗。

在 EF Core 3.0 中，我們已將用戶端評估限制為僅適用於最上層投影 (基本上是 `Select()` 的最後一次呼叫)。
當 EF Core 3.0 偵測到無法在查詢中的任何其他位置轉譯的運算式時，它會擲回執行階段例外狀況。

若要在用戶端上評估述詞條件，如先前範例所示，開發人員現在必須將查詢的評估明確切換為 LINQ to Objects： 

``` csharp
var specialCustomers =
  context.Customers
    .Where(c => c.Name.StartsWith(n)) 
    .AsEnumerable() // switches to LINQ to Objects
    .Where(c => IsSpecialCustomer(c));
```

如需有關這會如何影響現有應用程式的詳細資訊，請參閱[中斷性變更文件](xref:core/what-is-new/ef-core-3.0/breaking-changes#linq-queries-are-no-longer-evaluated-on-the-client)。

### <a name="single-sql-statement-per-linq-query"></a>每個 LINQ 查詢單一 SQL 陳述式

在 3.0 中，另一個大幅變更的設計層面，就是我們現在一律會針對每個 LINQ 查詢產生單一 SQL 陳述式。 在舊版中，我們通常會在某些情況下產生多個 SQL 陳述式，例如轉譯集合導覽屬性的 `Include()` 呼叫，以及轉譯遵循具有子查詢之特定模式的查詢。 雖然這在某些情況下很方便，而且對於 `Include()` 來說甚至可以協助避免透過網路傳送重複的資料，但實作起來很複雜，導致某些效率極低的行為 (N+1 個查詢)，且在某些情況下，跨多個查詢傳回的資料可能不一致。

與用戶端評估類似，如果 EF Core 3.0 無法將 LINQ 查詢轉譯為單一 SQL 陳述式，則會擲回執行階段例外狀況。 但是，我們使 EF Core 能夠將許多用來產生多個查詢的常見模式，轉譯成含有聯結的單一查詢。

## <a name="cosmos-db-support"></a>Cosmos DB 支援 

適用於 EF Core 的 Cosmos DB 提供者可讓開發人員熟悉 EF 程式設計模型，以輕鬆地使用 Azure Cosmos DB 作為應用程式資料庫。 目標是讓 .NET 開發人員能更輕鬆地發揮 Cosmos DB 的一些優點，例如全域散發、"Always On" 可用性、彈性的延展性，以及低延遲。 提供者會啟用大部分的 EF Core 功能，例如自動變更追蹤、LINQ 與值轉換 (根據 Cosmos DB 中的 SQL API)。

如需詳細資訊，請參閱 [Cosmos DB 提供者文件](xref:core/providers/cosmos/index)。

## <a name="c-80-support"></a>C# 8.0 支援

EF Core 3.0 會利用幾個 [C# 8.0 中的新功能](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8) \(部分機器翻譯\)：

### <a name="asynchronous-streams"></a>非同步資料流

非同步查詢結果現在會使用新的標準 `IAsyncEnumerable<T>` 介面公開，並可使用 `await foreach` 加以取用。

``` csharp
var orders = 
  from o in context.Orders
  where o.Status == OrderStatus.Pending
  select o;

await foreach(var o in orders.AsAsyncEnumerable())
{
  Process(o);
} 
```

如需詳細資訊，請參閱 [C# 文件中的非同步資料流](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8#asynchronous-streams) \(部分機器翻譯\)。

### <a name="nullable-reference-types"></a>可為 Null 的參考型別 

當您的程式碼中啟用這個新功能時，EF Core 會檢查參考型別屬性的可為 Null 性，並將它套用至資料庫中對應的資料行和關聯性：不可為 Null 之參考型別的屬性會被視為具有 `[Required]` 資料註解屬性。

例如，在下列類別中，標示為型別 `string?` 的屬性會設定為選擇性，而 `string` 會設定為必要項：

``` csharp
public class Customer
{
  public int Id { get; set; }
  public string FirstName { get; set; }
  public string LastName { get; set; }
  public string? MiddleName { get; set; }
}
```

如需詳細資訊，請參閱 EF Core 文件中的[使用可為 Null 的參考型別](xref:core/miscellaneous/nullable-reference-types)。

## <a name="interception-of-database-operations"></a>資料庫作業攔截

EF Core 3.0 中的新攔截 API 允許提供發生低層級資料庫作業 (在 EF Core 正常作業期間) 時，可自動叫用的自訂邏輯。 例如，開啟連線、認可交易或執行命令時。 

與 EF 6 中已存在的攔截功能類似，攔截器可讓您在作業發生之前或之後進行攔截。 如果在它們發生之前對其進行攔截，則可以跳過執行，並提供來自攔截邏輯的替代結果。 

例如，若要操作命令文字，您可以建立 `IDbCommandInterceptor`：

``` csharp 
public class HintCommandInterceptor : DbCommandInterceptor
{
  public override InterceptionResult ReaderExecuting(
    DbCommand command, 
    CommandEventData eventData, 
    InterceptionResult result)
  {
    // Manipulate the command text, etc. here...
    command.CommandText += " OPTION (OPTIMIZE FOR UNKNOWN)";
    return result;
  }
}
``` 

並使用您的  `DbContext` 註冊它：

``` csharp
services.AddDbContext(b => b
  .UseSqlServer(connectionString)
  .AddInterceptors(new HintCommandInterceptor()));
```

## <a name="reverse-engineering-of-database-views"></a>資料庫檢視的反向工程

查詢類型 (代表可從資料庫讀取但未更新的資料) 已重新命名為[無索引鍵實體類型](xref:core/modeling/keyless-entity-types)。 因為它們很適合在大部分的案例中用於對應資料庫檢視，所以 EF Core 現在會在反向工程資料庫檢視時，自動建立無索引鍵實體類型。

例如，您可以使用 [dotnet ef 命令列工具](xref:core/miscellaneous/cli/dotnet)來輸入：

``` console
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

而此工具現在會自動針對不含索引鍵之檢視與資料表的類型建立結構：

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
  modelBuilder.Entity<Names>(entity =>
  {
    entity.HasNoKey();
    entity.ToView("Names");
  });

  modelBuilder.Entity<Things>(entity =>
  {
    entity.HasNoKey();
  });
}
```

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a>現在可選用以主體來共用資料表的相依實體

從 EF Core 3.0 開始，如果 `OrderDetails` 由 `Order` 所擁有，或明確地對應到相同的資料表，即可新增 `Order` 而無需 `OrderDetails` 及所有 `OrderDetails` 屬性，但主索引鍵將會對應至可為 Null 的資料行。

進行查詢時，如果任何其必要的屬性不具有值，或如果其具有主索引鍵以外的非必要屬性，且所有屬性皆為 `null`，則 EF Core 會將 `OrderDetails` 設定為 `null`。

``` csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

[Owned]
public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```

## <a name="ef-63-on-net-core"></a>.NET Core 上的 EF 6.3

這不是 EF Core 3.0 的功能，但我們認為這對我們目前的客戶而言很重要。 

我們了解許多現有的應用程式都使用舊版 EF，而且因為要獲得 .NET Core 的優點而將它們移植到 EF Core 需要非常多的精力。 因此，我們決定移植最新版本的 EF 6，以在 .NET Core 3.0 上執行。 

如需詳細資訊，請參閱 [EF 6 中的新功能](xref:ef6/what-is-new/index)。

## <a name="postponed-features"></a>已延後的功能

原先針對 EF Core 3.0 規劃的一些功能，已延後到未來的版本：

- 在移轉中忽略模型組件的功能，以 [#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725) \(英文\) 追蹤。
- 屬性包實體，視為兩個不同的問題來追蹤：有關共用類型實體的 [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) \(英文\)，以及有關已編製索引屬性對應支援的 [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) \(英文\)。
