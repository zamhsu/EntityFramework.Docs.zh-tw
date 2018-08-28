---
title: 替代索引鍵的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 8a5931d4-b480-4298-af36-0e29d74a37c0
uid: core/modeling/alternate-keys
ms.openlocfilehash: b26d8bc1630af9e811d9c4e7da850a618bc8042e
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996967"
---
# <a name="alternate-keys"></a><span data-ttu-id="9beaa-102">替代索引鍵</span><span class="sxs-lookup"><span data-stu-id="9beaa-102">Alternate Keys</span></span>

<span data-ttu-id="9beaa-103">替代索引鍵做為替代的唯一識別項，除了的主索引鍵的每個實體執行個體。</span><span class="sxs-lookup"><span data-stu-id="9beaa-103">An alternate key serves as an alternate unique identifier for each entity instance in addition to the primary key.</span></span> <span data-ttu-id="9beaa-104">替代索引鍵可用來當做目標的關聯性。</span><span class="sxs-lookup"><span data-stu-id="9beaa-104">Alternate keys can be used as the target of a relationship.</span></span> <span data-ttu-id="9beaa-105">使用關聯式資料庫時這會對應至唯一索引/條件約束的替代索引鍵資料行和一個或多個 foreign key 條件約束參考資料行上的概念。</span><span class="sxs-lookup"><span data-stu-id="9beaa-105">When using a relational database this maps to the concept of a unique index/constraint on the alternate key column(s) and one or more foreign key constraints that reference the column(s).</span></span>

> [!TIP]  
> <span data-ttu-id="9beaa-106">如果您只想要強制執行唯一性的資料行，則您需要唯一的索引，而不是替代索引鍵，請參閱[索引](indexes.md)。</span><span class="sxs-lookup"><span data-stu-id="9beaa-106">If you just want to enforce uniqueness of a column then you want a unique index rather than an alternate key, see [Indexes](indexes.md).</span></span> <span data-ttu-id="9beaa-107">在 EF 中，替代索引鍵可提供更強大的功能比唯一索引，因為它們可以用做為外部索引鍵的目標。</span><span class="sxs-lookup"><span data-stu-id="9beaa-107">In EF, alternate keys provide greater functionality than unique indexes because they can be used as the target of a foreign key.</span></span>

<span data-ttu-id="9beaa-108">替代索引鍵通常會導入您需要時，您不需要手動加以設定。</span><span class="sxs-lookup"><span data-stu-id="9beaa-108">Alternate keys are typically introduced for you when needed and you do not need to manually configure them.</span></span> <span data-ttu-id="9beaa-109">請參閱[慣例](#conventions)如需詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="9beaa-109">See [Conventions](#conventions) for more details.</span></span>

## <a name="conventions"></a><span data-ttu-id="9beaa-110">慣例</span><span class="sxs-lookup"><span data-stu-id="9beaa-110">Conventions</span></span>

<span data-ttu-id="9beaa-111">依照慣例，替代索引鍵時引進了讓您識別屬性，做為關聯性的目標不是主索引鍵。</span><span class="sxs-lookup"><span data-stu-id="9beaa-111">By convention, an alternate key is introduced for you when you identify a property, that is not the primary key, as the target of a relationship.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/AlternateKey.cs?highlight=12)] -->
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

## <a name="data-annotations"></a><span data-ttu-id="9beaa-112">資料註釋</span><span class="sxs-lookup"><span data-stu-id="9beaa-112">Data Annotations</span></span>

<span data-ttu-id="9beaa-113">替代索引鍵不可以使用資料註解來設定。</span><span class="sxs-lookup"><span data-stu-id="9beaa-113">Alternate keys can not be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="9beaa-114">Fluent API</span><span class="sxs-lookup"><span data-stu-id="9beaa-114">Fluent API</span></span>

<span data-ttu-id="9beaa-115">您可以使用 Fluent API 來設定為替代索引鍵的單一屬性。</span><span class="sxs-lookup"><span data-stu-id="9beaa-115">You can use the Fluent API to configure a single property to be an alternate key.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/AlternateKeySingle.cs?highlight=7,8)] -->
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

<span data-ttu-id="9beaa-116">您也可以使用 Fluent API 來設定多個屬性 （又稱為複合的替代索引鍵） 為替代索引鍵。</span><span class="sxs-lookup"><span data-stu-id="9beaa-116">You can also use the Fluent API to configure multiple properties to be an alternate key (known as a composite alternate key).</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/AlternateKeyComposite.cs?highlight=7,8)] -->
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
