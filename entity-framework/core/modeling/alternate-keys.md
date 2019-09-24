---
title: 替代金鑰-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 8a5931d4-b480-4298-af36-0e29d74a37c0
uid: core/modeling/alternate-keys
ms.openlocfilehash: 87df5d174a1db12fb3ab763ac76c3b863a83087e
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197468"
---
# <a name="alternate-keys"></a>替代索引鍵

除了主鍵以外，其他索引鍵可作為每個實體實例的替代唯一識別碼。 您可以使用替代索引鍵做為關聯性的目標。 當使用關係資料庫時，這會對應至替代索引鍵資料行上唯一索引/條件約束的概念，以及一個或多個參考資料行的 foreign key 條件約束。

> [!TIP]  
> 如果您只想要強制執行資料行的唯一性，則您需要唯一索引，而不是替代索引鍵，請參閱[索引](indexes.md)。 在 EF 中，其他索引鍵提供的功能比唯一索引更高，因為它們可以當做外鍵的目標使用。

您通常會在需要時為您引進替代金鑰，而不需要手動設定。 如需詳細資訊，請參閱[慣例](#conventions)。

## <a name="conventions"></a>慣例

依照慣例，當您將不是主鍵的屬性識別為關聯性的目標時，會為您引進替代索引鍵。

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/AlternateKey.cs?highlight=12)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>()
            .HasOne(p => p.Blog)
            .WithMany(b => b.Posts)
            .HasForeignKey(p => p.BlogUrl)
            .HasPrincipalKey(b => b.Url);
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

    public string BlogUrl { get; set; }
    public Blog Blog { get; set; }
}
```

## <a name="data-annotations"></a>資料註釋

無法使用資料批註來設定替代索引鍵。

## <a name="fluent-api"></a>Fluent API

您可以使用流暢的 API，將單一屬性設定為替代金鑰。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/AlternateKeySingle.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Car> Cars { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Car>()
            .HasAlternateKey(c => c.LicensePlate);
    }
}

class Car
{
    public int CarId { get; set; }
    public string LicensePlate { get; set; }
    public string Make { get; set; }
    public string Model { get; set; }
}
```

您也可以使用流暢的 API，將多個屬性設定為另一個索引鍵（也稱為複合的替代索引鍵）。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/AlternateKeyComposite.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Car> Cars { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Car>()
            .HasAlternateKey(c => new { c.State, c.LicensePlate });
    }
}

class Car
{
    public int CarId { get; set; }
    public string State { get; set; }
    public string LicensePlate { get; set; }
    public string Make { get; set; }
    public string Model { get; set; }
}
```
