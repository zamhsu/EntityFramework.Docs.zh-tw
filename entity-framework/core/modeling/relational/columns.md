---
title: "資料行對應的 EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 05a47de9-1078-488e-a823-b516a4208f33
ms.technology: entity-framework-core
uid: core/modeling/relational/columns
ms.openlocfilehash: 697b966dbac892e332fe65feaa4dd11f00dd8298
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="column-mapping"></a><span data-ttu-id="483d8-102">資料行對應</span><span class="sxs-lookup"><span data-stu-id="483d8-102">Column Mapping</span></span>

> [!NOTE]  
> <span data-ttu-id="483d8-103">本節中的設定是一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="483d8-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="483d8-104">當您安裝的關聯式資料庫提供者，如下所示的擴充方法會變成可用 (因為共用*Microsoft.EntityFrameworkCore.Relational*封裝)。</span><span class="sxs-lookup"><span data-stu-id="483d8-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="483d8-105">資料行對應會識別應該要從查詢並儲存到資料庫中哪些資料行的資料。</span><span class="sxs-lookup"><span data-stu-id="483d8-105">Column mapping identifies which column data should be queried from and saved to in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="483d8-106">慣例</span><span class="sxs-lookup"><span data-stu-id="483d8-106">Conventions</span></span>

<span data-ttu-id="483d8-107">依照慣例，每個屬性會對應至具有相同名稱與屬性資料行的安裝程式。</span><span class="sxs-lookup"><span data-stu-id="483d8-107">By convention, each property will be setup to map to a column with the same name as the property.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="483d8-108">資料註釋</span><span class="sxs-lookup"><span data-stu-id="483d8-108">Data Annotations</span></span>

<span data-ttu-id="483d8-109">若要設定屬性所對應的資料行，您可以使用資料註解。</span><span class="sxs-lookup"><span data-stu-id="483d8-109">You can use Data Annotations to configure the column to which a property is mapped.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/DataAnnotations/Samples/Relational/Column.cs?highlight=3)] -->
``` csharp
public class Blog
{
    [Column("blog_id")]
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

## <a name="fluent-api"></a><span data-ttu-id="483d8-110">關於 fluent 應用程式開發介面</span><span class="sxs-lookup"><span data-stu-id="483d8-110">Fluent API</span></span>

<span data-ttu-id="483d8-111">您可以使用 fluent 應用程式開發的應用程式開發介面來設定屬性所對應的資料行。</span><span class="sxs-lookup"><span data-stu-id="483d8-111">You can use the Fluent API to configure the column to which a property is mapped.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/Column.cs?highlight=7,8,9)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .Property(b => b.BlogId)
            .HasColumnName("blog_id");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```
