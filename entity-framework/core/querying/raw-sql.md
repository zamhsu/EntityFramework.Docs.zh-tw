---
title: 原始 SQL 查詢 - EF Core
description: 在 Entity Framework Core 中使用原始 SQL 查詢查詢
author: smitpatel
ms.date: 10/08/2019
uid: core/querying/raw-sql
ms.openlocfilehash: 13f5cbfbd7a110394402bff74d51b5fcda04c642
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071130"
---
# <a name="raw-sql-queries"></a><span data-ttu-id="34099-103">原始 SQL 查詢</span><span class="sxs-lookup"><span data-stu-id="34099-103">Raw SQL Queries</span></span>

<span data-ttu-id="34099-104">Entity Framework Core 可讓您在處理關聯式資料庫時，下拉至原始 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="34099-104">Entity Framework Core allows you to drop down to raw SQL queries when working with a relational database.</span></span> <span data-ttu-id="34099-105">如果您想要的查詢無法使用 LINQ 表示，原始 SQL 查詢會很有用。</span><span class="sxs-lookup"><span data-stu-id="34099-105">Raw SQL queries are useful if the query you want can't be expressed using LINQ.</span></span> <span data-ttu-id="34099-106">如果使用 LINQ 查詢會導致 SQL 查詢效率不佳，也會使用原始 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="34099-106">Raw SQL queries are also used if using a LINQ query is resulting in an inefficient SQL query.</span></span> <span data-ttu-id="34099-107">原始 SQL 查詢可以傳回屬於模型一部分的一般實體類型或 [無索引鍵實體類型](xref:core/modeling/keyless-entity-types) 。</span><span class="sxs-lookup"><span data-stu-id="34099-107">Raw SQL queries can return regular entity types or [keyless entity types](xref:core/modeling/keyless-entity-types) that are part of your model.</span></span>

> [!TIP]  
> <span data-ttu-id="34099-108">您可以在 GitHub 上檢視此文章的[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="34099-108">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/) on GitHub.</span></span>

## <a name="basic-raw-sql-queries"></a><span data-ttu-id="34099-109">基本的原始 SQL 查詢</span><span class="sxs-lookup"><span data-stu-id="34099-109">Basic raw SQL queries</span></span>

<span data-ttu-id="34099-110">您可以使用 `FromSqlRaw` 擴充方法，根據原始 SQL 查詢來開始 LINQ 查詢。</span><span class="sxs-lookup"><span data-stu-id="34099-110">You can use the `FromSqlRaw` extension method to begin a LINQ query based on a raw SQL query.</span></span> <span data-ttu-id="34099-111">`FromSqlRaw` 只能在上直接位於的查詢根目錄上使用 `DbSet<>` 。</span><span class="sxs-lookup"><span data-stu-id="34099-111">`FromSqlRaw` can only be used on query roots, that is directly on the `DbSet<>`.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRaw)]

<span data-ttu-id="34099-112">原始 SQL 查詢可以用來執行預存程序。</span><span class="sxs-lookup"><span data-stu-id="34099-112">Raw SQL queries can be used to execute a stored procedure.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedure)]

## <a name="passing-parameters"></a><span data-ttu-id="34099-113">傳遞參數</span><span class="sxs-lookup"><span data-stu-id="34099-113">Passing parameters</span></span>

> [!WARNING]
> <span data-ttu-id="34099-114">**一律針對原始 SQL 查詢使用參數化**</span><span class="sxs-lookup"><span data-stu-id="34099-114">**Always use parameterization for raw SQL queries**</span></span>
>
> <span data-ttu-id="34099-115">將任何使用者提供的值引進原始 SQL 查詢時，必須小心避免 SQL 插入式攻擊。</span><span class="sxs-lookup"><span data-stu-id="34099-115">When introducing any user-provided values into a raw SQL query, care must be taken to avoid SQL injection attacks.</span></span> <span data-ttu-id="34099-116">除了驗證這類值不包含不正確字元，請一律使用參數化，以將值與 SQL 文字分開。</span><span class="sxs-lookup"><span data-stu-id="34099-116">In addition to validating that such values don't contain invalid characters, always use parameterization which sends the values separate from the SQL text.</span></span>
>
> <span data-ttu-id="34099-117">尤其是，絕對不會將串連或插入的字串 (`$""`) ，並將未經驗證的使用者提供值傳遞至 `FromSqlRaw` 或 `ExecuteSqlRaw` 。</span><span class="sxs-lookup"><span data-stu-id="34099-117">In particular, never pass a concatenated or interpolated string (`$""`) with non-validated user-provided values into `FromSqlRaw` or `ExecuteSqlRaw`.</span></span> <span data-ttu-id="34099-118">`FromSqlInterpolated`和 `ExecuteSqlInterpolated` 方法允許以防止 SQL 插入式攻擊的方式使用字串插補語法。</span><span class="sxs-lookup"><span data-stu-id="34099-118">The `FromSqlInterpolated` and `ExecuteSqlInterpolated` methods allow using string interpolation syntax in a way that protects against SQL injection attacks.</span></span>

<span data-ttu-id="34099-119">下列範例會藉由在 SQL 查詢字串中包含參數預留位置，並提供額外的引數，將單一參數傳遞至預存程式。</span><span class="sxs-lookup"><span data-stu-id="34099-119">The following example passes a single parameter to a stored procedure by including a parameter placeholder in the SQL query string and providing an additional argument.</span></span> <span data-ttu-id="34099-120">雖然此語法看起來可能像 `String.Format` 語法，但是提供的值會包裝在中， `DbParameter` 並在指定預留位置的位置插入所產生的參數名稱 `{0}` 。</span><span class="sxs-lookup"><span data-stu-id="34099-120">While this syntax may look like `String.Format` syntax, the supplied value is wrapped in a `DbParameter` and the generated parameter name inserted where the `{0}` placeholder was specified.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureParameter)]

<span data-ttu-id="34099-121">`FromSqlInterpolated` 類似于， `FromSqlRaw` 但可讓您使用字串插補語法。</span><span class="sxs-lookup"><span data-stu-id="34099-121">`FromSqlInterpolated` is similar to `FromSqlRaw` but allows you to use string interpolation syntax.</span></span> <span data-ttu-id="34099-122">就像 `FromSqlRaw` ， `FromSqlInterpolated` 只能在查詢根目錄上使用。</span><span class="sxs-lookup"><span data-stu-id="34099-122">Just like `FromSqlRaw`, `FromSqlInterpolated` can only be used on query roots.</span></span> <span data-ttu-id="34099-123">如同上述範例，此值會轉換為 `DbParameter` ，而且不容易遭受 SQL 插入式攻擊。</span><span class="sxs-lookup"><span data-stu-id="34099-123">As with the previous example, the value is converted to a `DbParameter` and isn't vulnerable to SQL injection.</span></span>

> [!NOTE]
> <span data-ttu-id="34099-124">在3.0 版之前， `FromSqlRaw` 以及 `FromSqlInterpolated` 兩個名為 `FromSql` 的多載。</span><span class="sxs-lookup"><span data-stu-id="34099-124">Prior to version 3.0, `FromSqlRaw` and `FromSqlInterpolated` were two overloads named `FromSql`.</span></span> <span data-ttu-id="34099-125">如需詳細資訊，請參閱「 [先前的版本」一節](#previous-versions)。</span><span class="sxs-lookup"><span data-stu-id="34099-125">For more information, see the [previous versions section](#previous-versions).</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedStoredProcedureParameter)]

<span data-ttu-id="34099-126">您也可以建構 DbParameter，並將它提供為參數值。</span><span class="sxs-lookup"><span data-stu-id="34099-126">You can also construct a DbParameter and supply it as a parameter value.</span></span> <span data-ttu-id="34099-127">因為使用一般 SQL 參數預留位置，而不是字串預留位置，所以 `FromSqlRaw` 可以安全地使用：</span><span class="sxs-lookup"><span data-stu-id="34099-127">Since a regular SQL parameter placeholder is used, rather than a string placeholder, `FromSqlRaw` can be safely used:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureSqlParameter)]

<span data-ttu-id="34099-128">`FromSqlRaw` 可讓您在 SQL 查詢字串中使用具名引數，這在預存程式具有選擇性參數時很有用：</span><span class="sxs-lookup"><span data-stu-id="34099-128">`FromSqlRaw` allows you to use named parameters in the SQL query string, which is useful when a stored procedure has optional parameters:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureNamedSqlParameter)]

> [!NOTE]
> <span data-ttu-id="34099-129">**參數排序** Entity Framework Core 會根據陣列的順序來傳遞參數 `SqlParameter[]` 。</span><span class="sxs-lookup"><span data-stu-id="34099-129">**Parameter Ordering** Entity Framework Core passes parameters based on the order of the `SqlParameter[]` array.</span></span> <span data-ttu-id="34099-130">傳遞多個時 `SqlParameter` ，SQL 字串中的順序必須符合預存程式定義中參數的順序。</span><span class="sxs-lookup"><span data-stu-id="34099-130">When passing multiple `SqlParameter`s, the ordering in the SQL string must match the order of the parameters in the stored procedure's definition.</span></span> <span data-ttu-id="34099-131">若未這麼做，可能會導致在執行程式時產生類型轉換例外狀況及/或非預期的行為。</span><span class="sxs-lookup"><span data-stu-id="34099-131">Failure to do this may result in type conversion exceptions and/or unexpected behavior when the procedure is executed.</span></span>

## <a name="composing-with-linq"></a><span data-ttu-id="34099-132">使用 LINQ 撰寫</span><span class="sxs-lookup"><span data-stu-id="34099-132">Composing with LINQ</span></span>

<span data-ttu-id="34099-133">您可以使用 LINQ 運算子來撰寫初始原始 SQL 查詢的最上層。</span><span class="sxs-lookup"><span data-stu-id="34099-133">You can compose on top of the initial raw SQL query using LINQ operators.</span></span> <span data-ttu-id="34099-134">EF Core 會將它視為子查詢，並在資料庫中撰寫它。</span><span class="sxs-lookup"><span data-stu-id="34099-134">EF Core will treat it as subquery and compose over it in the database.</span></span> <span data-ttu-id="34099-135">下列範例會使用從資料表值函數中選取的原始 SQL 查詢 (TVF) 。</span><span class="sxs-lookup"><span data-stu-id="34099-135">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF).</span></span> <span data-ttu-id="34099-136">然後使用 LINQ 來撰寫篩選和排序。</span><span class="sxs-lookup"><span data-stu-id="34099-136">And then composes on it using LINQ to do filtering and sorting.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedComposed)]

<span data-ttu-id="34099-137">上述查詢會產生下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="34099-137">Above query generates following SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM (
    SELECT * FROM dbo.SearchBlogs(@p0)
) AS [b]
WHERE [b].[Rating] > 3
ORDER BY [b].[Rating] DESC
```

### <a name="including-related-data"></a><span data-ttu-id="34099-138">包含相關資料</span><span class="sxs-lookup"><span data-stu-id="34099-138">Including related data</span></span>

<span data-ttu-id="34099-139">`Include` 方法可用來包含相關的資料，就如同所有其他的 LINQ 查詢一般：</span><span class="sxs-lookup"><span data-stu-id="34099-139">The `Include` method can be used to include related data, just like with any other LINQ query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedInclude)]

<span data-ttu-id="34099-140">撰寫 LINQ 需要可組合您的原始 SQL 查詢，因為 EF Core 會將提供的 SQL 視為子查詢。</span><span class="sxs-lookup"><span data-stu-id="34099-140">Composing with LINQ requires your raw SQL query to be composable since EF Core will treat the supplied SQL as a subquery.</span></span> <span data-ttu-id="34099-141">以 `SELECT` 關鍵字為開頭的 SQL 查詢才可撰寫。</span><span class="sxs-lookup"><span data-stu-id="34099-141">SQL queries that can be composed on begin with the `SELECT` keyword.</span></span> <span data-ttu-id="34099-142">此外，SQL 傳遞的不應包含任何在子查詢中不正確字元或選項，例如：</span><span class="sxs-lookup"><span data-stu-id="34099-142">Further, SQL passed shouldn't contain any characters or options that aren't valid on a subquery, such as:</span></span>

- <span data-ttu-id="34099-143">尾端分號</span><span class="sxs-lookup"><span data-stu-id="34099-143">A trailing semicolon</span></span>
- <span data-ttu-id="34099-144">在 SQL Server 上，結尾的查詢層級提示 (例如，`OPTION (HASH JOIN)`)</span><span class="sxs-lookup"><span data-stu-id="34099-144">On SQL Server, a trailing query-level hint (for example, `OPTION (HASH JOIN)`)</span></span>
- <span data-ttu-id="34099-145">在 SQL Server 上， `ORDER BY` 不會搭配 `OFFSET 0` `TOP 100 PERCENT` 子句或子句中使用的子句 `SELECT`</span><span class="sxs-lookup"><span data-stu-id="34099-145">On SQL Server, an `ORDER BY` clause that isn't used with `OFFSET 0` OR `TOP 100 PERCENT` in the `SELECT` clause</span></span>

<span data-ttu-id="34099-146">SQL Server 不允許撰寫預存程序呼叫，因此將其他查詢運算子套用到這類呼叫的任何嘗試都會導致不正確 SQL。</span><span class="sxs-lookup"><span data-stu-id="34099-146">SQL Server doesn't allow composing over stored procedure calls, so any attempt to apply additional query operators to such a call will result in invalid SQL.</span></span> <span data-ttu-id="34099-147">`AsEnumerable` `AsAsyncEnumerable` 請在或方法之後使用或方法 `FromSqlRaw` `FromSqlInterpolated` ，以確定 EF Core 不會嘗試在預存程式上撰寫。</span><span class="sxs-lookup"><span data-stu-id="34099-147">Use `AsEnumerable` or `AsAsyncEnumerable` method right after `FromSqlRaw` or `FromSqlInterpolated` methods to make sure that EF Core doesn't try to compose over a stored procedure.</span></span>

## <a name="change-tracking"></a><span data-ttu-id="34099-148">變更追蹤</span><span class="sxs-lookup"><span data-stu-id="34099-148">Change Tracking</span></span>

<span data-ttu-id="34099-149">使用或方法的查詢，會 `FromSqlRaw` `FromSqlInterpolated` 遵循與 EF Core 中的任何其他 LINQ 查詢完全相同的變更追蹤規則。</span><span class="sxs-lookup"><span data-stu-id="34099-149">Queries that use the `FromSqlRaw` or `FromSqlInterpolated` methods follow the exact same change tracking rules as any other LINQ query in EF Core.</span></span> <span data-ttu-id="34099-150">例如，若查詢投影實體類型，就會依預設追蹤結果。</span><span class="sxs-lookup"><span data-stu-id="34099-150">For example, if the query projects entity types, the results will be tracked by default.</span></span>

<span data-ttu-id="34099-151">下列範例會使用從資料表值函式中選取的原始 SQL 查詢 (TVF) ，然後透過呼叫來停用變更追蹤 `AsNoTracking` ：</span><span class="sxs-lookup"><span data-stu-id="34099-151">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF), then disables change tracking with the call to `AsNoTracking`:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedAsNoTracking)]

## <a name="limitations"></a><span data-ttu-id="34099-152">限制</span><span class="sxs-lookup"><span data-stu-id="34099-152">Limitations</span></span>

<span data-ttu-id="34099-153">使用原始 SQL 查詢時有一些要注意的限制：</span><span class="sxs-lookup"><span data-stu-id="34099-153">There are a few limitations to be aware of when using raw SQL queries:</span></span>

- <span data-ttu-id="34099-154">SQL 查詢必須傳回實體型別之所有屬性的資料。</span><span class="sxs-lookup"><span data-stu-id="34099-154">The SQL query must return data for all properties of the entity type.</span></span>
- <span data-ttu-id="34099-155">結果集中的資料行名稱必須符合屬性所對應的資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="34099-155">The column names in the result set must match the column names that properties are mapped to.</span></span> <span data-ttu-id="34099-156">請注意，這種行為不同于 EF6。</span><span class="sxs-lookup"><span data-stu-id="34099-156">Note this behavior is different from EF6.</span></span> <span data-ttu-id="34099-157">針對原始 SQL 查詢的資料行對應，EF6 忽略了屬性，而結果集資料行名稱必須與屬性名稱相符。</span><span class="sxs-lookup"><span data-stu-id="34099-157">EF6 ignored property to column mapping for raw SQL queries and result set column names had to match the property names.</span></span>
- <span data-ttu-id="34099-158">SQL 查詢不能包含相關資料。</span><span class="sxs-lookup"><span data-stu-id="34099-158">The SQL query can't contain related data.</span></span> <span data-ttu-id="34099-159">不過，在許多情況下，您可以使用 `Include` 運算子來傳回相關資料以在查詢上方進行撰寫 (請參閱[包含相關資料](#including-related-data))。</span><span class="sxs-lookup"><span data-stu-id="34099-159">However, in many cases you can compose on top of the query using the `Include` operator to return related data (see [Including related data](#including-related-data)).</span></span>

## <a name="previous-versions"></a><span data-ttu-id="34099-160">舊版</span><span class="sxs-lookup"><span data-stu-id="34099-160">Previous versions</span></span>

<span data-ttu-id="34099-161">EF Core 2.2 版和更早版本有兩個名為的方法多載 `FromSql` ，其行為與較新的 `FromSqlRaw` 和相同 `FromSqlInterpolated` 。</span><span class="sxs-lookup"><span data-stu-id="34099-161">EF Core version 2.2 and earlier had two overloads of method named `FromSql`, which behaved in the same way as the newer `FromSqlRaw` and `FromSqlInterpolated`.</span></span> <span data-ttu-id="34099-162">當意圖是要呼叫插入字串方法，而另一種方法很容易時，很容易就會不小心呼叫原始字串方法。</span><span class="sxs-lookup"><span data-stu-id="34099-162">It was easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span> <span data-ttu-id="34099-163">不小心呼叫錯誤的多載可能會導致查詢不會在應該是時進行參數化。</span><span class="sxs-lookup"><span data-stu-id="34099-163">Calling wrong overload accidentally could result in queries not being parameterized when they should have been.</span></span>
