---
title: 最大長度為 EF 核心
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: c39c5d43-018d-48b8-94f2-b8bc7c686c69
ms.technology: entity-framework-core
uid: core/modeling/max-length
ms.openlocfilehash: 7325c0c3328477473392bf9e7c82f1696bb4f424
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052668"
---
# <a name="maximum-length"></a>最大長度

設定最大長度提供提示，以指定屬性来使用適當的資料類型的相關資料存放區。 最大長度只適用於陣列資料類型，例如`string`和`byte[]`。

> [!NOTE]  
> Entity Framework 不會進行任何驗證的最大長度，然後將資料傳遞給提供者。 這是由提供者或資料存放區，以驗證是否適當。 比方說，當目標為 SQL Server，超出最大長度時，會導致例外狀況為基礎的資料行的資料類型將不允許儲存過多的資料。

## <a name="conventions"></a>慣例

依照慣例，它會保持最多資料庫提供者來選擇適當的資料類型的屬性。 具有長度的屬性，資料庫提供者通常會選擇允許的最長的資料長度的資料類型。 例如，Microsoft SQL Server 會使用`nvarchar(max)`如`string`屬性 (或`nvarchar(450)`如果使用做為索引鍵資料行)。

## <a name="data-annotations"></a>資料註釋

若要設定屬性的最大長度，您可以使用資料註解。 在此範例中，目標 SQL Server，這會導致`nvarchar(500)`所用的資料類型。

<!-- [!code-csharp[Main](samples/core/Modeling/DataAnnotations/Samples/MaxLength.cs?highlight=4)] -->
``` csharp
public class Blog
{
    public int BlogId { get; set; }
    [MaxLength(500)]
    public string Url { get; set; }
}
```

## <a name="fluent-api"></a>關於 fluent 應用程式開發介面

您可以使用 fluent 應用程式開發的應用程式開發介面來設定屬性的最大長度。 在此範例中，目標 SQL Server，這會導致`nvarchar(500)`所用的資料類型。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/MaxLength.cs?highlight=7,8,9)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .Property(b => b.Url)
            .HasMaxLength(500);
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```
