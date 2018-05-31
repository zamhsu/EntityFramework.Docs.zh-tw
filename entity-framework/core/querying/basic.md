---
title: 基本查詢 - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: ab6e35f1-397f-41c0-9ef4-85aec5466377
ms.technology: entity-framework-core
uid: core/querying/basic
ms.openlocfilehash: 5070faf2aeeffad680e24e7de5a0ffa03a8f0064
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052548"
---
# <a name="basic-queries"></a><span data-ttu-id="50c7d-102">基本查詢</span><span class="sxs-lookup"><span data-stu-id="50c7d-102">Basic Queries</span></span>

<span data-ttu-id="50c7d-103">了解如何使用 Language Integrated Query (LINQ) 從資料庫載入實體。</span><span class="sxs-lookup"><span data-stu-id="50c7d-103">Learn how to load entities from the database using Language Integrate Query (LINQ).</span></span>

> [!TIP]  
> <span data-ttu-id="50c7d-104">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="50c7d-104">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="101-linq-samples"></a><span data-ttu-id="50c7d-105">101 個 LINQ 範例</span><span class="sxs-lookup"><span data-stu-id="50c7d-105">101 LINQ samples</span></span>

<span data-ttu-id="50c7d-106">此頁面會顯示可使用 Entity Framework Core 來達成常見工作的一些範例。</span><span class="sxs-lookup"><span data-stu-id="50c7d-106">This page shows a few examples to achieve common tasks with Entity Framework Core.</span></span> <span data-ttu-id="50c7d-107">如需示範使用 LINQ 可能性的大量範例，請參閱 [101 個 LINQ 範例](https://code.msdn.microsoft.com/101-LINQ-Samples-3fb9811b) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="50c7d-107">For an extensive set of samples showing what is possible with LINQ, see [101 LINQ Samples](https://code.msdn.microsoft.com/101-LINQ-Samples-3fb9811b).</span></span>

## <a name="loading-all-data"></a><span data-ttu-id="50c7d-108">載入所有資料</span><span class="sxs-lookup"><span data-stu-id="50c7d-108">Loading all data</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Basics/Sample.cs)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.ToList();
}
```

## <a name="loading-a-single-entity"></a><span data-ttu-id="50c7d-109">載入單一實體</span><span class="sxs-lookup"><span data-stu-id="50c7d-109">Loading a single entity</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Basics/Sample.cs)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs
        .Single(b => b.BlogId == 1);
}
```

## <a name="filtering"></a><span data-ttu-id="50c7d-110">篩選</span><span class="sxs-lookup"><span data-stu-id="50c7d-110">Filtering</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Basics/Sample.cs)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs
        .Where(b => b.Url.Contains("dotnet"))
        .ToList();
}
```
