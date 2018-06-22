---
title: Foreign Key 條件約束的 EF 核心
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: dbaf4bac-1fd5-46c0-ac57-64d7153bc574
ms.technology: entity-framework-core
uid: core/modeling/relational/fk-constraints
ms.openlocfilehash: 726f03e2ee4cd3ec851c9a861b75dd12f9203e9c
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052738"
---
# <a name="foreign-key-constraints"></a><span data-ttu-id="ededc-102">外部索引鍵條件約束</span><span class="sxs-lookup"><span data-stu-id="ededc-102">Foreign Key Constraints</span></span>

> [!NOTE]  
> <span data-ttu-id="ededc-103">本節中的設定是一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="ededc-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="ededc-104">當您安裝的關聯式資料庫提供者，如下所示的擴充方法會變成可用 (因為共用*Microsoft.EntityFrameworkCore.Relational*封裝)。</span><span class="sxs-lookup"><span data-stu-id="ededc-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="ededc-105">針對每個關聯性模型中引進的外部索引鍵條件約束。</span><span class="sxs-lookup"><span data-stu-id="ededc-105">A foreign key constraint is introduced for each relationship in the model.</span></span>

## <a name="conventions"></a><span data-ttu-id="ededc-106">慣例</span><span class="sxs-lookup"><span data-stu-id="ededc-106">Conventions</span></span>

<span data-ttu-id="ededc-107">根據慣例，foreign key 條件約束會命名為`FK_<dependent type name>_<principal type name>_<foreign key property name>`。</span><span class="sxs-lookup"><span data-stu-id="ededc-107">By convention, foreign key constraints are named `FK_<dependent type name>_<principal type name>_<foreign key property name>`.</span></span> <span data-ttu-id="ededc-108">複合外部索引鍵`<foreign key property name>`變得的外部索引鍵屬性名稱的底線分隔清單。</span><span class="sxs-lookup"><span data-stu-id="ededc-108">For composite foreign keys `<foreign key property name>` becomes an underscore separated list of foreign key property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="ededc-109">資料註釋</span><span class="sxs-lookup"><span data-stu-id="ededc-109">Data Annotations</span></span>

<span data-ttu-id="ededc-110">無法使用資料註解來設定外部索引鍵條件約束名稱。</span><span class="sxs-lookup"><span data-stu-id="ededc-110">Foreign key constraint names cannot be configured using data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="ededc-111">關於 fluent 應用程式開發介面</span><span class="sxs-lookup"><span data-stu-id="ededc-111">Fluent API</span></span>

<span data-ttu-id="ededc-112">若要設定關聯性的外部索引鍵條件約束名稱，您可以使用 fluent 應用程式開發的應用程式開發介面。</span><span class="sxs-lookup"><span data-stu-id="ededc-112">You can use the Fluent API to configure the foreign key constraint name for a relationship.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/RelationshipConstraintName.cs?highlight=12)] -->
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
            .HasForeignKey(p => p.BlogId)
            .HasConstraintName("ForeignKey_Post_Blog");
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

    public int BlogId { get; set; }
    public Blog Blog { get; set; }
}
```
