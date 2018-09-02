---
title: 用戶端與伺服器評估 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 8b6697cc-7067-4dc2-8007-85d80503d123
uid: core/querying/client-eval
ms.openlocfilehash: 78f8d9576748a725634665f915def80b5a13820c
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997873"
---
# <a name="client-vs-server-evaluation"></a><span data-ttu-id="8f7c2-102">用戶端與伺服器評估</span><span class="sxs-lookup"><span data-stu-id="8f7c2-102">Client vs. Server Evaluation</span></span>

<span data-ttu-id="8f7c2-103">Entity Framework Core 支援要在用戶端評估的查詢組件，以及要發送到資料庫的查詢組件。</span><span class="sxs-lookup"><span data-stu-id="8f7c2-103">Entity Framework Core supports parts of the query being evaluated on the client and parts of it being pushed to the database.</span></span> <span data-ttu-id="8f7c2-104">由資料庫提供者決定將在資料庫中評估查詢的哪些組件。</span><span class="sxs-lookup"><span data-stu-id="8f7c2-104">It is up to the database provider to determine which parts of the query will be evaluated in the database.</span></span>

> [!TIP]  
> <span data-ttu-id="8f7c2-105">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="8f7c2-105">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="client-evaluation"></a><span data-ttu-id="8f7c2-106">用戶端評估</span><span class="sxs-lookup"><span data-stu-id="8f7c2-106">Client evaluation</span></span>

<span data-ttu-id="8f7c2-107">在下列範例中，會使用 Helper 方法來將從 SQL Server 資料庫所傳回部落格的 URL 標準化。</span><span class="sxs-lookup"><span data-stu-id="8f7c2-107">In the following example a helper method is used to standardize URLs for blogs that are returned from a SQL Server database.</span></span> <span data-ttu-id="8f7c2-108">由於 SQL Server 提供者未深入了解此方法的實作方式，因此，無法將它轉譯為 SQL。</span><span class="sxs-lookup"><span data-stu-id="8f7c2-108">Because the SQL Server provider has no insight into how this method is implemented, it is not possible to translate it into SQL.</span></span> <span data-ttu-id="8f7c2-109">查詢的所有其他層面均會在資料庫中進行評估，但透過此方法傳遞所傳回的 `URL` 則會在用戶端執行。</span><span class="sxs-lookup"><span data-stu-id="8f7c2-109">All other aspects of the query are evaluated in the database, but passing the returned `URL` through this method is performed on the client.</span></span>

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

## <a name="disabling-client-evaluation"></a><span data-ttu-id="8f7c2-110">停用用戶端評估</span><span class="sxs-lookup"><span data-stu-id="8f7c2-110">Disabling client evaluation</span></span>

<span data-ttu-id="8f7c2-111">雖然用戶端評估非常實用，但在某些情況下，可能會導致效能不佳。</span><span class="sxs-lookup"><span data-stu-id="8f7c2-111">While client evaluation can be very useful, in some instances it can result in poor performance.</span></span> <span data-ttu-id="8f7c2-112">請考慮下列查詢，此查詢目前會在篩選條件中使用 Helper 方法。</span><span class="sxs-lookup"><span data-stu-id="8f7c2-112">Consider the following query, where the helper method is now used in a filter.</span></span> <span data-ttu-id="8f7c2-113">因為這不能在資料庫中執行，所以會將所有資料提取到記憶體，然後在用戶端套用篩選條件。</span><span class="sxs-lookup"><span data-stu-id="8f7c2-113">Because this can't be performed in the database, all the data is pulled into memory and then the filter is applied on the client.</span></span> <span data-ttu-id="8f7c2-114">根據資料量以及要篩選出多少資料而定，這可能會導致效能不佳。</span><span class="sxs-lookup"><span data-stu-id="8f7c2-114">Depending on the amount of data, and how much of that data is filtered out, this could result in poor performance.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/ClientEval/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .Where(blog => StandardizeUrl(blog.Url).Contains("dotnet"))
    .ToList();
```

<span data-ttu-id="8f7c2-115">根據預設，執行用戶端評估時，EF Core 將會記錄警告。</span><span class="sxs-lookup"><span data-stu-id="8f7c2-115">By default, EF Core will log a warning when client evaluation is performed.</span></span> <span data-ttu-id="8f7c2-116">如需檢視記錄輸出的詳細資訊，請參閱[記錄](../miscellaneous/logging.md)。</span><span class="sxs-lookup"><span data-stu-id="8f7c2-116">See [Logging](../miscellaneous/logging.md) for more information on viewing logging output.</span></span> <span data-ttu-id="8f7c2-117">您可以變更在用戶端評估發生而擲回或不執行任何動作時的行為。</span><span class="sxs-lookup"><span data-stu-id="8f7c2-117">You can change the behavior when client evaluation occurs to either throw or do nothing.</span></span> <span data-ttu-id="8f7c2-118">這通常會在 `DbContext.OnConfiguring` 中為您的內容設定選項時完成，或者，如果您使用 ASP.NET Core，則是在 `Startup.cs` 中完成。</span><span class="sxs-lookup"><span data-stu-id="8f7c2-118">This is done when setting up the options for your context - typically in `DbContext.OnConfiguring`, or in `Startup.cs` if you are using ASP.NET Core.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/ClientEval/ThrowOnClientEval/BloggingContext.cs?highlight=5)] -->
``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFQuerying;Trusted_Connection=True;")
        .ConfigureWarnings(warnings => warnings.Throw(RelationalEventId.QueryClientEvaluationWarning));
}
```
