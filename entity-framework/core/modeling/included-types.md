---
title: 包含與排除類型-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: cbe6935e-2679-4b77-8914-a8d772240cf1
uid: core/modeling/included-types
ms.openlocfilehash: f533b24312af37634ce4957e43c39ce776bf0bf0
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929793"
---
# <a name="including--excluding-types"></a>包含與排除類型

納入模型表示 EF 相關的中繼資料的輸入，然後將嘗試讀取和寫入，或將資料庫的執行個體中的型別。

## <a name="conventions"></a>慣例

依照慣例，會在中公開的型別`DbSet`在您的內容上的屬性都包含在您的模型。 此外，型別中所述`OnModelCreating`方法也會包含在內。 最後，以遞迴方式瀏覽探索到的類型的導覽屬性，即可找到任何型別也會包含在模型中。

**例如，下列程式碼清單中會探索所有的三種類型：**

* `Blog` 因為它會公開在`DbSet`內容上的屬性

* `Post` 因為它透過探索`Blog.Posts`導覽屬性

* `AuditEntry` 因為它被提及中 `OnModelCreating`

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/IncludedTypes.cs?highlight=3,7,16)] -->
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

若要從模型中排除的型別，您可以使用資料註解。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/IgnoreType.cs?highlight=20)]

## <a name="fluent-api"></a>Fluent API

您可以使用 Fluent API，若要從模型中排除的型別。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/IgnoreType.cs?highlight=12)]
