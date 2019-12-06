---
title: Entity Framework Core 3.0 中的新功能 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: 2EBE2CCC-E52D-483F-834C-8877F5EB0C0C
uid: core/what-is-new/ef-core-3.0/index
ms.openlocfilehash: ebc676930ffc396aa70bb8afb91cf5a0cd43e04d
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824848"
---
# <a name="new-features-in-entity-framework-core-30"></a><span data-ttu-id="10ce1-102">Entity Framework Core 3.0 中的新功能</span><span class="sxs-lookup"><span data-stu-id="10ce1-102">New features in Entity Framework Core 3.0</span></span>

<span data-ttu-id="10ce1-103">下列清單包含 EF Core 3.0 中的主要新功能。</span><span class="sxs-lookup"><span data-stu-id="10ce1-103">The following list includes the major new features in EF Core 3.0.</span></span>

<span data-ttu-id="10ce1-104">作為主要版本，EF Core 3.0 也包含數個[中斷性變更](xref:core/what-is-new/ef-core-3.0/breaking-changes)，也就是可能對現有應用程式造成負面影響的 API 改進。</span><span class="sxs-lookup"><span data-stu-id="10ce1-104">As a major release, EF Core 3.0 also contains several [breaking changes](xref:core/what-is-new/ef-core-3.0/breaking-changes), which are API improvements that may have negative impact on existing applications.</span></span>  

## <a name="linq-overhaul"></a><span data-ttu-id="10ce1-105">LINQ 檢修</span><span class="sxs-lookup"><span data-stu-id="10ce1-105">LINQ overhaul</span></span>

<span data-ttu-id="10ce1-106">LINQ 可讓您以偏好的 .NET 語言撰寫資料庫查詢，進而利用豐富的型別資訊優勢來提供 IntelliSense 與編譯時間型別檢查。</span><span class="sxs-lookup"><span data-stu-id="10ce1-106">LINQ enables you to write database queries using your .NET language of choice, taking advantage of rich type information to offer IntelliSense and compile-time type checking.</span></span>
<span data-ttu-id="10ce1-107">但是 LINQ 也可以讓您撰寫不限數目的複雜查詢，其中包含任意運算式 (方法呼叫或作業)。</span><span class="sxs-lookup"><span data-stu-id="10ce1-107">But LINQ also enables you to write an unlimited number of complicated queries containing arbitrary expressions (method calls or operations).</span></span>
<span data-ttu-id="10ce1-108">如何處理所有這些組合是 LINQ 提供者的主要挑戰。</span><span class="sxs-lookup"><span data-stu-id="10ce1-108">How to handle all those combinations is the main challenge for LINQ providers.</span></span>

<span data-ttu-id="10ce1-109">在 EF Core 3.0 中，我們重新架構了 LINQ 提供者，讓您能夠將更多查詢模式轉譯為 SQL、在更多情況下產生有效率的查詢，以及防止無法偵測到無效率的查詢。</span><span class="sxs-lookup"><span data-stu-id="10ce1-109">In EF Core 3.0, we rearchitected our LINQ provider to enable translating more query patterns into SQL, generating efficient queries in more cases, and preventing inefficient queries from going undetected.</span></span> <span data-ttu-id="10ce1-110">以新的 LINQ 提供者為基礎，我們將在未來的版本中提供新的查詢功能和效能改進，而不會中斷現有的應用程式和資料提供者。</span><span class="sxs-lookup"><span data-stu-id="10ce1-110">The new LINQ provider is the foundation over which we'll be able to offer new query capabilities and performance improvements in future releases, without breaking existing applications and data providers.</span></span>

### <a name="restricted-client-evaluation"></a><span data-ttu-id="10ce1-111">受限制的用戶端評估</span><span class="sxs-lookup"><span data-stu-id="10ce1-111">Restricted client evaluation</span></span>

<span data-ttu-id="10ce1-112">最重要的設計變更，就是如何處理無法轉換成參數或轉譯為 SQL 的 LINQ 運算式。</span><span class="sxs-lookup"><span data-stu-id="10ce1-112">The most important design change has to do with how we handle LINQ expressions that cannot be converted to parameters or translated to SQL.</span></span>

<span data-ttu-id="10ce1-113">在舊版中，EF Core 會識別查詢的哪些部分可以轉譯成 SQL，並在用戶端上執行查詢的其餘部分。</span><span class="sxs-lookup"><span data-stu-id="10ce1-113">In previous versions, EF Core identified what portions of a query could be translated to SQL, and executed the rest of the query on the client.</span></span>
<span data-ttu-id="10ce1-114">在某些情況下，這種類型的用戶端執行是必要的，但在其他許多情況下，它可能會導致無效率的查詢。</span><span class="sxs-lookup"><span data-stu-id="10ce1-114">This type of client-side execution is desirable in some situations, but in many other cases it can result in inefficient queries.</span></span>

<span data-ttu-id="10ce1-115">例如，如果 EF Core 2.2 無法轉譯 `Where()` 呼叫中的述詞，它會不使用篩選執行 SQL 陳述式、從資料庫傳輸所有資料列，然後在記憶體內進行篩選：</span><span class="sxs-lookup"><span data-stu-id="10ce1-115">For example, if EF Core 2.2 couldn't translate a predicate in a `Where()` call, it executed an SQL statement without a filter, transferred all the rows from the database, and then filtered them in-memory:</span></span>

``` csharp
var specialCustomers = context.Customers
    .Where(c => c.Name.StartsWith(n) && IsSpecialCustomer(c));
```

<span data-ttu-id="10ce1-116">如果資料庫包含少量的資料列，那是可接受的，但是如果資料庫包含大量資料列，則可能導致明顯的效能問題，甚至導致應用程式失敗。</span><span class="sxs-lookup"><span data-stu-id="10ce1-116">That may be acceptable if the database contains a small number of rows but can result in significant performance issues or even application failure if the database contains a large number of rows.</span></span>

<span data-ttu-id="10ce1-117">在 EF Core 3.0 中，我們已將用戶端評估限制為僅適用於最上層投影 (基本上是 `Select()` 的最後一次呼叫)。</span><span class="sxs-lookup"><span data-stu-id="10ce1-117">In EF Core 3.0, we've restricted client evaluation to only happen on the top-level projection (essentially, the last call to `Select()`).</span></span>
<span data-ttu-id="10ce1-118">當 EF Core 3.0 偵測到無法在查詢中的任何其他位置轉譯的運算式時，它會擲回執行階段例外狀況。</span><span class="sxs-lookup"><span data-stu-id="10ce1-118">When EF Core 3.0 detects expressions that can't be translated anywhere else in the query, it throws a runtime exception.</span></span>

<span data-ttu-id="10ce1-119">若要在用戶端上評估述詞條件，如先前範例所示，開發人員現在必須將查詢的評估明確切換為 LINQ to Objects：</span><span class="sxs-lookup"><span data-stu-id="10ce1-119">To evaluate a predicate condition on the client as in the previous example, developers now need to explicitly switch evaluation of the query to LINQ to Objects:</span></span>

``` csharp
var specialCustomers = context.Customers
    .Where(c => c.Name.StartsWith(n))
    .AsEnumerable() // switches to LINQ to Objects
    .Where(c => IsSpecialCustomer(c));
```

<span data-ttu-id="10ce1-120">如需有關這會如何影響現有應用程式的詳細資訊，請參閱[中斷性變更文件](xref:core/what-is-new/ef-core-3.0/breaking-changes#linq-queries-are-no-longer-evaluated-on-the-client)。</span><span class="sxs-lookup"><span data-stu-id="10ce1-120">See the [breaking changes documentation](xref:core/what-is-new/ef-core-3.0/breaking-changes#linq-queries-are-no-longer-evaluated-on-the-client) for more details about how this can affect existing applications.</span></span>

### <a name="single-sql-statement-per-linq-query"></a><span data-ttu-id="10ce1-121">每個 LINQ 查詢單一 SQL 陳述式</span><span class="sxs-lookup"><span data-stu-id="10ce1-121">Single SQL statement per LINQ query</span></span>

<span data-ttu-id="10ce1-122">在 3.0 中，另一個大幅變更的設計層面，就是我們現在一律會針對每個 LINQ 查詢產生單一 SQL 陳述式。</span><span class="sxs-lookup"><span data-stu-id="10ce1-122">Another aspect of the design that changed significantly in 3.0 is that we now always generate a single SQL statement per LINQ query.</span></span> <span data-ttu-id="10ce1-123">在舊版中，我們通常會在某些情況下產生多個 SQL 陳述式，轉譯集合導覽屬性的 `Include()` 呼叫，以及轉譯遵循具有子查詢之特定模式的查詢。</span><span class="sxs-lookup"><span data-stu-id="10ce1-123">In previous versions, we used to generate multiple SQL statements in certain cases, translated `Include()` calls on collection navigation properties and translated queries that followed certain patterns with subqueries.</span></span> <span data-ttu-id="10ce1-124">雖然這在某些情況下很方便，而且對於 `Include()` 來說甚至可以協助避免透過網路傳送重複的資料，但實作起來很複雜，導致產生某些效率極低的行為 (N+1 個查詢)。</span><span class="sxs-lookup"><span data-stu-id="10ce1-124">Although this was in some cases convenient, and for `Include()` it even helped avoid sending redundant data over the wire, the implementation was complex, and it resulted in some extremely inefficient behaviors (N+1 queries).</span></span> <span data-ttu-id="10ce1-125">在某些情況下，跨多個查詢傳回的資料可能不一致。</span><span class="sxs-lookup"><span data-stu-id="10ce1-125">There were situations in which the data returned across multiple queries was potentially inconsistent.</span></span>

<span data-ttu-id="10ce1-126">與用戶端評估類似，如果 EF Core 3.0 無法將 LINQ 查詢轉譯為單一 SQL 陳述式，則會擲回執行階段例外狀況。</span><span class="sxs-lookup"><span data-stu-id="10ce1-126">Similarly to client evaluation, if EF Core 3.0 can't translate a LINQ query into a single SQL statement, it throws a runtime exception.</span></span> <span data-ttu-id="10ce1-127">但是，我們使 EF Core 能夠將許多用來產生多個查詢的常見模式，轉譯成含有聯結的單一查詢。</span><span class="sxs-lookup"><span data-stu-id="10ce1-127">But we made EF Core capable of translating many of the common patterns that used to generate multiple queries to a single query with JOINs.</span></span>

## <a name="cosmos-db-support"></a><span data-ttu-id="10ce1-128">Cosmos DB 支援</span><span class="sxs-lookup"><span data-stu-id="10ce1-128">Cosmos DB support</span></span>

<span data-ttu-id="10ce1-129">適用於 EF Core 的 Cosmos DB 提供者可讓開發人員熟悉 EF 程式設計模型，以輕鬆地使用 Azure Cosmos DB 作為應用程式資料庫。</span><span class="sxs-lookup"><span data-stu-id="10ce1-129">The Cosmos DB provider for EF Core enables developers familiar with the EF programing model to easily target Azure Cosmos DB as an application database.</span></span> <span data-ttu-id="10ce1-130">目標是讓 .NET 開發人員能更輕鬆地發揮 Cosmos DB 的一些優點，例如全域散發、"Always On" 可用性、彈性的延展性，以及低延遲。</span><span class="sxs-lookup"><span data-stu-id="10ce1-130">The goal is to make some of the advantages of Cosmos DB, like global distribution, "always on" availability, elastic scalability, and low latency, even more accessible to .NET developers.</span></span> <span data-ttu-id="10ce1-131">提供者會啟用大部分的 EF Core 功能，例如自動變更追蹤、LINQ 與值轉換 (根據 Cosmos DB 中的 SQL API)。</span><span class="sxs-lookup"><span data-stu-id="10ce1-131">The provider enables most EF Core features, like automatic change tracking, LINQ, and value conversions, against the SQL API in Cosmos DB.</span></span>

<span data-ttu-id="10ce1-132">如需詳細資訊，請參閱 [Cosmos DB 提供者文件](xref:core/providers/cosmos/index)。</span><span class="sxs-lookup"><span data-stu-id="10ce1-132">See the [Cosmos DB provider documentation](xref:core/providers/cosmos/index) for more details.</span></span>

## <a name="c-80-support"></a><span data-ttu-id="10ce1-133">C# 8.0 支援</span><span class="sxs-lookup"><span data-stu-id="10ce1-133">C# 8.0 support</span></span>

<span data-ttu-id="10ce1-134">EF Core 3.0 會利用幾個 [C# 8.0 中的新功能](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8) \(部分機器翻譯\)：</span><span class="sxs-lookup"><span data-stu-id="10ce1-134">EF Core 3.0 takes advantage of a couple of the [new features in C# 8.0](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8):</span></span>

### <a name="asynchronous-streams"></a><span data-ttu-id="10ce1-135">非同步資料流</span><span class="sxs-lookup"><span data-stu-id="10ce1-135">Asynchronous streams</span></span>

<span data-ttu-id="10ce1-136">非同步查詢結果現在會使用新的標準 `IAsyncEnumerable<T>` 介面公開，並可使用 `await foreach` 加以取用。</span><span class="sxs-lookup"><span data-stu-id="10ce1-136">Asynchronous query results are now exposed using the new standard `IAsyncEnumerable<T>` interface and can be consumed using `await foreach`.</span></span>

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

<span data-ttu-id="10ce1-137">如需詳細資訊，請參閱 [C# 文件中的非同步資料流](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8#asynchronous-streams) \(部分機器翻譯\)。</span><span class="sxs-lookup"><span data-stu-id="10ce1-137">See the [asynchronous streams in the C# documentation](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8#asynchronous-streams) for more details.</span></span>

### <a name="nullable-reference-types"></a><span data-ttu-id="10ce1-138">可為 Null 的參考型別</span><span class="sxs-lookup"><span data-stu-id="10ce1-138">Nullable reference types</span></span>

<span data-ttu-id="10ce1-139">當您的程式碼中啟用這個新功能時，EF Core 會檢查參考型別屬性的可為 Null 性，並將它套用至資料庫中對應的資料行和關聯性：不可為 Null 之參考型別的屬性會被視為具有 `[Required]` 資料註解屬性。</span><span class="sxs-lookup"><span data-stu-id="10ce1-139">When this new feature is enabled in your code, EF Core examines the nullability of reference type properties and applies it to corresponding columns and relationships in the database: properties of non-nullable references types are treated as if they had the `[Required]` data annotation attribute.</span></span>

<span data-ttu-id="10ce1-140">例如，在下列類別中，標示為型別 `string?` 的屬性會設定為選擇性，而 `string` 會設定為必要項：</span><span class="sxs-lookup"><span data-stu-id="10ce1-140">For example, in the following class, properties marked as of type `string?` will be configured as optional, whereas `string` will be configured as required:</span></span>

``` csharp
public class Customer
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string? MiddleName { get; set; }
}
```

<span data-ttu-id="10ce1-141">如需詳細資訊，請參閱 EF Core 文件中的[使用可為 Null 的參考型別](xref:core/miscellaneous/nullable-reference-types)。</span><span class="sxs-lookup"><span data-stu-id="10ce1-141">See [Working with nullable reference types](xref:core/miscellaneous/nullable-reference-types) in the EF Core documentation for more details.</span></span>

## <a name="interception-of-database-operations"></a><span data-ttu-id="10ce1-142">資料庫作業攔截</span><span class="sxs-lookup"><span data-stu-id="10ce1-142">Interception of database operations</span></span>

<span data-ttu-id="10ce1-143">EF Core 3.0 中的新攔截 API 允許提供發生低層級資料庫作業 (在 EF Core 正常作業期間) 時，可自動叫用的自訂邏輯。</span><span class="sxs-lookup"><span data-stu-id="10ce1-143">The new interception API in EF Core 3.0 allows providing custom logic to be invoked automatically whenever low-level database operations occur as part of the normal operation of EF Core.</span></span> <span data-ttu-id="10ce1-144">例如，開啟連線、認可交易或執行命令時。</span><span class="sxs-lookup"><span data-stu-id="10ce1-144">For example, when opening connections, committing transactions, or executing commands.</span></span>

<span data-ttu-id="10ce1-145">與 EF 6 中已存在的攔截功能類似，攔截器可讓您在作業發生之前或之後進行攔截。</span><span class="sxs-lookup"><span data-stu-id="10ce1-145">Similarly to the interception features that existed in EF 6, interceptors allow you to intercept operations before or after they happen.</span></span> <span data-ttu-id="10ce1-146">如果在它們發生之前對其進行攔截，則可以跳過執行，並提供來自攔截邏輯的替代結果。</span><span class="sxs-lookup"><span data-stu-id="10ce1-146">When you intercept them before they happen, you are allowed to by-pass execution and supply alternate results from the interception logic.</span></span>

<span data-ttu-id="10ce1-147">例如，若要操作命令文字，您可以建立 `IDbCommandInterceptor`：</span><span class="sxs-lookup"><span data-stu-id="10ce1-147">For example, to manipulate command text, you can create an `IDbCommandInterceptor`:</span></span>

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

<span data-ttu-id="10ce1-148">並使用您的  `DbContext` 註冊它：</span><span class="sxs-lookup"><span data-stu-id="10ce1-148">And register it with your `DbContext`:</span></span>

``` csharp
services.AddDbContext(b => b
    .UseSqlServer(connectionString)
    .AddInterceptors(new HintCommandInterceptor()));
```

## <a name="reverse-engineering-of-database-views"></a><span data-ttu-id="10ce1-149">資料庫檢視的反向工程</span><span class="sxs-lookup"><span data-stu-id="10ce1-149">Reverse engineering of database views</span></span>

<span data-ttu-id="10ce1-150">查詢類型 (代表可從資料庫讀取但未更新的資料) 已重新命名為[無索引鍵實體類型](xref:core/modeling/keyless-entity-types)。</span><span class="sxs-lookup"><span data-stu-id="10ce1-150">Query types, which represent data that can be read from the database but not updated, have been renamed to [keyless entity types](xref:core/modeling/keyless-entity-types).</span></span>
<span data-ttu-id="10ce1-151">因為它們很適合在大部分的案例中用於對應資料庫檢視，所以 EF Core 現在會在反向工程資料庫檢視時，自動建立無索引鍵實體類型。</span><span class="sxs-lookup"><span data-stu-id="10ce1-151">As they are an excellent fit for mapping database views in most scenarios, EF Core now automatically creates keyless entity types when reverse engineering database views.</span></span>

<span data-ttu-id="10ce1-152">例如，您可以使用 [dotnet ef 命令列工具](xref:core/miscellaneous/cli/dotnet)來輸入：</span><span class="sxs-lookup"><span data-stu-id="10ce1-152">For example, using the [dotnet ef command-line tool](xref:core/miscellaneous/cli/dotnet) you can type:</span></span>

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="10ce1-153">而此工具現在會自動針對不含索引鍵之檢視與資料表的類型建立結構：</span><span class="sxs-lookup"><span data-stu-id="10ce1-153">And the tool will now automatically scaffold types for views and tables without keys:</span></span>

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

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="10ce1-154">現在可選用以主體來共用資料表的相依實體</span><span class="sxs-lookup"><span data-stu-id="10ce1-154">Dependent entities sharing the table with the principal are now optional</span></span>

<span data-ttu-id="10ce1-155">從 EF Core 3.0 開始，如果 `OrderDetails` 由 `Order` 所擁有，或明確地對應到相同的資料表，即可新增 `Order` 而無需 `OrderDetails` 及所有 `OrderDetails` 屬性，但主索引鍵將會對應至可為 Null 的資料行。</span><span class="sxs-lookup"><span data-stu-id="10ce1-155">Starting with EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table, it will be possible to add an `Order` without an `OrderDetails` and all of the `OrderDetails` properties, except the primary key will be mapped to nullable columns.</span></span>

<span data-ttu-id="10ce1-156">進行查詢時，如果任何其必要的屬性不具有值，或如果其具有主索引鍵以外的非必要屬性，且所有屬性皆為 `null`，則 EF Core 會將 `OrderDetails` 設定為 `null`。</span><span class="sxs-lookup"><span data-stu-id="10ce1-156">When querying, EF Core will set `OrderDetails` to `null` if any of its required properties doesn't have a value, or if it has no required properties besides the primary key and all properties are `null`.</span></span>

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

## <a name="ef-63-on-net-core"></a><span data-ttu-id="10ce1-157">.NET Core 上的 EF 6.3</span><span class="sxs-lookup"><span data-stu-id="10ce1-157">EF 6.3 on .NET Core</span></span>

<span data-ttu-id="10ce1-158">這不是 EF Core 3.0 的功能，但我們認為這對我們目前的客戶而言很重要。</span><span class="sxs-lookup"><span data-stu-id="10ce1-158">This isn't really an EF Core 3.0 feature, but we think it is important to many of our current customers.</span></span>

<span data-ttu-id="10ce1-159">我們了解許多現有的應用程式都使用舊版 EF，而且因為要獲得 .NET Core 的優點而將它們移植到 EF Core 需要非常多的精力。</span><span class="sxs-lookup"><span data-stu-id="10ce1-159">We understand that many existing applications use previous versions of EF, and that porting them to EF Core only to take advantage of .NET Core can require a significant effort.</span></span>
<span data-ttu-id="10ce1-160">因此，我們決定移植最新版本的 EF 6，以在 .NET Core 3.0 上執行。</span><span class="sxs-lookup"><span data-stu-id="10ce1-160">For that reason, we decided to port the newest version of EF 6 to run on .NET Core 3.0.</span></span>

<span data-ttu-id="10ce1-161">如需詳細資訊，請參閱 [EF 6 中的新功能](xref:ef6/what-is-new/index)。</span><span class="sxs-lookup"><span data-stu-id="10ce1-161">For more details, see [what's new in EF 6](xref:ef6/what-is-new/index).</span></span>

## <a name="postponed-features"></a><span data-ttu-id="10ce1-162">已延後的功能</span><span class="sxs-lookup"><span data-stu-id="10ce1-162">Postponed features</span></span>

<span data-ttu-id="10ce1-163">原先針對 EF Core 3.0 規劃的一些功能，已延後到未來的版本：</span><span class="sxs-lookup"><span data-stu-id="10ce1-163">Some features originally planned for EF Core 3.0 were postponed to future releases:</span></span>

- <span data-ttu-id="10ce1-164">在移轉中忽略模型組件的功能，以 [#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725) \(英文\) 追蹤。</span><span class="sxs-lookup"><span data-stu-id="10ce1-164">Ability to ignore parts of a model in migrations, tracked as [#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725).</span></span>
- <span data-ttu-id="10ce1-165">屬性包實體，視為兩個不同的問題來追蹤：有關共用類型實體的 [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) \(英文\)，以及有關已編製索引屬性對應支援的 [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="10ce1-165">Property bag entities, tracked as two separate issues: [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) about shared-type entities and [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) about indexed property mapping support.</span></span>
