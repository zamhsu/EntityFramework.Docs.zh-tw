---
title: 外鍵條件約束-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: dbaf4bac-1fd5-46c0-ac57-64d7153bc574
uid: core/modeling/relational/fk-constraints
ms.openlocfilehash: d7ed4466f4df9ec01267b048ba1bbcc6e8bbdad5
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197064"
---
# <a name="foreign-key-constraints"></a><span data-ttu-id="808c7-102">外部索引鍵條件約束</span><span class="sxs-lookup"><span data-stu-id="808c7-102">Foreign Key Constraints</span></span>

> [!NOTE]  
> <span data-ttu-id="808c7-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="808c7-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="808c7-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="808c7-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="808c7-105">針對模型中的每個關聯性引進外鍵條件約束。</span><span class="sxs-lookup"><span data-stu-id="808c7-105">A foreign key constraint is introduced for each relationship in the model.</span></span>

## <a name="conventions"></a><span data-ttu-id="808c7-106">慣例</span><span class="sxs-lookup"><span data-stu-id="808c7-106">Conventions</span></span>

<span data-ttu-id="808c7-107">依照慣例，外鍵條件約束的`FK_<dependent type name>_<principal type name>_<foreign key property name>`名稱為。</span><span class="sxs-lookup"><span data-stu-id="808c7-107">By convention, foreign key constraints are named `FK_<dependent type name>_<principal type name>_<foreign key property name>`.</span></span> <span data-ttu-id="808c7-108">對於複合外鍵`<foreign key property name>` ，會變成以底線分隔的外鍵屬性名稱清單。</span><span class="sxs-lookup"><span data-stu-id="808c7-108">For composite foreign keys `<foreign key property name>` becomes an underscore separated list of foreign key property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="808c7-109">資料註釋</span><span class="sxs-lookup"><span data-stu-id="808c7-109">Data Annotations</span></span>

<span data-ttu-id="808c7-110">外鍵條件約束名稱無法使用資料批註來設定。</span><span class="sxs-lookup"><span data-stu-id="808c7-110">Foreign key constraint names cannot be configured using data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="808c7-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="808c7-111">Fluent API</span></span>

<span data-ttu-id="808c7-112">您可以使用流暢的 API 來設定關聯性的外鍵條件約束名稱。</span><span class="sxs-lookup"><span data-stu-id="808c7-112">You can use the Fluent API to configure the foreign key constraint name for a relationship.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Relational/RelationshipConstraintName.cs?highlight=12)] -->
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
