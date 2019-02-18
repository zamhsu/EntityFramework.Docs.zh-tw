---
title: 追蹤與不追蹤的查詢 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e17e060c-929f-4180-8883-40c438fbcc01
uid: core/querying/tracking
ms.openlocfilehash: 6c5d516fcb3950ae168860029660e1b1061546b8
ms.sourcegitcommit: 159c2e9afed7745e7512730ffffaf154bcf2ff4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/03/2019
ms.locfileid: "55668774"
---
# <a name="tracking-vs-no-tracking-queries"></a>追蹤與不追蹤的查詢

追蹤行為可控制 Entity Framework Core 是否將在其變更追蹤程式中保存有關實體執行個體的資訊。 如果追蹤實體，即會在 `SaveChanges()` 期間，將實體中偵測到的任何變更保存於資料庫。 Entity Framework Core 也將在從追蹤查詢中取得的實體，以及先前已載入至 DbContext 執行個體的實體之間修正導覽屬性。

> [!TIP]  
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) \(英文\)。

## <a name="tracking-queries"></a>追蹤查詢

預設會追蹤傳回實體類型的查詢。 這表示您可以對那些實體執行個體進行變更，並透過 `SaveChanges()` 來保存那些變更。

在下列範例中，將會偵測到對部落格評等的變更，並在 `SaveChanges()` 期間將其保存於資料庫。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Tracking/Sample.cs)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.SingleOrDefault(b => b.BlogId == 1);
    blog.Rating = 5;
    context.SaveChanges();
}
```

## <a name="no-tracking-queries"></a>無追蹤查詢

如果要在唯讀案例中使用結果，則不追蹤的查詢很實用。 它們會以更快的速度執行，因為不需要設定變更追蹤資訊。

您可以將個別查詢切換為不追蹤：

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Tracking/Sample.cs?highlight=4)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs
        .AsNoTracking()
        .ToList();
}
```

您也可以在內容執行個體層級變更預設的追蹤行為：

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Tracking/Sample.cs?highlight=3)] -->
``` csharp
using (var context = new BloggingContext())
{
    context.ChangeTracker.QueryTrackingBehavior = QueryTrackingBehavior.NoTracking;

    var blogs = context.Blogs.ToList();
}
```

> [!NOTE]  
> 不追蹤的查詢仍會在執行查詢內，進行識別解析。 如果結果集多次包含同一個實體，則將針對結果集中的每個相符項目傳回實體類別的相同執行個體。 不過，會使用弱式參考來持續追蹤已經傳回的實體。 如果先前具有相同身分識別的結果超出範圍，且執行記憶體回收，您可能會得到新的實體執行個體。 如需詳細資訊，請參閱[查詢的運作方式](overview.md)。

## <a name="tracking-and-projections"></a>追蹤和預測

即使查詢的結果類型不是實體類型，但若結果包含實體類型，預設仍將追蹤它們。 下列查詢會傳回匿名類型，並且將在結果集中追蹤 `Blog` 的執行個體。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Tracking/Sample.cs?highlight=7)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs
        .Select(b =>
            new
            {
                Blog = b,
                Posts = b.Posts.Count()
            });
}
```

如果結果集未包含任何實體類型，則不會執行追蹤。 下列查詢會傳回匿名類型，並具有一些來自實體的值 (但沒有實際實體類型的執行個體)，但不會執行任何追蹤。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Tracking/Sample.cs)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs
        .Select(b =>
            new
            {
                Id = b.BlogId,
                Url = b.Url
            });
}
```
