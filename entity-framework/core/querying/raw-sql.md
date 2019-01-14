---
title: 原始 SQL 查詢 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 70aae9b5-8743-4557-9c5d-239f688bf418
uid: core/querying/raw-sql
ms.openlocfilehash: 5bddddfbc2fe8d0ba99914f03b28bde4076fae42
ms.sourcegitcommit: e66745c9f91258b2cacf5ff263141be3cba4b09e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2019
ms.locfileid: "54058706"
---
# <a name="raw-sql-queries"></a>原始 SQL 查詢

Entity Framework Core 可讓您在處理關聯式資料庫時，下拉至原始 SQL 查詢。 這在您想要執行的查詢無法使用 LINQ 來表達時，或是使用 LINQ 查詢會導致 SQL 查詢沒有效率時，會非常實用。 原始 SQL 查詢可以傳回實體型別，或從 EF Core 2.1 開始，可以傳回屬於您模型之一部分的[查詢型別](xref:core/modeling/query-types)。

> [!TIP]  
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) \(英文\)。

## <a name="limitations"></a>限制

使用原始 SQL 查詢時有一些要注意的限制：

* SQL 查詢必須傳回實體或查詢類型所有屬性的資料。

* 結果集中的資料行名稱必須符合屬性所對應的資料行名稱。 請注意，此特性與 EF6 不同，因為在 EF6 中針對原始 SQL 查詢的屬性/資料行對應會被略過，而且結果集資料行名稱必須符合屬性名稱。

* SQL 查詢不能包含相關資料。 不過，在許多情況下，您可以使用 `Include` 運算子來傳回相關資料以在查詢上方進行撰寫 (請參閱[包含相關資料](#including-related-data))。

* 傳遞到此方法的 `SELECT` 陳述式通常應該是可組合的：如果 EF Core 必須評估伺服器上的其他查詢運算子 (例如，轉譯在 `FromSql` 之後套用的 LINQ 運算子)，則會將所提供的 SQL 視為子查詢。 這表示所傳遞的 SQL 不應包含在子查詢上無效的任何字元或選項，例如：
  * 結尾的分號
  * 在 SQL Server 上，結尾的查詢層級提示 (例如，`OPTION (HASH JOIN)`)
  * (在 SQL Server 上) `SELECT` 子句中未隨附 `TOP 100 PERCENT` 的 `ORDER BY` 子句

* `SELECT` 以外的 SQL 陳述式會自動被辨識為不可組合。 因此，會將預存程序的完整結果一律會傳回用戶端，並會在記憶體內評估於 `FromSql` 之後所套用的任何 LINQ 運算子。

## <a name="basic-raw-sql-queries"></a>基本的原始 SQL 查詢

您可以使用 *FromSql* 擴充方法來根據原始 SQL 查詢開始 LINQ 查詢。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSql("SELECT * FROM dbo.Blogs")
    .ToList();
```

原始 SQL 查詢可以用來執行預存程序。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogs")
    .ToList();
```

## <a name="passing-parameters"></a>傳遞參數

如同可接受 SQL 的任何 API，請務必將任何使用者輸入參數化，以防止受到 SQL 插入式攻擊。 您可以在 SQL 查詢字串中包含參數預留位置，然後提供參數值作為其他引數。 您提供的任何參數值都將自動轉換為 `DbParameter`。

下列範例會將單一參數傳遞到預存程序。 雖然這可能看起來很像 `String.Format` 語法，但會將提供的值包裝於參數中，並會在指定 `{0}` 預留位置的位置插入產生的參數名稱。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogsForUser {0}", user)
    .ToList();
```

以下為相同的查詢，但是使用由 EF Core 2.0 和更新版本所支援的字串內插補點語法：

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSql($"EXECUTE dbo.GetMostPopularBlogsForUser {user}")
    .ToList();
```

您也可以建構 DbParameter，並將它提供為參數值。 這可讓您在 SQL 查詢字串中使用具名參數。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogsForUser @user", user)
    .ToList();
```

## <a name="composing-with-linq"></a>使用 LINQ 撰寫

如果可以在資料庫中撰寫 SQL 查詢，則您可以使用 LINQ 運算子在初始原始 SQL 查詢上撰寫。 以 `SELECT` 關鍵字為開頭的 SQL 查詢才可撰寫。

下列範例使用的原始 SQL 查詢會從資料表值函式 (TVF) 中進行選取，然後使用 LINQ 在其上進行撰寫以執行篩選和排序。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Where(b => b.Rating > 3)
    .OrderByDescending(b => b.Rating)
    .ToList();
```

### <a name="including-related-data"></a>包含相關資料

使用 LINQ 運算子進行撰寫可用來在查詢中包含相關資料。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Include(b => b.Posts)
    .ToList();
```

> [!WARNING]  
> **一律針對原始 SQL 查詢使用參數化：** 接受原始 SQL 字串 (例如 `FromSql` 和 `ExecuteSqlCommand`) 的 API，能使值輕鬆地作為參數傳遞。 除了驗證使用者輸入之外，請一律針對用於 SQL 查詢/命令中的任何值使用參數化。 如果您使用字串串連來以動態方式建置查詢字串的任何部分，則必須負責驗證所有輸入以防止受到 SQL 插入式攻擊。
