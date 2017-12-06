---
title: "用戶端與伺服器評估-EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 8b6697cc-7067-4dc2-8007-85d80503d123
ms.technology: entity-framework-core
uid: core/querying/client-eval
ms.openlocfilehash: e1852b780041e9e92fb4d25129175346e3a601a3
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="client-vs-server-evaluation"></a>用戶端與伺服器評估

Entity Framework Core 支援用戶端和它發送到資料庫的組件上所評估的查詢部分。 這是由資料庫提供者，以判斷查詢的哪些部分會評估在資料庫中。

> [!TIP]  
> 您可以檢視這篇文章[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)GitHub 上。

## <a name="client-evaluation"></a>用戶端評估

在下列範例中的 helper 方法用來標準化從 SQL Server 資料庫所傳回的部落格的 Url。 SQL Server 提供者會不有任何深入了解如何實作這個方法，因為它不可能將它轉譯為 SQL。 評估查詢的所有其他部分會在資料庫中，但傳遞傳回`URL`透過這個方法不會對用戶端。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/ClientEval/Sample.cs?highlight=6)] -->
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

<!-- [!code-csharp[Main](samples/core/Querying/Querying/ClientEval/Sample.cs)] -->
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

## <a name="disabling-client-evaluation"></a>停用用戶端評估

雖然用戶端評估會非常實用，在某些情況下可能會導致效能不佳。 請考慮下列查詢中，現在在篩選中使用的 helper 方法。 這不能在資料庫中執行，因為所有資料提取到記憶體，然後篩選條件會套用至用戶端。 根據資料，以及多少資料都會被篩選掉的數量，這可能導致效能不佳。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/ClientEval/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .Where(blog => StandardizeUrl(blog.Url).Contains("dotnet"))
    .ToList();
```

根據預設，用戶端評估執行時 EF Core 時，會記錄警告。 請參閱[記錄](../miscellaneous/logging.md)檢視記錄輸出的詳細資訊。 用戶端評估擲回或不執行任何動作時，您可以變更此行為。 這是通常在設定為您的內容-選項時`DbContext.OnConfiguring`，或在`Startup.cs`如果您使用 ASP.NET Core。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/ClientEval/ThrowOnClientEval/BloggingContext.cs?highlight=5)] -->
``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFQuerying;Trusted_Connection=True;")
        .ConfigureWarnings(warnings => warnings.Throw(RelationalEventId.QueryClientEvaluationWarning));
}
```
