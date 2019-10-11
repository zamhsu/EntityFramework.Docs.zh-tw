---
title: 原始 SQL 查詢 - EF Core
author: smitpatel
ms.date: 10/08/2019
ms.assetid: 70aae9b5-8743-4557-9c5d-239f688bf418
uid: core/querying/raw-sql
ms.openlocfilehash: 33601d570fa0b7a1fcada1705843da3798c00094
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181977"
---
# <a name="raw-sql-queries"></a><span data-ttu-id="2e0fb-102">原始 SQL 查詢</span><span class="sxs-lookup"><span data-stu-id="2e0fb-102">Raw SQL Queries</span></span>

<span data-ttu-id="2e0fb-103">Entity Framework Core 可讓您在處理關聯式資料庫時，下拉至原始 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-103">Entity Framework Core allows you to drop down to raw SQL queries when working with a relational database.</span></span> <span data-ttu-id="2e0fb-104">如果您想要的查詢無法使用 LINQ 來表示，原始 SQL 查詢會很有用。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-104">Raw SQL queries are useful if the query you want can't be expressed using LINQ.</span></span> <span data-ttu-id="2e0fb-105">如果使用 LINQ 查詢會導致 SQL 查詢效率不佳，也會使用原始 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-105">Raw SQL queries are also used if using a LINQ query is resulting in an inefficient SQL query.</span></span> <span data-ttu-id="2e0fb-106">原始 SQL 查詢可以傳回屬於模型一部分的一般實體類型或[無索引鍵實體類型](xref:core/modeling/keyless-entity-types)。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-106">Raw SQL queries can return regular entity types or [keyless entity types](xref:core/modeling/keyless-entity-types) that are part of your model.</span></span>

> [!TIP]  
> <span data-ttu-id="2e0fb-107">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying/RawSQL/Sample.cs) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-107">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying/RawSQL/Sample.cs) on GitHub.</span></span>

## <a name="basic-raw-sql-queries"></a><span data-ttu-id="2e0fb-108">基本的原始 SQL 查詢</span><span class="sxs-lookup"><span data-stu-id="2e0fb-108">Basic raw SQL queries</span></span>

<span data-ttu-id="2e0fb-109">您可以使用 `FromSqlRaw` 擴充方法，根據原始 SQL 查詢來開始 LINQ 查詢。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-109">You can use the `FromSqlRaw` extension method to begin a LINQ query based on a raw SQL query.</span></span> <span data-ttu-id="2e0fb-110">`FromSqlRaw` 只能用在直接在 `DbSet<>` 上的查詢根目錄。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-110">`FromSqlRaw` can only be used on query roots, that is directly on the `DbSet<>`.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRaw)]

<span data-ttu-id="2e0fb-111">原始 SQL 查詢可以用來執行預存程序。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-111">Raw SQL queries can be used to execute a stored procedure.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedure)]

## <a name="passing-parameters"></a><span data-ttu-id="2e0fb-112">傳遞參數</span><span class="sxs-lookup"><span data-stu-id="2e0fb-112">Passing parameters</span></span>

> [!WARNING]
> <span data-ttu-id="2e0fb-113">**一律針對原始 SQL 查詢使用參數化**</span><span class="sxs-lookup"><span data-stu-id="2e0fb-113">**Always use parameterization for raw SQL queries**</span></span>
>
> <span data-ttu-id="2e0fb-114">將任何使用者提供的值引進原始 SQL 查詢時，必須小心避免 SQL 插入式攻擊。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-114">When introducing any user-provided values into a raw SQL query, care must be taken to avoid SQL injection attacks.</span></span> <span data-ttu-id="2e0fb-115">除了驗證此類值不包含不正確字元，請一律使用參數化，將值與 SQL 文字分開傳送。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-115">In addition to validating that such values don't contain invalid characters, always use parameterization which sends the values separate from the SQL text.</span></span>
>
> <span data-ttu-id="2e0fb-116">特別的是，絕對不要將未驗證之使用者提供值的串連或插入字串（`$""`）傳遞至 `FromSqlRaw` 或 `ExecuteSqlRaw`。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-116">In particular, never pass a concatenated or interpolated string (`$""`) with non-validated user-provided values into `FromSqlRaw` or `ExecuteSqlRaw`.</span></span> <span data-ttu-id="2e0fb-117">@No__t-0 和 @no__t 1 方法允許以防止 SQL 插入式攻擊的方式使用字串內插補點語法。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-117">The `FromSqlInterpolated` and `ExecuteSqlInterpolated` methods allow using string interpolation syntax in a way that protects against SQL injection attacks.</span></span>

<span data-ttu-id="2e0fb-118">下列範例會將參數預留位置包含在 SQL 查詢字串中，並提供其他引數，藉以將單一參數傳遞至預存程式。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-118">The following example passes a single parameter to a stored procedure by including a parameter placeholder in the SQL query string and providing an additional argument.</span></span> <span data-ttu-id="2e0fb-119">雖然此語法看起來可能像 `String.Format` 語法，但提供的值會包裝在 `DbParameter` 中，而產生的參數名稱會插入指定的 `{0}` 預留位置。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-119">While this syntax may look like `String.Format` syntax, the supplied value is wrapped in a `DbParameter` and the generated parameter name inserted where the `{0}` placeholder was specified.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureParameter)]

<span data-ttu-id="2e0fb-120">`FromSqlInterpolated` 類似于 `FromSqlRaw`，但可讓您使用字串內插補點語法。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-120">`FromSqlInterpolated` is similar to `FromSqlRaw` but allows you to use string interpolation syntax.</span></span> <span data-ttu-id="2e0fb-121">就像 `FromSqlRaw`，`FromSqlInterpolated` 只能用於查詢根目錄。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-121">Just like `FromSqlRaw`, `FromSqlInterpolated` can only be used on query roots.</span></span> <span data-ttu-id="2e0fb-122">如同先前的範例，此值會轉換為 `DbParameter`，而且不容易受到 SQL 插入。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-122">As with the previous example, the value is converted to a `DbParameter` and isn't vulnerable to SQL injection.</span></span>

> [!NOTE]
> <span data-ttu-id="2e0fb-123">在3.0 版之前，`FromSqlRaw` 和 `FromSqlInterpolated` 是名為 `FromSql` 的兩個多載。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-123">Prior to version 3.0, `FromSqlRaw` and `FromSqlInterpolated` were two overloads named `FromSql`.</span></span> <span data-ttu-id="2e0fb-124">如需詳細資訊，請參閱[先前的版本一節](#previous-versions)。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-124">For more information, see the [previous versions section](#previous-versions).</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedStoredProcedureParameter)]

<span data-ttu-id="2e0fb-125">您也可以建構 DbParameter，並將它提供為參數值。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-125">You can also construct a DbParameter and supply it as a parameter value.</span></span> <span data-ttu-id="2e0fb-126">因為使用了一般 SQL 參數預留位置，而不是字串預留位置，所以可以安全地使用 `FromSqlRaw`：</span><span class="sxs-lookup"><span data-stu-id="2e0fb-126">Since a regular SQL parameter placeholder is used, rather than a string placeholder, `FromSqlRaw` can be safely used:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureSqlParameter)]

<span data-ttu-id="2e0fb-127">`FromSqlRaw` 可讓您在 SQL 查詢字串中使用具名引數，當預存程式具有選擇性參數時，這會很有用：</span><span class="sxs-lookup"><span data-stu-id="2e0fb-127">`FromSqlRaw` allows you to use named parameters in the SQL query string, which is useful when a stored procedure has optional parameters:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureNamedSqlParameter)]

## <a name="composing-with-linq"></a><span data-ttu-id="2e0fb-128">使用 LINQ 撰寫</span><span class="sxs-lookup"><span data-stu-id="2e0fb-128">Composing with LINQ</span></span>

<span data-ttu-id="2e0fb-129">您可以使用 LINQ 運算子在初始原始 SQL 查詢之上進行撰寫。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-129">You can compose on top of the initial raw SQL query using LINQ operators.</span></span> <span data-ttu-id="2e0fb-130">EF Core 會將它視為子查詢，並在資料庫中撰寫它。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-130">EF Core will treat it as subquery and compose over it in the database.</span></span> <span data-ttu-id="2e0fb-131">下列範例會使用從資料表值函式（TVF）選取的原始 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-131">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF).</span></span> <span data-ttu-id="2e0fb-132">然後使用 LINQ 來進行篩選和排序，以在其上撰寫。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-132">And then composes on it using LINQ to do filtering and sorting.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedComposed)]

<span data-ttu-id="2e0fb-133">上述查詢會產生下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="2e0fb-133">Above query generates following SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM (
    SELECT * FROM dbo.SearchBlogs(@p0)
) AS [b]
WHERE [b].[Rating] > 3
ORDER BY [b].[Rating] DESC
```

### <a name="including-related-data"></a><span data-ttu-id="2e0fb-134">包含相關資料</span><span class="sxs-lookup"><span data-stu-id="2e0fb-134">Including related data</span></span>

<span data-ttu-id="2e0fb-135">`Include` 方法可用來包含相關的資料，就如同所有其他的 LINQ 查詢一般：</span><span class="sxs-lookup"><span data-stu-id="2e0fb-135">The `Include` method can be used to include related data, just like with any other LINQ query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedInclude)]

<span data-ttu-id="2e0fb-136">使用 LINQ 撰寫時，您的原始 SQL 查詢必須是可組合的，因為 EF Core 會將提供的 SQL 視為子查詢。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-136">Composing with LINQ requires your raw SQL query to be composable since EF Core will treat the supplied SQL as a subquery.</span></span> <span data-ttu-id="2e0fb-137">以 `SELECT` 關鍵字為開頭的 SQL 查詢才可撰寫。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-137">SQL queries that can be composed on begin with the `SELECT` keyword.</span></span> <span data-ttu-id="2e0fb-138">此外，SQL 傳遞不應包含在子查詢上不正確任何字元或選項，例如：</span><span class="sxs-lookup"><span data-stu-id="2e0fb-138">Further, SQL passed shouldn't contain any characters or options that aren't valid on a subquery, such as:</span></span>

- <span data-ttu-id="2e0fb-139">尾端分號</span><span class="sxs-lookup"><span data-stu-id="2e0fb-139">A trailing semicolon</span></span>
- <span data-ttu-id="2e0fb-140">在 SQL Server 上，結尾的查詢層級提示 (例如，`OPTION (HASH JOIN)`)</span><span class="sxs-lookup"><span data-stu-id="2e0fb-140">On SQL Server, a trailing query-level hint (for example, `OPTION (HASH JOIN)`)</span></span>
- <span data-ttu-id="2e0fb-141">在 SQL Server 上，未在 `SELECT` 子句中搭配 `OFFSET 0` 或 `TOP 100 PERCENT` 使用的 @no__t 0 子句</span><span class="sxs-lookup"><span data-stu-id="2e0fb-141">On SQL Server, an `ORDER BY` clause that isn't used with `OFFSET 0` OR `TOP 100 PERCENT` in the `SELECT` clause</span></span>

<span data-ttu-id="2e0fb-142">SQL Server 不允許撰寫預存程序呼叫，因此嘗試將其他查詢運算子套用至這類呼叫將會導致不正確 SQL。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-142">SQL Server doesn't allow composing over stored procedure calls, so any attempt to apply additional query operators to such a call will result in invalid SQL.</span></span> <span data-ttu-id="2e0fb-143">在 `FromSqlRaw` 或 @no__t 3 方法之後，使用 `AsEnumerable` 或 `AsAsyncEnumerable` 方法，確保 EF Core 不會嘗試撰寫預存程式。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-143">Use `AsEnumerable` or `AsAsyncEnumerable` method right after `FromSqlRaw` or `FromSqlInterpolated` methods to make sure that EF Core doesn't try to compose over a stored procedure.</span></span>

## <a name="change-tracking"></a><span data-ttu-id="2e0fb-144">變更追蹤</span><span class="sxs-lookup"><span data-stu-id="2e0fb-144">Change Tracking</span></span>

<span data-ttu-id="2e0fb-145">使用 `FromSqlRaw` 或 @no__t 1 方法的查詢，會遵循與 EF Core 中的任何其他 LINQ 查詢完全相同的變更追蹤規則。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-145">Queries that use the `FromSqlRaw` or `FromSqlInterpolated` methods follow the exact same change tracking rules as any other LINQ query in EF Core.</span></span> <span data-ttu-id="2e0fb-146">例如，若查詢投影實體類型，就會依預設追蹤結果。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-146">For example, if the query projects entity types, the results will be tracked by default.</span></span>

<span data-ttu-id="2e0fb-147">下列範例會使用從資料表值函式（TVF）選取的原始 SQL 查詢，然後使用 `AsNoTracking` 的呼叫來停用變更追蹤：</span><span class="sxs-lookup"><span data-stu-id="2e0fb-147">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF), then disables change tracking with the call to `AsNoTracking`:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedAsNoTracking)]

## <a name="limitations"></a><span data-ttu-id="2e0fb-148">限制</span><span class="sxs-lookup"><span data-stu-id="2e0fb-148">Limitations</span></span>

<span data-ttu-id="2e0fb-149">使用原始 SQL 查詢時有一些要注意的限制：</span><span class="sxs-lookup"><span data-stu-id="2e0fb-149">There are a few limitations to be aware of when using raw SQL queries:</span></span>

- <span data-ttu-id="2e0fb-150">SQL 查詢必須傳回實體類型之所有屬性的資料。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-150">The SQL query must return data for all properties of the entity type.</span></span>
- <span data-ttu-id="2e0fb-151">結果集中的資料行名稱必須符合屬性所對應的資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-151">The column names in the result set must match the column names that properties are mapped to.</span></span> <span data-ttu-id="2e0fb-152">請注意，此行為與 EF6 不同。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-152">Note this behavior is different from EF6.</span></span> <span data-ttu-id="2e0fb-153">原始 SQL 查詢的資料行對應和結果集資料行名稱的 EF6 忽略屬性必須符合屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-153">EF6 ignored property to column mapping for raw SQL queries and result set column names had to match the property names.</span></span>
- <span data-ttu-id="2e0fb-154">SQL 查詢不能包含相關資料。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-154">The SQL query can't contain related data.</span></span> <span data-ttu-id="2e0fb-155">不過，在許多情況下，您可以使用 `Include` 運算子來傳回相關資料以在查詢上方進行撰寫 (請參閱[包含相關資料](#including-related-data))。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-155">However, in many cases you can compose on top of the query using the `Include` operator to return related data (see [Including related data](#including-related-data)).</span></span>

## <a name="previous-versions"></a><span data-ttu-id="2e0fb-156">舊版本</span><span class="sxs-lookup"><span data-stu-id="2e0fb-156">Previous versions</span></span>

<span data-ttu-id="2e0fb-157">EF Core 2.2 版和更早版本有兩個名為 `FromSql` 的方法多載，其行為方式與較新的 `FromSqlRaw` 和 `FromSqlInterpolated` 相同。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-157">EF Core version 2.2 and earlier had two overloads of method named `FromSql`, which behaved in the same way as the newer `FromSqlRaw` and `FromSqlInterpolated`.</span></span> <span data-ttu-id="2e0fb-158">當意圖是呼叫插入字串方法時，很容易不小心呼叫原始字串方法，另一種方式則是。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-158">It was easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span> <span data-ttu-id="2e0fb-159">意外呼叫錯誤的多載，可能會導致查詢不會在應該發生時進行參數化。</span><span class="sxs-lookup"><span data-stu-id="2e0fb-159">Calling wrong overload accidentally could result in queries not being parameterized when they should have been.</span></span>
