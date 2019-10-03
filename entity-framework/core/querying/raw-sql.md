---
title: 原始 SQL 查詢 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 70aae9b5-8743-4557-9c5d-239f688bf418
uid: core/querying/raw-sql
ms.openlocfilehash: d8f52edfdf4bd7776ab8d81185c867cbfd7bcf44
ms.sourcegitcommit: 6c28926a1e35e392b198a8729fc13c1c1968a27b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71813602"
---
# <a name="raw-sql-queries"></a>原始 SQL 查詢

Entity Framework Core 可讓您在處理關聯式資料庫時，下拉至原始 SQL 查詢。 如果您想要執行的查詢無法使用 LINQ 來表示，或如果使用 LINQ 查詢導致 SQL 查詢效率不佳，這項功能就很有用。 原始 SQL 查詢可以傳回屬於模型一部分的一般實體類型或[無索引鍵實體類型](xref:core/modeling/keyless-entity-types)。

> [!TIP]  
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying/Querying/RawSQL/Sample.cs) \(英文\)。

## <a name="basic-raw-sql-queries"></a>基本的原始 SQL 查詢

您可以使用`FromSqlRaw`擴充方法，根據原始 SQL 查詢來開始 LINQ 查詢。

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSqlRaw("SELECT * FROM dbo.Blogs")
    .ToList();
```

原始 SQL 查詢可以用來執行預存程序。

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSqlRaw("EXECUTE dbo.GetMostPopularBlogs")
    .ToList();
```

## <a name="passing-parameters"></a>傳遞參數

> [!WARNING]
> **一律針對原始 SQL 查詢使用參數化**
>
> 將任何使用者提供的值引進原始 SQL 查詢時，必須小心避免 SQL 插入式攻擊。 除了驗證此類值不包含不正確字元，請一律使用參數化，將值與 SQL 文字分開傳送。
>
> 特別的是，絕對不要將串連或插入字串`$""`（）與未驗證使用者提供的值`FromSqlRaw`傳遞`ExecuteSqlRaw`至或。 `FromSqlInterpolated` 和`ExecuteSqlInterpolated`方法允許以防止 SQL 插入式攻擊的方式來使用字串插補語法。

下列範例會將參數預留位置包含在 SQL 查詢字串中，並提供其他引數，藉以將單一參數傳遞至預存程式。 雖然這看起來可能`String.Format`像語法，但是提供的值會包裝`DbParameter`在中，並在指定`{0}`預留位置的位置插入所產生的參數名稱。

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSqlRaw("EXECUTE dbo.GetMostPopularBlogsForUser {0}", user)
    .ToList();
```

除了，您還`FromSqlRaw`可以使用`FromSqlInterpolated`它來允許安全地使用字串插補。 如同先前的範例，此值會轉換成`DbParameter` ，因此不容易遭受 SQL 插入式攻擊：

> [!NOTE]
> 在3.0 版之前， `FromSqlRaw`和`FromSqlInterpolated`是兩個名`FromSql`為的多載。 如需詳細資訊，請參閱[先前的版本一節](#previous-versions)。

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSqlInterpolated($"EXECUTE dbo.GetMostPopularBlogsForUser {user}")
    .ToList();
```

您也可以建構 DbParameter，並將它提供為參數值。 因為使用了一般 SQL 參數預留位置，而不是字串預留位置， `FromSqlRaw`所以可以安全地使用：

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSqlRaw("EXECUTE dbo.GetMostPopularBlogsForUser @user", user)
    .ToList();
```

這可讓您在 SQL 查詢字串中使用具名的參數，這在當預存程序具有選擇性參數時會很有幫助：

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSqlRaw("EXECUTE dbo.GetMostPopularBlogs @filterByUser=@user", user)
    .ToList();
```

## <a name="composing-with-linq"></a>使用 LINQ 撰寫

如果可以在資料庫中撰寫 SQL 查詢，則您可以使用 LINQ 運算子在初始原始 SQL 查詢上撰寫。 以 `SELECT` 關鍵字為開頭的 SQL 查詢才可撰寫。

下列範例使用的原始 SQL 查詢會從資料表值函式 (TVF) 中進行選取，然後使用 LINQ 在其上進行撰寫以執行篩選和排序。

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSqlInterpolated($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Where(b => b.Rating > 3)
    .OrderByDescending(b => b.Rating)
    .ToList();
```

這會產生下列 SQL 查詢：

``` sql
SELECT [b].[Id], [b].[Name], [b].[Rating]
        FROM (
            SELECT * FROM dbo.SearchBlogs(@p0)
        ) AS b
        WHERE b."Rating" > 3
        ORDER BY b."Rating" DESC
```

## <a name="change-tracking"></a>變更追蹤

使用`FromSql`方法的查詢會遵循與 EF Core 中的任何其他 LINQ 查詢完全相同的變更追蹤規則。 例如，若查詢投影實體類型，就會依預設追蹤結果。

下列範例會使用從資料表值函式（TVF）選取的原始 SQL 查詢，然後透過呼叫來`AsNoTracking`停用變更追蹤：

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Query<SearchBlogsDto>()
    .FromSqlInterpolated($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .AsNoTracking()
    .ToList();
```

## <a name="including-related-data"></a>包含相關資料

`Include` 方法可用來包含相關的資料，就如同所有其他的 LINQ 查詢一般：

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSqlInterpolated($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Include(b => b.Posts)
    .ToList();
```

請注意，這需要您的原始 SQL 查詢可組合;這不會與預存程序呼叫搭配使用。 請參閱[限制](#limitations)底下的複合性注意事項）。

## <a name="limitations"></a>限制

使用原始 SQL 查詢時有一些要注意的限制：

* SQL 查詢必須傳回實體類型之所有屬性的資料。

* 結果集中的資料行名稱必須符合屬性所對應的資料行名稱。 請注意，此特性與 EF6 不同，因為在 EF6 中針對原始 SQL 查詢的屬性/資料行對應會被略過，而且結果集資料行名稱必須符合屬性名稱。

* SQL 查詢不能包含相關資料。 不過，在許多情況下，您可以使用 `Include` 運算子來傳回相關資料以在查詢上方進行撰寫 (請參閱[包含相關資料](#including-related-data))。

* 傳遞到此方法的 `SELECT` 陳述式通常應該是可組合的：如果 EF Core 需要評估伺服器上的其他查詢運算子（例如，轉譯在方法之後`FromSql`套用的 LINQ 運算子），則會將所提供的 SQL 視為子查詢。 這表示所傳遞的 SQL 不應包含在子查詢上無效的任何字元或選項，例如：
  * 尾端分號
  * 在 SQL Server 上，結尾的查詢層級提示 (例如，`OPTION (HASH JOIN)`)
  * 在 SQL Server 上，`SELECT` 子句中未隨附 `OFFSET 0` 或 `TOP 100 PERCENT` 的 `ORDER BY` 子句

* 請注意，SQL Server 不允許撰寫預存程序呼叫，因此嘗試將其他查詢運算子套用至這類呼叫將會導致不正確 SQL。 在用戶端評估之後`AsEnumerable()` ，可能會引進查詢運算子。

## <a name="previous-versions"></a>舊版本

EF Core 2.2 版和更早版本有兩`FromSql`個名為的多載，其行為`FromSqlRaw`方式`FromSqlInterpolated`與較新的和相同。 如此一來，當意圖呼叫插入字串方法時，就很容易不小心呼叫未經處理的字串方法，另一種方法則是這樣。 這可能會導致查詢在應該參數化時不進行參數化。
