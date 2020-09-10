---
title: 無追蹤查詢-EF6
description: Entity Framework 6 中的無追蹤查詢
author: divega
ms.date: 10/23/2016
ms.assetid: f80ac260-c2dc-484d-94a3-3424fd862f8b
uid: ef6/querying/no-tracking
ms.openlocfilehash: eb9e29c219e0cdf1e379cf8bb925f4226b1434a9
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620297"
---
# <a name="no-tracking-queries"></a>不追蹤的查詢
有時您可能會想要從查詢中取回實體，但內容不會追蹤這些實體。 在唯讀案例中查詢大量實體時，這可能會導致更好的效能。 本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。  

新的擴充方法 AsNoTracking 讓任何查詢都能以這種方式執行。 例如：  

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
