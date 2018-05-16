---
title: 原始的 SQL 查詢的 EF 核心
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 70aae9b5-8743-4557-9c5d-239f688bf418
ms.technology: entity-framework-core
uid: core/querying/raw-sql
ms.openlocfilehash: 29b7e20e875bf791a88a92636c1df4bc4e31656b
ms.sourcegitcommit: 038acd91ce2f5a28d76dcd2eab72eeba225e366d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2018
---
# <a name="raw-sql-queries"></a><span data-ttu-id="a9044-102">原始 SQL 查詢</span><span class="sxs-lookup"><span data-stu-id="a9044-102">Raw SQL Queries</span></span>

<span data-ttu-id="a9044-103">Entity Framework Core 可讓您使用關聯式資料庫時，下拉式清單來原始的 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="a9044-103">Entity Framework Core allows you to drop down to raw SQL queries when working with a relational database.</span></span> <span data-ttu-id="a9044-104">這可以是很有用，如果不使用 LINQ，表示您想要執行的查詢，或使用 LINQ 查詢會導致效率不佳 SQL 傳送給資料庫。</span><span class="sxs-lookup"><span data-stu-id="a9044-104">This can be useful if the query you want to perform can't be expressed using LINQ, or if using a LINQ query is resulting in inefficient SQL being sent to the database.</span></span>

> [!TIP]  
> <span data-ttu-id="a9044-105">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="a9044-105">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="limitations"></a><span data-ttu-id="a9044-106">限制</span><span class="sxs-lookup"><span data-stu-id="a9044-106">Limitations</span></span>

<span data-ttu-id="a9044-107">有一些限制時要注意的使用原始的 SQL 查詢：</span><span class="sxs-lookup"><span data-stu-id="a9044-107">There are a few limitations to be aware of when using raw SQL queries:</span></span>
* <span data-ttu-id="a9044-108">SQL 查詢可以只用來傳回屬於您的模型的實體類型。</span><span class="sxs-lookup"><span data-stu-id="a9044-108">SQL queries can only be used to return entity types that are part of your model.</span></span> <span data-ttu-id="a9044-109">沒有在我們的待辦項目，來增強[啟用從原始的 SQL 查詢傳回特定型別](https://github.com/aspnet/EntityFramework/issues/1862)。</span><span class="sxs-lookup"><span data-stu-id="a9044-109">There is an enhancement on our backlog to [enable returning ad-hoc types from raw SQL queries](https://github.com/aspnet/EntityFramework/issues/1862).</span></span>

* <span data-ttu-id="a9044-110">SQL 查詢必須傳回所有屬性的實體或查詢類型的資料。</span><span class="sxs-lookup"><span data-stu-id="a9044-110">The SQL query must return data for all properties of the entity or query type.</span></span>

* <span data-ttu-id="a9044-111">在結果集中的資料行名稱必須符合屬性對應到資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="a9044-111">The column names in the result set must match the column names that properties are mapped to.</span></span> <span data-ttu-id="a9044-112">請注意這點不同於 EF6 屬性/資料行對應已忽略原始的 SQL 查詢和結果集資料行名稱必須符合的屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="a9044-112">Note this is different from EF6 where property/column mapping was ignored for raw SQL queries and result set column names had to match the property names.</span></span>

* <span data-ttu-id="a9044-113">SQL 查詢不能包含相關的資料。</span><span class="sxs-lookup"><span data-stu-id="a9044-113">The SQL query cannot contain related data.</span></span> <span data-ttu-id="a9044-114">不過，在許多情況下您可以撰寫查詢使用之上`Include`運算子傳回相關的資料 (請參閱[包括相關的資料](#including-related-data))。</span><span class="sxs-lookup"><span data-stu-id="a9044-114">However, in many cases you can compose on top of the query using the `Include` operator to return related data (see [Including related data](#including-related-data)).</span></span>

* <span data-ttu-id="a9044-115">`SELECT` 陳述式傳遞給這個方法通常應該是可組合： 如果 EF 核心必須評估伺服器上的其他查詢運算子 (例如轉譯 LINQ 運算子之後套用`FromSql`)，提供的 SQL 將會被視為子查詢。</span><span class="sxs-lookup"><span data-stu-id="a9044-115">`SELECT` statements passed to this method should generally be composable: If EF Core needs to evaluate additional query operators on the server (e.g. to translate LINQ operators applied after `FromSql`), the supplied SQL will be treated as a subquery.</span></span> <span data-ttu-id="a9044-116">這表示傳遞 SQL 應該不會包含任何字元或不是有效的子查詢，這類的選項：</span><span class="sxs-lookup"><span data-stu-id="a9044-116">This means that the SQL passed should not contain any characters or options that are not valid on a subquery, such as:</span></span>
  * <span data-ttu-id="a9044-117">尾端的分號</span><span class="sxs-lookup"><span data-stu-id="a9044-117">a trailing semicolon</span></span>
  * <span data-ttu-id="a9044-118">SQL Server 上尾端查詢層級提示，例如： `OPTION (HASH JOIN)`</span><span class="sxs-lookup"><span data-stu-id="a9044-118">On SQL Server, a trailing query-level hint, e.g. `OPTION (HASH JOIN)`</span></span>
  * <span data-ttu-id="a9044-119">SQL Server 上`ORDER BY`子句未隨附的`TOP 100 PERCENT`中`SELECT`子句</span><span class="sxs-lookup"><span data-stu-id="a9044-119">On SQL Server, an `ORDER BY` clause that is not accompanied of `TOP 100 PERCENT` in the `SELECT` clause</span></span>

* <span data-ttu-id="a9044-120">SQL 陳述式以外`SELECT`會自動被辨識為非可組合。</span><span class="sxs-lookup"><span data-stu-id="a9044-120">SQL statements other than `SELECT` are recognized automatically as non-composable.</span></span> <span data-ttu-id="a9044-121">因此，預存程序的完整結果一律會傳回用戶端和之後套用任何 LINQ 運算子`FromSql`是在記憶體中評估。</span><span class="sxs-lookup"><span data-stu-id="a9044-121">As a consequence, the full results of stored procedures are always returned to the client and any LINQ operators applied after `FromSql` are evaluated in-memory.</span></span> 

## <a name="basic-raw-sql-queries"></a><span data-ttu-id="a9044-122">基本的原始 SQL 查詢</span><span class="sxs-lookup"><span data-stu-id="a9044-122">Basic raw SQL queries</span></span>

<span data-ttu-id="a9044-123">您可以使用*FromSql*開始 LINQ 查詢是根據原始的 SQL 查詢的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="a9044-123">You can use the *FromSql* extension method to begin a LINQ query based on a raw SQL query.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSql("SELECT * FROM dbo.Blogs")
    .ToList();
```

<span data-ttu-id="a9044-124">原始的 SQL 查詢可以用來執行預存程序。</span><span class="sxs-lookup"><span data-stu-id="a9044-124">Raw SQL queries can be used to execute a stored procedure.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogs")
    .ToList();
```

## <a name="passing-parameters"></a><span data-ttu-id="a9044-125">傳遞參數</span><span class="sxs-lookup"><span data-stu-id="a9044-125">Passing parameters</span></span>

<span data-ttu-id="a9044-126">如同任何應用程式開發介面可接受 SQL，請務必將參數化的任何使用者輸入，以防止 SQL 資料隱碼攻擊。</span><span class="sxs-lookup"><span data-stu-id="a9044-126">As with any API that accepts SQL, it is important to parameterize any user input to protect against a SQL injection attack.</span></span> <span data-ttu-id="a9044-127">您可以在 SQL 查詢字串中包含參數位置，然後提供 做為其他引數的參數值。</span><span class="sxs-lookup"><span data-stu-id="a9044-127">You can include parameter placeholders in the SQL query string and then supply parameter values as additional arguments.</span></span> <span data-ttu-id="a9044-128">您提供任何參數值將自動轉換成`DbParameter`。</span><span class="sxs-lookup"><span data-stu-id="a9044-128">Any parameter values you supply will automatically be converted to a `DbParameter`.</span></span>

<span data-ttu-id="a9044-129">下列範例會將單一參數傳遞至預存程序。</span><span class="sxs-lookup"><span data-stu-id="a9044-129">The following example passes a single parameter to a stored procedure.</span></span> <span data-ttu-id="a9044-130">雖然這看起來像`String.Format`所提供的值會包裝在語法中，插入的位置參數，以及與產生的參數名稱`{0}`指定預留位置。</span><span class="sxs-lookup"><span data-stu-id="a9044-130">While this may look like `String.Format` syntax, the supplied value is wrapped in a parameter and the generated parameter name inserted where the `{0}` placeholder was specified.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogsForUser {0}", user)
    .ToList();
```

<span data-ttu-id="a9044-131">這是相同的查詢，但使用字串插值語法 EF 核心 2.0 和更新版本都支援：</span><span class="sxs-lookup"><span data-stu-id="a9044-131">This is the same query but using string interpolation syntax, which is supported in EF Core 2.0 and above:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSql($"EXECUTE dbo.GetMostPopularBlogsForUser {user}")
    .ToList();
```

<span data-ttu-id="a9044-132">您也可以建構 DbParameter，並提供它做為參數值。</span><span class="sxs-lookup"><span data-stu-id="a9044-132">You can also construct a DbParameter and supply it as a parameter value.</span></span> <span data-ttu-id="a9044-133">這可讓您在 SQL 查詢字串中使用具名的參數</span><span class="sxs-lookup"><span data-stu-id="a9044-133">This allows you to use named parameters in the SQL query string</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogsForUser @user", user)
    .ToList();
```

## <a name="composing-with-linq"></a><span data-ttu-id="a9044-134">使用 LINQ 撰寫</span><span class="sxs-lookup"><span data-stu-id="a9044-134">Composing with LINQ</span></span>

<span data-ttu-id="a9044-135">如果資料庫中，可以在撰寫 SQL 查詢，您可以撰寫在初始原始 SQL 查詢使用 LINQ 運算子之上。</span><span class="sxs-lookup"><span data-stu-id="a9044-135">If the SQL query can be composed on in the database, then you can compose on top of the initial raw SQL query using LINQ operators.</span></span> <span data-ttu-id="a9044-136">必須與可撰寫 SQL 查詢`SELECT`關鍵字。</span><span class="sxs-lookup"><span data-stu-id="a9044-136">SQL queries that can be composed on being with the `SELECT` keyword.</span></span>

<span data-ttu-id="a9044-137">下列範例會使用原始的 SQL 查詢選取資料表值函式 (TVF)，然後撰寫使用 LINQ 來進行篩選和排序。</span><span class="sxs-lookup"><span data-stu-id="a9044-137">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF) and then composes on it using LINQ to perform filtering and sorting.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Where(b => b.Rating > 3)
    .OrderByDescending(b => b.Rating)
    .ToList();
```

### <a name="including-related-data"></a><span data-ttu-id="a9044-138">包括相關的資料</span><span class="sxs-lookup"><span data-stu-id="a9044-138">Including related data</span></span>

<span data-ttu-id="a9044-139">撰寫 LINQ 運算子可以用來在查詢中包含相關的資料。</span><span class="sxs-lookup"><span data-stu-id="a9044-139">Composing with LINQ operators can be used to include related data in the query.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Include(b => b.Posts)
    .ToList();
```

> [!WARNING]  
> <span data-ttu-id="a9044-140">**一律使用參數化的未經處理的 SQL 查詢：** 接受原始 SQL Api 的這類字串`FromSql`和`ExecuteSqlCommand`允許輕鬆地傳遞做為參數的值。</span><span class="sxs-lookup"><span data-stu-id="a9044-140">**Always use parameterization for raw SQL queries:** APIs that accept a raw SQL string such as `FromSql` and `ExecuteSqlCommand` allow values to be easily passed as parameters.</span></span> <span data-ttu-id="a9044-141">除了驗證使用者輸入，請一律使用參數化的未經處理的 SQL 查詢/命令中使用的任何值。</span><span class="sxs-lookup"><span data-stu-id="a9044-141">In addition to validating user input, always use parameterization for any values used in a raw SQL query/command.</span></span> <span data-ttu-id="a9044-142">如果您使用字串串連以動態方式建立查詢字串的任何部分，則您必須負責驗證任何輸入，以防止 SQL 資料隱碼攻擊。</span><span class="sxs-lookup"><span data-stu-id="a9044-142">If you are using string concatenation to dynamically build any part of the query string then you are responsible for validating any input to protect against SQL injection attacks.</span></span>
