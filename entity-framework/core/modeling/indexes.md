---
title: 索引的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 85b92003-b692-417d-ac1d-76d40dce664b
uid: core/modeling/indexes
ms.openlocfilehash: 87fe893243377e3ab83d419ae9bedf813ca50c3f
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995476"
---
# <a name="indexes"></a>索引

索引是許多資料存放區之間的共通的概念。 其資料存放區中的實作可能有所不同，它們用來讓資料行 （或一組資料行） 為基礎的查閱更有效率。

## <a name="conventions"></a>慣例

依照慣例，會建立做為外部索引鍵，每個屬性 （或屬性集） 中的索引。

## <a name="data-annotations"></a>資料註釋

可以使用資料註解建立索引。

## <a name="fluent-api"></a>Fluent API

您可以使用 Fluent API，來指定單一屬性上的索引。 根據預設，索引是非唯一的。

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

您也可以指定索引應該是唯一的這表示任何兩個實體可以有相同的值為指定的屬性。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/IndexUnique.cs?highlight=3)] -->
``` csharp
        modelBuilder.Entity<Blog>()
            .HasIndex(b => b.Url)
            .IsUnique();
```

您也可以指定多個資料行的索引。

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
> 沒有每一組不同的屬性只能有一個索引。 如果您使用 Fluent API 來設定索引上的一組屬性已經有索引定義，藉由慣例或先前的設定，然後您將會變更該索引的定義。 這非常有用，如果您想要進一步設定由慣例所建立的索引。
