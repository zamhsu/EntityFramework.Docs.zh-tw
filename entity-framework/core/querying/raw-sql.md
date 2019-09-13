---
title: 原始 SQL 查詢 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 70aae9b5-8743-4557-9c5d-239f688bf418
uid: core/querying/raw-sql
ms.openlocfilehash: 7a0df6fb656be58103971f45b9e12e9f1383311f
ms.sourcegitcommit: b2b9468de2cf930687f8b85c3ce54ff8c449f644
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/12/2019
ms.locfileid: "70921723"
---
# <a name="raw-sql-queries"></a>原始 SQL 查詢

Entity Framework Core 可讓您在處理關聯式資料庫時，下拉至原始 SQL 查詢。 這在您想要執行的查詢無法使用 LINQ 來表達時，或是使用 LINQ 查詢會導致 SQL 查詢沒有效率時，會非常實用。 原始 SQL 查詢可以傳回實體型別，或從 EF Core 2.1 開始，可以傳回屬於您模型之一部分的[查詢型別](xref:core/modeling/query-types)。

> [!TIP]  
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) \(英文\)。

## <a name="basic-raw-sql-queries"></a>基本的原始 SQL 查詢

您可以使用 *FromSql* 擴充方法來根據原始 SQL 查詢開始 LINQ 查詢。

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSql("SELECT * FROM dbo.Blogs")
    .ToList();
```

原始 SQL 查詢可以用來執行預存程序。

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogs")
    .ToList();
```

## <a name="passing-parameters"></a>傳遞參數

如同可接受 SQL 的任何 API，請務必將任何使用者輸入參數化，以防止受到 SQL 插入式攻擊。 您可以在 SQL 查詢字串中包含參數預留位置，然後提供參數值作為其他引數。 您提供的任何參數值都將自動轉換為 `DbParameter`。

下列範例會將單一參數傳遞到預存程序。 雖然這可能看起來很像 `String.Format` 語法，但會將提供的值包裝於參數中，並會在指定 `{0}` 預留位置的位置插入產生的參數名稱。

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogsForUser {0}", user)
    .ToList();
```

以下為相同的查詢，但是使用由 EF Core 2.0 和更新版本所支援的字串內插補點語法：

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSql($"EXECUTE dbo.GetMostPopularBlogsForUser {user}")
    .ToList();
```

您也可以建構 DbParameter，並將其提供為參數值：

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogsForUser @user", user)
    .ToList();
```

這可讓您在 SQL 查詢字串中使用具名的參數，這在當預存程序具有選擇性參數時會很有幫助：

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogs @filterByUser=@user", user)
    .ToList();
```

## <a name="composing-with-linq"></a>使用 LINQ 撰寫

如果可以在資料庫中撰寫 SQL 查詢，則您可以使用 LINQ 運算子在初始原始 SQL 查詢上撰寫。 以 `SELECT` 關鍵字為開頭的 SQL 查詢才可撰寫。

下列範例使用的原始 SQL 查詢會從資料表值函式 (TVF) 中進行選取，然後使用 LINQ 在其上進行撰寫以執行篩選和排序。

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Where(b => b.Rating > 3)
    .OrderByDescending(b => b.Rating)
    .ToList();
```

## <a name="change-tracking"></a>變更追蹤

使用 `FromSql()` 的查詢就如同 EF Core 中所有其他的 LINQ 查詢一般，遵循完全相同的變更追蹤規則。 例如，若查詢投影實體類型，就會依預設追蹤結果。  

以下範例會使用從資料表值函式 (TVF) 選取的原始 SQL 查詢，然後透過 呼叫 .AsNoTracking() 來停用變更追蹤：

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Query<SearchBlogsDto>()
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .AsNoTracking()
    .ToList();
```

## <a name="including-related-data"></a>包含相關資料

`Include()` 方法可用來包含相關的資料，就如同所有其他的 LINQ 查詢一般：

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Include(b => b.Posts)
    .ToList();
```

## <a name="limitations"></a>限制

使用原始 SQL 查詢時有一些要注意的限制：

* SQL 查詢必須傳回實體或查詢類型所有屬性的資料。

* 結果集中的資料行名稱必須符合屬性所對應的資料行名稱。 請注意，此特性與 EF6 不同，因為在 EF6 中針對原始 SQL 查詢的屬性/資料行對應會被略過，而且結果集資料行名稱必須符合屬性名稱。

* SQL 查詢不能包含相關資料。 不過，在許多情況下，您可以使用 `Include` 運算子來傳回相關資料以在查詢上方進行撰寫 (請參閱[包含相關資料](#including-related-data))。

* 傳遞到此方法的 `SELECT` 陳述式通常應該是可組合的：如果 EF Core 必須評估伺服器上的其他查詢運算子 (例如，轉譯在 `FromSql` 之後套用的 LINQ 運算子)，則會將所提供的 SQL 視為子查詢。 這表示所傳遞的 SQL 不應包含在子查詢上無效的任何字元或選項，例如：
  * 結尾的分號
  * 在 SQL Server 上，結尾的查詢層級提示 (例如，`OPTION (HASH JOIN)`)
  * 在 SQL Server 上，`SELECT` 子句中未隨附 `OFFSET 0` 或 `TOP 100 PERCENT` 的 `ORDER BY` 子句

* `SELECT` 以外的 SQL 陳述式會自動被辨識為不可組合。 因此，會將預存程序的完整結果一律會傳回用戶端，並會在記憶體內評估於 `FromSql` 之後所套用的任何 LINQ 運算子。

> [!WARNING]  
> **一律針對原始 SQL 查詢使用參數化：** 除了驗證使用者輸入之外，請一律針對用於 SQL 查詢/命令中的任何值使用參數化。 接受原始 SQL 字串 (例如 `FromSql` 和 `ExecuteSqlCommand`) 的 API，能使值輕鬆地作為參數傳遞。 接受 FormattableString 的 `FromSql` 和 `ExecuteSqlCommand` 多載，也允許使用字串插補語法，以協助防止 SQL 插入式攻擊。 
> 
> 如果您使用字串串連或插補來動態建置查詢字串的任一部分，或是將使用者輸入傳遞至陳述式或預存程序以便將這些輸入當做動態 SQL 來執行，則必須負責驗證所有輸入以防止受到 SQL 插入式攻擊。
