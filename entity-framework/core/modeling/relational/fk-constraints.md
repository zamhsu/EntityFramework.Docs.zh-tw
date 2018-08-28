---
title: Foreign Key 條件約束的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: dbaf4bac-1fd5-46c0-ac57-64d7153bc574
uid: core/modeling/relational/fk-constraints
ms.openlocfilehash: a83f72b5d832e349fb4a5fb3b2de0b82bd79ef2a
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993984"
---
# <a name="foreign-key-constraints"></a><span data-ttu-id="a1ddb-102">Foreign Key 條件約束</span><span class="sxs-lookup"><span data-stu-id="a1ddb-102">Foreign Key Constraints</span></span>

> [!NOTE]  
> <span data-ttu-id="a1ddb-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="a1ddb-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="a1ddb-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="a1ddb-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="a1ddb-105">針對每個關聯性模型中引進的外部索引鍵條件約束。</span><span class="sxs-lookup"><span data-stu-id="a1ddb-105">A foreign key constraint is introduced for each relationship in the model.</span></span>

## <a name="conventions"></a><span data-ttu-id="a1ddb-106">慣例</span><span class="sxs-lookup"><span data-stu-id="a1ddb-106">Conventions</span></span>

<span data-ttu-id="a1ddb-107">依照慣例，foreign key 條件約束會命名為`FK_<dependent type name>_<principal type name>_<foreign key property name>`。</span><span class="sxs-lookup"><span data-stu-id="a1ddb-107">By convention, foreign key constraints are named `FK_<dependent type name>_<principal type name>_<foreign key property name>`.</span></span> <span data-ttu-id="a1ddb-108">複合外部索引鍵`<foreign key property name>`成為外部索引鍵屬性名稱的底線分隔清單。</span><span class="sxs-lookup"><span data-stu-id="a1ddb-108">For composite foreign keys `<foreign key property name>` becomes an underscore separated list of foreign key property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="a1ddb-109">資料註釋</span><span class="sxs-lookup"><span data-stu-id="a1ddb-109">Data Annotations</span></span>

<span data-ttu-id="a1ddb-110">無法使用資料註解來設定外部索引鍵條件約束名稱。</span><span class="sxs-lookup"><span data-stu-id="a1ddb-110">Foreign key constraint names cannot be configured using data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="a1ddb-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="a1ddb-111">Fluent API</span></span>

<span data-ttu-id="a1ddb-112">您可以使用 Fluent API 來設定關聯性的外部索引鍵條件約束名稱。</span><span class="sxs-lookup"><span data-stu-id="a1ddb-112">You can use the Fluent API to configure the foreign key constraint name for a relationship.</span></span>

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
