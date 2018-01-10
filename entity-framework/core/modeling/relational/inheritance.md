---
title: "繼承 （關聯式資料庫） 的 EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 9a7c5488-aaf4-4b40-b1ff-f435ff30f6ec
ms.technology: entity-framework-core
uid: core/modeling/relational/inheritance
ms.openlocfilehash: 55286adf08a6a1c3286b7059d747a62e1feffd22
ms.sourcegitcommit: ced2637bf8cc5964c6daa6c7fcfce501bf9ef6e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2017
---
# <a name="inheritance-relational-database"></a><span data-ttu-id="7aca5-102">繼承 （關聯式資料庫）</span><span class="sxs-lookup"><span data-stu-id="7aca5-102">Inheritance (Relational Database)</span></span>

> [!NOTE]  
> <span data-ttu-id="7aca5-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="7aca5-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="7aca5-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="7aca5-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="7aca5-105">EF 模型中的繼承用來控制如何在實體類別中的繼承表示在資料庫中。</span><span class="sxs-lookup"><span data-stu-id="7aca5-105">Inheritance in the EF model is used to control how inheritance in the entity classes is represented in the database.</span></span>

> [!NOTE]  
> <span data-ttu-id="7aca5-106">目前，只有每個階層的資料表 (TPH) 模式中實作的 EF 核心。</span><span class="sxs-lookup"><span data-stu-id="7aca5-106">Currently, only the table-per-hierarchy (TPH) pattern is implemented in EF Core.</span></span> <span data-ttu-id="7aca5-107">其他常見的模式資料表每個類型 (TPT) 等資料表的個別實體類型 (TPC) 尚無法使用。</span><span class="sxs-lookup"><span data-stu-id="7aca5-107">Other common patterns like table-per-type (TPT) and table-per-concrete-type (TPC) are not yet available.</span></span>

## <a name="conventions"></a><span data-ttu-id="7aca5-108">慣例</span><span class="sxs-lookup"><span data-stu-id="7aca5-108">Conventions</span></span>

<span data-ttu-id="7aca5-109">依照慣例，繼承將會使用每個階層資料表 (TPH) 模式對應。</span><span class="sxs-lookup"><span data-stu-id="7aca5-109">By convention, inheritance will be mapped using the table-per-hierarchy (TPH) pattern.</span></span> <span data-ttu-id="7aca5-110">TPH 使用單一資料表儲存在階層中所有類型的資料。</span><span class="sxs-lookup"><span data-stu-id="7aca5-110">TPH uses a single table to store the data for all types in the hierarchy.</span></span> <span data-ttu-id="7aca5-111">鑑別子資料行用來識別每個資料列都代表哪種類型。</span><span class="sxs-lookup"><span data-stu-id="7aca5-111">A discriminator column is used to identify which type each row represents.</span></span>

<span data-ttu-id="7aca5-112">EF 核心只會設定繼承，如果兩個或多個繼承的型別會明確地包含在模型中 (請參閱[繼承](../inheritance.md)如需詳細資訊)。</span><span class="sxs-lookup"><span data-stu-id="7aca5-112">EF Core will only setup inheritance if two or more inherited types are explicitly included in the model (see [Inheritance](../inheritance.md) for more details).</span></span>

<span data-ttu-id="7aca5-113">以下是範例，示範一個簡單的繼承案例及使用 TPH 模式關聯式資料庫資料表中儲存的資料。</span><span class="sxs-lookup"><span data-stu-id="7aca5-113">Below is an example showing a simple inheritance scenario and the data stored in a relational database table using the TPH pattern.</span></span> <span data-ttu-id="7aca5-114">*鑑別子*資料行會識別哪一種*部落格*會儲存在每個資料列。</span><span class="sxs-lookup"><span data-stu-id="7aca5-114">The *Discriminator* column identifies which type of *Blog* is stored in each row.</span></span>

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

## <a name="data-annotations"></a><span data-ttu-id="7aca5-116">資料註釋</span><span class="sxs-lookup"><span data-stu-id="7aca5-116">Data Annotations</span></span>

<span data-ttu-id="7aca5-117">若要設定繼承，您無法使用資料註解。</span><span class="sxs-lookup"><span data-stu-id="7aca5-117">You cannot use Data Annotations to configure inheritance.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="7aca5-118">Fluent API</span><span class="sxs-lookup"><span data-stu-id="7aca5-118">Fluent API</span></span>

<span data-ttu-id="7aca5-119">您可以使用 fluent 應用程式開發的應用程式開發介面來設定的名稱和類型鑑別子資料行以及用來識別階層中的每個類型的值。</span><span class="sxs-lookup"><span data-stu-id="7aca5-119">You can use the Fluent API to configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy.</span></span>

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
