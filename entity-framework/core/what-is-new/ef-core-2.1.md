---
title: EF Core 2.1 中的新功能 - EF Core
author: divega
ms.author: divega
ms.date: 2/20/2018
ms.assetid: 585F90A3-4D5A-4DD1-92D8-5243B14E0FEC
ms.technology: entity-framework-core
uid: core/what-is-new/ef-core-2.1
ms.openlocfilehash: db1648095aa4d612af53f4e10a30be36edc40da5
ms.sourcegitcommit: 4997314356118d0d97b04ad82e433e49bb9420a2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="new-features-in-ef-core-21"></a><span data-ttu-id="a7bdd-102">EF Core 2.1 中的新功能</span><span class="sxs-lookup"><span data-stu-id="a7bdd-102">New features in EF Core 2.1</span></span>
> [!NOTE]  
> <span data-ttu-id="a7bdd-103">這個版本仍處於預覽狀態。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-103">This release is still in preview.</span></span>

<span data-ttu-id="a7bdd-104">除了許多 Bug 修正和小型的功能和效能增強之外，EF Core 2.1 還包含一些吸引人的新功能：</span><span class="sxs-lookup"><span data-stu-id="a7bdd-104">Besides numerous bug fixes and small functional and performance enhancements, EF Core 2.1 includes some compelling new features:</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="a7bdd-105">消極式載入</span><span class="sxs-lookup"><span data-stu-id="a7bdd-105">Lazy loading</span></span>
<span data-ttu-id="a7bdd-106">EF Core 現在包含必要的建置組塊，可讓任何人撰寫實體類別，以視需要載入其導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-106">EF Core now contains the necessary building blocks for anyone to author entity classes that can load their navigation properties on demand.</span></span> <span data-ttu-id="a7bdd-107">我們也已建立新套件 Microsoft.EntityFrameworkCore.Proxies，該套件利用這些建置組塊來產生以最小修改之實體類別為基礎的消極式載入 Proxy 類別 (例如具有虛擬導覽屬性的類別)。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-107">We have also created a new package, Microsoft.EntityFrameworkCore.Proxies, that leverages those building blocks to produce lazy loading proxy classes based on minimally modified entity classes (e.g. classes with virtual navigation properties).</span></span>

<span data-ttu-id="a7bdd-108">如需此主題的詳細資訊，請閱讀[消極式載入](xref:core/querying/related-data#lazy-loading)一節。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-108">Read the [section on lazy loading](xref:core/querying/related-data#lazy-loading) for more information about this topic.</span></span>

## <a name="parameters-in-entity-constructors"></a><span data-ttu-id="a7bdd-109">實體建構函式中的參數</span><span class="sxs-lookup"><span data-stu-id="a7bdd-109">Parameters in entity constructors</span></span>
<span data-ttu-id="a7bdd-110">因為是消極式載入所需要建置組塊之一，所以我們允許建立實體，由其建構函式來接受參數。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-110">As one of the required building blocks for lazy loading, we enabled the creation of entities that take parameters in their constructors.</span></span> <span data-ttu-id="a7bdd-111">您可以使用這些參數來插入屬性值、消極式載入委派和服務。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-111">You can use parameters to inject property values, lazy loading delegates, and services.</span></span>

<span data-ttu-id="a7bdd-112">如需此主題的詳細資訊，請閱讀[含參數的實體建構函式](xref:core/modeling/constructors)一節。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-112">Read the [section on entity constructor with parameters](xref:core/modeling/constructors) for more information about this topic.</span></span>

## <a name="value-conversions"></a><span data-ttu-id="a7bdd-113">值轉換</span><span class="sxs-lookup"><span data-stu-id="a7bdd-113">Value conversions</span></span>
<span data-ttu-id="a7bdd-114">到目前為止，EF Core 只能對應基礎資料庫提供者原生支援類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-114">Until now, EF Core could only map properties of types natively supported by the underlying database provider.</span></span> <span data-ttu-id="a7bdd-115">這些值會在資料行與屬性之間來回複製，無須進行任何轉換。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-115">Values were copied back and forth between columns and properties without any transformation.</span></span> <span data-ttu-id="a7bdd-116">從 EF Core 2.1 開始，您可以套用值轉換來轉換從資料行取得的值，再將這些值套用至屬性，反之亦然。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-116">Starting with EF Core 2.1, value conversions can be applied to transform the values obtained from columns before they are applied to properties, and vice versa.</span></span> <span data-ttu-id="a7bdd-117">我們有一些可以依照慣例視需要套用的轉換，以及允許在資料行與屬性之間註冊自訂轉換的明確組態 API。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-117">We have a number of conversions that can be applied by convention as necessary, as well as an explicit configuration API that allows registering custom conversions between columns and properties.</span></span> <span data-ttu-id="a7bdd-118">這項功能的一些應用包括：</span><span class="sxs-lookup"><span data-stu-id="a7bdd-118">Some of the application of this feature are:</span></span>

- <span data-ttu-id="a7bdd-119">將列舉儲存為字串</span><span class="sxs-lookup"><span data-stu-id="a7bdd-119">Storing enums as strings</span></span>
- <span data-ttu-id="a7bdd-120">使用 SQL Server 對應不帶正負號的整數</span><span class="sxs-lookup"><span data-stu-id="a7bdd-120">Mapping unsigned integers with SQL Server</span></span>
- <span data-ttu-id="a7bdd-121">屬性值的自動加密和解密</span><span class="sxs-lookup"><span data-stu-id="a7bdd-121">Automatic encryption and decryption of property values</span></span>

<span data-ttu-id="a7bdd-122">如需此主題的詳細資訊，請閱讀[值轉換](xref:core/modeling/value-conversions)一節。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-122">Read the [section on value conversions](xref:core/modeling/value-conversions) for more information about this topic.</span></span>  

## <a name="linq-groupby-translation"></a><span data-ttu-id="a7bdd-123">LINQ GroupBy 轉譯</span><span class="sxs-lookup"><span data-stu-id="a7bdd-123">LINQ GroupBy translation</span></span>
<span data-ttu-id="a7bdd-124">在 2.1 版之前，EF Core 中的 GroupBy LINQ 運算子永遠會在記憶體內部評估。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-124">Before version 2.1, in EF Core the GroupBy LINQ operator was always be evaluated in memory.</span></span> <span data-ttu-id="a7bdd-125">我們現在支援在最常見的案例中將它轉譯成 SQL GROUP BY 子句。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-125">We now support translating it to the SQL GROUP BY clause in most common cases.</span></span>

<span data-ttu-id="a7bdd-126">此範例會顯示使用 GroupBy 來計算各種彙總函式的查詢：</span><span class="sxs-lookup"><span data-stu-id="a7bdd-126">This example shows a query with GroupBy used to compute various aggregate functions:</span></span>

``` csharp
var query = context.Orders
    .GroupBy(o => new { o.CustomerId, o.EmployeeId })
    .Select(g => new
        {
          g.Key.CustomerId,
          g.Key.EmployeeId,
          Sum = g.Sum(o => o.Amount),
          Min = g.Min(o => o.Amount),
          Max = g.Max(o => o.Amount),
          Avg = g.Average(o => Amount)
        });
```

<span data-ttu-id="a7bdd-127">對應的 SQL 轉譯如下所示：</span><span class="sxs-lookup"><span data-stu-id="a7bdd-127">The corresponding SQL translation looks like this:</span></span>

``` SQL
SELECT [o].[CustomerId], [o].[EmployeeId],
    SUM([o].[Amount]), MIN([o].[Amount]), MAX([o].[Amount]), AVG([o].[Amount])
FROM [Orders] AS [o]
GROUP BY [o].[CustomerId], [o].[EmployeeId];
```

## <a name="data-seeding"></a><span data-ttu-id="a7bdd-128">資料植入</span><span class="sxs-lookup"><span data-stu-id="a7bdd-128">Data Seeding</span></span>
<span data-ttu-id="a7bdd-129">在此新版本中，您可以提供初始資料來填入資料庫。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-129">With the new release it will be possible to provide initial data to populate a database.</span></span> <span data-ttu-id="a7bdd-130">不同於 EF6，植入資料與屬於模型組態一部分的實體類型相關聯。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-130">Unlike in EF6, seeding data is associated to an entity type as part of the model configuration.</span></span> <span data-ttu-id="a7bdd-131">然後，將資料庫升級至新版模型時，EF Core 移轉可以自動計算需要套用的插入、更新或刪除作業。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-131">Then EF Core migrations can automatically compute what insert, update or delete operations need to be applied when upgrading the database to a new version of the model.</span></span>

<span data-ttu-id="a7bdd-132">例如，您可以使用此選項，為 `OnModelCreating` 中的 Post 設定種子資料：</span><span class="sxs-lookup"><span data-stu-id="a7bdd-132">As an example, you can use this to configure seed data for a Post in `OnModelCreating`:</span></span>

``` csharp
modelBuilder.Entity<Post>().HasData(new Post{ Id = 1, Text = "Hello World!" });
```

<span data-ttu-id="a7bdd-133">如需此主題的詳細資訊，請閱讀[資料植入](xref:core/modeling/data-seeding)一節。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-133">Read the [section on data seeding](xref:core/modeling/data-seeding) for more information about this topic.</span></span>  

## <a name="query-types"></a><span data-ttu-id="a7bdd-134">查詢類型</span><span class="sxs-lookup"><span data-stu-id="a7bdd-134">Query types</span></span>
<span data-ttu-id="a7bdd-135">EF Core 模型現在可以包含查詢類型。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-135">An EF Core model can now include query types.</span></span> <span data-ttu-id="a7bdd-136">不同於實體類型，查詢類型並未定義索引鍵，而且無法插入、刪除或更新 (亦即這些類型是唯讀的)，但可以直接由查詢傳回。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-136">Unlike entity types, query types do not have keys defined on them and cannot be inserted, deleted or updated (i.e. they are read-only), but they can be returned directly by queries.</span></span> <span data-ttu-id="a7bdd-137">查詢類型的一些使用方式情節包括：</span><span class="sxs-lookup"><span data-stu-id="a7bdd-137">Some of the usage scenarios for query types are:</span></span>

- <span data-ttu-id="a7bdd-138">對應至不含主索引鍵的檢視表</span><span class="sxs-lookup"><span data-stu-id="a7bdd-138">Mapping to views without primary keys</span></span>
- <span data-ttu-id="a7bdd-139">對應至不含主索引鍵的資料表</span><span class="sxs-lookup"><span data-stu-id="a7bdd-139">Mapping to tables without primary keys</span></span>
- <span data-ttu-id="a7bdd-140">對應至模型中已定義的查詢</span><span class="sxs-lookup"><span data-stu-id="a7bdd-140">Mapping to queries defined in the model</span></span>
- <span data-ttu-id="a7bdd-141">作為 `FromSql()` 查詢的傳回型別</span><span class="sxs-lookup"><span data-stu-id="a7bdd-141">Serving as the return type for `FromSql()` queries</span></span>

<span data-ttu-id="a7bdd-142">如需此主題的詳細資訊，請閱讀[查詢類型](xref:core/modeling/query-types)一節。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-142">Read the [section on query types](xref:core/modeling/query-types) for more information about this topic.</span></span>

## <a name="include-for-derived-types"></a><span data-ttu-id="a7bdd-143">隨附於衍生類型</span><span class="sxs-lookup"><span data-stu-id="a7bdd-143">Include for derived types</span></span>
<span data-ttu-id="a7bdd-144">現在，當您為 `Include` 方法撰寫運算式時，您可以指定只在衍生類型上定義的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-144">It will be now possible to specify navigation properties only defined on derived types when writing expressions for the `Include` method.</span></span> <span data-ttu-id="a7bdd-145">針對 `Include` 的強型別版本，我們支援使用明確轉換或 `as` 運算子。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-145">For the strongly typed version of `Include`, we support using either an explicit cast or the `as` operator.</span></span> <span data-ttu-id="a7bdd-146">我們現在也支援參考 `Include` 的字串版本中衍生類型上所定義的導覽屬性名稱：</span><span class="sxs-lookup"><span data-stu-id="a7bdd-146">We also now support referencing the names of navigation property defined on derived types in the string version of `Include`:</span></span>

``` csharp
var option1 = context.People.Include(p => ((Student)p).School);
var option2 = context.People.Include(p => (p as Student).School);
var option3 = context.People.Include("School");
```

<span data-ttu-id="a7bdd-147">如需此主題的詳細資訊，請閱讀[隨附於衍生類型](xref:core/querying/related-data#include-on-derived-types)一節。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-147">Read the [section on Include with derived types](xref:core/querying/related-data#include-on-derived-types) for more information about this topic.</span></span>

## <a name="systemtransactions-support"></a><span data-ttu-id="a7bdd-148">System.Transactions 支援</span><span class="sxs-lookup"><span data-stu-id="a7bdd-148">System.Transactions support</span></span>
<span data-ttu-id="a7bdd-149">我們新增了使用 System.Transactions 功能的能力，例如 TransactionScope。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-149">We have added the ability to work with System.Transactions features such as TransactionScope.</span></span> <span data-ttu-id="a7bdd-150">使用支援的資料庫提供者時，這會適用於 .NET Framework 和 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-150">This will work on both .NET Framework and .NET Core when using database providers that support it.</span></span>

<span data-ttu-id="a7bdd-151">如需此主題的詳細資訊，請閱讀 [System.Transactions](xref:core/saving/transactions#using-systemtransactions) 一節。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-151">Read the [section on System.Transactions](xref:core/saving/transactions#using-systemtransactions) for more information about this topic.</span></span>

## <a name="better-column-ordering-in-initial-migration"></a><span data-ttu-id="a7bdd-152">改進初始移轉中的資料行排序</span><span class="sxs-lookup"><span data-stu-id="a7bdd-152">Better column ordering in initial migration</span></span>
<span data-ttu-id="a7bdd-153">根據客戶的意見反應，我們已更新移轉，一開始就依類別中宣告屬性的相同順序來產生資料表的資料行。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-153">Based on customer feedback, we have updated migrations to initially generate columns for tables in the same order as properties are declared in classes.</span></span> <span data-ttu-id="a7bdd-154">請注意，若在初始資料表建立之後新增成員，EF Core 就無法變更順序。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-154">Note that EF Core cannot change order when new members are added after the initial table creation.</span></span>

## <a name="optimization-of-correlated-subqueries"></a><span data-ttu-id="a7bdd-155">相互關聯子查詢的最佳化</span><span class="sxs-lookup"><span data-stu-id="a7bdd-155">Optimization of correlated subqueries</span></span>
<span data-ttu-id="a7bdd-156">我們已改善查詢轉譯，避免在許多常見情節中執行 "N + 1" SQL 查詢，在這些情節中，於投影中使用導覽屬性會導致根查詢中的資料與相互關聯子查詢中的資料相聯結。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-156">We have improved our query translation to avoid executing "N + 1" SQL queries in many common scenarios in which the usage of a navigation property in the projection leads to joining data from the root query with data from a correlated subquery.</span></span> <span data-ttu-id="a7bdd-157">最佳化需要緩衝處理形成子查詢的結果，而且我們需要您修改查詢以加入新的行為。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-157">The optimization requires buffering the results form the subquery, and we require that you modify the query to opt-in the new behavior.</span></span>

<span data-ttu-id="a7bdd-158">例如，下列查詢通常會轉譯成一個用於客戶的查詢，再加上 N (其中 "N" 是傳回的客戶數目) 個用於訂單的個別查詢：</span><span class="sxs-lookup"><span data-stu-id="a7bdd-158">As an example, the following query normally gets translated into one query for Customers, plus N (where "N" is the number of customers returned) separate queries for Orders:</span></span>

``` csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount));
```

<span data-ttu-id="a7bdd-159">藉由在正確位置包含 `ToList()`，您可以指出緩衝處理適用於訂單，因此可啟用最佳化：</span><span class="sxs-lookup"><span data-stu-id="a7bdd-159">By including `ToList()` in the right place, you indicate that buffering is appropriate for the Orders, which enable the optimization:</span></span>

``` csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount).ToList());
```

<span data-ttu-id="a7bdd-160">請注意這項查詢只會轉譯成兩個 SQL 查詢：一個用於客戶，另一個用於訂單。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-160">Note that this query will be translated to only two SQL queries: One for Customers and the next one for Orders.</span></span>

## <a name="ownedattribute"></a><span data-ttu-id="a7bdd-161">OwnedAttribute</span><span class="sxs-lookup"><span data-stu-id="a7bdd-161">OwnedAttribute</span></span>

<span data-ttu-id="a7bdd-162">您現在可以直接對類型標註 `[Owned]`，然後確定擁有者實體已新增至模型，來設定[擁有的實體類型](xref:core/modeling/owned-entities)：</span><span class="sxs-lookup"><span data-stu-id="a7bdd-162">It is now possible to configure [owned entity types](xref:core/modeling/owned-entities) by simply annotating the type with `[Owned]` and then making sure the owner entity is added to the model:</span></span>

``` csharp
[Owned]
public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public StreetAddress ShippingAddress { get; set; }
}
```

## <a name="new-dotnet-ef-global-tool"></a><span data-ttu-id="a7bdd-163">新的 dotnet-ef 通用工具</span><span class="sxs-lookup"><span data-stu-id="a7bdd-163">New dotnet-ef global tool</span></span>

<span data-ttu-id="a7bdd-164">_dotnet-ef_ 命令已轉換為 .NET CLI 通用工具，因此不再需要在專案中使用 DotNetCliToolReference 才能使用移轉，或從現有的資料庫建立 DbContext 結構。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-164">The _dotnet-ef_ commands have been converted to a .NET CLI global tool, so it will no longer be necessary to use DotNetCliToolReference in the project to be able to use migrations or to scaffold a DbContext from an existing database.</span></span>

## <a name="microsoftentityframeworkcoreabstractions-package"></a><span data-ttu-id="a7bdd-165">Microsoft.EntityFrameworkCore.Abstractions 套件</span><span class="sxs-lookup"><span data-stu-id="a7bdd-165">Microsoft.EntityFrameworkCore.Abstractions package</span></span>
<span data-ttu-id="a7bdd-166">新的套件包含屬性和介面，您可以在專案中使用這些屬性和介面來啟用 EF Core 功能，而不必仰賴整體 EF Core。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-166">The new package contains attributes and interfaces that you can use in your projects to light up EF Core features without taking a dependency on EF Core as a whole.</span></span> <span data-ttu-id="a7bdd-167">例如，</span><span class="sxs-lookup"><span data-stu-id="a7bdd-167">E.g.</span></span> <span data-ttu-id="a7bdd-168">Preview 1 中引進的 [Owned] 屬性已移到此處。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-168">the [Owned] attribute introduced in Preview 1 was moved here.</span></span>

## <a name="state-change-events"></a><span data-ttu-id="a7bdd-169">狀態變更事件</span><span class="sxs-lookup"><span data-stu-id="a7bdd-169">State change events</span></span>

<span data-ttu-id="a7bdd-170">`ChangeTracker` 上的新 `Tracked` 和 `StateChanged` 事件可用於撰寫邏輯，該邏輯會針對進入 DbContext 或改變其狀態的實體進行回應。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-170">New `Tracked` And `StateChanged` events on `ChangeTracker` can be used to write logic that reacts to entities entering the DbContext or changing their state.</span></span>

## <a name="raw-sql-parameter-analyzer"></a><span data-ttu-id="a7bdd-171">原始的 SQL 參數分析器</span><span class="sxs-lookup"><span data-stu-id="a7bdd-171">Raw SQL parameter analyzer</span></span>

<span data-ttu-id="a7bdd-172">EF Core 隨附一個新的程式碼分析工具，可偵測原始 SQL API (例如 `FromSql` 或 `ExecuteSqlCommand`) 可能不安全的使用方式。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-172">A new code analyzer is included with EF Core that detects potentially unsafe usages of our raw-SQL APIs, like `FromSql` or `ExecuteSqlCommand`.</span></span> <span data-ttu-id="a7bdd-173">例如，</span><span class="sxs-lookup"><span data-stu-id="a7bdd-173">E.g.</span></span> <span data-ttu-id="a7bdd-174">下列查詢中，您將看到警告，因為 _minAge_ 未參數化：</span><span class="sxs-lookup"><span data-stu-id="a7bdd-174">for the following query, you will see a warning because _minAge_ is not parameterized:</span></span>

``` csharp
var sql = $"SELECT * FROM People WHERE Age > {minAge}";
var query = context.People.FromSql(sql);
```

## <a name="database-provider-compatibility"></a><span data-ttu-id="a7bdd-175">資料庫提供者相容性</span><span class="sxs-lookup"><span data-stu-id="a7bdd-175">Database provider compatibility</span></span>

<span data-ttu-id="a7bdd-176">EF Core 2.1 已設計成與專為 EF Core 2.0 建立的資料庫提供者相容，或至少需要極少的變更。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-176">EF Core 2.1 was designed to be compatible with database providers created for EF Core 2.0, or at least require minimal changes.</span></span> <span data-ttu-id="a7bdd-177">雖然上述某些功能 (例如值轉換) 需要更新的提供者，但其他功能 (例如消極式載入) 則受到現有提供者的支援。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-177">While some of the features described above (e.g. value conversions) require an updated provider, others (e.g. lazy loading) will light up with existing providers.</span></span>

> [!TIP]
> <span data-ttu-id="a7bdd-178">如果您在新功能中發現未預期的不相容或任何問題，或者如果您有相關意見反應，請使用[我們的問題追蹤程式](https://github.com/aspnet/EntityFrameworkCore/issues/new)進行回報。</span><span class="sxs-lookup"><span data-stu-id="a7bdd-178">If you find any unexpected incompatibility or any issue in the new features, or if you have feedback on them, please report it using [our issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues/new).</span></span>
