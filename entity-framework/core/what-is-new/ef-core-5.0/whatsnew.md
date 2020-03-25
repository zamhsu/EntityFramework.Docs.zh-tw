---
title: 5\.0 EF Core 的新功能
author: ajcvickers
ms.date: 03/15/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: 08a93555fd76d8a9f6d3011f59d9a34f76d0b22f
ms.sourcegitcommit: c3b8386071d64953ee68788ef9d951144881a6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "80136249"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="10074-102">5\.0 EF Core 的新功能</span><span class="sxs-lookup"><span data-stu-id="10074-102">What's New in EF Core 5.0</span></span>

<span data-ttu-id="10074-103">EF Core 5.0 目前正在開發中。</span><span class="sxs-lookup"><span data-stu-id="10074-103">EF Core 5.0 is currently in development.</span></span>
<span data-ttu-id="10074-104">此頁面將包含每個預覽中所引進之有趣變更的總覽。</span><span class="sxs-lookup"><span data-stu-id="10074-104">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="10074-105">此頁面不會複製[EF Core 5.0 的計畫](plan.md)。</span><span class="sxs-lookup"><span data-stu-id="10074-105">This page does not duplicate the [plan for EF Core 5.0](plan.md).</span></span>
<span data-ttu-id="10074-106">此計畫描述 EF Core 5.0 的整體主題，包括我們打算在交付最終版本之前包含的所有專案。</span><span class="sxs-lookup"><span data-stu-id="10074-106">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="10074-107">我們會將這裡的連結新增至正式檔，因為它已發佈。</span><span class="sxs-lookup"><span data-stu-id="10074-107">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-1"></a><span data-ttu-id="10074-108">Preview 1</span><span class="sxs-lookup"><span data-stu-id="10074-108">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="10074-109">簡單記錄</span><span class="sxs-lookup"><span data-stu-id="10074-109">Simple logging</span></span>

<span data-ttu-id="10074-110">這項功能會新增類似于 EF6 中 `Database.Log` 的功能。</span><span class="sxs-lookup"><span data-stu-id="10074-110">This feature adds functionality similar to `Database.Log` in EF6.</span></span>
<span data-ttu-id="10074-111">也就是說，它提供簡單的方法，讓您不需要設定任何類型的外部記錄架構，即可從 EF Core 取得記錄。</span><span class="sxs-lookup"><span data-stu-id="10074-111">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="10074-112">[2019 年12月5日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)會包含初稿檔。</span><span class="sxs-lookup"><span data-stu-id="10074-112">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="10074-113">其他檔則是由問題[#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085)追蹤。</span><span class="sxs-lookup"><span data-stu-id="10074-113">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="10074-114">取得產生之 SQL 的簡單方式</span><span class="sxs-lookup"><span data-stu-id="10074-114">Simple way to get generated SQL</span></span>

<span data-ttu-id="10074-115">EF Core 5.0 引進 `ToQueryString` 擴充方法，其會傳回執行 LINQ 查詢時 EF Core 將產生的 SQL。</span><span class="sxs-lookup"><span data-stu-id="10074-115">EF Core 5.0 introduces the `ToQueryString` extension method which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="10074-116">初稿檔包含在[2020 年1月9日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)。</span><span class="sxs-lookup"><span data-stu-id="10074-116">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="10074-117">其他檔則是由問題[#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331)追蹤。</span><span class="sxs-lookup"><span data-stu-id="10074-117">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="10074-118">使用C#屬性來表示實體沒有索引鍵</span><span class="sxs-lookup"><span data-stu-id="10074-118">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="10074-119">現在可以使用新的 `KeylessAttribute`，將實體類型設定為沒有任何索引鍵。</span><span class="sxs-lookup"><span data-stu-id="10074-119">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span>
<span data-ttu-id="10074-120">例如：</span><span class="sxs-lookup"><span data-stu-id="10074-120">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="10074-121">檔是由問題[#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186)追蹤。</span><span class="sxs-lookup"><span data-stu-id="10074-121">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="10074-122">連接或連接字串可以在初始化的 DbCoNtext 上變更</span><span class="sxs-lookup"><span data-stu-id="10074-122">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="10074-123">現在您可以更輕鬆地建立 DbCoNtext 實例，而不需要任何連接或連接字串。</span><span class="sxs-lookup"><span data-stu-id="10074-123">It is now easier to create a DbContext instance without any connection or connection string.</span></span>
<span data-ttu-id="10074-124">此外，連接或連接字串現在可以在內容實例上進行變化。</span><span class="sxs-lookup"><span data-stu-id="10074-124">Also, the connection or connection string can now be mutated on the context instance.</span></span>
<span data-ttu-id="10074-125">這可讓相同的內容實例動態連接到不同的資料庫。</span><span class="sxs-lookup"><span data-stu-id="10074-125">This allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="10074-126">檔是由問題[#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075)追蹤。</span><span class="sxs-lookup"><span data-stu-id="10074-126">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="10074-127">變更追蹤 proxy</span><span class="sxs-lookup"><span data-stu-id="10074-127">Change-tracking proxies</span></span>

<span data-ttu-id="10074-128">EF Core 現在可以產生自動執行[INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1)和[INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1)的執行時間 proxy。</span><span class="sxs-lookup"><span data-stu-id="10074-128">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span>
<span data-ttu-id="10074-129">這些之後，實體屬性上的這些值會直接變更為 EF Core，避免需要掃描變更。</span><span class="sxs-lookup"><span data-stu-id="10074-129">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span>
<span data-ttu-id="10074-130">不過，proxy 會有自己的一組限制，因此不適合所有人。</span><span class="sxs-lookup"><span data-stu-id="10074-130">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="10074-131">檔是由問題[#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076)追蹤。</span><span class="sxs-lookup"><span data-stu-id="10074-131">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="10074-132">增強的調試視圖</span><span class="sxs-lookup"><span data-stu-id="10074-132">Enhanced debug views</span></span>

<span data-ttu-id="10074-133">「偵錯工具」是一種簡單的方式，可在偵錯工具發生問題時查看 EF Core 的內部。</span><span class="sxs-lookup"><span data-stu-id="10074-133">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span>
<span data-ttu-id="10074-134">模型的「偵錯工具」視圖已在一段時間前完成。</span><span class="sxs-lookup"><span data-stu-id="10074-134">A debug view for the Model was implemented some time ago.</span></span>
<span data-ttu-id="10074-135">針對 EF Core 5.0，我們讓模型視圖更容易閱讀，並在狀態管理員中為追蹤的實體新增新的 [偵錯工具]。</span><span class="sxs-lookup"><span data-stu-id="10074-135">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="10074-136">[2019 年12月12日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)會包含初稿檔。</span><span class="sxs-lookup"><span data-stu-id="10074-136">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="10074-137">其他檔則是由問題[#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086)追蹤。</span><span class="sxs-lookup"><span data-stu-id="10074-137">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="10074-138">改善資料庫 null 語義的處理</span><span class="sxs-lookup"><span data-stu-id="10074-138">Improved handling of database null semantics</span></span>

<span data-ttu-id="10074-139">關係資料庫通常會將 Null 視為未知的值，因此不等於任何其他 Null。</span><span class="sxs-lookup"><span data-stu-id="10074-139">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span>
<span data-ttu-id="10074-140">C#相反地，會將 null 視為已定義的值，其比較是否等於任何其他 null。</span><span class="sxs-lookup"><span data-stu-id="10074-140">C#, on the other hand, treats null as a defined value which compares equal to any other null.</span></span>
<span data-ttu-id="10074-141">EF Core 預設會轉譯查詢，使其使用C# null 的語義。</span><span class="sxs-lookup"><span data-stu-id="10074-141">EF Core by default translates queries so that they use C# null semantics.</span></span>
<span data-ttu-id="10074-142">EF Core 5.0 大幅提升了這些翻譯的效率。</span><span class="sxs-lookup"><span data-stu-id="10074-142">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="10074-143">檔是由問題[#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612)追蹤。</span><span class="sxs-lookup"><span data-stu-id="10074-143">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="10074-144">索引子屬性</span><span class="sxs-lookup"><span data-stu-id="10074-144">Indexer properties</span></span>

<span data-ttu-id="10074-145">EF Core 5.0 支援索引子C#屬性的對應。</span><span class="sxs-lookup"><span data-stu-id="10074-145">EF Core 5.0 supports mapping of C# indexer properties.</span></span>
<span data-ttu-id="10074-146">這可讓實體作為屬性包，其中的資料行會對應到包中的命名屬性。</span><span class="sxs-lookup"><span data-stu-id="10074-146">This allows entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="10074-147">檔是由問題[#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018)追蹤。</span><span class="sxs-lookup"><span data-stu-id="10074-147">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="10074-148">列舉對應的檢查條件約束產生</span><span class="sxs-lookup"><span data-stu-id="10074-148">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="10074-149">EF Core 5.0 遷移現在可以產生 enum 屬性對應的檢查條件約束。</span><span class="sxs-lookup"><span data-stu-id="10074-149">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span>
<span data-ttu-id="10074-150">例如：</span><span class="sxs-lookup"><span data-stu-id="10074-150">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="10074-151">檔是由問題[#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082)追蹤。</span><span class="sxs-lookup"><span data-stu-id="10074-151">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="10074-152">IsRelational</span><span class="sxs-lookup"><span data-stu-id="10074-152">IsRelational</span></span>

<span data-ttu-id="10074-153">除了現有的 `IsSqlServer`、`IsSqlite`和 `IsInMemory`以外，還加入了新的 `IsRelational` 方法。</span><span class="sxs-lookup"><span data-stu-id="10074-153">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span>
<span data-ttu-id="10074-154">這可以用來測試 DbCoNtext 是否使用任何關係資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="10074-154">This can be used to test if the DbContext is using any relational database provider.</span></span>
<span data-ttu-id="10074-155">例如：</span><span class="sxs-lookup"><span data-stu-id="10074-155">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="10074-156">檔是由問題[#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185)追蹤。</span><span class="sxs-lookup"><span data-stu-id="10074-156">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="10074-157">使用 Etag Cosmos 開放式平行存取</span><span class="sxs-lookup"><span data-stu-id="10074-157">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="10074-158">Azure Cosmos DB 資料庫提供者現在支援使用 Etag 的開放式平行存取。</span><span class="sxs-lookup"><span data-stu-id="10074-158">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span>
<span data-ttu-id="10074-159">使用 OnModelCreating 中的模型產生器來設定 ETag：</span><span class="sxs-lookup"><span data-stu-id="10074-159">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="10074-160">接著，SaveChanges 會擲回並行衝突的 `DbUpdateConcurrencyException`，[可加以處理](https://docs.microsoft.com/ef/core/saving/concurrency)以執行重試等。</span><span class="sxs-lookup"><span data-stu-id="10074-160">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](https://docs.microsoft.com/ef/core/saving/concurrency) to implement retries, etc.</span></span>


<span data-ttu-id="10074-161">檔是由問題[#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099)追蹤。</span><span class="sxs-lookup"><span data-stu-id="10074-161">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="10074-162">更多日期時間結構的查詢翻譯</span><span class="sxs-lookup"><span data-stu-id="10074-162">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="10074-163">包含新日期時間結構的查詢現在已轉譯。</span><span class="sxs-lookup"><span data-stu-id="10074-163">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="10074-164">此外，現在已對應下列 SQL Server 函式：</span><span class="sxs-lookup"><span data-stu-id="10074-164">In addition, the following SQL Server functions are now mapped:</span></span>
* <span data-ttu-id="10074-165">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="10074-165">DateDiffWeek</span></span>
* <span data-ttu-id="10074-166">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="10074-166">DateFromParts</span></span>

<span data-ttu-id="10074-167">例如：</span><span class="sxs-lookup"><span data-stu-id="10074-167">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="10074-168">檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="10074-168">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="10074-169">更多位元組陣列結構的查詢翻譯</span><span class="sxs-lookup"><span data-stu-id="10074-169">Query translations for more byte array constructs</span></span>

<span data-ttu-id="10074-170">在 byte [] 屬性上使用 Contains、Length、Enumerable.sequenceequal 等的查詢現在會轉譯為 SQL。</span><span class="sxs-lookup"><span data-stu-id="10074-170">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="10074-171">[2019 年12月5日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)會包含初稿檔。</span><span class="sxs-lookup"><span data-stu-id="10074-171">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="10074-172">其他檔則是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="10074-172">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="10074-173">反向的查詢轉譯</span><span class="sxs-lookup"><span data-stu-id="10074-173">Query translation for Reverse</span></span>

<span data-ttu-id="10074-174">現在會轉譯使用 `Reverse` 的查詢。</span><span class="sxs-lookup"><span data-stu-id="10074-174">Queries using `Reverse` are now translated.</span></span>
<span data-ttu-id="10074-175">例如：</span><span class="sxs-lookup"><span data-stu-id="10074-175">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="10074-176">檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="10074-176">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="10074-177">位運算子的查詢轉譯</span><span class="sxs-lookup"><span data-stu-id="10074-177">Query translation for bitwise operators</span></span>

<span data-ttu-id="10074-178">使用位運算子的查詢現在會在更多情況下轉譯，例如：</span><span class="sxs-lookup"><span data-stu-id="10074-178">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="10074-179">檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="10074-179">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="10074-180">Cosmos 上字串的查詢轉譯</span><span class="sxs-lookup"><span data-stu-id="10074-180">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="10074-181">使用 Azure Cosmos DB 提供者時，現在會轉譯使用字串方法 Contains、StartsWith 和 EndsWith 的查詢。</span><span class="sxs-lookup"><span data-stu-id="10074-181">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="10074-182">檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="10074-182">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
