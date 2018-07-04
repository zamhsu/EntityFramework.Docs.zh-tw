---
title: 索引的 EF Core
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
# <a name="indexes"></a>索引

索引是跨多個資料存放區的一般概念。 其資料存放區中的實作可能有所不同，它們用來使資料行 （或一組資料行） 為基礎的查閱更有效率。

## <a name="conventions"></a>慣例

依照慣例，會當做外部索引鍵使用每個屬性 （或屬性集） 中建立索引。

## <a name="data-annotations"></a>資料註釋

可以使用資料註解建立索引。

## <a name="fluent-api"></a>關於 fluent 應用程式開發介面

若要在單一屬性上指定的索引，您可以使用 fluent 應用程式開發的應用程式開發介面。 根據預設，索引為非唯一的。

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
> 沒有每個相異的屬性集的一個索引。 如果您使用 fluent 應用程式開發的應用程式開發介面來設定索引上的一組屬性已索引定義，藉由慣例或先前的設定，然後將會變更該索引的定義。 這非常有用，如果您想要進一步設定由慣例所建立的索引。
