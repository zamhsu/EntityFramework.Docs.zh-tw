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
# <a name="inheritance-relational-database"></a><span data-ttu-id="70392-102">繼承 (關聯式資料庫)</span><span class="sxs-lookup"><span data-stu-id="70392-102">Inheritance (Relational Database)</span></span>

> [!NOTE]  
> <span data-ttu-id="70392-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="70392-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="70392-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="70392-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="70392-105">EF 模型中的繼承是用來控制實體類別中的繼承在資料庫中的呈現方式。</span><span class="sxs-lookup"><span data-stu-id="70392-105">Inheritance in the EF model is used to control how inheritance in the entity classes is represented in the database.</span></span>

> [!NOTE]  
> <span data-ttu-id="70392-106">目前，只有每個階層的資料表（TPH）模式會在 EF Core 中執行。</span><span class="sxs-lookup"><span data-stu-id="70392-106">Currently, only the table-per-hierarchy (TPH) pattern is implemented in EF Core.</span></span> <span data-ttu-id="70392-107">其他常見的模式（例如，每個類型的資料表（TPT）和每個具體的資料表類型（TPC））尚無法使用。</span><span class="sxs-lookup"><span data-stu-id="70392-107">Other common patterns like table-per-type (TPT) and table-per-concrete-type (TPC) are not yet available.</span></span>

## <a name="conventions"></a><span data-ttu-id="70392-108">慣例</span><span class="sxs-lookup"><span data-stu-id="70392-108">Conventions</span></span>

<span data-ttu-id="70392-109">依照慣例，會使用每個階層的資料表（TPH）模式來對應繼承。</span><span class="sxs-lookup"><span data-stu-id="70392-109">By convention, inheritance will be mapped using the table-per-hierarchy (TPH) pattern.</span></span> <span data-ttu-id="70392-110">TPH 會使用單一資料表來儲存階層中所有類型的資料。</span><span class="sxs-lookup"><span data-stu-id="70392-110">TPH uses a single table to store the data for all types in the hierarchy.</span></span> <span data-ttu-id="70392-111">鑒別子資料行是用來識別每個資料列所代表的類型。</span><span class="sxs-lookup"><span data-stu-id="70392-111">A discriminator column is used to identify which type each row represents.</span></span>

<span data-ttu-id="70392-112">只有在模型中明確包含兩個或多個繼承類型時，EF Core 才會設定繼承（如需詳細資訊，請參閱[繼承](../inheritance.md)）。</span><span class="sxs-lookup"><span data-stu-id="70392-112">EF Core will only setup inheritance if two or more inherited types are explicitly included in the model (see [Inheritance](../inheritance.md) for more details).</span></span>

<span data-ttu-id="70392-113">以下範例顯示簡單的繼承案例，以及使用 TPH 模式儲存在關係資料庫資料表中的資料。</span><span class="sxs-lookup"><span data-stu-id="70392-113">Below is an example showing a simple inheritance scenario and the data stored in a relational database table using the TPH pattern.</span></span> <span data-ttu-id="70392-114">*鑒別*子資料行會識別每個資料列中所儲存的*Blog*類型。</span><span class="sxs-lookup"><span data-stu-id="70392-114">The *Discriminator* column identifies which type of *Blog* is stored in each row.</span></span>

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
> <span data-ttu-id="70392-116">使用 TPH 對應時，視需要自動將資料庫資料行設為可為 null。</span><span class="sxs-lookup"><span data-stu-id="70392-116">Database columns are automatically made nullable as necessary when using TPH mapping.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="70392-117">資料註釋</span><span class="sxs-lookup"><span data-stu-id="70392-117">Data Annotations</span></span>

<span data-ttu-id="70392-118">您不能使用資料批註來設定繼承。</span><span class="sxs-lookup"><span data-stu-id="70392-118">You cannot use Data Annotations to configure inheritance.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="70392-119">Fluent API</span><span class="sxs-lookup"><span data-stu-id="70392-119">Fluent API</span></span>

<span data-ttu-id="70392-120">您可以使用流暢的 API 來設定鑒別子資料行的名稱和類型，以及用來識別階層中每個類型的值。</span><span class="sxs-lookup"><span data-stu-id="70392-120">You can use the Fluent API to configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy.</span></span>

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

## <a name="configuring-the-discriminator-property"></a><span data-ttu-id="70392-121">設定鑒別子屬性</span><span class="sxs-lookup"><span data-stu-id="70392-121">Configuring the discriminator property</span></span>

<span data-ttu-id="70392-122">在上述範例中，會在階層的基底實體上建立鑒別子做為[陰影屬性](xref:core/modeling/shadow-properties)。</span><span class="sxs-lookup"><span data-stu-id="70392-122">In the examples above, the discriminator is created as a [shadow property](xref:core/modeling/shadow-properties) on the base entity of the hierarchy.</span></span> <span data-ttu-id="70392-123">由於它是模型中的屬性，因此可以像其他屬性一樣進行設定。</span><span class="sxs-lookup"><span data-stu-id="70392-123">Since it is a property in the model, it can be configured just like other properties.</span></span> <span data-ttu-id="70392-124">例如，若要設定預設值時使用的最大長度（依慣例鑒別子）：</span><span class="sxs-lookup"><span data-stu-id="70392-124">For example, to set the max length when the default, by-convention discriminator is being used:</span></span>

```C#
modelBuilder.Entity<Blog>()
    .Property("Discriminator")
    .HasMaxLength(200);
```

<span data-ttu-id="70392-125">鑒別子也可以對應至實體中的實際 CLR 屬性。</span><span class="sxs-lookup"><span data-stu-id="70392-125">The discriminator can also be mapped to an actual CLR property in your entity.</span></span> <span data-ttu-id="70392-126">例如:</span><span class="sxs-lookup"><span data-stu-id="70392-126">For example:</span></span>

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

<span data-ttu-id="70392-127">將這兩個專案結合在一起，可以將鑒別子對應到 real 屬性並加以設定：</span><span class="sxs-lookup"><span data-stu-id="70392-127">Combining these two things together it is possible to both map the discriminator to a real property and configure it:</span></span>

```C#
modelBuilder.Entity<Blog>(b =>
{
    b.HasDiscriminator<string>("BlogType");

    b.Property(e => e.BlogType)
        .HasMaxLength(200)
        .HasColumnName("blog_type");
});
```
