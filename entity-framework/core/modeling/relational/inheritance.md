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
# <a name="inheritance-relational-database"></a><span data-ttu-id="ce9ee-102">繼承 （關聯式資料庫）</span><span class="sxs-lookup"><span data-stu-id="ce9ee-102">Inheritance (Relational Database)</span></span>

> [!NOTE]  
> <span data-ttu-id="ce9ee-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="ce9ee-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="ce9ee-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="ce9ee-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="ce9ee-105">EF 模型中的繼承用來控制如何將實體類別中的繼承表示在資料庫中。</span><span class="sxs-lookup"><span data-stu-id="ce9ee-105">Inheritance in the EF model is used to control how inheritance in the entity classes is represented in the database.</span></span>

> [!NOTE]  
> <span data-ttu-id="ce9ee-106">目前，只有每個階層的資料表 (TPH) 模式中實作的 EF Core。</span><span class="sxs-lookup"><span data-stu-id="ce9ee-106">Currently, only the table-per-hierarchy (TPH) pattern is implemented in EF Core.</span></span> <span data-ttu-id="ce9ee-107">其他常見的模式，例如，每一類一表 (TPT) 和每個實體-類一表 (TPC) 尚無法使用。</span><span class="sxs-lookup"><span data-stu-id="ce9ee-107">Other common patterns like table-per-type (TPT) and table-per-concrete-type (TPC) are not yet available.</span></span>

## <a name="conventions"></a><span data-ttu-id="ce9ee-108">慣例</span><span class="sxs-lookup"><span data-stu-id="ce9ee-108">Conventions</span></span>

<span data-ttu-id="ce9ee-109">依照慣例，將使用的每個階層的資料表 (TPH) 模式會對應繼承。</span><span class="sxs-lookup"><span data-stu-id="ce9ee-109">By convention, inheritance will be mapped using the table-per-hierarchy (TPH) pattern.</span></span> <span data-ttu-id="ce9ee-110">TPH 會使用單一的資料表，儲存在階層中的所有類型的資料。</span><span class="sxs-lookup"><span data-stu-id="ce9ee-110">TPH uses a single table to store the data for all types in the hierarchy.</span></span> <span data-ttu-id="ce9ee-111">鑑別子資料行用來識別每個資料列代表哪種類型。</span><span class="sxs-lookup"><span data-stu-id="ce9ee-111">A discriminator column is used to identify which type each row represents.</span></span>

<span data-ttu-id="ce9ee-112">EF Core 只會設定繼承，如果兩個或多個繼承的型別會明確地包含在模型中 (請參閱[繼承](../inheritance.md)如需詳細資訊)。</span><span class="sxs-lookup"><span data-stu-id="ce9ee-112">EF Core will only setup inheritance if two or more inherited types are explicitly included in the model (see [Inheritance](../inheritance.md) for more details).</span></span>

<span data-ttu-id="ce9ee-113">以下是範例顯示簡單的繼承案例，並儲存在關聯式資料庫資料表中使用 TPH 模式的資料。</span><span class="sxs-lookup"><span data-stu-id="ce9ee-113">Below is an example showing a simple inheritance scenario and the data stored in a relational database table using the TPH pattern.</span></span> <span data-ttu-id="ce9ee-114">*鑑別子*資料行會識別哪一種*部落格*會儲存在每個資料列。</span><span class="sxs-lookup"><span data-stu-id="ce9ee-114">The *Discriminator* column identifies which type of *Blog* is stored in each row.</span></span>

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
> <span data-ttu-id="ce9ee-116">使用 TPH 對應時，資料庫 colmmns 會自動進行視需要的可為 null。</span><span class="sxs-lookup"><span data-stu-id="ce9ee-116">Database colmmns are automatically made nullable as necessary when using TPH mapping.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="ce9ee-117">資料註釋</span><span class="sxs-lookup"><span data-stu-id="ce9ee-117">Data Annotations</span></span>

<span data-ttu-id="ce9ee-118">您無法使用資料註解來設定繼承。</span><span class="sxs-lookup"><span data-stu-id="ce9ee-118">You cannot use Data Annotations to configure inheritance.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="ce9ee-119">Fluent API</span><span class="sxs-lookup"><span data-stu-id="ce9ee-119">Fluent API</span></span>

<span data-ttu-id="ce9ee-120">您可以使用 Fluent API，若要設定的名稱和類型的鑑別子資料行以及用來識別階層中的每個類型的值。</span><span class="sxs-lookup"><span data-stu-id="ce9ee-120">You can use the Fluent API to configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy.</span></span>

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

## <a name="configuring-the-discriminator-property"></a><span data-ttu-id="ce9ee-121">設定鑑別子屬性</span><span class="sxs-lookup"><span data-stu-id="ce9ee-121">Configuring the discriminator property</span></span>

<span data-ttu-id="ce9ee-122">在上述範例中，做為建立鑑別子[陰影屬性](xref:core/modeling/shadow-properties)在階層的基底實體。</span><span class="sxs-lookup"><span data-stu-id="ce9ee-122">In the examples above, the discriminator is created as a [shadow property](xref:core/modeling/shadow-properties) on the base entity of the hierarchy.</span></span> <span data-ttu-id="ce9ee-123">因為它是模型中的屬性，可以設定其他屬性一樣。</span><span class="sxs-lookup"><span data-stu-id="ce9ee-123">Since it is a property in the model, it can be configured just like other properties.</span></span> <span data-ttu-id="ce9ee-124">例如，若要使用預設值，依慣例鑑別子時，設定最大長度：</span><span class="sxs-lookup"><span data-stu-id="ce9ee-124">For example, to set the max length when the default, by-convention discriminator is being used:</span></span>

```C#
modelBuilder.Entity<Blog>()
    .Property("Discriminator")
    .HasMaxLength(200);
```

<span data-ttu-id="ce9ee-125">鑑別子也可以對應至您的實體中的實際 CLR 屬性。</span><span class="sxs-lookup"><span data-stu-id="ce9ee-125">The discriminator can also be mapped to an actual CLR property in your entity.</span></span> <span data-ttu-id="ce9ee-126">例如: </span><span class="sxs-lookup"><span data-stu-id="ce9ee-126">For example:</span></span>
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

<span data-ttu-id="ce9ee-127">它結合這兩個條件，可以將鑑別子對應至一個真正的屬性，並設定它：</span><span class="sxs-lookup"><span data-stu-id="ce9ee-127">Combining these two things together it is possible to both map the discriminator to a real property and configure it:</span></span>
```C#
modelBuilder.Entity<Blog>(b =>
{
    b.HasDiscriminator<string>("BlogType");

    b.Property(e => e.BlogType)
        .HasMaxLength(200)
        .HasColumnName("blog_type");
});
```
