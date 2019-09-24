---
title: 索引的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 85b92003-b692-417d-ac1d-76d40dce664b
uid: core/modeling/indexes
ms.openlocfilehash: b6f11401b69bd8e8795f6b22e5392ba16fc9ba2e
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197253"
---
# <a name="indexes"></a><span data-ttu-id="b215b-102">索引</span><span class="sxs-lookup"><span data-stu-id="b215b-102">Indexes</span></span>

<span data-ttu-id="b215b-103">索引是許多資料存放區的通用概念。</span><span class="sxs-lookup"><span data-stu-id="b215b-103">Indexes are a common concept across many data stores.</span></span> <span data-ttu-id="b215b-104">雖然其在資料存放區中的執行可能會有所不同，但它們是用來讓根據資料行（或資料行集）進行查閱更有效率。</span><span class="sxs-lookup"><span data-stu-id="b215b-104">While their implementation in the data store may vary, they are used to make lookups based on a column (or set of columns) more efficient.</span></span>

## <a name="conventions"></a><span data-ttu-id="b215b-105">慣例</span><span class="sxs-lookup"><span data-stu-id="b215b-105">Conventions</span></span>

<span data-ttu-id="b215b-106">依照慣例，索引會在當做外鍵使用的每一個屬性（或一組屬性）中建立。</span><span class="sxs-lookup"><span data-stu-id="b215b-106">By convention, an index is created in each property (or set of properties) that are used as a foreign key.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="b215b-107">資料註釋</span><span class="sxs-lookup"><span data-stu-id="b215b-107">Data Annotations</span></span>

<span data-ttu-id="b215b-108">無法使用資料批註來建立索引。</span><span class="sxs-lookup"><span data-stu-id="b215b-108">Indexes can not be created using data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="b215b-109">Fluent API</span><span class="sxs-lookup"><span data-stu-id="b215b-109">Fluent API</span></span>

<span data-ttu-id="b215b-110">您可以使用流暢的 API，在單一屬性上指定索引。</span><span class="sxs-lookup"><span data-stu-id="b215b-110">You can use the Fluent API to specify an index on a single property.</span></span> <span data-ttu-id="b215b-111">根據預設，索引不是唯一的。</span><span class="sxs-lookup"><span data-stu-id="b215b-111">By default, indexes are non-unique.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Index.cs?highlight=7,8)] -->
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

<span data-ttu-id="b215b-112">您也可以指定索引應該是唯一的，這表示沒有兩個實體可以有指定屬性的相同值。</span><span class="sxs-lookup"><span data-stu-id="b215b-112">You can also specify that an index should be unique, meaning that no two entities can have the same value(s) for the given property(s).</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/IndexUnique.cs?highlight=3)] -->
``` csharp
        modelBuilder.Entity<Blog>()
            .HasIndex(b => b.Url)
            .IsUnique();
```

<span data-ttu-id="b215b-113">您也可以在一個以上的資料行上指定索引。</span><span class="sxs-lookup"><span data-stu-id="b215b-113">You can also specify an index over more than one column.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/IndexComposite.cs?highlight=7,8)] -->
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
> <span data-ttu-id="b215b-114">每一組不同的屬性只有一個索引。</span><span class="sxs-lookup"><span data-stu-id="b215b-114">There is only one index per distinct set of properties.</span></span> <span data-ttu-id="b215b-115">如果您使用流暢的 API，在已定義索引（依慣例或先前的設定）的一組屬性上設定索引，則您將會變更該索引的定義。</span><span class="sxs-lookup"><span data-stu-id="b215b-115">If you use the Fluent API to configure an index on a set of properties that already has an index defined, either by convention or previous configuration, then you will be changing the definition of that index.</span></span> <span data-ttu-id="b215b-116">如果您想要進一步設定依照慣例所建立的索引，這會很有用。</span><span class="sxs-lookup"><span data-stu-id="b215b-116">This is useful if you want to further configure an index that was created by convention.</span></span>
