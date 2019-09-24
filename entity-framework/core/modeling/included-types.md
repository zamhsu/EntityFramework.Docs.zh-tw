---
title: 包括 & 排除類型-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: cbe6935e-2679-4b77-8914-a8d772240cf1
uid: core/modeling/included-types
ms.openlocfilehash: ca83b1c432bdf4853dba81e12ec4a739bc8218dc
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197383"
---
# <a name="including--excluding-types"></a>包含與排除類型

在模型中包含類型，表示 EF 具有該類型的中繼資料，並會嘗試從資料庫讀取及寫入實例。

## <a name="conventions"></a>慣例

依照慣例，在內容的`DbSet`屬性中公開的類型會包含在您的模型中。 此外，也包含`OnModelCreating`方法中所述的類型。 最後，以遞迴方式探索已探索類型的導覽屬性所找到的任何類型也會包含在模型中。

**例如，在下列程式碼中，會探索所有三種類型：**

* `Blog`因為它是在內容的`DbSet`屬性中公開

* `Post`因為它是透過`Blog.Posts`導覽屬性來探索的

* `AuditEntry`因為已在中提及`OnModelCreating`

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/IncludedTypes.cs?highlight=3,7,16)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<AuditEntry>();
    }
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

public class AuditEntry
{
    public int AuditEntryId { get; set; }
    public string Username { get; set; }
    public string Action { get; set; }
}
```

## <a name="data-annotations"></a>資料註釋

您可以使用資料批註，從模型中排除型別。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?highlight=20)]

## <a name="fluent-api"></a>Fluent API

您可以使用流暢的 API，從模型中排除型別。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?highlight=12)]
