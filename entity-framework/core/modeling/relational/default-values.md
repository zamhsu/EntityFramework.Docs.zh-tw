---
title: 預設值-EF 核心
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: e541366a-130f-47dd-9997-1b110a11febe
ms.technology: entity-framework-core
uid: core/modeling/relational/default-values
ms.openlocfilehash: 73b916b6d9f9c984c8ea010f2319eafa7d031a58
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052758"
---
# <a name="default-values"></a>預設值

> [!NOTE]  
> 本節中的設定是一般適用於關聯式資料庫。 當您安裝的關聯式資料庫提供者，如下所示的擴充方法會變成可用 (因為共用*Microsoft.EntityFrameworkCore.Relational*封裝)。

將會插入在插入新資料列，但未指定值的資料行的值為資料行的預設值。

## <a name="conventions"></a>慣例

依照慣例，未設定預設值。

## <a name="data-annotations"></a>資料註釋

您可以設定使用資料註解的預設值。

## <a name="fluent-api"></a>關於 fluent 應用程式開發介面

您可以使用 fluent 應用程式開發的應用程式開發介面來指定屬性的預設值。

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/DefaultValue.cs?highlight=9)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .Property(b => b.Rating)
            .HasDefaultValue(3);
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
    public int Rating { get; set; }
}
```

您也可以指定用來計算預設值是 SQL 片段。

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/DefaultValueSql.cs?highlight=9)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .Property(b => b.Created)
            .HasDefaultValueSql("getdate()");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
    public DateTime Created { get; set; }
}
```
