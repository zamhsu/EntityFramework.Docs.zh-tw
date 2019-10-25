---
title: 繼承（關係資料庫）-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9a7c5488-aaf4-4b40-b1ff-f435ff30f6ec
uid: core/modeling/relational/inheritance
ms.openlocfilehash: c660107619470a726fe13ad8eee2850749e6dcd9
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72812089"
---
# <a name="inheritance-relational-database"></a>繼承 (關聯式資料庫)

> [!NOTE]  
> 本節中的組態一般適用於關聯式資料庫。 當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。

EF 模型中的繼承是用來控制實體類別中的繼承在資料庫中的呈現方式。

> [!NOTE]  
> 目前，只有每個階層的資料表（TPH）模式會在 EF Core 中執行。 其他常見的模式（例如，每個類型的資料表（TPT）和每個具體的資料表類型（TPC））尚無法使用。

## <a name="conventions"></a>慣例

依照慣例，會使用每個階層的資料表（TPH）模式來對應繼承。 TPH 會使用單一資料表來儲存階層中所有類型的資料。 鑒別子資料行是用來識別每個資料列所代表的類型。

只有在模型中明確包含兩個或多個繼承類型時，EF Core 才會設定繼承（如需詳細資訊，請參閱[繼承](../inheritance.md)）。

以下範例顯示簡單的繼承案例，以及使用 TPH 模式儲存在關係資料庫資料表中的資料。 *鑒別*子資料行會識別每個資料列中所儲存的*Blog*類型。

<!-- [!code-csharp[Main](samples/core/relational/Modeling/Conventions/InheritanceDbSets.cs)] -->
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
> 使用 TPH 對應時，視需要自動將資料庫資料行設為可為 null。

## <a name="data-annotations"></a>資料註釋

您不能使用資料批註來設定繼承。

## <a name="fluent-api"></a>Fluent API

您可以使用流暢的 API 來設定鑒別子資料行的名稱和類型，以及用來識別階層中每個類型的值。

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/InheritanceTPHDiscriminator.cs?highlight=7,8,9,10)] -->
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

## <a name="configuring-the-discriminator-property"></a>設定鑒別子屬性

在上述範例中，會在階層的基底實體上建立鑒別子做為[陰影屬性](xref:core/modeling/shadow-properties)。 由於它是模型中的屬性，因此可以像其他屬性一樣進行設定。 例如，若要設定預設值時使用的最大長度（依慣例鑒別子）：

```C#
modelBuilder.Entity<Blog>()
    .Property("Discriminator")
    .HasMaxLength(200);
```

鑒別子也可以對應至實體中的實際 CLR 屬性。 例如:

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

將這兩個專案結合在一起，可以將鑒別子對應到 real 屬性並加以設定：

```C#
modelBuilder.Entity<Blog>(b =>
{
    b.HasDiscriminator<string>("BlogType");

    b.Property(e => e.BlogType)
        .HasMaxLength(200)
        .HasColumnName("blog_type");
});
```
