---
title: 用戶端與伺服器評估 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 8b6697cc-7067-4dc2-8007-85d80503d123
uid: core/querying/client-eval
ms.openlocfilehash: cb207d9e1b1004a4084dd6fc66712183b5bdd5dc
ms.sourcegitcommit: b2b9468de2cf930687f8b85c3ce54ff8c449f644
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/12/2019
ms.locfileid: "70921706"
---
# <a name="client-vs-server-evaluation"></a>用戶端與伺服器評估

Entity Framework Core 支援要在用戶端評估的查詢組件，以及要發送到資料庫的查詢組件。 由資料庫提供者決定將在資料庫中評估查詢的哪些組件。

> [!TIP]  
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) \(英文\)。

## <a name="client-evaluation"></a>用戶端評估

在下列範例中，會使用 Helper 方法來將從 SQL Server 資料庫所傳回部落格的 URL 標準化。 由於 SQL Server 提供者未深入了解此方法的實作方式，因此，無法將它轉譯為 SQL。 查詢的所有其他層面均會在資料庫中進行評估，但透過此方法傳遞所傳回的 `URL` 則會在用戶端執行。

<!-- [!code-csharp[Main](samples/core/Querying/ClientEval/Sample.cs?highlight=6)] -->
``` csharp
var blogs = context.Blogs
    .OrderByDescending(blog => blog.Rating)
    .Select(blog => new
    {
        Id = blog.BlogId,
        Url = StandardizeUrl(blog.Url)
    })
    .ToList();
```

<!-- [!code-csharp[Main](samples/core/Querying/ClientEval/Sample.cs)] -->
``` csharp
public static string StandardizeUrl(string url)
{
    url = url.ToLower();

    if (!url.StartsWith("http://"))
    {
        url = string.Concat("http://", url);
    }

    return url;
}
```

## <a name="client-evaluation-performance-issues"></a>用戶端評估效能問題

雖然用戶端評估非常實用，但在某些情況下，可能會導致效能不佳。 請考慮下列查詢，此查詢目前會在篩選條件中使用 Helper 方法。 因為這不能在資料庫中執行，所以會將所有資料提取到記憶體，然後在用戶端套用篩選條件。 根據資料量以及要篩選出多少資料而定，這可能會導致效能不佳。

<!-- [!code-csharp[Main](samples/core/Querying/ClientEval/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .Where(blog => StandardizeUrl(blog.Url).Contains("dotnet"))
    .ToList();
```

## <a name="client-evaluation-logging"></a>用戶端評估記錄

根據預設，執行用戶端評估時，EF Core 將會記錄警告。 如需檢視記錄輸出的詳細資訊，請參閱[記錄](../miscellaneous/logging.md)。 

## <a name="optional-behavior-throw-an-exception-for-client-evaluation"></a>選擇性行為：針對用戶端評估擲回例外狀況

您可以變更在用戶端評估發生而擲回或不執行任何動作時的行為。 這通常會在 `DbContext.OnConfiguring` 中為您的內容設定選項時完成，或者，如果您使用 ASP.NET Core，則是在 `Startup.cs` 中完成。

<!-- [!code-csharp[Main](samples/core/Querying/ClientEval/ThrowOnClientEval/BloggingContext.cs?highlight=5)] -->
``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFQuerying;Trusted_Connection=True;")
        .ConfigureWarnings(warnings => warnings.Throw(RelationalEventId.QueryClientEvaluationWarning));
}
```
