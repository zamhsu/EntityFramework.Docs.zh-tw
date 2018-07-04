---
title: 替代索引鍵的 EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 8a5931d4-b480-4298-af36-0e29d74a37c0
ms.technology: entity-framework-core
uid: core/modeling/alternate-keys
ms.openlocfilehash: 09f86a8932b71ec8f30ee90a088091a00233c20f
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052468"
---
# <a name="alternate-keys"></a><span data-ttu-id="c97f4-102">替代索引鍵</span><span class="sxs-lookup"><span data-stu-id="c97f4-102">Alternate Keys</span></span>

<span data-ttu-id="c97f4-103">其他的索引鍵做為每個實體執行個體的主索引鍵除了替代唯一識別碼。</span><span class="sxs-lookup"><span data-stu-id="c97f4-103">An alternate key serves as an alternate unique identifier for each entity instance in addition to the primary key.</span></span> <span data-ttu-id="c97f4-104">替代索引鍵可用來當做目標的關聯性。</span><span class="sxs-lookup"><span data-stu-id="c97f4-104">Alternate keys can be used as the target of a relationship.</span></span> <span data-ttu-id="c97f4-105">使用關聯式資料庫時這會對應到唯一的索引/限制替代索引鍵資料行和一個或多個 foreign key 條件約束參考資料行上的概念。</span><span class="sxs-lookup"><span data-stu-id="c97f4-105">When using a relational database this maps to the concept of a unique index/constraint on the alternate key column(s) and one or more foreign key constraints that reference the column(s).</span></span>

> [!TIP]  
> <span data-ttu-id="c97f4-106">如果您只想要強制執行唯一性的資料行，則您需要唯一的索引，而不是替代索引鍵，請參閱[索引](indexes.md)。</span><span class="sxs-lookup"><span data-stu-id="c97f4-106">If you just want to enforce uniqueness of a column then you want a unique index rather than an alternate key, see [Indexes](indexes.md).</span></span> <span data-ttu-id="c97f4-107">在 EF，替代索引鍵會提供更強大的功能比唯一索引，因為它們可以用做為目標的外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="c97f4-107">In EF, alternate keys provide greater functionality than unique indexes because they can be used as the target of a foreign key.</span></span>

<span data-ttu-id="c97f4-108">替代索引鍵通常針對您在需要時採用的您不需要手動設定它們。</span><span class="sxs-lookup"><span data-stu-id="c97f4-108">Alternate keys are typically introduced for you when needed and you do not need to manually configure them.</span></span> <span data-ttu-id="c97f4-109">請參閱[慣例](#conventions)如需詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="c97f4-109">See [Conventions](#conventions) for more details.</span></span>

## <a name="conventions"></a><span data-ttu-id="c97f4-110">慣例</span><span class="sxs-lookup"><span data-stu-id="c97f4-110">Conventions</span></span>

<span data-ttu-id="c97f4-111">依照慣例，其他的索引鍵引進為您當您識別屬性，做為關聯性的目標不是主索引鍵。</span><span class="sxs-lookup"><span data-stu-id="c97f4-111">By convention, an alternate key is introduced for you when you identify a property, that is not the primary key, as the target of a relationship.</span></span>

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

## <a name="data-annotations"></a><span data-ttu-id="c97f4-112">資料註釋</span><span class="sxs-lookup"><span data-stu-id="c97f4-112">Data Annotations</span></span>

<span data-ttu-id="c97f4-113">替代索引鍵不可以使用資料註解來設定。</span><span class="sxs-lookup"><span data-stu-id="c97f4-113">Alternate keys can not be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="c97f4-114">關於 fluent 應用程式開發介面</span><span class="sxs-lookup"><span data-stu-id="c97f4-114">Fluent API</span></span>

<span data-ttu-id="c97f4-115">您可以使用 fluent 應用程式開發的應用程式開發介面來設定單一屬性是替代索引鍵。</span><span class="sxs-lookup"><span data-stu-id="c97f4-115">You can use the Fluent API to configure a single property to be an alternate key.</span></span>

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

<span data-ttu-id="c97f4-116">您也可以使用 fluent 應用程式開發的應用程式開發介面來設定多個屬性 （又稱為複合的替代索引鍵） 的替代金鑰。</span><span class="sxs-lookup"><span data-stu-id="c97f4-116">You can also use the Fluent API to configure multiple properties to be an alternate key (known as a composite alternate key).</span></span>

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
