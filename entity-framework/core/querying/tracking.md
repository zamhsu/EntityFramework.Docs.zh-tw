---
title: "追蹤 vs。不是追蹤查詢的 EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: e17e060c-929f-4180-8883-40c438fbcc01
ms.technology: entity-framework-core
uid: core/querying/tracking
ms.openlocfilehash: 9a22c893f3b1e9991560e25e0252287a2844b39e
ms.sourcegitcommit: 3b6159db8a6c0653f13c7b528367b4e69ac3d51e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="tracking-vs-no-tracking-queries"></a>追蹤 vs。不追蹤查詢

Entity Framework Core 會保留在其變更追蹤程式中的實體執行個體的相關資訊，請追蹤行為的控制項。 如果在追蹤實體，實體中偵測到的任何變更將會保留在資料庫`SaveChanges()`。 Entity Framework Core 將也 v-table 修正導覽屬性取得追蹤查詢的實體與先前已載入至 DbContext 執行個體的實體。

> [!TIP]  
> 您可以檢視這篇文章[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)GitHub 上。

## <a name="tracking-queries"></a>追蹤查詢

根據預設，追蹤的查詢來傳回實體類型。 這表示您可以對這些實體執行個體中的變更和這些變更已保存`SaveChanges()`。

在下列範例中，部落格評等的變更會被偵測到並保存到資料庫期間`SaveChanges()`。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Tracking/Sample.cs)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.SingleOrDefault(b => b.BlogId == 1);
    blog.Rating = 5;
    context.SaveChanges();
}
```

## <a name="no-tracking-queries"></a>不追蹤查詢

結果會以唯讀狀態的案例時，則沒有追蹤的查詢會很有用。 它們是更快速地執行，因為不需要安裝程式變更追蹤資訊。

您可以交換是沒有追蹤的個別查詢：

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Tracking/Sample.cs?highlight=4)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs
        .AsNoTracking()
        .ToList();
}
```

您也可以變更預設的追蹤內容執行個體層級的行為：

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Tracking/Sample.cs?highlight=3)] -->
``` csharp
using (var context = new BloggingContext())
{
    context.ChangeTracker.QueryTrackingBehavior = QueryTrackingBehavior.NoTracking;

    var blogs = context.Blogs.ToList();
}
```

> [!NOTE]  
> 沒有追蹤的查詢仍會執行識別解析內執行查詢。 如果結果集包含同一個實體多次，相同的實體類別的執行個體將會傳回結果集中的每個相符項目。 不過，弱式參考可用來追蹤已傳回的實體。 如果具有相同識別是之前的結果超出範圍，而且執行記憶體回收，可能會收到新的實體執行個體。 如需詳細資訊，請參閱[查詢的運作方式](overview.md)。

## <a name="tracking-and-projections"></a>追蹤和預測

即使查詢的結果型別不是實體類型，如果結果包含實體類型仍被追蹤的預設。 在下列查詢中，它會傳回匿名類型的執行個體`Blog`組將追蹤結果中。

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

如果結果集未包含任何實體類型，則會不執行任何追蹤。 在下列查詢中，它會傳回匿名型別有某些實體 （但實際的實體類型的任何執行個體） 的值，就不追蹤執行。

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
