---
title: "順序-EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 94f81a92-3c72-4e14-912a-f99310374e42
ms.technology: entity-framework-core
uid: core/modeling/relational/sequences
ms.openlocfilehash: 98a40aeecbec0fd9fb9cc108d6b5f98178dea403
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="sequences"></a><span data-ttu-id="422d6-102">序列</span><span class="sxs-lookup"><span data-stu-id="422d6-102">Sequences</span></span>

> [!NOTE]  
> <span data-ttu-id="422d6-103">本節中的設定是一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="422d6-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="422d6-104">當您安裝的關聯式資料庫提供者，如下所示的擴充方法會變成可用 (因為共用*Microsoft.EntityFrameworkCore.Relational*封裝)。</span><span class="sxs-lookup"><span data-stu-id="422d6-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="422d6-105">序列會在資料庫中產生連續的數字值。</span><span class="sxs-lookup"><span data-stu-id="422d6-105">A sequence generates a sequential numeric values in the database.</span></span> <span data-ttu-id="422d6-106">順序不會與特定資料表相關聯。</span><span class="sxs-lookup"><span data-stu-id="422d6-106">Sequences are not associated with a specific table.</span></span>

## <a name="conventions"></a><span data-ttu-id="422d6-107">慣例</span><span class="sxs-lookup"><span data-stu-id="422d6-107">Conventions</span></span>

<span data-ttu-id="422d6-108">依照慣例，順序不會在模型中引入。</span><span class="sxs-lookup"><span data-stu-id="422d6-108">By convention, sequences are not introduced in to the model.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="422d6-109">資料註釋</span><span class="sxs-lookup"><span data-stu-id="422d6-109">Data Annotations</span></span>

<span data-ttu-id="422d6-110">您不可以設定使用資料註解的順序。</span><span class="sxs-lookup"><span data-stu-id="422d6-110">You can not configure a sequence using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="422d6-111">關於 fluent 應用程式開發介面</span><span class="sxs-lookup"><span data-stu-id="422d6-111">Fluent API</span></span>

<span data-ttu-id="422d6-112">您可以使用 Fluent API 模型中建立的順序。</span><span class="sxs-lookup"><span data-stu-id="422d6-112">You can use the Fluent API to create a sequence in the model.</span></span>

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

<span data-ttu-id="422d6-113">您也可以設定的順序，例如其結構描述、 開始值和遞增值的其他層面。</span><span class="sxs-lookup"><span data-stu-id="422d6-113">You can also configure additional aspect of the sequence, such as its schema, start value, and increment.</span></span>

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

<span data-ttu-id="422d6-114">序列導入之後，您可以使用它來產生模型中屬性的值。</span><span class="sxs-lookup"><span data-stu-id="422d6-114">Once a sequence is introduced, you can use it to generate values for properties in your model.</span></span> <span data-ttu-id="422d6-115">例如，您可以使用[Default Values](default-values.md)從序列中插入下一個值。</span><span class="sxs-lookup"><span data-stu-id="422d6-115">For example, you can use [Default Values](default-values.md) to insert the next value from the sequence.</span></span>

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
