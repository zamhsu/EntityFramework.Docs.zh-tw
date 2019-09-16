---
title: EF Core 3.0 的新功能 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: 2EBE2CCC-E52D-483F-834C-8877F5EB0C0C
uid: core/what-is-new/ef-core-3.0/features
ms.openlocfilehash: 528733d6eec33de2c9538541a6ed5be704b9d433
ms.sourcegitcommit: d01fc19aa42ca34c3bebccbc96ee26d06fcecaa2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2019
ms.locfileid: "71005561"
---
# <a name="new-features-included-in-ef-core-30"></a><span data-ttu-id="95c77-102">EF Core 3.0 中包含的新功能</span><span class="sxs-lookup"><span data-stu-id="95c77-102">New features included in EF Core 3.0</span></span>

<span data-ttu-id="95c77-103">下列清單包含為 EF Core 3.0 新規劃的重要功能。</span><span class="sxs-lookup"><span data-stu-id="95c77-103">The following list includes the major new features planned for EF Core 3.0.</span></span>

<span data-ttu-id="95c77-104">EF Core 3.0 是一個主要版本，也包含許多[重大變更](xref:core/what-is-new/ef-core-3.0/breaking-changes)，也就是可能對現有應用程式造成負面影響的 API 改進。</span><span class="sxs-lookup"><span data-stu-id="95c77-104">EF Core 3.0 is a major release and also contains numerous [breaking changes](xref:core/what-is-new/ef-core-3.0/breaking-changes), which are API improvements that may have negative impact on existing applications.</span></span>  

## <a name="linq-improvements"></a><span data-ttu-id="95c77-105">LINQ 改善</span><span class="sxs-lookup"><span data-stu-id="95c77-105">LINQ improvements</span></span> 

<span data-ttu-id="95c77-106">LINQ 可讓您撰寫資料庫查詢，而不需要離開您所選擇的語言，利用豐富型別資訊來提供 IntelliSense 和編譯時間型別檢查。</span><span class="sxs-lookup"><span data-stu-id="95c77-106">LINQ enables you to write database queries without leaving your language of choice, taking advantage of rich type information to offer IntelliSense and compile-time type checking.</span></span>
<span data-ttu-id="95c77-107">但是 LINQ 也可以讓您撰寫不限數目的複雜查詢，其中包含任意運算式（方法呼叫或作業）。</span><span class="sxs-lookup"><span data-stu-id="95c77-107">But LINQ also enables you to write an unlimited number of complicated queries containing arbitrary expressions (method calls or operations).</span></span>
<span data-ttu-id="95c77-108">處理所有這些組合一直是 LINQ 提供者的重大挑戰。</span><span class="sxs-lookup"><span data-stu-id="95c77-108">Handling all those combinations has always been a significant challenge for LINQ providers.</span></span>
<span data-ttu-id="95c77-109">在 EF Core 3.0 中，我們已重寫 LINQ 執行，讓您能夠將更多運算式轉譯為 SQL，以在更多情況下產生有效率的查詢，以避免無法偵測到效率不佳的查詢，並讓我們更輕鬆地引進新的查詢功能和效能 improvementswithout 中斷現有的應用程式和資料提供者。</span><span class="sxs-lookup"><span data-stu-id="95c77-109">In EF Core 3.0, we've rewritten our LINQ implementation to enable translating more expressions into SQL, to generate efficient queries in more cases, to prevent inefficient queries from going undetected, and to make it easier for us to gradually introduce new query capabilities and performance improvementswithout breaking existing applications and data providers.</span></span>

### <a name="client-evaluation"></a><span data-ttu-id="95c77-110">用戶端評估</span><span class="sxs-lookup"><span data-stu-id="95c77-110">Client evaluation</span></span>

<span data-ttu-id="95c77-111">EF Core 3.0 中的主要設計變更，必須與它如何處理無法轉譯成 SQL 或參數的 LINQ 運算式有關：</span><span class="sxs-lookup"><span data-stu-id="95c77-111">The main design change in EF Core 3.0 has to do with how it handles LINQ expressions that it cannot translate to SQL or parameters:</span></span>

<span data-ttu-id="95c77-112">在前幾個版本中，EF Core 只會找出查詢的哪些部分可以轉譯成 SQL，並在用戶端上執行查詢的其餘部分。</span><span class="sxs-lookup"><span data-stu-id="95c77-112">In the first few versions, EF Core simply figured out what portions of a query could be translated to SQL, and executed the rest of the query on the client.</span></span>
<span data-ttu-id="95c77-113">在某些情況下，這種類型的用戶端執行可能是必要的，但在許多其他情況下，它可能會導致沒有效率的查詢。</span><span class="sxs-lookup"><span data-stu-id="95c77-113">This type of client-side execution can be desirable in some situations, but in many other cases it can result in inefficient queries.</span></span>
<span data-ttu-id="95c77-114">例如，如果 EF Core 2.2 無法轉譯`Where()`呼叫中的述詞，它會執行不含篩選的 SQL 語句、讀取資料庫中所有的資料列，然後在記憶體中進行篩選。</span><span class="sxs-lookup"><span data-stu-id="95c77-114">For example, if EF Core 2.2 couldn't translate a predicate in a `Where()` call, it executed a SQL statement without a filter, read all all the rows from the database, and then filtered them in-memory.</span></span>
<span data-ttu-id="95c77-115">如果資料庫包含少量的資料列，這可能是可接受的，但如果資料庫包含大量或多個資料列，則會導致嚴重的效能問題，甚至是應用程式失敗。</span><span class="sxs-lookup"><span data-stu-id="95c77-115">That may be acceptable if the database contains a small number of rows, but can result in significant performance issues or even application failure if the database contains a large number or rows.</span></span>
<span data-ttu-id="95c77-116">在 EF Core 3.0 中，我們已將用戶端評估限制為僅適用于最上層投射（最後一個`Select()`呼叫）。</span><span class="sxs-lookup"><span data-stu-id="95c77-116">In EF Core 3.0 we have restricted client evaluation to only happen on the top-level projection (the last call to `Select()`).</span></span>
<span data-ttu-id="95c77-117">當 EF Core 3.0 偵測到無法在查詢中的任何地方轉譯的運算式時，就會擲回執行時間例外狀況。</span><span class="sxs-lookup"><span data-stu-id="95c77-117">When EF Core 3.0 detects expressions that cannot be translated anywhere else in the query, it throws a runtime exception.</span></span>

## <a name="cosmos-db-support"></a><span data-ttu-id="95c77-118">Cosmos DB 支援</span><span class="sxs-lookup"><span data-stu-id="95c77-118">Cosmos DB support</span></span> 

<span data-ttu-id="95c77-119">適用于 EF Core 的 Cosmos DB 提供者可讓熟悉 EF 程式設計模型的開發人員輕鬆地以應用程式資料庫為目標 Azure Cosmos DB。</span><span class="sxs-lookup"><span data-stu-id="95c77-119">The Cosmos DB provider for EF Core enables developers familiar with the EF programing model to easily target Azure Cosmos DB as an application database.</span></span>
<span data-ttu-id="95c77-120">目標是讓 .NET 開發人員能更輕鬆地發揮 Cosmos DB 的一些優點，例如全域散發、"Always On" 可用性、彈性的延展性，以及低延遲。</span><span class="sxs-lookup"><span data-stu-id="95c77-120">The goal is to make some of the advantages of Cosmos DB, like global distribution, "always on" availability, elastic scalability, and low latency, even more accessible to .NET developers.</span></span>
<span data-ttu-id="95c77-121">提供者將啟用大部分的 EF Core 功能，例如自動變更追蹤、LINQ 與值轉換 (根據 Cosmos DB 中的 SQL API)。</span><span class="sxs-lookup"><span data-stu-id="95c77-121">The provider will enable most EF Core features, like automatic change tracking, LINQ, and value conversions, against the SQL API in Cosmos DB.</span></span>

## <a name="c-80-support"></a><span data-ttu-id="95c77-122">C# 8.0 支援</span><span class="sxs-lookup"><span data-stu-id="95c77-122">C# 8.0 support</span></span>

<span data-ttu-id="95c77-123">EF Core 3.0 會利用8.0 中C#的一些新功能：</span><span class="sxs-lookup"><span data-stu-id="95c77-123">EF Core 3.0 takes advantage of some of the new features in C# 8.0:</span></span>

### <a name="asynchronous-streams"></a><span data-ttu-id="95c77-124">非同步資料流</span><span class="sxs-lookup"><span data-stu-id="95c77-124">Asynchronous streams</span></span>

<span data-ttu-id="95c77-125">非同步查詢結果現在會使用新的標準`IAsyncEnumerable<T>`介面公開，並可使用`await foreach`加以取用。</span><span class="sxs-lookup"><span data-stu-id="95c77-125">Asynchronous query results are now exposed using the new standard `IAsyncEnumerable<T>` interface and can be consumed using `await foreach`.</span></span>

``` csharp
var orders = 
  from o in context.Orders
  where o.Status == OrderStatus.Pending
  select o;

await foreach(var o in orders)
{
  Proccess(o);
} 
```

### <a name="nullable-reference-types"></a><span data-ttu-id="95c77-126">可為 Null 的參考型別</span><span class="sxs-lookup"><span data-stu-id="95c77-126">Nullable reference types</span></span> 

<span data-ttu-id="95c77-127">當您的程式碼中啟用這項新功能時，EF Core 可能會導致參考類型的屬性（例如，字串或導覽屬性等基本類型）的 null 屬性，決定資料庫中資料行和關聯性的 null 屬性。</span><span class="sxs-lookup"><span data-stu-id="95c77-127">When this new feature is enabled in your code, EF Core can reason about the nullability of properties of refrence types (either of primitive types like string or navigation properties) to decide the nullability of columns and relationships in the database.</span></span>

## <a name="interception"></a><span data-ttu-id="95c77-128">攔截</span><span class="sxs-lookup"><span data-stu-id="95c77-128">Interception</span></span>

<span data-ttu-id="95c77-129">EF Core 3.0 中的新攔截 API，可讓您以程式設計方式觀察和修改低層級資料庫作業的結果，這是 EF Core 正常運作的一部分，例如開啟連接、initating 交易，以及執行命令。</span><span class="sxs-lookup"><span data-stu-id="95c77-129">The new interception API in EF Core 3.0 allows programatically observing and modifying the outcome of low-level database operations that occur as part of the normal operation of EF Core, such as opening connections, initating transactions, and executing commands.</span></span> 

## <a name="reverse-engineering-of-database-views"></a><span data-ttu-id="95c77-130">資料庫檢視的反向工程</span><span class="sxs-lookup"><span data-stu-id="95c77-130">Reverse engineering of database views</span></span>

<span data-ttu-id="95c77-131">沒有索引鍵的實體類型（先前稱為[查詢類型](xref:core/modeling/query-types)）代表可從資料庫讀取但無法更新的資料。</span><span class="sxs-lookup"><span data-stu-id="95c77-131">Entity types without keys (previously known as [query types](xref:core/modeling/query-types)) represent data that can be read from the database, but cannot be updated.</span></span>
<span data-ttu-id="95c77-132">這項特性讓它們成為在大多數情況下對應資料庫檢視的絕佳大小，因此，我們會在反向工程資料庫流覽時，自動建立不含索引鍵的實體類型。</span><span class="sxs-lookup"><span data-stu-id="95c77-132">This characteristic makes them an excellent fit for mapping database views in most scenarios, so we automated the creation of entity types without keys when reverse engineering database views.</span></span>

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="95c77-133">現在可選用以主體來共用資料表的相依實體</span><span class="sxs-lookup"><span data-stu-id="95c77-133">Dependent entities sharing the table with the principal are now optional</span></span>

<span data-ttu-id="95c77-134">從 EF Core 3.0 開始，如果 `OrderDetails` 由 `Order` 所擁有，或明確地對應到相同的資料表，即可新增 `Order` 而無需 `OrderDetails` 及所有 `OrderDetails` 屬性，但主索引鍵將會對應至可為 Null 的資料行。</span><span class="sxs-lookup"><span data-stu-id="95c77-134">Starting with EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table, it will be possible to add an `Order` without an `OrderDetails` and all of the `OrderDetails` properties, except the primary key will be mapped to nullable columns.</span></span>

<span data-ttu-id="95c77-135">進行查詢時，如果任何其必要的屬性不具有值，或如果其具有主索引鍵以外的非必要屬性，且所有屬性皆為 `null`，則 EF Core 會將 `OrderDetails` 設定為 `null`。</span><span class="sxs-lookup"><span data-stu-id="95c77-135">When querying, EF Core will set `OrderDetails` to `null` if any of its required properties doesn't have a value, or if it has no required properties besides the primary key and all properties are `null`.</span></span>

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

## <a name="ef-63-on-net-core"></a><span data-ttu-id="95c77-136">.NET Core 上的 EF 6.3</span><span class="sxs-lookup"><span data-stu-id="95c77-136">EF 6.3 on .NET Core</span></span>

<span data-ttu-id="95c77-137">我們了解許多現有的應用程式都使用舊版 EF，而且因為要獲得 .NET Core 的優點而將它們移植到 EF Core 有時候需要非常多的精力。</span><span class="sxs-lookup"><span data-stu-id="95c77-137">We understand that many existing applications use previous versions of EF, and that porting them to EF Core only to take advantage of .NET Core can sometimes require a significant effort.</span></span>
<span data-ttu-id="95c77-138">基於這個理由，我們已啟用 EF 6 的 newewst 版本，以便在 .NET Core 3.0 上執行。</span><span class="sxs-lookup"><span data-stu-id="95c77-138">For that reason, we have enabled the newewst version of EF 6 to run on .NET Core 3.0.</span></span>
<span data-ttu-id="95c77-139">有一些限制，例如：</span><span class="sxs-lookup"><span data-stu-id="95c77-139">There are some limitations, for example:</span></span>
- <span data-ttu-id="95c77-140">需要新的提供者，才能在 .NET Core 上工作</span><span class="sxs-lookup"><span data-stu-id="95c77-140">New providers are required to work on .NET Core</span></span>
- <span data-ttu-id="95c77-141">SQL Server 的空間支援將不會啟用</span><span class="sxs-lookup"><span data-stu-id="95c77-141">Spatial support with SQL Server won't be enabled</span></span>

## <a name="postponed-features"></a><span data-ttu-id="95c77-142">延後的功能</span><span class="sxs-lookup"><span data-stu-id="95c77-142">Postponed features</span></span>

<span data-ttu-id="95c77-143">原先規劃用於 EF Core 3.0 的一些功能已延後到未來的版本：</span><span class="sxs-lookup"><span data-stu-id="95c77-143">Some features originally planned for EF Core 3.0 were postponed to future releases:</span></span> 

- <span data-ttu-id="95c77-144">能夠在遷移中忽略模型的各個部分， [#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725)追蹤。</span><span class="sxs-lookup"><span data-stu-id="95c77-144">Ability to ingore parts of a model in migrations, tracked by [#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725).</span></span>
- <span data-ttu-id="95c77-145">屬性包實體，由兩個不同的問題所追蹤： [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914)關於共用類型實體，以及有關索引屬性對應支援的[#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) 。</span><span class="sxs-lookup"><span data-stu-id="95c77-145">Property bag entities, tracked by two separate issues: [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) about shared-type entities and [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) about indexed property mapping support.</span></span>
