---
title: 索引的 EF 核心
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 85b92003-b692-417d-ac1d-76d40dce664b
ms.technology: entity-framework-core
uid: core/modeling/indexes
ms.openlocfilehash: f57b545d53613cec6887734bf434958ee8fff4d8
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
ms.locfileid: "26054882"
---
# <a name="indexes"></a><span data-ttu-id="85816-102">索引</span><span class="sxs-lookup"><span data-stu-id="85816-102">Indexes</span></span>

<span data-ttu-id="85816-103">索引是跨多個資料存放區的一般概念。</span><span class="sxs-lookup"><span data-stu-id="85816-103">Indexes are a common concept across many data stores.</span></span> <span data-ttu-id="85816-104">其資料存放區中的實作可能有所不同，它們用來使資料行 （或一組資料行） 為基礎的查閱更有效率。</span><span class="sxs-lookup"><span data-stu-id="85816-104">While their implementation in the data store may vary, they are used to make lookups based on a column (or set of columns) more efficient.</span></span>

## <a name="conventions"></a><span data-ttu-id="85816-105">慣例</span><span class="sxs-lookup"><span data-stu-id="85816-105">Conventions</span></span>

<span data-ttu-id="85816-106">依照慣例，會當做外部索引鍵使用每個屬性 （或屬性集） 中建立索引。</span><span class="sxs-lookup"><span data-stu-id="85816-106">By convention, an index is created in each property (or set of properties) that are used as a foreign key.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="85816-107">資料註釋</span><span class="sxs-lookup"><span data-stu-id="85816-107">Data Annotations</span></span>

<span data-ttu-id="85816-108">可以使用資料註解建立索引。</span><span class="sxs-lookup"><span data-stu-id="85816-108">Indexes can not be created using data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="85816-109">關於 fluent 應用程式開發介面</span><span class="sxs-lookup"><span data-stu-id="85816-109">Fluent API</span></span>

<span data-ttu-id="85816-110">若要在單一屬性上指定的索引，您可以使用 fluent 應用程式開發的應用程式開發介面。</span><span class="sxs-lookup"><span data-stu-id="85816-110">You can use the Fluent API to specify an index on a single property.</span></span> <span data-ttu-id="85816-111">根據預設，索引為非唯一的。</span><span class="sxs-lookup"><span data-stu-id="85816-111">By default, indexes are non-unique.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Index.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .HasIndex(b => b.Url);
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

<span data-ttu-id="85816-112">您也可以指定索引應該是唯一的這表示任何兩個實體可以有相同的值為指定的屬性。</span><span class="sxs-lookup"><span data-stu-id="85816-112">You can also specify that an index should be unique, meaning that no two entities can have the same value(s) for the given property(s).</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/IndexUnique.cs?highlight=3)] -->
``` csharp
        modelBuilder.Entity<Blog>()
            .HasIndex(b => b.Url)
            .IsUnique();
```

<span data-ttu-id="85816-113">您也可以指定多個資料行的索引。</span><span class="sxs-lookup"><span data-stu-id="85816-113">You can also specify an index over more than one column.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/IndexComposite.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Person> People { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Person>()
            .HasIndex(p => new { p.FirstName, p.LastName });
    }
}

public class Person
{
    public int PersonId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

> [!TIP]  
> <span data-ttu-id="85816-114">沒有每個相異的屬性集的一個索引。</span><span class="sxs-lookup"><span data-stu-id="85816-114">There is only one index per distinct set of properties.</span></span> <span data-ttu-id="85816-115">如果您使用 fluent 應用程式開發的應用程式開發介面來設定索引上的一組屬性已索引定義，藉由慣例或先前的設定，然後將會變更該索引的定義。</span><span class="sxs-lookup"><span data-stu-id="85816-115">If you use the Fluent API to configure an index on a set of properties that already has an index defined, either by convention or previous configuration, then you will be changing the definition of that index.</span></span> <span data-ttu-id="85816-116">這非常有用，如果您想要進一步設定由慣例所建立的索引。</span><span class="sxs-lookup"><span data-stu-id="85816-116">This is useful if you want to further configure an index that was created by convention.</span></span>
