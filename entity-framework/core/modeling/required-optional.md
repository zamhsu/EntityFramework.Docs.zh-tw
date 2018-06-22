---
title: 必要/選用的屬性-EF 核心
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: ddaa0a54-9f43-4c34-aae3-f95c96c69842
ms.technology: entity-framework-core
uid: core/modeling/required-optional
ms.openlocfilehash: 2af1d49e12ef980f81cb9c00556dee471673ccae
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052848"
---
# <a name="required-and-optional-properties"></a>必要和選擇性的屬性

屬性會被視為選擇性是否有效，才能包含`null`。 如果`null`不是有效的值，以指派給屬性，則它會被視為必要的屬性。

## <a name="conventions"></a>慣例

依照慣例，其 CLR 類型可以包含 null 的屬性會設定為選擇性 (`string`， `int?`，`byte[]`等。)。 將設定其 CLR 類型不能包含 null 的屬性視需要 (`int`， `decimal`，`bool`等。)。

> [!NOTE]  
> CLR 類型不能包含 null 的屬性無法設定為選擇性。 此屬性將一律視為 Entity Framework 所需。

## <a name="data-annotations"></a>資料註釋

您可以使用資料註解表示是必要屬性。

<!-- [!code-csharp[Main](samples/core/Modeling/DataAnnotations/Samples/Required.cs?highlight=4)] -->
``` csharp
public class Blog
{
    public int BlogId { get; set; }
    [Required]
    public string Url { get; set; }
}
```

## <a name="fluent-api"></a>關於 fluent 應用程式開發介面

您可以使用 fluent 應用程式開發的應用程式開發介面，表示是必要屬性。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Required.cs?highlight=7,8,9)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .Property(b => b.Url)
            .IsRequired();
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```
