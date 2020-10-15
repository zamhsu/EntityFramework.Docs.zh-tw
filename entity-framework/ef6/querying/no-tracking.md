---
title: No-Tracking 查詢-EF6
description: Entity Framework 6 中的 No-Tracking 查詢
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/querying/no-tracking
ms.openlocfilehash: 74826d3052cf6a249796db2845fa6b96e5ecb8f9
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065858"
---
# <a name="no-tracking-queries"></a><span data-ttu-id="c528d-103">不追蹤的查詢</span><span class="sxs-lookup"><span data-stu-id="c528d-103">No-Tracking Queries</span></span>
<span data-ttu-id="c528d-104">有時您可能會想要從查詢中取回實體，但內容不會追蹤這些實體。</span><span class="sxs-lookup"><span data-stu-id="c528d-104">Sometimes you may want to get entities back from a query but not have those entities be tracked by the context.</span></span> <span data-ttu-id="c528d-105">在唯讀案例中查詢大量實體時，這可能會導致更好的效能。</span><span class="sxs-lookup"><span data-stu-id="c528d-105">This may result in better performance when querying for large numbers of entities in read-only scenarios.</span></span> <span data-ttu-id="c528d-106">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="c528d-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="c528d-107">新的擴充方法 AsNoTracking 讓任何查詢都能以這種方式執行。</span><span class="sxs-lookup"><span data-stu-id="c528d-107">A new extension method AsNoTracking allows any query to be run in this way.</span></span> <span data-ttu-id="c528d-108">例如︰</span><span class="sxs-lookup"><span data-stu-id="c528d-108">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Query for all blogs without tracking them
    var blogs1 = context.Blogs.AsNoTracking();

    // Query for some blogs without tracking them
    var blogs2 = context.Blogs
                        .Where(b => b.Name.Contains(".NET"))
                        .AsNoTracking()
                        .ToList();
}
```  
