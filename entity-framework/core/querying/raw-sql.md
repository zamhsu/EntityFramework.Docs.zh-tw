---
title: 原始 SQL 查詢 - EF Core
author: smitpatel
ms.date: 10/08/2019
ms.assetid: 70aae9b5-8743-4557-9c5d-239f688bf418
uid: core/querying/raw-sql
ms.openlocfilehash: a54bb67c0fce9d621382f6372e70fe4cdca48a20
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "78417667"
---
# <a name="raw-sql-queries"></a><span data-ttu-id="4fb05-102">原始 SQL 查詢</span><span class="sxs-lookup"><span data-stu-id="4fb05-102">Raw SQL Queries</span></span>

<span data-ttu-id="4fb05-103">Entity Framework Core 可讓您在處理關聯式資料庫時，下拉至原始 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="4fb05-103">Entity Framework Core allows you to drop down to raw SQL queries when working with a relational database.</span></span> <span data-ttu-id="4fb05-104">如果無法使用 LINQ 表示所需的查詢,則原始 SQL 查詢非常有用。</span><span class="sxs-lookup"><span data-stu-id="4fb05-104">Raw SQL queries are useful if the query you want can't be expressed using LINQ.</span></span> <span data-ttu-id="4fb05-105">如果使用 LINQ 查詢會導致 SQL 查詢效率低下,則也使用原始 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="4fb05-105">Raw SQL queries are also used if using a LINQ query is resulting in an inefficient SQL query.</span></span> <span data-ttu-id="4fb05-106">原始 SQL 查詢可以傳回為模型一部份的一般實體類型或[無鑰匙實體類型](xref:core/modeling/keyless-entity-types)。</span><span class="sxs-lookup"><span data-stu-id="4fb05-106">Raw SQL queries can return regular entity types or [keyless entity types](xref:core/modeling/keyless-entity-types) that are part of your model.</span></span>

> [!TIP]  
> <span data-ttu-id="4fb05-107">您可以在 GitHub 上查看本文[的範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/)。</span><span class="sxs-lookup"><span data-stu-id="4fb05-107">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/) on GitHub.</span></span>

## <a name="basic-raw-sql-queries"></a><span data-ttu-id="4fb05-108">基本的原始 SQL 查詢</span><span class="sxs-lookup"><span data-stu-id="4fb05-108">Basic raw SQL queries</span></span>

<span data-ttu-id="4fb05-109">可以使用`FromSqlRaw`擴充方法基於原始 SQL 查詢開始 LINQ 查詢。</span><span class="sxs-lookup"><span data-stu-id="4fb05-109">You can use the `FromSqlRaw` extension method to begin a LINQ query based on a raw SQL query.</span></span> <span data-ttu-id="4fb05-110">`FromSqlRaw`只能用於查詢根,即直接在上`DbSet<>`。</span><span class="sxs-lookup"><span data-stu-id="4fb05-110">`FromSqlRaw` can only be used on query roots, that is directly on the `DbSet<>`.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRaw)]

<span data-ttu-id="4fb05-111">原始 SQL 查詢可以用來執行預存程序。</span><span class="sxs-lookup"><span data-stu-id="4fb05-111">Raw SQL queries can be used to execute a stored procedure.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedure)]

## <a name="passing-parameters"></a><span data-ttu-id="4fb05-112">傳遞參數</span><span class="sxs-lookup"><span data-stu-id="4fb05-112">Passing parameters</span></span>

> [!WARNING]
> <span data-ttu-id="4fb05-113">**始終對原始 SQL 查詢使用參數化**</span><span class="sxs-lookup"><span data-stu-id="4fb05-113">**Always use parameterization for raw SQL queries**</span></span>
>
> <span data-ttu-id="4fb05-114">在原始 SQL 查詢中引入任何使用者提供的值時,必須注意避免 SQL 注入攻擊。</span><span class="sxs-lookup"><span data-stu-id="4fb05-114">When introducing any user-provided values into a raw SQL query, care must be taken to avoid SQL injection attacks.</span></span> <span data-ttu-id="4fb05-115">除了驗證此類值不包含無效字元外,始終使用參數化將值發送與 SQL 文本分開。</span><span class="sxs-lookup"><span data-stu-id="4fb05-115">In addition to validating that such values don't contain invalid characters, always use parameterization which sends the values separate from the SQL text.</span></span>
>
> <span data-ttu-id="4fb05-116">特別是,切勿將具有未經驗證的使用者提供的值的串聯或插值`$""`字串 ()`FromSqlRaw`傳遞`ExecuteSqlRaw`到或中。</span><span class="sxs-lookup"><span data-stu-id="4fb05-116">In particular, never pass a concatenated or interpolated string (`$""`) with non-validated user-provided values into `FromSqlRaw` or `ExecuteSqlRaw`.</span></span> <span data-ttu-id="4fb05-117">`FromSqlInterpolated`和`ExecuteSqlInterpolated`方法允許使用字串插值語法,以防止 SQL 注入攻擊。</span><span class="sxs-lookup"><span data-stu-id="4fb05-117">The `FromSqlInterpolated` and `ExecuteSqlInterpolated` methods allow using string interpolation syntax in a way that protects against SQL injection attacks.</span></span>

<span data-ttu-id="4fb05-118">下面的範例透過在 SQL 查詢字串中包括參數占位元並提供其他參數,將單個參數傳遞給儲存過程。</span><span class="sxs-lookup"><span data-stu-id="4fb05-118">The following example passes a single parameter to a stored procedure by including a parameter placeholder in the SQL query string and providing an additional argument.</span></span> <span data-ttu-id="4fb05-119">雖然此語法可能類似於`String.Format`語法,但提供的值將包裝在和`DbParameter`生成參數名稱中,插入指定`{0}`占位符的位置符的位置。</span><span class="sxs-lookup"><span data-stu-id="4fb05-119">While this syntax may look like `String.Format` syntax, the supplied value is wrapped in a `DbParameter` and the generated parameter name inserted where the `{0}` placeholder was specified.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureParameter)]

<span data-ttu-id="4fb05-120">`FromSqlInterpolated`類似於`FromSqlRaw`但允許您使用字串插值語法。</span><span class="sxs-lookup"><span data-stu-id="4fb05-120">`FromSqlInterpolated` is similar to `FromSqlRaw` but allows you to use string interpolation syntax.</span></span> <span data-ttu-id="4fb05-121">就像`FromSqlRaw``FromSqlInterpolated`, 只能在查詢根上使用。</span><span class="sxs-lookup"><span data-stu-id="4fb05-121">Just like `FromSqlRaw`, `FromSqlInterpolated` can only be used on query roots.</span></span> <span data-ttu-id="4fb05-122">與前面的範例一樣,該值將轉換為,`DbParameter`並且不受 SQL 注入的影響。</span><span class="sxs-lookup"><span data-stu-id="4fb05-122">As with the previous example, the value is converted to a `DbParameter` and isn't vulnerable to SQL injection.</span></span>

> [!NOTE]
> <span data-ttu-id="4fb05-123">在版本`FromSqlRaw`3.0`FromSqlInterpolated`之前 ,`FromSql`並且是名為的兩個重載。</span><span class="sxs-lookup"><span data-stu-id="4fb05-123">Prior to version 3.0, `FromSqlRaw` and `FromSqlInterpolated` were two overloads named `FromSql`.</span></span> <span data-ttu-id="4fb05-124">有關詳細資訊,請參閱[以前的版本部分](#previous-versions)。</span><span class="sxs-lookup"><span data-stu-id="4fb05-124">For more information, see the [previous versions section](#previous-versions).</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedStoredProcedureParameter)]

<span data-ttu-id="4fb05-125">您也可以建構 DbParameter，並將它提供為參數值。</span><span class="sxs-lookup"><span data-stu-id="4fb05-125">You can also construct a DbParameter and supply it as a parameter value.</span></span> <span data-ttu-id="4fb05-126">由於可以使用一般 SQL 參數占位元,而不是字串佔位`FromSqlRaw`元, 因此可以安全地使用:</span><span class="sxs-lookup"><span data-stu-id="4fb05-126">Since a regular SQL parameter placeholder is used, rather than a string placeholder, `FromSqlRaw` can be safely used:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureSqlParameter)]

<span data-ttu-id="4fb05-127">`FromSqlRaw`允許您在 SQL 查詢字串中使用命名參數,這在儲存過程具有可選參數時非常有用:</span><span class="sxs-lookup"><span data-stu-id="4fb05-127">`FromSqlRaw` allows you to use named parameters in the SQL query string, which is useful when a stored procedure has optional parameters:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureNamedSqlParameter)]

## <a name="composing-with-linq"></a><span data-ttu-id="4fb05-128">使用 LINQ 撰寫</span><span class="sxs-lookup"><span data-stu-id="4fb05-128">Composing with LINQ</span></span>

<span data-ttu-id="4fb05-129">您可以使用 LINQ 運算子在初始原始 SQL 查詢的頂部進行組合。</span><span class="sxs-lookup"><span data-stu-id="4fb05-129">You can compose on top of the initial raw SQL query using LINQ operators.</span></span> <span data-ttu-id="4fb05-130">EF Core 將將其視為子查詢,並在資料庫中進行組合。</span><span class="sxs-lookup"><span data-stu-id="4fb05-130">EF Core will treat it as subquery and compose over it in the database.</span></span> <span data-ttu-id="4fb05-131">下面的範例使用從表值函數 (TVF) 中選擇的原始 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="4fb05-131">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF).</span></span> <span data-ttu-id="4fb05-132">然後使用 LINQ 進行篩選和排序。</span><span class="sxs-lookup"><span data-stu-id="4fb05-132">And then composes on it using LINQ to do filtering and sorting.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedComposed)]

<span data-ttu-id="4fb05-133">上面查詢產生以下 SQL:</span><span class="sxs-lookup"><span data-stu-id="4fb05-133">Above query generates following SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM (
    SELECT * FROM dbo.SearchBlogs(@p0)
) AS [b]
WHERE [b].[Rating] > 3
ORDER BY [b].[Rating] DESC
```

### <a name="including-related-data"></a><span data-ttu-id="4fb05-134">包含相關資料</span><span class="sxs-lookup"><span data-stu-id="4fb05-134">Including related data</span></span>

<span data-ttu-id="4fb05-135">`Include` 方法可用來包含相關的資料，就如同所有其他的 LINQ 查詢一般：</span><span class="sxs-lookup"><span data-stu-id="4fb05-135">The `Include` method can be used to include related data, just like with any other LINQ query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedInclude)]

<span data-ttu-id="4fb05-136">使用 LINQ 組合需要原始 SQL 查詢是可組合的,因為 EF Core 會將提供的 SQL 視為子查詢。</span><span class="sxs-lookup"><span data-stu-id="4fb05-136">Composing with LINQ requires your raw SQL query to be composable since EF Core will treat the supplied SQL as a subquery.</span></span> <span data-ttu-id="4fb05-137">以 `SELECT` 關鍵字為開頭的 SQL 查詢才可撰寫。</span><span class="sxs-lookup"><span data-stu-id="4fb05-137">SQL queries that can be composed on begin with the `SELECT` keyword.</span></span> <span data-ttu-id="4fb05-138">此外,SQL 傳遞不應包含任何在子查詢上無效的字元或選項,例如:</span><span class="sxs-lookup"><span data-stu-id="4fb05-138">Further, SQL passed shouldn't contain any characters or options that aren't valid on a subquery, such as:</span></span>

- <span data-ttu-id="4fb05-139">尾隨分號</span><span class="sxs-lookup"><span data-stu-id="4fb05-139">A trailing semicolon</span></span>
- <span data-ttu-id="4fb05-140">在 SQL Server 上，結尾的查詢層級提示 (例如，`OPTION (HASH JOIN)`)</span><span class="sxs-lookup"><span data-stu-id="4fb05-140">On SQL Server, a trailing query-level hint (for example, `OPTION (HASH JOIN)`)</span></span>
- <span data-ttu-id="4fb05-141">在 SQL`ORDER BY`Server`OFFSET 0``SELECT`上,子句`TOP 100 PERCENT`中未與 OR 一起使用</span><span class="sxs-lookup"><span data-stu-id="4fb05-141">On SQL Server, an `ORDER BY` clause that isn't used with `OFFSET 0` OR `TOP 100 PERCENT` in the `SELECT` clause</span></span>

<span data-ttu-id="4fb05-142">SQL Server 不允許對儲存過程呼叫進行群組,因此任何嘗試將其他查詢運算符應用於此類調用都將導致 SQL 無效。</span><span class="sxs-lookup"><span data-stu-id="4fb05-142">SQL Server doesn't allow composing over stored procedure calls, so any attempt to apply additional query operators to such a call will result in invalid SQL.</span></span> <span data-ttu-id="4fb05-143">使用`AsEnumerable``AsAsyncEnumerable`或`FromSqlRaw`方法`FromSqlInterpolated`, 以確保 EF Core 不會嘗試透過儲存過程進行撰寫。</span><span class="sxs-lookup"><span data-stu-id="4fb05-143">Use `AsEnumerable` or `AsAsyncEnumerable` method right after `FromSqlRaw` or `FromSqlInterpolated` methods to make sure that EF Core doesn't try to compose over a stored procedure.</span></span>

## <a name="change-tracking"></a><span data-ttu-id="4fb05-144">變更追蹤</span><span class="sxs-lookup"><span data-stu-id="4fb05-144">Change Tracking</span></span>

<span data-ttu-id="4fb05-145">使用`FromSqlRaw``FromSqlInterpolated`或方法的查詢遵循與 EF Core 中的任何其他 LINQ 查詢完全相同的更改追蹤規則。</span><span class="sxs-lookup"><span data-stu-id="4fb05-145">Queries that use the `FromSqlRaw` or `FromSqlInterpolated` methods follow the exact same change tracking rules as any other LINQ query in EF Core.</span></span> <span data-ttu-id="4fb05-146">例如，若查詢投影實體類型，就會依預設追蹤結果。</span><span class="sxs-lookup"><span data-stu-id="4fb05-146">For example, if the query projects entity types, the results will be tracked by default.</span></span>

<span data-ttu-id="4fb05-147">下面的範例使用原始 SQL 查詢,該查詢從表值函數 (TVF) 中選擇,然後停用使用呼`AsNoTracking`叫 的更改追蹤 :</span><span class="sxs-lookup"><span data-stu-id="4fb05-147">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF), then disables change tracking with the call to `AsNoTracking`:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedAsNoTracking)]

## <a name="limitations"></a><span data-ttu-id="4fb05-148">限制</span><span class="sxs-lookup"><span data-stu-id="4fb05-148">Limitations</span></span>

<span data-ttu-id="4fb05-149">使用原始 SQL 查詢時有一些要注意的限制：</span><span class="sxs-lookup"><span data-stu-id="4fb05-149">There are a few limitations to be aware of when using raw SQL queries:</span></span>

- <span data-ttu-id="4fb05-150">SQL 查詢必須返回實體類型的所有屬性的數據。</span><span class="sxs-lookup"><span data-stu-id="4fb05-150">The SQL query must return data for all properties of the entity type.</span></span>
- <span data-ttu-id="4fb05-151">結果集中的資料行名稱必須符合屬性所對應的資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="4fb05-151">The column names in the result set must match the column names that properties are mapped to.</span></span> <span data-ttu-id="4fb05-152">請注意,此行為與 EF6 不同。</span><span class="sxs-lookup"><span data-stu-id="4fb05-152">Note this behavior is different from EF6.</span></span> <span data-ttu-id="4fb05-153">EF6 忽略了原始 SQL 查詢的列映射屬性,結果集列名稱必須與屬性名稱匹配。</span><span class="sxs-lookup"><span data-stu-id="4fb05-153">EF6 ignored property to column mapping for raw SQL queries and result set column names had to match the property names.</span></span>
- <span data-ttu-id="4fb05-154">SQL 查詢不能包含相關數據。</span><span class="sxs-lookup"><span data-stu-id="4fb05-154">The SQL query can't contain related data.</span></span> <span data-ttu-id="4fb05-155">不過，在許多情況下，您可以使用 `Include` 運算子來傳回相關資料以在查詢上方進行撰寫 (請參閱[包含相關資料](#including-related-data))。</span><span class="sxs-lookup"><span data-stu-id="4fb05-155">However, in many cases you can compose on top of the query using the `Include` operator to return related data (see [Including related data](#including-related-data)).</span></span>

## <a name="previous-versions"></a><span data-ttu-id="4fb05-156">舊版本</span><span class="sxs-lookup"><span data-stu-id="4fb05-156">Previous versions</span></span>

<span data-ttu-id="4fb05-157">EF Core 版本 2.2 和`FromSql`早期有兩個 稱為 的方法重載,`FromSqlRaw``FromSqlInterpolated`其行為方式與較新的 和相同。</span><span class="sxs-lookup"><span data-stu-id="4fb05-157">EF Core version 2.2 and earlier had two overloads of method named `FromSql`, which behaved in the same way as the newer `FromSqlRaw` and `FromSqlInterpolated`.</span></span> <span data-ttu-id="4fb05-158">當意圖調用插值字串方法時,很容易意外地調用原始字串方法,而相反。</span><span class="sxs-lookup"><span data-stu-id="4fb05-158">It was easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span> <span data-ttu-id="4fb05-159">意外調用錯誤的重載可能會導致查詢在本應被參數化時。</span><span class="sxs-lookup"><span data-stu-id="4fb05-159">Calling wrong overload accidentally could result in queries not being parameterized when they should have been.</span></span>
