---
title: 無追蹤查詢-EF6
description: Entity Framework 6 中的無追蹤查詢
author: divega
ms.date: 10/23/2016
uid: ef6/querying/no-tracking
ms.openlocfilehash: ea4f05eb7a9b95fba55f70f249876bc9c5630f18
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073895"
---
# <a name="no-tracking-queries"></a><span data-ttu-id="abf38-103">不追蹤的查詢</span><span class="sxs-lookup"><span data-stu-id="abf38-103">No-Tracking Queries</span></span>
<span data-ttu-id="abf38-104">有時您可能會想要從查詢中取回實體，但內容不會追蹤這些實體。</span><span class="sxs-lookup"><span data-stu-id="abf38-104">Sometimes you may want to get entities back from a query but not have those entities be tracked by the context.</span></span> <span data-ttu-id="abf38-105">在唯讀案例中查詢大量實體時，這可能會導致更好的效能。</span><span class="sxs-lookup"><span data-stu-id="abf38-105">This may result in better performance when querying for large numbers of entities in read-only scenarios.</span></span> <span data-ttu-id="abf38-106">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="abf38-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="abf38-107">新的擴充方法 AsNoTracking 讓任何查詢都能以這種方式執行。</span><span class="sxs-lookup"><span data-stu-id="abf38-107">A new extension method AsNoTracking allows any query to be run in this way.</span></span> <span data-ttu-id="abf38-108">例如：</span><span class="sxs-lookup"><span data-stu-id="abf38-108">For example:</span></span>  

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
