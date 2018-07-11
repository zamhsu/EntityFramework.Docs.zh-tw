---
title: 資料行對應的 EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 05a47de9-1078-488e-a823-b516a4208f33
ms.technology: entity-framework-core
uid: core/modeling/relational/columns
ms.openlocfilehash: ac3ab2ce3faa54eb8e862d01dcecb48cb0d1f811
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37949407"
---
# <a name="column-mapping"></a><span data-ttu-id="d1d60-102">資料行對應</span><span class="sxs-lookup"><span data-stu-id="d1d60-102">Column Mapping</span></span>

> [!NOTE]  
> <span data-ttu-id="d1d60-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="d1d60-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="d1d60-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="d1d60-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="d1d60-105">資料行對應會識別哪一個資料行的資料應該從查詢，並儲存到資料庫中。</span><span class="sxs-lookup"><span data-stu-id="d1d60-105">Column mapping identifies which column data should be queried from and saved to in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="d1d60-106">慣例</span><span class="sxs-lookup"><span data-stu-id="d1d60-106">Conventions</span></span>

<span data-ttu-id="d1d60-107">依照慣例，請將對應至名稱與屬性相同的資料行設定的每個屬性。</span><span class="sxs-lookup"><span data-stu-id="d1d60-107">By convention, each property will be set up to map to a column with the same name as the property.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="d1d60-108">資料註釋</span><span class="sxs-lookup"><span data-stu-id="d1d60-108">Data Annotations</span></span>

<span data-ttu-id="d1d60-109">若要設定屬性所對應的資料行，您可以使用資料註解。</span><span class="sxs-lookup"><span data-stu-id="d1d60-109">You can use Data Annotations to configure the column to which a property is mapped.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/DataAnnotations/Samples/Relational/Column.cs?highlight=3)] -->
``` csharp
public class Blog
{
    [Column("blog_id")]
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

## <a name="fluent-api"></a><span data-ttu-id="d1d60-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="d1d60-110">Fluent API</span></span>

<span data-ttu-id="d1d60-111">您可以使用 Fluent API 來設定屬性所對應的資料行。</span><span class="sxs-lookup"><span data-stu-id="d1d60-111">You can use the Fluent API to configure the column to which a property is mapped.</span></span>

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
