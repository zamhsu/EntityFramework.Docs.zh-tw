---
title: 遮蔽屬性的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
uid: core/modeling/shadow-properties
ms.openlocfilehash: b7b7b10642564dfa3dbc05755188b5b5c63e0d03
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993798"
---
# <a name="shadow-properties"></a>陰影屬性

陰影屬性是.NET 實體類別中未定義，但該 EF Core 模型中的實體類型定義的屬性。 值，這些屬性的狀態會保持單純變更追蹤程式。

不會公開對應的實體類型的資料庫中的資料時，陰影屬性相當實用。 它們通常用於外部索引鍵的屬性，其中兩個實體之間的關聯性由在資料庫中，外部索引鍵值，但使用實體類型之間的導覽屬性的實體類型上管理關聯性。

取得與透過變更陰影屬性值`ChangeTracker`API。

``` csharp
   context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

您可以透過 LINQ 查詢中參考遮蔽屬性`EF.Property`靜態方法。

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

## <a name="conventions"></a>慣例

可以依照慣例建立陰影屬性，探索到的關聯性，但沒有外部索引鍵屬性相依實體類別中找到。 在此情況下，將會介紹陰影的外部索引鍵屬性。 陰影的外部索引鍵屬性將會命名為`<navigation property name><principal key property name>`（相依的實體，指向主要實體，瀏覽用來命名）。 如果主體索引鍵屬性名稱包含導覽屬性的名稱，則只是名稱`<principal key property name>`。 如果相依的實體上沒有任何導覽屬性，則會使用主體的類型名稱在其位置。

例如，下列程式碼清單會導致`BlogId`陰影屬性來引進`Post`實體。

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/ShadowForeignKey.cs)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog { get; set; }
}
```

## <a name="data-annotations"></a>資料註釋

無法使用資料註解建立陰影屬性。

## <a name="fluent-api"></a>Fluent API

您可以使用 Fluent API，若要設定陰影屬性。 一旦您已呼叫的字串多載`Property`您也可以將任何就和其他屬性設定呼叫鏈結。

如果名稱提供給`Property`方法符合名稱的現有屬性 （陰影屬性或其中一個定義的實體類別上），然後將程式碼會設定該現有的屬性，而不是引進新的陰影屬性。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/ShadowProperty.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .Property<DateTime>("LastUpdated");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```
