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
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="3450b-103">5.0 EF Core 的新功能</span><span class="sxs-lookup"><span data-stu-id="3450b-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="3450b-104">EF Core 5.0 目前正在開發中。</span><span class="sxs-lookup"><span data-stu-id="3450b-104">EF Core 5.0 is currently in development.</span></span> <span data-ttu-id="3450b-105">此頁面將包含每個預覽中所引進之有趣變更的總覽。</span><span class="sxs-lookup"><span data-stu-id="3450b-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="3450b-106">此頁面不會複製[EF Core 5.0 的計畫](xref:core/what-is-new/ef-core-5.0/plan)。</span><span class="sxs-lookup"><span data-stu-id="3450b-106">This page does not duplicate the [plan for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span></span> <span data-ttu-id="3450b-107">此計畫描述 EF Core 5.0 的整體主題，包括我們打算在交付最終版本之前包含的所有專案。</span><span class="sxs-lookup"><span data-stu-id="3450b-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="3450b-108">我們會將這裡的連結新增至正式檔，因為它已發佈。</span><span class="sxs-lookup"><span data-stu-id="3450b-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-6"></a><span data-ttu-id="3450b-109">Preview 6</span><span class="sxs-lookup"><span data-stu-id="3450b-109">Preview 6</span></span>

### <a name="split-queries-for-related-collections"></a><span data-ttu-id="3450b-110">相關集合的分割查詢</span><span class="sxs-lookup"><span data-stu-id="3450b-110">Split queries for related collections</span></span>

<span data-ttu-id="3450b-111">從 EF Core 3.0 開始，EF Core 一律會針對每個 LINQ 查詢產生單一 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="3450b-111">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="3450b-112">這可確保在使用中交易模式的條件約束內傳回的資料一致。</span><span class="sxs-lookup"><span data-stu-id="3450b-112">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="3450b-113">不過，當查詢使用 `Include` 或預測來重新顯示多個相關的集合時，這可能會變得非常緩慢。</span><span class="sxs-lookup"><span data-stu-id="3450b-113">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="3450b-114">EF Core 5.0 現在允許單一 LINQ 查詢，包括要分割成多個 SQL 查詢的相關集合。</span><span class="sxs-lookup"><span data-stu-id="3450b-114">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="3450b-115">這可以大幅提升效能，但如果兩個查詢之間的資料有所變更，可能會導致傳回的結果不一致。</span><span class="sxs-lookup"><span data-stu-id="3450b-115">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="3450b-116">可序列化或快照集交易可以用來減輕這個問題，並利用分割查詢達成一致性，但這可能會帶來其他效能成本和行為上的差異。</span><span class="sxs-lookup"><span data-stu-id="3450b-116">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

#### <a name="split-queries-with-include"></a><span data-ttu-id="3450b-117">使用 Include 分割查詢</span><span class="sxs-lookup"><span data-stu-id="3450b-117">Split queries with Include</span></span>

<span data-ttu-id="3450b-118">例如，假設有一個查詢會使用提取兩個層級的相關集合 `Include` ：</span><span class="sxs-lookup"><span data-stu-id="3450b-118">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```CSharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="3450b-119">根據預設，當使用 SQLite 提供者時，EF Core 將會產生下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="3450b-119">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

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

<span data-ttu-id="3450b-120">新的 `AsSplitQuery` API 可用於變更此行為。</span><span class="sxs-lookup"><span data-stu-id="3450b-120">The new `AsSplitQuery` API can be used to change this behavior.</span></span> <span data-ttu-id="3450b-121">例如：</span><span class="sxs-lookup"><span data-stu-id="3450b-121">For example:</span></span>

```CSharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="3450b-122">AsSplitQuery 適用于所有關系資料庫提供者，而且可以在查詢中的任何位置使用，就像 AsNoTracking 一樣。</span><span class="sxs-lookup"><span data-stu-id="3450b-122">AsSplitQuery is available for all relational database providers and can be used anywhere in the query, just like AsNoTracking.</span></span> <span data-ttu-id="3450b-123">EF Core 現在會產生下列三個 SQL 查詢：</span><span class="sxs-lookup"><span data-stu-id="3450b-123">EF Core will now generate the following three SQL queries:</span></span>

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

<span data-ttu-id="3450b-124">支援查詢根目錄上的所有作業。</span><span class="sxs-lookup"><span data-stu-id="3450b-124">All operations on the query root are supported.</span></span> <span data-ttu-id="3450b-125">這包括 OrderBy/Skip/Take、Join 作業、FirstOrDefault 和類似的單一結果選取作業。</span><span class="sxs-lookup"><span data-stu-id="3450b-125">This includes OrderBy/Skip/Take, Join operations, FirstOrDefault and similar single result selecting operations.</span></span>

<span data-ttu-id="3450b-126">請注意，preview 6 不支援具有 OrderBy/Skip/Take 的篩選包含，但在每日組建中都有提供，而且將包含在 preview 7 中。</span><span class="sxs-lookup"><span data-stu-id="3450b-126">Note that filtered Includes with OrderBy/Skip/Take are not supported in preview 6, but are available in the daily builds and will be included in preview 7.</span></span>

#### <a name="split-queries-with-collection-projections"></a><span data-ttu-id="3450b-127">使用集合投射分割查詢</span><span class="sxs-lookup"><span data-stu-id="3450b-127">Split queries with collection projections</span></span>

<span data-ttu-id="3450b-128">`AsSplitQuery`在投影中載入集合時，也可以使用。</span><span class="sxs-lookup"><span data-stu-id="3450b-128">`AsSplitQuery` can also be used when collections are loaded in projections.</span></span> <span data-ttu-id="3450b-129">例如：</span><span class="sxs-lookup"><span data-stu-id="3450b-129">For example:</span></span>

```CSharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

<span data-ttu-id="3450b-130">使用 SQLite 提供者時，此 LINQ 查詢會產生下列兩個 SQL 查詢：</span><span class="sxs-lookup"><span data-stu-id="3450b-130">This LINQ query generates the following two SQL queries when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

<span data-ttu-id="3450b-131">請注意，只支援集合的具體化。</span><span class="sxs-lookup"><span data-stu-id="3450b-131">Note that only materialization of the collection is supported.</span></span> <span data-ttu-id="3450b-132">`e.Albums`在上述情況下的任何組合都不會產生分割查詢。</span><span class="sxs-lookup"><span data-stu-id="3450b-132">Any composition after `e.Albums` in above case won't result in a split query.</span></span> <span data-ttu-id="3450b-133">此區域中的改進功能會由[#21234](https://github.com/dotnet/efcore/issues/21234)追蹤。</span><span class="sxs-lookup"><span data-stu-id="3450b-133">Improvements in this area are tracked by [#21234](https://github.com/dotnet/efcore/issues/21234).</span></span>

### <a name="indexattribute"></a><span data-ttu-id="3450b-134">IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="3450b-134">IndexAttribute</span></span>

<span data-ttu-id="3450b-135">新的 IndexAttribute 可以放在實體類型上，以指定單一資料行的索引。</span><span class="sxs-lookup"><span data-stu-id="3450b-135">The new IndexAttribute can be placed on an entity type to specify an index for a single column.</span></span> <span data-ttu-id="3450b-136">例如：</span><span class="sxs-lookup"><span data-stu-id="3450b-136">For example:</span></span>

```CSharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }
    
    [MaxLength(128)]
    public string FullName { get; set; }
}
```

<span data-ttu-id="3450b-137">針對 SQL Server，遷移將會產生下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="3450b-137">For SQL Server, Migrations will then generate the following SQL:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

<span data-ttu-id="3450b-138">IndexAttribute 也可以用來指定跨越多個資料行的索引。</span><span class="sxs-lookup"><span data-stu-id="3450b-138">IndexAttribute can also be used to specify an index spanning multiple columns.</span></span> <span data-ttu-id="3450b-139">例如：</span><span class="sxs-lookup"><span data-stu-id="3450b-139">For example:</span></span>

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

<span data-ttu-id="3450b-140">針對 SQL Server，這會導致：</span><span class="sxs-lookup"><span data-stu-id="3450b-140">For SQL Server, this results in:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

<span data-ttu-id="3450b-141">檔是由問題[#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407)追蹤。</span><span class="sxs-lookup"><span data-stu-id="3450b-141">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="improved-query-translation-exceptions"></a><span data-ttu-id="3450b-142">改善的查詢轉譯例外狀況</span><span class="sxs-lookup"><span data-stu-id="3450b-142">Improved query translation exceptions</span></span>

<span data-ttu-id="3450b-143">我們會繼續改善查詢轉譯失敗時所產生的例外狀況訊息。</span><span class="sxs-lookup"><span data-stu-id="3450b-143">We are continuing to improve the exception messages generated when query translation fails.</span></span> <span data-ttu-id="3450b-144">例如，此查詢會使用未對應的屬性 `IsSigned` ：</span><span class="sxs-lookup"><span data-stu-id="3450b-144">For example, this query uses the unmapped property `IsSigned`:</span></span>

```CSharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

<span data-ttu-id="3450b-145">EF Core 將會擲回下列例外狀況，指出轉譯失敗，因為未 `IsSigned` 對應：</span><span class="sxs-lookup"><span data-stu-id="3450b-145">EF Core will throw the following exception indicating that translation failed because `IsSigned` is not mapped:</span></span>

> <span data-ttu-id="3450b-146">未處理的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="3450b-146">Unhandled exception.</span></span> <span data-ttu-id="3450b-147">InvalidOperationException： LINQ 運算式的 DbSet <Artist> （）。Where （a => IsSigned） ' 無法轉譯。</span><span class="sxs-lookup"><span data-stu-id="3450b-147">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.IsSigned)' could not be translated.</span></span> <span data-ttu-id="3450b-148">其他資訊：實體類型 ' 演出者 ' 上成員 ' IsSigned ' 的轉譯失敗。</span><span class="sxs-lookup"><span data-stu-id="3450b-148">Additional information: Translation of member 'IsSigned' on entity type 'Artist' failed.</span></span> <span data-ttu-id="3450b-149">可能是指定的成員未對應。</span><span class="sxs-lookup"><span data-stu-id="3450b-149">Possibly the specified member is not mapped.</span></span> <span data-ttu-id="3450b-150">請以可翻譯的形式重寫查詢，或將呼叫插入 Enumerable.asenumerable （）、AsAsyncEnumerable （）、ToList （）或 ToListAsync （），以明確地切換至用戶端評估。</span><span class="sxs-lookup"><span data-stu-id="3450b-150">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="3450b-151">如需相關資訊，請參閱 https://go.microsoft.com/fwlink/?linkid=2101038 。</span><span class="sxs-lookup"><span data-stu-id="3450b-151">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

<span data-ttu-id="3450b-152">同樣地，當嘗試使用與文化特性相關的語義來轉譯字串比較時，現在會產生更好的例外狀況訊息。</span><span class="sxs-lookup"><span data-stu-id="3450b-152">Similarly, better exception messages are now generated when attempting to translate string comparisons with culture-dependent semantics.</span></span> <span data-ttu-id="3450b-153">例如，此查詢會嘗試使用 `StringComparison.CurrentCulture` ：</span><span class="sxs-lookup"><span data-stu-id="3450b-153">For example, this query attempts to use `StringComparison.CurrentCulture`:</span></span>

```CSharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

<span data-ttu-id="3450b-154">EF Core 現在會擲回下列例外狀況：</span><span class="sxs-lookup"><span data-stu-id="3450b-154">EF Core will now throw the following exception:</span></span>

> <span data-ttu-id="3450b-155">未處理的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="3450b-155">Unhandled exception.</span></span> <span data-ttu-id="3450b-156">InvalidOperationException： LINQ 運算式的 DbSet <Artist> （）。其中（a =>. Name. Equals （值： "the 獨角獸"，comparisonType： CurrentCulture）） ' 無法轉譯。</span><span class="sxs-lookup"><span data-stu-id="3450b-156">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))' could not be translated.</span></span> <span data-ttu-id="3450b-157">其他資訊： ' string ' 的轉譯。不支援採用 ' StringComparison ' 引數的 Equals ' 方法。</span><span class="sxs-lookup"><span data-stu-id="3450b-157">Additional information: Translation of 'string.Equals' method which takes 'StringComparison' argument is not supported.</span></span> <span data-ttu-id="3450b-158">如需相關資訊，請參閱 https://go.microsoft.com/fwlink/?linkid=2129535 。</span><span class="sxs-lookup"><span data-stu-id="3450b-158">See https://go.microsoft.com/fwlink/?linkid=2129535 for more information.</span></span> <span data-ttu-id="3450b-159">請以可翻譯的形式重寫查詢，或將呼叫插入 Enumerable.asenumerable （）、AsAsyncEnumerable （）、ToList （）或 ToListAsync （），以明確地切換至用戶端評估。</span><span class="sxs-lookup"><span data-stu-id="3450b-159">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="3450b-160">如需相關資訊，請參閱 https://go.microsoft.com/fwlink/?linkid=2101038 。</span><span class="sxs-lookup"><span data-stu-id="3450b-160">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

### <a name="specify-transaction-id"></a><span data-ttu-id="3450b-161">指定交易識別碼</span><span class="sxs-lookup"><span data-stu-id="3450b-161">Specify transaction ID</span></span>

<span data-ttu-id="3450b-162">這項功能是由的「社區」提供 [@Marusyk](https://github.com/Marusyk) 。</span><span class="sxs-lookup"><span data-stu-id="3450b-162">This feature was contributed from the community by [@Marusyk](https://github.com/Marusyk).</span></span> <span data-ttu-id="3450b-163">感謝您的貢獻！</span><span class="sxs-lookup"><span data-stu-id="3450b-163">Many thanks for the contribution!</span></span>

<span data-ttu-id="3450b-164">EF Core 會公開交易識別碼，以便在呼叫之間相互關聯。</span><span class="sxs-lookup"><span data-stu-id="3450b-164">EF Core exposes a transaction ID for correlation of transactions across calls.</span></span> <span data-ttu-id="3450b-165">此識別碼通常會在交易啟動時由 EF Core 設定。</span><span class="sxs-lookup"><span data-stu-id="3450b-165">This ID typically set by EF Core when a transaction is started.</span></span> <span data-ttu-id="3450b-166">如果應用程式改為啟動交易，則此功能可讓應用程式明確設定交易識別碼，讓它在使用的任何地方都能正確地相互關聯。</span><span class="sxs-lookup"><span data-stu-id="3450b-166">If the application starts the transaction instead, then this feature allows the application to explicitly set the transaction ID so it is correlated correctly everywhere it is used.</span></span> <span data-ttu-id="3450b-167">例如：</span><span class="sxs-lookup"><span data-stu-id="3450b-167">For example:</span></span>

```CSharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a><span data-ttu-id="3450b-168">IPAddress 對應</span><span class="sxs-lookup"><span data-stu-id="3450b-168">IPAddress mapping</span></span>

<span data-ttu-id="3450b-169">這項功能是由的「社區」提供 [@ralmsdeveloper](https://github.com/ralmsdeveloper) 。</span><span class="sxs-lookup"><span data-stu-id="3450b-169">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="3450b-170">感謝您的貢獻！</span><span class="sxs-lookup"><span data-stu-id="3450b-170">Many thanks for the contribution!</span></span>

<span data-ttu-id="3450b-171">標準 .NET [IPAddress 類別](/dotnet/api/system.net.ipaddress)現在會自動對應至尚未具備原生支援之資料庫的字串資料行。</span><span class="sxs-lookup"><span data-stu-id="3450b-171">The standard .NET [IPAddress class](/dotnet/api/system.net.ipaddress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="3450b-172">例如，請考慮對應此實體類型：</span><span class="sxs-lookup"><span data-stu-id="3450b-172">For example, consider mapping this entity type:</span></span>

```CSharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

<span data-ttu-id="3450b-173">在 SQL Server 上，這會導致遷移建立下列資料表：</span><span class="sxs-lookup"><span data-stu-id="3450b-173">On SQL Server, this will result in Migrations creating the following table:</span></span>

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
``` 

<span data-ttu-id="3450b-174">然後，實體可以透過正常方式新增：</span><span class="sxs-lookup"><span data-stu-id="3450b-174">Entities can then be added in the normal way:</span></span>

```CSharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
``` 

<span data-ttu-id="3450b-175">而產生的 SQL 會插入正規化的 IPv4 或 IPv6 位址：</span><span class="sxs-lookup"><span data-stu-id="3450b-175">And the resulting SQL will insert the normalized IPv4 or IPv6 address:</span></span>

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a><span data-ttu-id="3450b-176">在架構時排除 OnConfiguring</span><span class="sxs-lookup"><span data-stu-id="3450b-176">Exclude OnConfiguring when scaffolding</span></span>

<span data-ttu-id="3450b-177">從現有的資料庫 scaffold DbCoNtext 時，EF Core 預設會使用連接字串建立 OnConfiguring 多載，以便立即使用內容。</span><span class="sxs-lookup"><span data-stu-id="3450b-177">When a DbContext is scaffolded from an existing database, EF Core by default creates an OnConfiguring overload with a connection string so that the context is immediately usable.</span></span> <span data-ttu-id="3450b-178">不過，如果您已經有部分類別具有 OnConfiguring，或如果您要以其他方式設定內容，這就不會很有用。</span><span class="sxs-lookup"><span data-stu-id="3450b-178">However, this is not useful if you already have a partial class with OnConfiguring, or if you are configuring the context some other way.</span></span>

<span data-ttu-id="3450b-179">為了解決這個情況，現在可以指示樣板命令省略產生 OnConfiguring。</span><span class="sxs-lookup"><span data-stu-id="3450b-179">To address this, the scaffolding commands can now be instructed to omit generation of OnConfiguring.</span></span> <span data-ttu-id="3450b-180">例如：</span><span class="sxs-lookup"><span data-stu-id="3450b-180">For example:</span></span>

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

<span data-ttu-id="3450b-181">或在 [套件管理員主控台] 中：</span><span class="sxs-lookup"><span data-stu-id="3450b-181">Or in the Package Manager Console:</span></span>

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring 
``` 

<span data-ttu-id="3450b-182">請注意，我們建議使用[已命名的連接字串，並保護像是使用者秘密的儲存體](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets)。</span><span class="sxs-lookup"><span data-stu-id="3450b-182">Note that we recommend using [a named connection string and secure storage like User Secrets](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).</span></span>

### <a name="translations-for-firstordefault-on-strings"></a><span data-ttu-id="3450b-183">字串上 FirstOrDefault 的翻譯</span><span class="sxs-lookup"><span data-stu-id="3450b-183">Translations for FirstOrDefault on strings</span></span>

<span data-ttu-id="3450b-184">這項功能是由的「社區」提供 [@dvoreckyaa](https://github.com/dvoreckyaa) 。</span><span class="sxs-lookup"><span data-stu-id="3450b-184">This feature was contributed from the community by [@dvoreckyaa](https://github.com/dvoreckyaa).</span></span> <span data-ttu-id="3450b-185">感謝您的貢獻！</span><span class="sxs-lookup"><span data-stu-id="3450b-185">Many thanks for the contribution!</span></span>

<span data-ttu-id="3450b-186">現在會轉譯字串中字元的 FirstOrDefault 和類似運算子。</span><span class="sxs-lookup"><span data-stu-id="3450b-186">FirstOrDefault and similar operators for characters in strings are now translated.</span></span> <span data-ttu-id="3450b-187">例如，此 LINQ 查詢：</span><span class="sxs-lookup"><span data-stu-id="3450b-187">For example, this LINQ query:</span></span>

```CSharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

<span data-ttu-id="3450b-188">使用 SQL Server 時，將會轉譯為下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="3450b-188">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a><span data-ttu-id="3450b-189">簡化案例區塊</span><span class="sxs-lookup"><span data-stu-id="3450b-189">Simplify case blocks</span></span>

<span data-ttu-id="3450b-190">EF Core 現在會以案例區塊產生更佳的查詢。</span><span class="sxs-lookup"><span data-stu-id="3450b-190">EF Core now generates better queries with CASE blocks.</span></span> <span data-ttu-id="3450b-191">例如，此 LINQ 查詢：</span><span class="sxs-lookup"><span data-stu-id="3450b-191">For example, this LINQ query:</span></span> 

```CSharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

<span data-ttu-id="3450b-192">Was SQL Server 正式轉譯為：</span><span class="sxs-lookup"><span data-stu-id="3450b-192">Was on SQL Server formally translated to:</span></span>

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

<span data-ttu-id="3450b-193">但現在已轉譯為：</span><span class="sxs-lookup"><span data-stu-id="3450b-193">But is now translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
``` 

## <a name="preview-5"></a><span data-ttu-id="3450b-194">Preview 5</span><span class="sxs-lookup"><span data-stu-id="3450b-194">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="3450b-195">資料庫定序</span><span class="sxs-lookup"><span data-stu-id="3450b-195">Database collations</span></span>

<span data-ttu-id="3450b-196">現在，您可以在 EF 模型中指定資料庫的預設定序。</span><span class="sxs-lookup"><span data-stu-id="3450b-196">The default collation for a database can now be specified in the EF model.</span></span> <span data-ttu-id="3450b-197">這會傳送至產生的遷移，以便在建立資料庫時設定定序。</span><span class="sxs-lookup"><span data-stu-id="3450b-197">This will flow through to generated migrations to set the collation when the database is created.</span></span> <span data-ttu-id="3450b-198">例如：</span><span class="sxs-lookup"><span data-stu-id="3450b-198">For example:</span></span>

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="3450b-199">接著，遷移會產生下列內容，以在 SQL Server 上建立資料庫：</span><span class="sxs-lookup"><span data-stu-id="3450b-199">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="3450b-200">您也可以指定用於特定資料庫資料行的定序。</span><span class="sxs-lookup"><span data-stu-id="3450b-200">The collation to use for specific database columns can also be specified.</span></span> <span data-ttu-id="3450b-201">例如：</span><span class="sxs-lookup"><span data-stu-id="3450b-201">For example:</span></span>

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="3450b-202">對於未使用遷移的人員，定序現在會在 DbCoNtext 架構時，從資料庫進行反向工程。</span><span class="sxs-lookup"><span data-stu-id="3450b-202">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="3450b-203">最後， `EF.Functions.Collate()` 允許使用不同定序的特定查詢。</span><span class="sxs-lookup"><span data-stu-id="3450b-203">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span> <span data-ttu-id="3450b-204">例如：</span><span class="sxs-lookup"><span data-stu-id="3450b-204">For example:</span></span>

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="3450b-205">這會產生 SQL Server 的下列查詢：</span><span class="sxs-lookup"><span data-stu-id="3450b-205">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="3450b-206">請注意，特定定序應謹慎使用，因為它們可能會對資料庫效能造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="3450b-206">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="3450b-207">檔是由問題[#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273)追蹤。</span><span class="sxs-lookup"><span data-stu-id="3450b-207">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="3450b-208">IDesignTimeDbCoNtextFactory 中的流程引數</span><span class="sxs-lookup"><span data-stu-id="3450b-208">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="3450b-209">引數現在會從命令列流動到 `CreateDbContext` [IDesignTimeDbCoNtextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1)的方法中。</span><span class="sxs-lookup"><span data-stu-id="3450b-209">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span></span> <span data-ttu-id="3450b-210">例如，若要指出這是開發組建， `dev` 可以在命令列上傳遞自訂引數（例如）：</span><span class="sxs-lookup"><span data-stu-id="3450b-210">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can passed on the command line:</span></span>

```
dotnet ef migrations add two --verbose --dev
``` 

<span data-ttu-id="3450b-211">這個引數接著會流入處理站，它可以用來控制內容的建立和初始化方式。</span><span class="sxs-lookup"><span data-stu-id="3450b-211">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span> <span data-ttu-id="3450b-212">例如：</span><span class="sxs-lookup"><span data-stu-id="3450b-212">For example:</span></span>

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args) 
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="3450b-213">檔是由問題[#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419)追蹤。</span><span class="sxs-lookup"><span data-stu-id="3450b-213">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="3450b-214">沒有識別解析的追蹤查詢</span><span class="sxs-lookup"><span data-stu-id="3450b-214">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="3450b-215">現在沒有任何追蹤查詢可以設定為執行識別解析。</span><span class="sxs-lookup"><span data-stu-id="3450b-215">No-tracking queries can now be configured to perform identity resolution.</span></span> <span data-ttu-id="3450b-216">例如，下列查詢會針對每個貼文建立新的 Blog 實例，即使每個 Blog 都有相同的主要金鑰。</span><span class="sxs-lookup"><span data-stu-id="3450b-216">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span> 

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="3450b-217">不過，通常會稍微變慢，而且一定會使用更多的記憶體，因此可以變更此查詢，以確保只會建立單一的 Blog 實例：</span><span class="sxs-lookup"><span data-stu-id="3450b-217">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="3450b-218">請注意，這只適用于沒有追蹤的查詢，因為所有追蹤查詢都已經呈現此行為。</span><span class="sxs-lookup"><span data-stu-id="3450b-218">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="3450b-219">此外，在遵循 API 審查之後， `PerformIdentityResolution` 語法將會變更。</span><span class="sxs-lookup"><span data-stu-id="3450b-219">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span> <span data-ttu-id="3450b-220">請參閱[#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073)。</span><span class="sxs-lookup"><span data-stu-id="3450b-220">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="3450b-221">檔是由問題[#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895)追蹤。</span><span class="sxs-lookup"><span data-stu-id="3450b-221">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="3450b-222">儲存的（保存）計算資料行</span><span class="sxs-lookup"><span data-stu-id="3450b-222">Stored (persisted) computed columns</span></span>

<span data-ttu-id="3450b-223">大部分的資料庫都允許在計算後儲存計算的資料行值。</span><span class="sxs-lookup"><span data-stu-id="3450b-223">Most databases allow computed column values to be stored after computation.</span></span> <span data-ttu-id="3450b-224">雖然這會佔用磁碟空間，但計算的資料行只會在 update 上計算一次，而不是每次抓取它的值。</span><span class="sxs-lookup"><span data-stu-id="3450b-224">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span> <span data-ttu-id="3450b-225">這也可讓某些資料庫的資料行編制索引。</span><span class="sxs-lookup"><span data-stu-id="3450b-225">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="3450b-226">EF Core 5.0 允許將計算資料行設定為已儲存。</span><span class="sxs-lookup"><span data-stu-id="3450b-226">EF Core 5.0 allows computed columns to be configured as stored.</span></span> <span data-ttu-id="3450b-227">例如：</span><span class="sxs-lookup"><span data-stu-id="3450b-227">For example:</span></span>
 
```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="3450b-228">SQLite 計算資料行</span><span class="sxs-lookup"><span data-stu-id="3450b-228">SQLite computed columns</span></span>

<span data-ttu-id="3450b-229">EF Core 現在支援 SQLite 資料庫中的計算資料行。</span><span class="sxs-lookup"><span data-stu-id="3450b-229">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="3450b-230">Preview 4</span><span class="sxs-lookup"><span data-stu-id="3450b-230">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="3450b-231">在模型中設定資料庫精確度/規模</span><span class="sxs-lookup"><span data-stu-id="3450b-231">Configure database precision/scale in model</span></span>

<span data-ttu-id="3450b-232">屬性的有效位數和小數位數現在可以使用模型產生器來指定。</span><span class="sxs-lookup"><span data-stu-id="3450b-232">Precision and scale for a property can now be specified using the model builder.</span></span> <span data-ttu-id="3450b-233">例如：</span><span class="sxs-lookup"><span data-stu-id="3450b-233">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="3450b-234">精確度和小數位數仍然可以透過完整資料庫類型來設定，例如 "decimal （16，4）"。</span><span class="sxs-lookup"><span data-stu-id="3450b-234">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span> 

<span data-ttu-id="3450b-235">檔是由問題[#527](https://github.com/dotnet/EntityFramework.Docs/issues/527)追蹤。</span><span class="sxs-lookup"><span data-stu-id="3450b-235">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="3450b-236">指定 SQL Server 索引填滿因數</span><span class="sxs-lookup"><span data-stu-id="3450b-236">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="3450b-237">在 SQL Server 上建立索引時，現在可以指定填滿因數。</span><span class="sxs-lookup"><span data-stu-id="3450b-237">The fill factor can now be specified when creating an index on SQL Server.</span></span> <span data-ttu-id="3450b-238">例如：</span><span class="sxs-lookup"><span data-stu-id="3450b-238">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="3450b-239">Preview 3</span><span class="sxs-lookup"><span data-stu-id="3450b-239">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="3450b-240">篩選的包含</span><span class="sxs-lookup"><span data-stu-id="3450b-240">Filtered Include</span></span>

<span data-ttu-id="3450b-241">Include 方法現在支援篩選包含的實體。</span><span class="sxs-lookup"><span data-stu-id="3450b-241">The Include method now supports filtering of the entities included.</span></span> <span data-ttu-id="3450b-242">例如：</span><span class="sxs-lookup"><span data-stu-id="3450b-242">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="3450b-243">此查詢將會連同每個相關聯的貼文一起傳回 blog，但只有在貼文標題包含「乳酪」時。</span><span class="sxs-lookup"><span data-stu-id="3450b-243">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="3450b-244">Skip 和 Take 也可以用來減少包含的實體數目。</span><span class="sxs-lookup"><span data-stu-id="3450b-244">Skip and Take can also be used to reduce the number of included entities.</span></span> <span data-ttu-id="3450b-245">例如：</span><span class="sxs-lookup"><span data-stu-id="3450b-245">For example:</span></span>
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="3450b-246">此查詢將會傳回包含每個 blog 中最多五篇文章的 blog。</span><span class="sxs-lookup"><span data-stu-id="3450b-246">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="3450b-247">如需完整詳細資料，請參閱[包含檔](xref:core/querying/related-data#filtered-include)。</span><span class="sxs-lookup"><span data-stu-id="3450b-247">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="3450b-248">導覽屬性的新 ModelBuilder API</span><span class="sxs-lookup"><span data-stu-id="3450b-248">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="3450b-249">導覽屬性主要是在[定義關聯](xref:core/modeling/relationships)性時設定。</span><span class="sxs-lookup"><span data-stu-id="3450b-249">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span> <span data-ttu-id="3450b-250">不過，在 `Navigation` 導覽屬性需要其他設定的情況下，可以使用新的方法。</span><span class="sxs-lookup"><span data-stu-id="3450b-250">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span> <span data-ttu-id="3450b-251">例如，當依照慣例找不到欄位時，設定導覽的支援欄位：</span><span class="sxs-lookup"><span data-stu-id="3450b-251">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="3450b-252">請注意， `Navigation` API 不會取代關聯性設定。</span><span class="sxs-lookup"><span data-stu-id="3450b-252">Note that the `Navigation` API does not replace relationship configuration.</span></span> <span data-ttu-id="3450b-253">相反地，它允許在已經探索或定義的關聯性中，額外設定導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="3450b-253">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="3450b-254">請參閱設定[導覽屬性檔](xref:core/modeling/relationships#configuring-navigation-properties)。</span><span class="sxs-lookup"><span data-stu-id="3450b-254">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="3450b-255">命名空間和連接字串的新命令列參數</span><span class="sxs-lookup"><span data-stu-id="3450b-255">New command-line parameters for namespaces and connection strings</span></span> 

<span data-ttu-id="3450b-256">現在，遷移和架構可讓您在命令列上指定命名空間。</span><span class="sxs-lookup"><span data-stu-id="3450b-256">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span> <span data-ttu-id="3450b-257">例如，若要對資料庫進行反向工程，將內容和模型類別放在不同的命名空間中：</span><span class="sxs-lookup"><span data-stu-id="3450b-257">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span> 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="3450b-258">如需完整詳細資料，請參閱[遷移](xref:core/managing-schemas/migrations/index#namespaces)和[還原工程](xref:core/managing-schemas/scaffolding#directories-and-namespaces)檔。</span><span class="sxs-lookup"><span data-stu-id="3450b-258">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="3450b-259">此外，連接字串現在可以傳遞至 `database-update` 命令：</span><span class="sxs-lookup"><span data-stu-id="3450b-259">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="3450b-260">如需完整詳細資料，請參閱[工具檔](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update)。</span><span class="sxs-lookup"><span data-stu-id="3450b-260">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="3450b-261">對等的參數也已新增至 VS 套件管理員主控台中使用的 PowerShell 命令。</span><span class="sxs-lookup"><span data-stu-id="3450b-261">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="3450b-262">EnableDetailedErrors 已傳回</span><span class="sxs-lookup"><span data-stu-id="3450b-262">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="3450b-263">基於效能考慮，從資料庫讀取值時，EF 不會執行額外的 null 檢查。</span><span class="sxs-lookup"><span data-stu-id="3450b-263">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span> <span data-ttu-id="3450b-264">當遇到未預期的 null 時，這可能會導致難以造成根本原因的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="3450b-264">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="3450b-265">使用 `EnableDetailedErrors` 會在查詢中加入額外的 null 檢查，如此一來，在小型的效能額外負荷下，這些錯誤就會更容易追蹤到根本原因。</span><span class="sxs-lookup"><span data-stu-id="3450b-265">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>  

<span data-ttu-id="3450b-266">例如：</span><span class="sxs-lookup"><span data-stu-id="3450b-266">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="3450b-267">檔是由問題[#955](https://github.com/dotnet/EntityFramework.Docs/issues/955)追蹤。</span><span class="sxs-lookup"><span data-stu-id="3450b-267">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="3450b-268">Cosmos 分割區索引鍵</span><span class="sxs-lookup"><span data-stu-id="3450b-268">Cosmos partition keys</span></span>

<span data-ttu-id="3450b-269">您現在可以在查詢中指定要用於給定查詢的分割區索引鍵。</span><span class="sxs-lookup"><span data-stu-id="3450b-269">The partition key to use for a given query can now be specified in the query.</span></span> <span data-ttu-id="3450b-270">例如：</span><span class="sxs-lookup"><span data-stu-id="3450b-270">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="3450b-271">檔是由問題[#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199)追蹤。</span><span class="sxs-lookup"><span data-stu-id="3450b-271">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="3450b-272">支援 SQL Server DATALENGTH 函數</span><span class="sxs-lookup"><span data-stu-id="3450b-272">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="3450b-273">這可使用新的方法來存取 `EF.Functions.DataLength` 。</span><span class="sxs-lookup"><span data-stu-id="3450b-273">This can be accessed using the new `EF.Functions.DataLength` method.</span></span> <span data-ttu-id="3450b-274">例如：</span><span class="sxs-lookup"><span data-stu-id="3450b-274">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a><span data-ttu-id="3450b-275">Preview 2</span><span class="sxs-lookup"><span data-stu-id="3450b-275">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="3450b-276">使用 c # 屬性指定屬性支援欄位</span><span class="sxs-lookup"><span data-stu-id="3450b-276">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="3450b-277">C # 屬性現在可以用來指定屬性的支援欄位。</span><span class="sxs-lookup"><span data-stu-id="3450b-277">A C# attribute can now be used to specify the backing field for a property.</span></span> <span data-ttu-id="3450b-278">即使無法自動找到支援欄位，此屬性仍然可以讓 EF Core 仍會如往常般寫入和讀取支援欄位。</span><span class="sxs-lookup"><span data-stu-id="3450b-278">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span> <span data-ttu-id="3450b-279">例如：</span><span class="sxs-lookup"><span data-stu-id="3450b-279">For example:</span></span>

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

<span data-ttu-id="3450b-280">檔是由問題[#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230)追蹤。</span><span class="sxs-lookup"><span data-stu-id="3450b-280">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="3450b-281">完成鑒別子對應</span><span class="sxs-lookup"><span data-stu-id="3450b-281">Complete discriminator mapping</span></span>

<span data-ttu-id="3450b-282">EF Core 針對[繼承階層的 TPH 對應](/ef/core/modeling/inheritance)使用鑒別子資料行。</span><span class="sxs-lookup"><span data-stu-id="3450b-282">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span> <span data-ttu-id="3450b-283">只要 EF Core 知道鑒別子的所有可能值，就可能會有一些效能增強功能。</span><span class="sxs-lookup"><span data-stu-id="3450b-283">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span> <span data-ttu-id="3450b-284">EF Core 5.0 現在會實行這些增強功能。</span><span class="sxs-lookup"><span data-stu-id="3450b-284">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="3450b-285">例如，舊版的 EF Core 一律會針對傳回階層中所有類型的查詢產生此 SQL：</span><span class="sxs-lookup"><span data-stu-id="3450b-285">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="3450b-286">當設定完整的鑒別子對應時，EF Core 5.0 現在會產生下列內容：</span><span class="sxs-lookup"><span data-stu-id="3450b-286">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="3450b-287">這會是從 preview 3 開始的預設行為。</span><span class="sxs-lookup"><span data-stu-id="3450b-287">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="3450b-288">Microsoft 中的效能改進。 Sqlite</span><span class="sxs-lookup"><span data-stu-id="3450b-288">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="3450b-289">我們對 SQLIte 進行了兩項效能改善：</span><span class="sxs-lookup"><span data-stu-id="3450b-289">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="3450b-290">藉由使用 SqliteBlob 和資料流程，以 GetBytes、GetChars 和 GetTextReader 來抓取二進位和字串資料現在更有效率。</span><span class="sxs-lookup"><span data-stu-id="3450b-290">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="3450b-291">SqliteConnection 的初始化現在是延遲的。</span><span class="sxs-lookup"><span data-stu-id="3450b-291">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="3450b-292">這些改良功能位於 ADO.NET 的 Microsoft. Sqlite 提供者，因此也會改善 EF Core 之外的效能。</span><span class="sxs-lookup"><span data-stu-id="3450b-292">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="3450b-293">Preview 1</span><span class="sxs-lookup"><span data-stu-id="3450b-293">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="3450b-294">簡單記錄</span><span class="sxs-lookup"><span data-stu-id="3450b-294">Simple logging</span></span>

<span data-ttu-id="3450b-295">這項功能會 `Database.Log` 在 EF6 中新增類似的功能。</span><span class="sxs-lookup"><span data-stu-id="3450b-295">This feature adds functionality similar to `Database.Log` in EF6.</span></span> <span data-ttu-id="3450b-296">也就是說，它提供簡單的方法，讓您不需要設定任何類型的外部記錄架構，即可從 EF Core 取得記錄。</span><span class="sxs-lookup"><span data-stu-id="3450b-296">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="3450b-297">[2019 年12月5日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)會包含初稿檔。</span><span class="sxs-lookup"><span data-stu-id="3450b-297">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="3450b-298">其他檔則是由問題[#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085)追蹤。</span><span class="sxs-lookup"><span data-stu-id="3450b-298">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="3450b-299">取得產生之 SQL 的簡單方式</span><span class="sxs-lookup"><span data-stu-id="3450b-299">Simple way to get generated SQL</span></span>

<span data-ttu-id="3450b-300">EF Core 5.0 引進了 `ToQueryString` 擴充方法，這會傳回在執行 LINQ 查詢時，EF Core 將產生的 SQL。</span><span class="sxs-lookup"><span data-stu-id="3450b-300">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="3450b-301">初稿檔包含在[2020 年1月9日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)。</span><span class="sxs-lookup"><span data-stu-id="3450b-301">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="3450b-302">其他檔則是由問題[#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331)追蹤。</span><span class="sxs-lookup"><span data-stu-id="3450b-302">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="3450b-303">使用 c # 屬性來表示實體沒有索引鍵</span><span class="sxs-lookup"><span data-stu-id="3450b-303">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="3450b-304">現在可以使用新的，將實體類型設定為沒有任何索引鍵 `KeylessAttribute` 。</span><span class="sxs-lookup"><span data-stu-id="3450b-304">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span> <span data-ttu-id="3450b-305">例如：</span><span class="sxs-lookup"><span data-stu-id="3450b-305">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="3450b-306">檔是由問題[#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186)追蹤。</span><span class="sxs-lookup"><span data-stu-id="3450b-306">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="3450b-307">連接或連接字串可以在初始化的 DbCoNtext 上變更</span><span class="sxs-lookup"><span data-stu-id="3450b-307">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="3450b-308">現在您可以更輕鬆地建立 DbCoNtext 實例，而不需要任何連接或連接字串。</span><span class="sxs-lookup"><span data-stu-id="3450b-308">It is now easier to create a DbContext instance without any connection or connection string.</span></span> <span data-ttu-id="3450b-309">此外，連接或連接字串現在可以在內容實例上進行變化。</span><span class="sxs-lookup"><span data-stu-id="3450b-309">Also, the connection or connection string can now be mutated on the context instance.</span></span> <span data-ttu-id="3450b-310">這項功能可讓相同的內容實例動態連接到不同的資料庫。</span><span class="sxs-lookup"><span data-stu-id="3450b-310">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="3450b-311">檔是由問題[#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075)追蹤。</span><span class="sxs-lookup"><span data-stu-id="3450b-311">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="3450b-312">變更追蹤 proxy</span><span class="sxs-lookup"><span data-stu-id="3450b-312">Change-tracking proxies</span></span>

<span data-ttu-id="3450b-313">EF Core 現在可以產生自動執行[INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1)和[INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1)的執行時間 proxy。</span><span class="sxs-lookup"><span data-stu-id="3450b-313">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span> <span data-ttu-id="3450b-314">這些之後，實體屬性上的這些值會直接變更為 EF Core，避免需要掃描變更。</span><span class="sxs-lookup"><span data-stu-id="3450b-314">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span> <span data-ttu-id="3450b-315">不過，proxy 會有自己的一組限制，因此不適合所有人。</span><span class="sxs-lookup"><span data-stu-id="3450b-315">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="3450b-316">檔是由問題[#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076)追蹤。</span><span class="sxs-lookup"><span data-stu-id="3450b-316">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="3450b-317">增強的調試視圖</span><span class="sxs-lookup"><span data-stu-id="3450b-317">Enhanced debug views</span></span>

<span data-ttu-id="3450b-318">「偵錯工具」是一種簡單的方式，可在偵錯工具發生問題時查看 EF Core 的內部。</span><span class="sxs-lookup"><span data-stu-id="3450b-318">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span> <span data-ttu-id="3450b-319">模型的「偵錯工具」視圖已在一段時間前完成。</span><span class="sxs-lookup"><span data-stu-id="3450b-319">A debug view for the Model was implemented some time ago.</span></span> <span data-ttu-id="3450b-320">針對 EF Core 5.0，我們讓模型視圖更容易閱讀，並在狀態管理員中為追蹤的實體新增新的 [偵錯工具]。</span><span class="sxs-lookup"><span data-stu-id="3450b-320">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="3450b-321">[2019 年12月12日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)會包含初稿檔。</span><span class="sxs-lookup"><span data-stu-id="3450b-321">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="3450b-322">其他檔則是由問題[#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086)追蹤。</span><span class="sxs-lookup"><span data-stu-id="3450b-322">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="3450b-323">改善資料庫 null 語義的處理</span><span class="sxs-lookup"><span data-stu-id="3450b-323">Improved handling of database null semantics</span></span>

<span data-ttu-id="3450b-324">關係資料庫通常會將 Null 視為未知的值，因此不等於任何其他 Null。</span><span class="sxs-lookup"><span data-stu-id="3450b-324">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span> <span data-ttu-id="3450b-325">雖然 c # 會將 null 視為已定義的值，但其比較會等於任何其他 null。</span><span class="sxs-lookup"><span data-stu-id="3450b-325">While C# treats null as a defined value, which compares equal to any other null.</span></span> <span data-ttu-id="3450b-326">EF Core 預設會轉譯查詢，使其使用 c # null 語義。</span><span class="sxs-lookup"><span data-stu-id="3450b-326">EF Core by default translates queries so that they use C# null semantics.</span></span> <span data-ttu-id="3450b-327">EF Core 5.0 大幅提升了這些翻譯的效率。</span><span class="sxs-lookup"><span data-stu-id="3450b-327">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="3450b-328">檔是由問題[#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612)追蹤。</span><span class="sxs-lookup"><span data-stu-id="3450b-328">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="3450b-329">索引子屬性</span><span class="sxs-lookup"><span data-stu-id="3450b-329">Indexer properties</span></span>

<span data-ttu-id="3450b-330">EF Core 5.0 支援 c # 索引子屬性的對應。</span><span class="sxs-lookup"><span data-stu-id="3450b-330">EF Core 5.0 supports mapping of C# indexer properties.</span></span> <span data-ttu-id="3450b-331">這些屬性可讓實體作為屬性包，其中的資料行會對應到包中的命名屬性。</span><span class="sxs-lookup"><span data-stu-id="3450b-331">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="3450b-332">檔是由問題[#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018)追蹤。</span><span class="sxs-lookup"><span data-stu-id="3450b-332">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="3450b-333">列舉對應的檢查條件約束產生</span><span class="sxs-lookup"><span data-stu-id="3450b-333">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="3450b-334">EF Core 5.0 遷移現在可以產生 enum 屬性對應的檢查條件約束。</span><span class="sxs-lookup"><span data-stu-id="3450b-334">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span> <span data-ttu-id="3450b-335">例如：</span><span class="sxs-lookup"><span data-stu-id="3450b-335">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="3450b-336">檔是由問題[#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082)追蹤。</span><span class="sxs-lookup"><span data-stu-id="3450b-336">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="3450b-337">IsRelational</span><span class="sxs-lookup"><span data-stu-id="3450b-337">IsRelational</span></span>

<span data-ttu-id="3450b-338">`IsRelational`除了現有的、和之外，還加入了新的方法 `IsSqlServer` `IsSqlite` `IsInMemory` 。</span><span class="sxs-lookup"><span data-stu-id="3450b-338">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span> <span data-ttu-id="3450b-339">這個方法可以用來測試 DbCoNtext 是否使用任何關係資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="3450b-339">This method can be used to test if the DbContext is using any relational database provider.</span></span> <span data-ttu-id="3450b-340">例如：</span><span class="sxs-lookup"><span data-stu-id="3450b-340">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="3450b-341">檔是由問題[#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185)追蹤。</span><span class="sxs-lookup"><span data-stu-id="3450b-341">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="3450b-342">使用 Etag Cosmos 開放式平行存取</span><span class="sxs-lookup"><span data-stu-id="3450b-342">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="3450b-343">Azure Cosmos DB 資料庫提供者現在支援使用 Etag 的開放式平行存取。</span><span class="sxs-lookup"><span data-stu-id="3450b-343">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span> <span data-ttu-id="3450b-344">使用 OnModelCreating 中的模型產生器來設定 ETag：</span><span class="sxs-lookup"><span data-stu-id="3450b-344">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="3450b-345">接著，SaveChanges 會擲回 `DbUpdateConcurrencyException` 並行衝突，以[處理](https://docs.microsoft.com/ef/core/saving/concurrency)以執行重試等。</span><span class="sxs-lookup"><span data-stu-id="3450b-345">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](https://docs.microsoft.com/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="3450b-346">檔是由問題[#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099)追蹤。</span><span class="sxs-lookup"><span data-stu-id="3450b-346">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="3450b-347">更多日期時間結構的查詢翻譯</span><span class="sxs-lookup"><span data-stu-id="3450b-347">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="3450b-348">包含新日期時間結構的查詢現在已轉譯。</span><span class="sxs-lookup"><span data-stu-id="3450b-348">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="3450b-349">此外，現在已對應下列 SQL Server 函式：</span><span class="sxs-lookup"><span data-stu-id="3450b-349">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="3450b-350">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="3450b-350">DateDiffWeek</span></span>
* <span data-ttu-id="3450b-351">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="3450b-351">DateFromParts</span></span>

<span data-ttu-id="3450b-352">例如：</span><span class="sxs-lookup"><span data-stu-id="3450b-352">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="3450b-353">檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="3450b-353">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="3450b-354">更多位元組陣列結構的查詢翻譯</span><span class="sxs-lookup"><span data-stu-id="3450b-354">Query translations for more byte array constructs</span></span>

<span data-ttu-id="3450b-355">在 byte [] 屬性上使用 Contains、Length、Enumerable.sequenceequal 等的查詢現在會轉譯為 SQL。</span><span class="sxs-lookup"><span data-stu-id="3450b-355">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="3450b-356">[2019 年12月5日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)會包含初稿檔。</span><span class="sxs-lookup"><span data-stu-id="3450b-356">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="3450b-357">其他檔則是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="3450b-357">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="3450b-358">反向的查詢轉譯</span><span class="sxs-lookup"><span data-stu-id="3450b-358">Query translation for Reverse</span></span>

<span data-ttu-id="3450b-359">使用 `Reverse` 的查詢現在已轉譯。</span><span class="sxs-lookup"><span data-stu-id="3450b-359">Queries using `Reverse` are now translated.</span></span> <span data-ttu-id="3450b-360">例如：</span><span class="sxs-lookup"><span data-stu-id="3450b-360">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="3450b-361">檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="3450b-361">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="3450b-362">位運算子的查詢轉譯</span><span class="sxs-lookup"><span data-stu-id="3450b-362">Query translation for bitwise operators</span></span>

<span data-ttu-id="3450b-363">使用位運算子的查詢現在會在更多情況下轉譯，例如：</span><span class="sxs-lookup"><span data-stu-id="3450b-363">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="3450b-364">檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="3450b-364">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="3450b-365">Cosmos 上字串的查詢轉譯</span><span class="sxs-lookup"><span data-stu-id="3450b-365">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="3450b-366">使用 Azure Cosmos DB 提供者時，現在會轉譯使用字串方法 Contains、StartsWith 和 EndsWith 的查詢。</span><span class="sxs-lookup"><span data-stu-id="3450b-366">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="3450b-367">檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="3450b-367">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
