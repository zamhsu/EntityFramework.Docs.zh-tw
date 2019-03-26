---
title: 繼承 （關聯式資料庫） 的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9a7c5488-aaf4-4b40-b1ff-f435ff30f6ec
uid: core/modeling/relational/inheritance
ms.openlocfilehash: 2aaceb05bbc1b0eb5c116b3dc1fb33c90c115a70
ms.sourcegitcommit: 645785187ae23ddf7d7b0642c7a4da5ffb0c7f30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2019
ms.locfileid: "58419675"
---
# <a name="inheritance-relational-database"></a>繼承 （關聯式資料庫）

> [!NOTE]  
> 本節中的組態一般適用於關聯式資料庫。 當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。

EF 模型中的繼承用來控制如何將實體類別中的繼承表示在資料庫中。

> [!NOTE]  
> 目前，只有每個階層的資料表 (TPH) 模式中實作的 EF Core。 其他常見的模式，例如，每一類一表 (TPT) 和每個實體-類一表 (TPC) 尚無法使用。

## <a name="conventions"></a>慣例

依照慣例，將使用的每個階層的資料表 (TPH) 模式會對應繼承。 TPH 會使用單一的資料表，儲存在階層中的所有類型的資料。 鑑別子資料行用來識別每個資料列代表哪種類型。

EF Core 只會設定繼承，如果兩個或多個繼承的型別會明確地包含在模型中 (請參閱[繼承](../inheritance.md)如需詳細資訊)。

以下是範例顯示簡單的繼承案例，並儲存在關聯式資料庫資料表中使用 TPH 模式的資料。 *鑑別子*資料行會識別哪一種*部落格*會儲存在每個資料列。

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

>[!NOTE]
> 使用 TPH 對應時，資料庫 colmmns 會自動進行視需要的可為 null。

## <a name="data-annotations"></a>資料註釋

您無法使用資料註解來設定繼承。

## <a name="fluent-api"></a>Fluent API

您可以使用 Fluent API，若要設定的名稱和類型的鑑別子資料行以及用來識別階層中的每個類型的值。

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

在上述範例中，做為建立鑑別子[陰影屬性](xref:core/modeling/shadow-properties)在階層的基底實體。 因為它是模型中的屬性，可以設定其他屬性一樣。 例如，若要使用預設值，依慣例鑑別子時，設定最大長度：

```C#
modelBuilder.Entity<Blog>()
    .Property("Discriminator")
    .HasMaxLength(200);
```

鑑別子也可以對應至您的實體中的實際 CLR 屬性。 例如: 
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

它結合這兩個條件，可以將鑑別子對應至一個真正的屬性，並設定它：
```C#
modelBuilder.Entity<Blog>(b =>
{
    b.HasDiscriminator<string>("BlogType");

    b.Property(e => e.BlogType)
        .HasMaxLength(200)
        .HasColumnName("blog_type");
});
```
