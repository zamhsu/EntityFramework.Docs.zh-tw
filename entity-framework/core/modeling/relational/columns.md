---
title: 資料行對應的 EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 05a47de9-1078-488e-a823-b516a4208f33
ms.technology: entity-framework-core
uid: core/modeling/relational/columns
ms.openlocfilehash: 697b966dbac892e332fe65feaa4dd11f00dd8298
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052898"
---
# <a name="column-mapping"></a>資料行對應

> [!NOTE]  
> 本節中的設定是一般適用於關聯式資料庫。 當您安裝的關聯式資料庫提供者，如下所示的擴充方法會變成可用 (因為共用*Microsoft.EntityFrameworkCore.Relational*封裝)。

資料行對應會識別應該要從查詢並儲存到資料庫中哪些資料行的資料。

## <a name="conventions"></a>慣例

依照慣例，每個屬性會對應至具有相同名稱與屬性資料行的安裝程式。

## <a name="data-annotations"></a>資料註釋

若要設定屬性所對應的資料行，您可以使用資料註解。

<!-- [!code-csharp[Main](samples/core/relational/Modeling/DataAnnotations/Samples/Relational/Column.cs?highlight=3)] -->
``` csharp
public class Blog
{
    [Column("blog_id")]
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

## <a name="fluent-api"></a>關於 fluent 應用程式開發介面

您可以使用 fluent 應用程式開發的應用程式開發介面來設定屬性所對應的資料行。

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/Column.cs?highlight=7,8,9)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .Property(b => b.BlogId)
            .HasColumnName("blog_id");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```
