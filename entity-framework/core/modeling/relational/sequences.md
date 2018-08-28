---
title: 順序-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 94f81a92-3c72-4e14-912a-f99310374e42
uid: core/modeling/relational/sequences
ms.openlocfilehash: eb9d9896966af0ad6b778047a1ed6af7358e8eb2
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994512"
---
# <a name="sequences"></a><span data-ttu-id="90468-102">序列</span><span class="sxs-lookup"><span data-stu-id="90468-102">Sequences</span></span>

> [!NOTE]  
> <span data-ttu-id="90468-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="90468-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="90468-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="90468-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="90468-105">序列會在資料庫中產生連續的數字值。</span><span class="sxs-lookup"><span data-stu-id="90468-105">A sequence generates a sequential numeric values in the database.</span></span> <span data-ttu-id="90468-106">順序不會與特定資料表相關聯。</span><span class="sxs-lookup"><span data-stu-id="90468-106">Sequences are not associated with a specific table.</span></span>

## <a name="conventions"></a><span data-ttu-id="90468-107">慣例</span><span class="sxs-lookup"><span data-stu-id="90468-107">Conventions</span></span>

<span data-ttu-id="90468-108">依照慣例，未導入序列中的模型。</span><span class="sxs-lookup"><span data-stu-id="90468-108">By convention, sequences are not introduced in to the model.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="90468-109">資料註釋</span><span class="sxs-lookup"><span data-stu-id="90468-109">Data Annotations</span></span>

<span data-ttu-id="90468-110">您可以設定使用資料註解的順序。</span><span class="sxs-lookup"><span data-stu-id="90468-110">You can not configure a sequence using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="90468-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="90468-111">Fluent API</span></span>

<span data-ttu-id="90468-112">您可以使用 Fluent API 模型中建立的順序。</span><span class="sxs-lookup"><span data-stu-id="90468-112">You can use the Fluent API to create a sequence in the model.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/Sequence.cs?highlight=7)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Order> Orders { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.HasSequence<int>("OrderNumbers");
    }
}

public class Order
{
    public int OrderId { get; set; }
    public int OrderNo { get; set; }
    public string Url { get; set; }
}
```

<span data-ttu-id="90468-113">您也可以設定的順序，例如結構描述、 開始值和遞增值的其他層面。</span><span class="sxs-lookup"><span data-stu-id="90468-113">You can also configure additional aspect of the sequence, such as its schema, start value, and increment.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/SequenceConfigured.cs?highlight=7,8,9)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Order> Orders { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.HasSequence<int>("OrderNumbers", schema: "shared")
            .StartsAt(1000)
            .IncrementsBy(5);
    }
}
```

<span data-ttu-id="90468-114">序列導入之後，您可以使用它來在模型中產生屬性的值。</span><span class="sxs-lookup"><span data-stu-id="90468-114">Once a sequence is introduced, you can use it to generate values for properties in your model.</span></span> <span data-ttu-id="90468-115">例如，您可以使用[預設值](default-values.md)插入序列中的 [下一步] 的值。</span><span class="sxs-lookup"><span data-stu-id="90468-115">For example, you can use [Default Values](default-values.md) to insert the next value from the sequence.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/SequenceUsed.cs?highlight=11,12,13)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Order> Orders { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.HasSequence<int>("OrderNumbers", schema: "shared")
            .StartsAt(1000)
            .IncrementsBy(5);

        modelBuilder.Entity<Order>()
            .Property(o => o.OrderNo)
            .HasDefaultValueSql("NEXT VALUE FOR shared.OrderNumbers");
    }
}

public class Order
{
    public int OrderId { get; set; }
    public int OrderNo { get; set; }
    public string Url { get; set; }
}
```
