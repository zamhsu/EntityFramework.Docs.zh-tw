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
# <a name="client-vs-server-evaluation"></a><span data-ttu-id="69c15-102">用戶端與伺服器評估</span><span class="sxs-lookup"><span data-stu-id="69c15-102">Client vs. Server Evaluation</span></span>

<span data-ttu-id="69c15-103">Entity Framework Core 支援用戶端和它發送到資料庫的組件上所評估的查詢部分。</span><span class="sxs-lookup"><span data-stu-id="69c15-103">Entity Framework Core supports parts of the query being evaluated on the client and parts of it being pushed to the database.</span></span> <span data-ttu-id="69c15-104">這是由資料庫提供者，以判斷查詢的哪些部分會評估在資料庫中。</span><span class="sxs-lookup"><span data-stu-id="69c15-104">It is up to the database provider to determine which parts of the query will be evaluated in the database.</span></span>

> [!TIP]  
> <span data-ttu-id="69c15-105">您可以檢視這篇文章[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)GitHub 上。</span><span class="sxs-lookup"><span data-stu-id="69c15-105">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="client-evaluation"></a><span data-ttu-id="69c15-106">用戶端評估</span><span class="sxs-lookup"><span data-stu-id="69c15-106">Client evaluation</span></span>

<span data-ttu-id="69c15-107">在下列範例中的 helper 方法用來標準化從 SQL Server 資料庫所傳回的部落格的 Url。</span><span class="sxs-lookup"><span data-stu-id="69c15-107">In the following example a helper method is used to standardize URLs for blogs that are returned from a SQL Server database.</span></span> <span data-ttu-id="69c15-108">SQL Server 提供者會不有任何深入了解如何實作這個方法，因為它不可能將它轉譯為 SQL。</span><span class="sxs-lookup"><span data-stu-id="69c15-108">Because the SQL Server provider has no insight into how this method is implemented, it is not possible to translate it into SQL.</span></span> <span data-ttu-id="69c15-109">評估查詢的所有其他部分會在資料庫中，但傳遞傳回`URL`透過這個方法不會對用戶端。</span><span class="sxs-lookup"><span data-stu-id="69c15-109">All other aspects of the query are evaluated in the database, but passing the returned `URL` through this method is performed on the client.</span></span>

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

## <a name="disabling-client-evaluation"></a><span data-ttu-id="69c15-110">停用用戶端評估</span><span class="sxs-lookup"><span data-stu-id="69c15-110">Disabling client evaluation</span></span>

<span data-ttu-id="69c15-111">雖然用戶端評估會非常實用，在某些情況下可能會導致效能不佳。</span><span class="sxs-lookup"><span data-stu-id="69c15-111">While client evaluation can be very useful, in some instances it can result in poor performance.</span></span> <span data-ttu-id="69c15-112">請考慮下列查詢中，現在在篩選中使用的 helper 方法。</span><span class="sxs-lookup"><span data-stu-id="69c15-112">Consider the following query, where the helper method is now used in a filter.</span></span> <span data-ttu-id="69c15-113">這不能在資料庫中執行，因為所有資料提取到記憶體，然後篩選條件會套用至用戶端。</span><span class="sxs-lookup"><span data-stu-id="69c15-113">Because this can't be performed in the database, all the data is pulled into memory and then the filter is applied on the client.</span></span> <span data-ttu-id="69c15-114">根據資料，以及多少資料都會被篩選掉的數量，這可能導致效能不佳。</span><span class="sxs-lookup"><span data-stu-id="69c15-114">Depending on the amount of data, and how much of that data is filtered out, this could result in poor performance.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/ClientEval/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .Where(blog => StandardizeUrl(blog.Url).Contains("dotnet"))
    .ToList();
```

<span data-ttu-id="69c15-115">根據預設，用戶端評估執行時 EF Core 時，會記錄警告。</span><span class="sxs-lookup"><span data-stu-id="69c15-115">By default, EF Core will log a warning when client evaluation is performed.</span></span> <span data-ttu-id="69c15-116">請參閱[記錄](../miscellaneous/logging.md)檢視記錄輸出的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="69c15-116">See [Logging](../miscellaneous/logging.md) for more information on viewing logging output.</span></span> <span data-ttu-id="69c15-117">用戶端評估擲回或不執行任何動作時，您可以變更此行為。</span><span class="sxs-lookup"><span data-stu-id="69c15-117">You can change the behavior when client evaluation occurs to either throw or do nothing.</span></span> <span data-ttu-id="69c15-118">這是通常在設定為您的內容-選項時`DbContext.OnConfiguring`，或在`Startup.cs`如果您使用 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="69c15-118">This is done when setting up the options for your context - typically in `DbContext.OnConfiguring`, or in `Startup.cs` if you are using ASP.NET Core.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/ClientEval/ThrowOnClientEval/BloggingContext.cs?highlight=5)] -->
``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFQuerying;Trusted_Connection=True;")
        .ConfigureWarnings(warnings => warnings.Throw(RelationalEventId.QueryClientEvaluationWarning));
}
```
