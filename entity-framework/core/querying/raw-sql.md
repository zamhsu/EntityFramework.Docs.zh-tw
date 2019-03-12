---
title: 原始 SQL 查詢 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 70aae9b5-8743-4557-9c5d-239f688bf418
uid: core/querying/raw-sql
ms.openlocfilehash: ad7ac3099cfd4c49b88acfbbff61f2af9294b6ec
ms.sourcegitcommit: a013e243a14f384999ceccaf9c779b8c1ae3b936
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2019
ms.locfileid: "57463239"
---
# <a name="raw-sql-queries"></a><span data-ttu-id="868b4-102">原始 SQL 查詢</span><span class="sxs-lookup"><span data-stu-id="868b4-102">Raw SQL Queries</span></span>

<span data-ttu-id="868b4-103">Entity Framework Core 可讓您在處理關聯式資料庫時，下拉至原始 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="868b4-103">Entity Framework Core allows you to drop down to raw SQL queries when working with a relational database.</span></span> <span data-ttu-id="868b4-104">這在您想要執行的查詢無法使用 LINQ 來表達時，或是使用 LINQ 查詢會導致 SQL 查詢沒有效率時，會非常實用。</span><span class="sxs-lookup"><span data-stu-id="868b4-104">This can be useful if the query you want to perform can't be expressed using LINQ, or if using a LINQ query is resulting in inefficient SQL queries.</span></span> <span data-ttu-id="868b4-105">原始 SQL 查詢可以傳回實體型別，或從 EF Core 2.1 開始，可以傳回屬於您模型之一部分的[查詢型別](xref:core/modeling/query-types)。</span><span class="sxs-lookup"><span data-stu-id="868b4-105">Raw SQL queries can return entity types or, starting with EF Core 2.1, [query types](xref:core/modeling/query-types) that are part of your model.</span></span>

> [!TIP]  
> <span data-ttu-id="868b4-106">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="868b4-106">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="basic-raw-sql-queries"></a><span data-ttu-id="868b4-107">基本的原始 SQL 查詢</span><span class="sxs-lookup"><span data-stu-id="868b4-107">Basic raw SQL queries</span></span>

<span data-ttu-id="868b4-108">您可以使用 *FromSql* 擴充方法來根據原始 SQL 查詢開始 LINQ 查詢。</span><span class="sxs-lookup"><span data-stu-id="868b4-108">You can use the *FromSql* extension method to begin a LINQ query based on a raw SQL query.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSql("SELECT * FROM dbo.Blogs")
    .ToList();
```

<span data-ttu-id="868b4-109">原始 SQL 查詢可以用來執行預存程序。</span><span class="sxs-lookup"><span data-stu-id="868b4-109">Raw SQL queries can be used to execute a stored procedure.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogs")
    .ToList();
```

## <a name="passing-parameters"></a><span data-ttu-id="868b4-110">傳遞參數</span><span class="sxs-lookup"><span data-stu-id="868b4-110">Passing parameters</span></span>

<span data-ttu-id="868b4-111">如同可接受 SQL 的任何 API，請務必將任何使用者輸入參數化，以防止受到 SQL 插入式攻擊。</span><span class="sxs-lookup"><span data-stu-id="868b4-111">As with any API that accepts SQL, it is important to parameterize any user input to protect against a SQL injection attack.</span></span> <span data-ttu-id="868b4-112">您可以在 SQL 查詢字串中包含參數預留位置，然後提供參數值作為其他引數。</span><span class="sxs-lookup"><span data-stu-id="868b4-112">You can include parameter placeholders in the SQL query string and then supply parameter values as additional arguments.</span></span> <span data-ttu-id="868b4-113">您提供的任何參數值都將自動轉換為 `DbParameter`。</span><span class="sxs-lookup"><span data-stu-id="868b4-113">Any parameter values you supply will automatically be converted to a `DbParameter`.</span></span>

<span data-ttu-id="868b4-114">下列範例會將單一參數傳遞到預存程序。</span><span class="sxs-lookup"><span data-stu-id="868b4-114">The following example passes a single parameter to a stored procedure.</span></span> <span data-ttu-id="868b4-115">雖然這可能看起來很像 `String.Format` 語法，但會將提供的值包裝於參數中，並會在指定 `{0}` 預留位置的位置插入產生的參數名稱。</span><span class="sxs-lookup"><span data-stu-id="868b4-115">While this may look like `String.Format` syntax, the supplied value is wrapped in a parameter and the generated parameter name inserted where the `{0}` placeholder was specified.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogsForUser {0}", user)
    .ToList();
```

<span data-ttu-id="868b4-116">以下為相同的查詢，但是使用由 EF Core 2.0 和更新版本所支援的字串內插補點語法：</span><span class="sxs-lookup"><span data-stu-id="868b4-116">This is the same query but using string interpolation syntax, which is supported in EF Core 2.0 and above:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSql($"EXECUTE dbo.GetMostPopularBlogsForUser {user}")
    .ToList();
```

<span data-ttu-id="868b4-117">您也可以建構 DbParameter，並將它提供為參數值。</span><span class="sxs-lookup"><span data-stu-id="868b4-117">You can also construct a DbParameter and supply it as a parameter value.</span></span> <span data-ttu-id="868b4-118">這可讓您在 SQL 查詢字串中使用具名參數。</span><span class="sxs-lookup"><span data-stu-id="868b4-118">This allows you to use named parameters in the SQL query string.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogsForUser @user", user)
    .ToList();
```

## <a name="composing-with-linq"></a><span data-ttu-id="868b4-119">使用 LINQ 撰寫</span><span class="sxs-lookup"><span data-stu-id="868b4-119">Composing with LINQ</span></span>

<span data-ttu-id="868b4-120">如果可以在資料庫中撰寫 SQL 查詢，則您可以使用 LINQ 運算子在初始原始 SQL 查詢上撰寫。</span><span class="sxs-lookup"><span data-stu-id="868b4-120">If the SQL query can be composed on in the database, then you can compose on top of the initial raw SQL query using LINQ operators.</span></span> <span data-ttu-id="868b4-121">以 `SELECT` 關鍵字為開頭的 SQL 查詢才可撰寫。</span><span class="sxs-lookup"><span data-stu-id="868b4-121">SQL queries that can be composed on begin with the `SELECT` keyword.</span></span>

<span data-ttu-id="868b4-122">下列範例使用的原始 SQL 查詢會從資料表值函式 (TVF) 中進行選取，然後使用 LINQ 在其上進行撰寫以執行篩選和排序。</span><span class="sxs-lookup"><span data-stu-id="868b4-122">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF) and then composes on it using LINQ to perform filtering and sorting.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Where(b => b.Rating > 3)
    .OrderByDescending(b => b.Rating)
    .ToList();
```

## <a name="change-tracking"></a><span data-ttu-id="868b4-123">變更追蹤</span><span class="sxs-lookup"><span data-stu-id="868b4-123">Change Tracking</span></span>

<span data-ttu-id="868b4-124">使用 `FromSql()` 的查詢就如同 EF Core 中所有其他的 LINQ 查詢一般，遵循完全相同的變更追蹤規則。</span><span class="sxs-lookup"><span data-stu-id="868b4-124">Queries that use the `FromSql()` follow the exact same change tracking rules as any other LINQ query in EF Core.</span></span> <span data-ttu-id="868b4-125">例如，若查詢投影實體類型，就會依預設追蹤結果。</span><span class="sxs-lookup"><span data-stu-id="868b4-125">For example, if the query projects entity types, the results will be tracked by default.</span></span>  

<span data-ttu-id="868b4-126">以下範例會使用從資料表值函式 (TVF) 選取的原始 SQL 查詢，然後透過 teh 呼叫 .AsNoTracking() 來停用變更追蹤：</span><span class="sxs-lookup"><span data-stu-id="868b4-126">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF), then disables change tracking with teh call to .AsNoTracking():</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Query<SearchBlogsDto>()
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .AsNoTracking()
    .ToList();
```

## <a name="including-related-data"></a><span data-ttu-id="868b4-127">包含相關資料</span><span class="sxs-lookup"><span data-stu-id="868b4-127">Including related data</span></span>

<span data-ttu-id="868b4-128">`Include()` 方法可用來包含相關的資料，就如同所有其他的 LINQ 查詢一般：</span><span class="sxs-lookup"><span data-stu-id="868b4-128">The `Include()` method can be used to include related data, just like with any other LINQ query:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Include(b => b.Posts)
    .ToList();
```

## <a name="limitations"></a><span data-ttu-id="868b4-129">限制</span><span class="sxs-lookup"><span data-stu-id="868b4-129">Limitations</span></span>

<span data-ttu-id="868b4-130">使用原始 SQL 查詢時有一些要注意的限制：</span><span class="sxs-lookup"><span data-stu-id="868b4-130">There are a few limitations to be aware of when using raw SQL queries:</span></span>

* <span data-ttu-id="868b4-131">SQL 查詢必須傳回實體或查詢類型所有屬性的資料。</span><span class="sxs-lookup"><span data-stu-id="868b4-131">The SQL query must return data for all properties of the entity or query type.</span></span>

* <span data-ttu-id="868b4-132">結果集中的資料行名稱必須符合屬性所對應的資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="868b4-132">The column names in the result set must match the column names that properties are mapped to.</span></span> <span data-ttu-id="868b4-133">請注意，此特性與 EF6 不同，因為在 EF6 中針對原始 SQL 查詢的屬性/資料行對應會被略過，而且結果集資料行名稱必須符合屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="868b4-133">Note this is different from EF6 where property/column mapping was ignored for raw SQL queries and result set column names had to match the property names.</span></span>

* <span data-ttu-id="868b4-134">SQL 查詢不能包含相關資料。</span><span class="sxs-lookup"><span data-stu-id="868b4-134">The SQL query cannot contain related data.</span></span> <span data-ttu-id="868b4-135">不過，在許多情況下，您可以使用 `Include` 運算子來傳回相關資料以在查詢上方進行撰寫 (請參閱[包含相關資料](#including-related-data))。</span><span class="sxs-lookup"><span data-stu-id="868b4-135">However, in many cases you can compose on top of the query using the `Include` operator to return related data (see [Including related data](#including-related-data)).</span></span>

* <span data-ttu-id="868b4-136">傳遞到此方法的 `SELECT` 陳述式通常應該是可組合的：如果 EF Core 必須評估伺服器上的其他查詢運算子 (例如，轉譯在 `FromSql` 之後套用的 LINQ 運算子)，則會將所提供的 SQL 視為子查詢。</span><span class="sxs-lookup"><span data-stu-id="868b4-136">`SELECT` statements passed to this method should generally be composable: If EF Core needs to evaluate additional query operators on the server (for example, to translate LINQ operators applied after `FromSql`), the supplied SQL will be treated as a subquery.</span></span> <span data-ttu-id="868b4-137">這表示所傳遞的 SQL 不應包含在子查詢上無效的任何字元或選項，例如：</span><span class="sxs-lookup"><span data-stu-id="868b4-137">This means that the SQL passed should not contain any characters or options that are not valid on a subquery, such as:</span></span>
  * <span data-ttu-id="868b4-138">結尾的分號</span><span class="sxs-lookup"><span data-stu-id="868b4-138">a trailing semicolon</span></span>
  * <span data-ttu-id="868b4-139">在 SQL Server 上，結尾的查詢層級提示 (例如，`OPTION (HASH JOIN)`)</span><span class="sxs-lookup"><span data-stu-id="868b4-139">On SQL Server, a trailing query-level hint (for example, `OPTION (HASH JOIN)`)</span></span>
  * <span data-ttu-id="868b4-140">(在 SQL Server 上) `SELECT` 子句中未隨附 `TOP 100 PERCENT` 的 `ORDER BY` 子句</span><span class="sxs-lookup"><span data-stu-id="868b4-140">On SQL Server, an `ORDER BY` clause that is not accompanied of `TOP 100 PERCENT` in the `SELECT` clause</span></span>

* <span data-ttu-id="868b4-141">`SELECT` 以外的 SQL 陳述式會自動被辨識為不可組合。</span><span class="sxs-lookup"><span data-stu-id="868b4-141">SQL statements other than `SELECT` are recognized automatically as non-composable.</span></span> <span data-ttu-id="868b4-142">因此，會將預存程序的完整結果一律會傳回用戶端，並會在記憶體內評估於 `FromSql` 之後所套用的任何 LINQ 運算子。</span><span class="sxs-lookup"><span data-stu-id="868b4-142">As a consequence, the full results of stored procedures are always returned to the client and any LINQ operators applied after `FromSql` are evaluated in-memory.</span></span>

> [!WARNING]  
> <span data-ttu-id="868b4-143">**一律針對原始 SQL 查詢使用參數化：** 除了驗證使用者輸入之外，請一律針對用於 SQL 查詢/命令中的任何值使用參數化。</span><span class="sxs-lookup"><span data-stu-id="868b4-143">**Always use parameterization for raw SQL queries:** In addition to validating user input, always use parameterization for any values used in a raw SQL query/command.</span></span> <span data-ttu-id="868b4-144">接受原始 SQL 字串 (例如 `FromSql` 和 `ExecuteSqlCommand`) 的 API，能使值輕鬆地作為參數傳遞。</span><span class="sxs-lookup"><span data-stu-id="868b4-144">APIs that accept a raw SQL string such as `FromSql` and `ExecuteSqlCommand` allow values to be easily passed as parameters.</span></span> <span data-ttu-id="868b4-145">接受 FormattableString 的 `FromSql` 和 `ExecuteSqlCommand` 多載，也允許使用字串插補語法，藉此有助於防止受到 SQL 插入式攻擊。</span><span class="sxs-lookup"><span data-stu-id="868b4-145">Overloads of `FromSql` and `ExecuteSqlCommand` that accept FormattableString also allow using string interpolation syntaxt in a way that helps protect against SQL injection attacks.</span></span> 
> 
> <span data-ttu-id="868b4-146">如果您使用字串串連或插補來動態建置查詢字串的任一部分，或是將使用者輸入傳遞至陳述式或預存程序以便將這些輸入當做動態 SQL 來執行，則必須負責驗證所有輸入以防止受到 SQL 插入式攻擊。</span><span class="sxs-lookup"><span data-stu-id="868b4-146">If you are using string concatenation or interpolation to dynamically build any part of the query string, or passing user input to statements or stored procedures that can execute those inputs as dynamic SQL, then you are responsible for validating any input to protect against SQL injection attacks.</span></span>
