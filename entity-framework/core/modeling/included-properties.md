---
title: 包含與排除內容-EF 核心
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: e9dff604-3469-4a05-8f9e-18ac281d82a9
ms.technology: entity-framework-core
uid: core/modeling/included-properties
ms.openlocfilehash: a6eaea4319f6a4d30c223265bf75a88731a38443
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052488"
---
# <a name="including--excluding-properties"></a><span data-ttu-id="feb02-102">包含與排除內容</span><span class="sxs-lookup"><span data-stu-id="feb02-102">Including & Excluding Properties</span></span>

<span data-ttu-id="feb02-103">包含模型中的屬性，表示 EF 具有該屬性的相關中繼資料，並將嘗試讀取和寫入自/至資料庫的值。</span><span class="sxs-lookup"><span data-stu-id="feb02-103">Including a property in the model means that EF has metadata about that property and will attempt to read and write values from/to the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="feb02-104">慣例</span><span class="sxs-lookup"><span data-stu-id="feb02-104">Conventions</span></span>

<span data-ttu-id="feb02-105">依照慣例，公用屬性 getter 和 setter 將會包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="feb02-105">By convention, public properties with a getter and a setter will be included in the model.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="feb02-106">資料註釋</span><span class="sxs-lookup"><span data-stu-id="feb02-106">Data Annotations</span></span>

<span data-ttu-id="feb02-107">若要從模型中排除屬性，您可以使用資料註解。</span><span class="sxs-lookup"><span data-stu-id="feb02-107">You can use Data Annotations to exclude a property from the model.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/DataAnnotations/Samples/IgnoreProperty.cs?highlight=6)] -->
``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    [NotMapped]
    public DateTime LoadedFromDatabase { get; set; }
}
```

## <a name="fluent-api"></a><span data-ttu-id="feb02-108">關於 fluent 應用程式開發介面</span><span class="sxs-lookup"><span data-stu-id="feb02-108">Fluent API</span></span>

<span data-ttu-id="feb02-109">若要從模型中排除屬性，您可以使用 fluent 應用程式開發的應用程式開發介面。</span><span class="sxs-lookup"><span data-stu-id="feb02-109">You can use the Fluent API to exclude a property from the model.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/IgnoreProperty.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .Ignore(b => b.LoadedFromDatabase);
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public DateTime LoadedFromDatabase { get; set; }
}
```
