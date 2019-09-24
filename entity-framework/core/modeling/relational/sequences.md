---
title: 序列-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 94f81a92-3c72-4e14-912a-f99310374e42
uid: core/modeling/relational/sequences
ms.openlocfilehash: ce02b9840e58102a60c1d8eacf6810365104d7d7
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71196916"
---
# <a name="sequences"></a><span data-ttu-id="8492a-102">序列</span><span class="sxs-lookup"><span data-stu-id="8492a-102">Sequences</span></span>

> [!NOTE]  
> <span data-ttu-id="8492a-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="8492a-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="8492a-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="8492a-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="8492a-105">序列會在資料庫中產生連續的數值。</span><span class="sxs-lookup"><span data-stu-id="8492a-105">A sequence generates a sequential numeric values in the database.</span></span> <span data-ttu-id="8492a-106">順序不會與特定資料表相關聯。</span><span class="sxs-lookup"><span data-stu-id="8492a-106">Sequences are not associated with a specific table.</span></span>

## <a name="conventions"></a><span data-ttu-id="8492a-107">慣例</span><span class="sxs-lookup"><span data-stu-id="8492a-107">Conventions</span></span>

<span data-ttu-id="8492a-108">依照慣例，順序不會在模型中引進。</span><span class="sxs-lookup"><span data-stu-id="8492a-108">By convention, sequences are not introduced in to the model.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="8492a-109">資料註釋</span><span class="sxs-lookup"><span data-stu-id="8492a-109">Data Annotations</span></span>

<span data-ttu-id="8492a-110">您無法使用資料批註來設定順序。</span><span class="sxs-lookup"><span data-stu-id="8492a-110">You can not configure a sequence using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="8492a-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="8492a-111">Fluent API</span></span>

<span data-ttu-id="8492a-112">您可以使用流暢的 API 在模型中建立序列。</span><span class="sxs-lookup"><span data-stu-id="8492a-112">You can use the Fluent API to create a sequence in the model.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Relational/Sequence.cs?highlight=7)] -->
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

<span data-ttu-id="8492a-113">您也可以設定序列的其他層面，例如其架構、開始值和增量。</span><span class="sxs-lookup"><span data-stu-id="8492a-113">You can also configure additional aspect of the sequence, such as its schema, start value, and increment.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Relational/SequenceConfigured.cs?highlight=7,8,9)] -->
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

<span data-ttu-id="8492a-114">一旦引進序列之後，您就可以用它來產生模型中的屬性值。</span><span class="sxs-lookup"><span data-stu-id="8492a-114">Once a sequence is introduced, you can use it to generate values for properties in your model.</span></span> <span data-ttu-id="8492a-115">例如，您可以使用[預設值](default-values.md)來插入序列中的下一個值。</span><span class="sxs-lookup"><span data-stu-id="8492a-115">For example, you can use [Default Values](default-values.md) to insert the next value from the sequence.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Relational/SequenceUsed.cs?highlight=11,12,13)] -->
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
