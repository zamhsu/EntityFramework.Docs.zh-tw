---
title: 原始 SQL 查詢 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 70aae9b5-8743-4557-9c5d-239f688bf418
uid: core/querying/raw-sql
ms.openlocfilehash: ebec5775770c0f1e297eaaf35bf644c605a69afc
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197767"
---
# <a name="raw-sql-queries"></a><span data-ttu-id="30fc3-102">原始 SQL 查詢</span><span class="sxs-lookup"><span data-stu-id="30fc3-102">Raw SQL Queries</span></span>

<span data-ttu-id="30fc3-103">Entity Framework Core 可讓您在處理關聯式資料庫時，下拉至原始 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="30fc3-103">Entity Framework Core allows you to drop down to raw SQL queries when working with a relational database.</span></span> <span data-ttu-id="30fc3-104">如果您想要執行的查詢無法使用 LINQ 來表示，或如果使用 LINQ 查詢導致 SQL 查詢效率不佳，這項功能就很有用。</span><span class="sxs-lookup"><span data-stu-id="30fc3-104">This can be useful if the query you want to perform can't be expressed using LINQ, or if using a LINQ query is resulting in an inefficient SQL query.</span></span> <span data-ttu-id="30fc3-105">原始 SQL 查詢可以傳回屬於模型一部分的一般實體類型或[無索引鍵實體類型](xref:core/modeling/keyless-entity-types)。</span><span class="sxs-lookup"><span data-stu-id="30fc3-105">Raw SQL queries can return regular entity types or [keyless entity types](xref:core/modeling/keyless-entity-types) that are part of your model.</span></span>

> [!TIP]  
> <span data-ttu-id="30fc3-106">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying/Querying/RawSQL/Sample.cs) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="30fc3-106">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying/Querying/RawSQL/Sample.cs) on GitHub.</span></span>

## <a name="basic-raw-sql-queries"></a><span data-ttu-id="30fc3-107">基本的原始 SQL 查詢</span><span class="sxs-lookup"><span data-stu-id="30fc3-107">Basic raw SQL queries</span></span>

<span data-ttu-id="30fc3-108">您可以使用`FromSqlRaw`擴充方法，根據原始 SQL 查詢來開始 LINQ 查詢。</span><span class="sxs-lookup"><span data-stu-id="30fc3-108">You can use the `FromSqlRaw` extension method to begin a LINQ query based on a raw SQL query.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSqlRaw("SELECT * FROM dbo.Blogs")
    .ToList();
```

<span data-ttu-id="30fc3-109">原始 SQL 查詢可以用來執行預存程序。</span><span class="sxs-lookup"><span data-stu-id="30fc3-109">Raw SQL queries can be used to execute a stored procedure.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSqlRaw("EXECUTE dbo.GetMostPopularBlogs")
    .ToList();
```

## <a name="passing-parameters"></a><span data-ttu-id="30fc3-110">傳遞參數</span><span class="sxs-lookup"><span data-stu-id="30fc3-110">Passing parameters</span></span>

> [!WARNING]
> <span data-ttu-id="30fc3-111">**一律針對原始 SQL 查詢使用參數化**</span><span class="sxs-lookup"><span data-stu-id="30fc3-111">**Always use parameterization for raw SQL queries**</span></span>
>
> <span data-ttu-id="30fc3-112">將任何使用者提供的值引進原始 SQL 查詢時，必須小心避免 SQL 插入式攻擊。</span><span class="sxs-lookup"><span data-stu-id="30fc3-112">When introducing any user-provided values into a raw SQL query, care must be taken to avoid SQL injection attacks.</span></span> <span data-ttu-id="30fc3-113">除了驗證此類值不包含不正確字元，請一律使用參數化，將值與 SQL 文字分開傳送。</span><span class="sxs-lookup"><span data-stu-id="30fc3-113">In addition to validating that such values don't contain invalid characters, always use parameterization which sends the values separate from the SQL text.</span></span>
>
> <span data-ttu-id="30fc3-114">特別的是，絕對不要將串連或插入字串`$""`（）與未驗證使用者提供的值`FromSqlRaw`傳遞`ExecuteSqlRaw`至或。</span><span class="sxs-lookup"><span data-stu-id="30fc3-114">In particular, never pass a concatenated or interpolated string (`$""`) with unvalidated user-provided values into `FromSqlRaw` or `ExecuteSqlRaw`.</span></span> <span data-ttu-id="30fc3-115">`FromSqlInterpolated` 和`ExecuteSqlInterpolated`方法允許以防止 SQL 插入式攻擊的方式來使用字串插補語法。</span><span class="sxs-lookup"><span data-stu-id="30fc3-115">The `FromSqlInterpolated` and `ExecuteSqlInterpolated` methods allow using string interpolation syntax in a way that protects against SQL injection attacks.</span></span>

<span data-ttu-id="30fc3-116">下列範例會將參數預留位置包含在 SQL 查詢字串中，並提供其他引數，藉以將單一參數傳遞至預存程式。</span><span class="sxs-lookup"><span data-stu-id="30fc3-116">The following example passes a single parameter to a stored procedure by including a parameter placeholder in the SQL query string and providing an additional argument.</span></span> <span data-ttu-id="30fc3-117">雖然這看起來可能`String.Format`像語法，但是提供的值會包裝`DbParameter`在中，並在指定`{0}`預留位置的位置插入所產生的參數名稱。</span><span class="sxs-lookup"><span data-stu-id="30fc3-117">While this may look like `String.Format` syntax, the supplied value is wrapped in a `DbParameter` and the generated parameter name inserted where the `{0}` placeholder was specified.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSqlRaw("EXECUTE dbo.GetMostPopularBlogsForUser {0}", user)
    .ToList();
```

<span data-ttu-id="30fc3-118">除了，您還`FromSqlRaw`可以使用`FromSqlInterpolated`它來允許安全地使用字串插補。</span><span class="sxs-lookup"><span data-stu-id="30fc3-118">As an alternative to `FromSqlRaw`, you can use `FromSqlInterpolated` which allows the safe use of string interpolation.</span></span> <span data-ttu-id="30fc3-119">如同先前的範例，此值會轉換成`DbParameter` ，因此不容易遭受 SQL 插入式攻擊：</span><span class="sxs-lookup"><span data-stu-id="30fc3-119">As with the previous example, the value is converted to a `DbParameter` and is therefore not vulnerable to SQL injection:</span></span>

> [!NOTE]
> <span data-ttu-id="30fc3-120">在3.0 版之前， `FromSqlRaw`和`FromSqlInterpolated`是兩個名`FromSql`為的多載。</span><span class="sxs-lookup"><span data-stu-id="30fc3-120">Prior to version 3.0, `FromSqlRaw` and `FromSqlInterpolated` were two overloads named `FromSql`.</span></span> <span data-ttu-id="30fc3-121">如需詳細資訊，請參閱[先前的版本一節](#previous-versions)。</span><span class="sxs-lookup"><span data-stu-id="30fc3-121">See the [previous versions section](#previous-versions) for more details.</span></span>


<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSqlInterpolated($"EXECUTE dbo.GetMostPopularBlogsForUser {user}")
    .ToList();
```

<span data-ttu-id="30fc3-122">您也可以建構 DbParameter，並將它提供為參數值。</span><span class="sxs-lookup"><span data-stu-id="30fc3-122">You can also construct a DbParameter and supply it as a parameter value.</span></span> <span data-ttu-id="30fc3-123">因為使用了一般 SQL 參數預留位置，而不是字串預留位置， `FromSqlRaw`所以可以安全地使用：</span><span class="sxs-lookup"><span data-stu-id="30fc3-123">Since a regular SQL parameter placeholder is used, rather than a string placeholder, `FromSqlRaw` can be safely used:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSqlRaw("EXECUTE dbo.GetMostPopularBlogsForUser @user", user)
    .ToList();
```

<span data-ttu-id="30fc3-124">這可讓您在 SQL 查詢字串中使用具名的參數，這在當預存程序具有選擇性參數時會很有幫助：</span><span class="sxs-lookup"><span data-stu-id="30fc3-124">This allows you to use named parameters in the SQL query string, which is useful when a stored procedure has optional parameters:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSqlRaw("EXECUTE dbo.GetMostPopularBlogs @filterByUser=@user", user)
    .ToList();
```

## <a name="composing-with-linq"></a><span data-ttu-id="30fc3-125">使用 LINQ 撰寫</span><span class="sxs-lookup"><span data-stu-id="30fc3-125">Composing with LINQ</span></span>

<span data-ttu-id="30fc3-126">如果可以在資料庫中撰寫 SQL 查詢，則您可以使用 LINQ 運算子在初始原始 SQL 查詢上撰寫。</span><span class="sxs-lookup"><span data-stu-id="30fc3-126">If the SQL query can be composed on in the database, then you can compose on top of the initial raw SQL query using LINQ operators.</span></span> <span data-ttu-id="30fc3-127">以 `SELECT` 關鍵字為開頭的 SQL 查詢才可撰寫。</span><span class="sxs-lookup"><span data-stu-id="30fc3-127">SQL queries that can be composed on begin with the `SELECT` keyword.</span></span>

<span data-ttu-id="30fc3-128">下列範例使用的原始 SQL 查詢會從資料表值函式 (TVF) 中進行選取，然後使用 LINQ 在其上進行撰寫以執行篩選和排序。</span><span class="sxs-lookup"><span data-stu-id="30fc3-128">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF) and then composes on it using LINQ to perform filtering and sorting.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSqlInterpolated($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Where(b => b.Rating > 3)
    .OrderByDescending(b => b.Rating)
    .ToList();
```

<span data-ttu-id="30fc3-129">這會產生下列 SQL 查詢：</span><span class="sxs-lookup"><span data-stu-id="30fc3-129">This will produce the following SQL query:</span></span>

``` sql
SELECT [b].[Id], [b].[Name], [b].[Rating]
        FROM (
            SELECT * FROM dbo.SearchBlogs(@p0)
        ) AS b
        WHERE b."Rating" > 3
        ORDER BY b."Rating" DESC
```

## <a name="change-tracking"></a><span data-ttu-id="30fc3-130">變更追蹤</span><span class="sxs-lookup"><span data-stu-id="30fc3-130">Change Tracking</span></span>

<span data-ttu-id="30fc3-131">使用`FromSql`方法的查詢會遵循與 EF Core 中的任何其他 LINQ 查詢完全相同的變更追蹤規則。</span><span class="sxs-lookup"><span data-stu-id="30fc3-131">Queries that use the `FromSql` methods follow the exact same change tracking rules as any other LINQ query in EF Core.</span></span> <span data-ttu-id="30fc3-132">例如，若查詢投影實體類型，就會依預設追蹤結果。</span><span class="sxs-lookup"><span data-stu-id="30fc3-132">For example, if the query projects entity types, the results will be tracked by default.</span></span>

<span data-ttu-id="30fc3-133">下列範例會使用從資料表值函式（TVF）選取的原始 SQL 查詢，然後透過呼叫來`AsNoTracking`停用變更追蹤：</span><span class="sxs-lookup"><span data-stu-id="30fc3-133">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF), then disables change tracking with the call to `AsNoTracking`:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Query<SearchBlogsDto>()
    .FromSqlInterpolated($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .AsNoTracking()
    .ToList();
```

## <a name="including-related-data"></a><span data-ttu-id="30fc3-134">包含相關資料</span><span class="sxs-lookup"><span data-stu-id="30fc3-134">Including related data</span></span>

<span data-ttu-id="30fc3-135">`Include` 方法可用來包含相關的資料，就如同所有其他的 LINQ 查詢一般：</span><span class="sxs-lookup"><span data-stu-id="30fc3-135">The `Include` method can be used to include related data, just like with any other LINQ query:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSqlInterpolated($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Include(b => b.Posts)
    .ToList();
```

<span data-ttu-id="30fc3-136">請注意，這需要您的原始 SQL 查詢可組合;這不會與預存程序呼叫搭配使用。</span><span class="sxs-lookup"><span data-stu-id="30fc3-136">Note that this requires your raw SQL query to be composable; it will notably not work with stored procedure calls.</span></span> <span data-ttu-id="30fc3-137">請參閱[限制](#limitations)底下的複合性注意事項）。</span><span class="sxs-lookup"><span data-stu-id="30fc3-137">See notes on composability under [Limitations](#limitations)).</span></span>

## <a name="limitations"></a><span data-ttu-id="30fc3-138">限制</span><span class="sxs-lookup"><span data-stu-id="30fc3-138">Limitations</span></span>

<span data-ttu-id="30fc3-139">使用原始 SQL 查詢時有一些要注意的限制：</span><span class="sxs-lookup"><span data-stu-id="30fc3-139">There are a few limitations to be aware of when using raw SQL queries:</span></span>

* <span data-ttu-id="30fc3-140">SQL 查詢必須傳回實體類型之所有屬性的資料。</span><span class="sxs-lookup"><span data-stu-id="30fc3-140">The SQL query must return data for all properties of the entity type.</span></span>

* <span data-ttu-id="30fc3-141">結果集中的資料行名稱必須符合屬性所對應的資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="30fc3-141">The column names in the result set must match the column names that properties are mapped to.</span></span> <span data-ttu-id="30fc3-142">請注意，此特性與 EF6 不同，因為在 EF6 中針對原始 SQL 查詢的屬性/資料行對應會被略過，而且結果集資料行名稱必須符合屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="30fc3-142">Note this is different from EF6 where property/column mapping was ignored for raw SQL queries and result set column names had to match the property names.</span></span>

* <span data-ttu-id="30fc3-143">SQL 查詢不能包含相關資料。</span><span class="sxs-lookup"><span data-stu-id="30fc3-143">The SQL query cannot contain related data.</span></span> <span data-ttu-id="30fc3-144">不過，在許多情況下，您可以使用 `Include` 運算子來傳回相關資料以在查詢上方進行撰寫 (請參閱[包含相關資料](#including-related-data))。</span><span class="sxs-lookup"><span data-stu-id="30fc3-144">However, in many cases you can compose on top of the query using the `Include` operator to return related data (see [Including related data](#including-related-data)).</span></span>

* <span data-ttu-id="30fc3-145">傳遞到此方法的 `SELECT` 陳述式通常應該是可組合的：如果 EF Core 需要評估伺服器上的其他查詢運算子（例如，轉譯在方法之後`FromSql`套用的 LINQ 運算子），則會將所提供的 SQL 視為子查詢。</span><span class="sxs-lookup"><span data-stu-id="30fc3-145">`SELECT` statements passed to this method should generally be composable: If EF Core needs to evaluate additional query operators on the server (for example, to translate LINQ operators applied after `FromSql` methods), the supplied SQL will be treated as a subquery.</span></span> <span data-ttu-id="30fc3-146">這表示所傳遞的 SQL 不應包含在子查詢上無效的任何字元或選項，例如：</span><span class="sxs-lookup"><span data-stu-id="30fc3-146">This means that the SQL passed should not contain any characters or options that are not valid on a subquery, such as:</span></span>
  * <span data-ttu-id="30fc3-147">尾端分號</span><span class="sxs-lookup"><span data-stu-id="30fc3-147">A trailing semicolon</span></span>
  * <span data-ttu-id="30fc3-148">在 SQL Server 上，結尾的查詢層級提示 (例如，`OPTION (HASH JOIN)`)</span><span class="sxs-lookup"><span data-stu-id="30fc3-148">On SQL Server, a trailing query-level hint (for example, `OPTION (HASH JOIN)`)</span></span>
  * <span data-ttu-id="30fc3-149">在 SQL Server 上，`SELECT` 子句中未隨附 `OFFSET 0` 或 `TOP 100 PERCENT` 的 `ORDER BY` 子句</span><span class="sxs-lookup"><span data-stu-id="30fc3-149">On SQL Server, an `ORDER BY` clause that is not accompanied of `OFFSET 0` OR `TOP 100 PERCENT` in the `SELECT` clause</span></span>

* <span data-ttu-id="30fc3-150">請注意，SQL Server 不允許撰寫預存程序呼叫，因此嘗試將其他查詢運算子套用至這類呼叫將會導致不正確 SQL。</span><span class="sxs-lookup"><span data-stu-id="30fc3-150">Note that SQL Server does not allow composing over stored procedure calls, so any attempt to apply additional query operators to such a call will result in invalid SQL.</span></span> <span data-ttu-id="30fc3-151">在用戶端評估之後`AsEnumerable()` ，可能會引進查詢運算子。</span><span class="sxs-lookup"><span data-stu-id="30fc3-151">Query operators may be introduced after `AsEnumerable()` for client evaluation.</span></span>

# <a name="previous-versions"></a><span data-ttu-id="30fc3-152">舊版本</span><span class="sxs-lookup"><span data-stu-id="30fc3-152">Previous versions</span></span>

<span data-ttu-id="30fc3-153">EF Core 2.2 版和更早版本有兩`FromSql`個名為的多載，其行為`FromSqlRaw`方式`FromSqlInterpolated`與較新的和相同。</span><span class="sxs-lookup"><span data-stu-id="30fc3-153">EF Core version 2.2 and earlier had two overloads named `FromSql` which behaved in the same way as the newer `FromSqlRaw` and `FromSqlInterpolated`.</span></span> <span data-ttu-id="30fc3-154">如此一來，當意圖呼叫插入字串方法時，就很容易不小心呼叫未經處理的字串方法，另一種方法則是這樣。</span><span class="sxs-lookup"><span data-stu-id="30fc3-154">This made it very easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span> <span data-ttu-id="30fc3-155">這可能會導致查詢在應該參數化時不進行參數化。</span><span class="sxs-lookup"><span data-stu-id="30fc3-155">This could result in queries not being parameterized when they should have been.</span></span>
