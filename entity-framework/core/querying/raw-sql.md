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
# <a name="raw-sql-queries"></a>原始 SQL 查詢

Entity Framework Core 可讓您使用關聯式資料庫時，下拉式清單來原始的 SQL 查詢。 這可以是很有用，如果不使用 LINQ，表示您想要執行的查詢，或使用 LINQ 查詢會導致效率不佳 SQL 傳送給資料庫。

> [!TIP]  
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) \(英文\)。

## <a name="limitations"></a>限制

有一些限制時要注意的使用原始的 SQL 查詢：
* SQL 查詢可以只用來傳回屬於您的模型的實體類型。 沒有在我們的待辦項目，來增強[啟用從原始的 SQL 查詢傳回特定型別](https://github.com/aspnet/EntityFramework/issues/1862)。

* SQL 查詢必須傳回所有屬性的實體或查詢類型的資料。

* 在結果集中的資料行名稱必須符合屬性對應到資料行名稱。 請注意這點不同於 EF6 屬性/資料行對應已忽略原始的 SQL 查詢和結果集資料行名稱必須符合的屬性名稱。

* SQL 查詢不能包含相關的資料。 不過，在許多情況下您可以撰寫查詢使用之上`Include`運算子傳回相關的資料 (請參閱[包括相關的資料](#including-related-data))。

* `SELECT` 陳述式傳遞給這個方法通常應該是可組合： 如果 EF 核心必須評估伺服器上的其他查詢運算子 (例如轉譯 LINQ 運算子之後套用`FromSql`)，提供的 SQL 將會被視為子查詢。 這表示傳遞 SQL 應該不會包含任何字元或不是有效的子查詢，這類的選項：
  * 尾端的分號
  * SQL Server 上尾端查詢層級提示，例如： `OPTION (HASH JOIN)`
  * SQL Server 上`ORDER BY`子句未隨附的`TOP 100 PERCENT`中`SELECT`子句

* SQL 陳述式以外`SELECT`會自動被辨識為非可組合。 因此，預存程序的完整結果一律會傳回用戶端和之後套用任何 LINQ 運算子`FromSql`是在記憶體中評估。 

## <a name="basic-raw-sql-queries"></a>基本的原始 SQL 查詢

您可以使用*FromSql*開始 LINQ 查詢是根據原始的 SQL 查詢的擴充方法。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSql("SELECT * FROM dbo.Blogs")
    .ToList();
```

原始的 SQL 查詢可以用來執行預存程序。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogs")
    .ToList();
```

## <a name="passing-parameters"></a>傳遞參數

如同任何應用程式開發介面可接受 SQL，請務必將參數化的任何使用者輸入，以防止 SQL 資料隱碼攻擊。 您可以在 SQL 查詢字串中包含參數位置，然後提供 做為其他引數的參數值。 您提供任何參數值將自動轉換成`DbParameter`。

下列範例會將單一參數傳遞至預存程序。 雖然這看起來像`String.Format`所提供的值會包裝在語法中，插入的位置參數，以及與產生的參數名稱`{0}`指定預留位置。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogsForUser {0}", user)
    .ToList();
```

這是相同的查詢，但使用字串插值語法 EF 核心 2.0 和更新版本都支援：

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSql($"EXECUTE dbo.GetMostPopularBlogsForUser {user}")
    .ToList();
```

您也可以建構 DbParameter，並提供它做為參數值。 這可讓您在 SQL 查詢字串中使用具名的參數

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogsForUser @user", user)
    .ToList();
```

## <a name="composing-with-linq"></a>使用 LINQ 撰寫

如果資料庫中，可以在撰寫 SQL 查詢，您可以撰寫在初始原始 SQL 查詢使用 LINQ 運算子之上。 必須與可撰寫 SQL 查詢`SELECT`關鍵字。

下列範例會使用原始的 SQL 查詢選取資料表值函式 (TVF)，然後撰寫使用 LINQ 來進行篩選和排序。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Where(b => b.Rating > 3)
    .OrderByDescending(b => b.Rating)
    .ToList();
```

### <a name="including-related-data"></a>包括相關的資料

撰寫 LINQ 運算子可以用來在查詢中包含相關的資料。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Include(b => b.Posts)
    .ToList();
```

> [!WARNING]  
> **一律使用參數化的未經處理的 SQL 查詢：** 接受原始 SQL Api 的這類字串`FromSql`和`ExecuteSqlCommand`允許輕鬆地傳遞做為參數的值。 除了驗證使用者輸入，請一律使用參數化的未經處理的 SQL 查詢/命令中使用的任何值。 如果您使用字串串連以動態方式建立查詢字串的任何部分，則您必須負責驗證任何輸入，以防止 SQL 資料隱碼攻擊。
