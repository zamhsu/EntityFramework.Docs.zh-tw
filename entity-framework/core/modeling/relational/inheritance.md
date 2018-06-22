---
title: 繼承 （關聯式資料庫） 的 EF 核心
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 9a7c5488-aaf4-4b40-b1ff-f435ff30f6ec
ms.technology: entity-framework-core
uid: core/modeling/relational/inheritance
ms.openlocfilehash: 22eed0002b5903d3cfd18a7e4af0fcd2d46a5c4c
ms.sourcegitcommit: d2434edbfa6fbcee7287e33b4915033b796e417e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2018
ms.locfileid: "29152351"
---
# <a name="inheritance-relational-database"></a>繼承 （關聯式資料庫）

> [!NOTE]  
> 本節中的組態一般適用於關聯式資料庫。 當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。

EF 模型中的繼承用來控制如何在實體類別中的繼承表示在資料庫中。

> [!NOTE]  
> 目前，只有每個階層的資料表 (TPH) 模式中實作的 EF 核心。 其他常見的模式資料表每個類型 (TPT) 等資料表的個別實體類型 (TPC) 尚無法使用。

## <a name="conventions"></a>慣例

依照慣例，繼承將會使用每個階層資料表 (TPH) 模式對應。 TPH 使用單一資料表儲存在階層中所有類型的資料。 鑑別子資料行用來識別每個資料列都代表哪種類型。

EF 核心只會設定繼承，如果兩個或多個繼承的型別會明確地包含在模型中 (請參閱[繼承](../inheritance.md)如需詳細資訊)。

以下是範例，示範一個簡單的繼承案例及使用 TPH 模式關聯式資料庫資料表中儲存的資料。 *鑑別子*資料行會識別哪一種*部落格*會儲存在每個資料列。

<!-- [!code-csharp[Main](samples/core/relational/Modeling/Conventions/Samples/InheritanceDbSets.cs)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<RssBlog> RssBlogs { get; set; }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}

public class RssBlog : Blog
{
    public string RssUrl { get; set; }
}
```

![影像](_static/inheritance-tph-data.png)

## <a name="data-annotations"></a>資料註釋

若要設定繼承，您無法使用資料註解。

## <a name="fluent-api"></a>Fluent API

您可以使用 fluent 應用程式開發的應用程式開發介面來設定的名稱和類型鑑別子資料行以及用來識別階層中的每個類型的值。

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/InheritanceTPHDiscriminator.cs?highlight=7,8,9,10)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .HasDiscriminator<string>("blog_type")
            .HasValue<Blog>("blog_base")
            .HasValue<RssBlog>("blog_rss");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}

public class RssBlog : Blog
{
    public string RssUrl { get; set; }
}
```

## <a name="configuring-the-discriminator-property"></a>設定鑑別子屬性

在上述範例中，鑑別子會建立為[陰影屬性](xref:core/modeling/shadow-properties)在階層的基底實體。 因為這是模型中的屬性，它可以就像其他屬性設定。 例如，若要使用預設值，依慣例鑑別子而設定的最大長度：

```C#
modelBuilder.Entity<Blog>()
    .Property("Discriminator")
    .HasMaxLength(200);
```

鑑別子也可以對應至實際的 CLR 屬性實體中。 例如: 
```C#
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .HasDiscriminator<string>("BlogType");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
    public string BlogType { get; set; }
}

public class RssBlog : Blog
{
    public string RssUrl { get; set; }
}
```

結合這兩個條件也可同時將鑑別子對應到實際屬性並加以設定：
```C#
modelBuilder.Entity<Blog>(b =>
{
    b.HasDiscriminator<string>("BlogType");

    b.Property(e => e.BlogType)
        .HasMaxLength(200)
        .HasColumnName("blog_type");
});
```
