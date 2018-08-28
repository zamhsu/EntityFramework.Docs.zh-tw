---
title: 無追蹤查詢-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: f80ac260-c2dc-484d-94a3-3424fd862f8b
ms.openlocfilehash: dba4127ade9481b40d4fd3c4323532ddfedf6980
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994236"
---
# <a name="no-tracking-queries"></a>不追蹤的查詢
有時候您可能想要從查詢取得實體，但不要讓這些內容所追蹤的實體。 查詢在唯讀案例中的實體數目很大時，這可能會導致更好的效能。 本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。  

新的擴充方法 AsNoTracking 可讓任何查詢都是以這種方式。 例如:   

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
