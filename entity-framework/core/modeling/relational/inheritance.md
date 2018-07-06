---
title: 繼承 （關聯式資料庫） 的 EF Core
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
# <a name="inheritance-relational-database"></a><span data-ttu-id="18b9c-102">繼承 （關聯式資料庫）</span><span class="sxs-lookup"><span data-stu-id="18b9c-102">Inheritance (Relational Database)</span></span>

> [!NOTE]  
> <span data-ttu-id="18b9c-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="18b9c-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="18b9c-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="18b9c-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="18b9c-105">EF 模型中的繼承用來控制如何在實體類別中的繼承表示在資料庫中。</span><span class="sxs-lookup"><span data-stu-id="18b9c-105">Inheritance in the EF model is used to control how inheritance in the entity classes is represented in the database.</span></span>

> [!NOTE]  
> <span data-ttu-id="18b9c-106">目前，只有每個階層的資料表 (TPH) 模式中實作的 EF Core。</span><span class="sxs-lookup"><span data-stu-id="18b9c-106">Currently, only the table-per-hierarchy (TPH) pattern is implemented in EF Core.</span></span> <span data-ttu-id="18b9c-107">其他常見的模式資料表每個類型 (TPT) 等資料表的個別實體類型 (TPC) 尚無法使用。</span><span class="sxs-lookup"><span data-stu-id="18b9c-107">Other common patterns like table-per-type (TPT) and table-per-concrete-type (TPC) are not yet available.</span></span>

## <a name="conventions"></a><span data-ttu-id="18b9c-108">慣例</span><span class="sxs-lookup"><span data-stu-id="18b9c-108">Conventions</span></span>

<span data-ttu-id="18b9c-109">依照慣例，繼承將會使用每個階層資料表 (TPH) 模式對應。</span><span class="sxs-lookup"><span data-stu-id="18b9c-109">By convention, inheritance will be mapped using the table-per-hierarchy (TPH) pattern.</span></span> <span data-ttu-id="18b9c-110">TPH 使用單一資料表儲存在階層中所有類型的資料。</span><span class="sxs-lookup"><span data-stu-id="18b9c-110">TPH uses a single table to store the data for all types in the hierarchy.</span></span> <span data-ttu-id="18b9c-111">鑑別子資料行用來識別每個資料列都代表哪種類型。</span><span class="sxs-lookup"><span data-stu-id="18b9c-111">A discriminator column is used to identify which type each row represents.</span></span>

<span data-ttu-id="18b9c-112">EF Core 只會設定繼承，如果兩個或多個繼承的型別會明確地包含在模型中 (請參閱[繼承](../inheritance.md)如需詳細資訊)。</span><span class="sxs-lookup"><span data-stu-id="18b9c-112">EF Core will only setup inheritance if two or more inherited types are explicitly included in the model (see [Inheritance](../inheritance.md) for more details).</span></span>

<span data-ttu-id="18b9c-113">以下是範例，示範一個簡單的繼承案例及使用 TPH 模式關聯式資料庫資料表中儲存的資料。</span><span class="sxs-lookup"><span data-stu-id="18b9c-113">Below is an example showing a simple inheritance scenario and the data stored in a relational database table using the TPH pattern.</span></span> <span data-ttu-id="18b9c-114">*鑑別子*資料行會識別哪一種*部落格*會儲存在每個資料列。</span><span class="sxs-lookup"><span data-stu-id="18b9c-114">The *Discriminator* column identifies which type of *Blog* is stored in each row.</span></span>

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

## <a name="data-annotations"></a><span data-ttu-id="18b9c-116">資料註釋</span><span class="sxs-lookup"><span data-stu-id="18b9c-116">Data Annotations</span></span>

<span data-ttu-id="18b9c-117">若要設定繼承，您無法使用資料註解。</span><span class="sxs-lookup"><span data-stu-id="18b9c-117">You cannot use Data Annotations to configure inheritance.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="18b9c-118">Fluent API</span><span class="sxs-lookup"><span data-stu-id="18b9c-118">Fluent API</span></span>

<span data-ttu-id="18b9c-119">您可以使用 fluent 應用程式開發的應用程式開發介面來設定的名稱和類型鑑別子資料行以及用來識別階層中的每個類型的值。</span><span class="sxs-lookup"><span data-stu-id="18b9c-119">You can use the Fluent API to configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy.</span></span>

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

## <a name="configuring-the-discriminator-property"></a><span data-ttu-id="18b9c-120">設定鑑別子屬性</span><span class="sxs-lookup"><span data-stu-id="18b9c-120">Configuring the discriminator property</span></span>

<span data-ttu-id="18b9c-121">在上述範例中，鑑別子會建立為[陰影屬性](xref:core/modeling/shadow-properties)在階層的基底實體。</span><span class="sxs-lookup"><span data-stu-id="18b9c-121">In the examples above, the discriminator is created as a [shadow property](xref:core/modeling/shadow-properties) on the base entity of the hierarchy.</span></span> <span data-ttu-id="18b9c-122">因為這是模型中的屬性，它可以就像其他屬性設定。</span><span class="sxs-lookup"><span data-stu-id="18b9c-122">Since it is a property in the model, it can be configured just like other properties.</span></span> <span data-ttu-id="18b9c-123">例如，若要使用預設值，依慣例鑑別子而設定的最大長度：</span><span class="sxs-lookup"><span data-stu-id="18b9c-123">For example, to set the max length when the default, by-convention discriminator is being used:</span></span>

```C#
modelBuilder.Entity<Blog>()
    .Property("Discriminator")
    .HasMaxLength(200);
```

<span data-ttu-id="18b9c-124">鑑別子也可以對應至實際的 CLR 屬性實體中。</span><span class="sxs-lookup"><span data-stu-id="18b9c-124">The discriminator can also be mapped to an actual CLR property in your entity.</span></span> <span data-ttu-id="18b9c-125">例如: </span><span class="sxs-lookup"><span data-stu-id="18b9c-125">For example:</span></span>
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

<span data-ttu-id="18b9c-126">結合這兩個條件也可同時將鑑別子對應到實際屬性並加以設定：</span><span class="sxs-lookup"><span data-stu-id="18b9c-126">Combining these two things together it is possible to both map the discriminator to a real property and configure it:</span></span>
```C#
modelBuilder.Entity<Blog>(b =>
{
    b.HasDiscriminator<string>("BlogType");

    b.Property(e => e.BlogType)
        .HasMaxLength(200)
        .HasColumnName("blog_type");
});
```
