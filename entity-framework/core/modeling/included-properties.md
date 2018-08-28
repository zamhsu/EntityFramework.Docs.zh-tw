---
title: 包含與排除內容-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e9dff604-3469-4a05-8f9e-18ac281d82a9
uid: core/modeling/included-properties
ms.openlocfilehash: 07b70e4517b67490e04a9ec9fa22b9b5d5217681
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42998251"
---
# <a name="including--excluding-properties"></a><span data-ttu-id="a69ef-102">包含與排除屬性</span><span class="sxs-lookup"><span data-stu-id="a69ef-102">Including & Excluding Properties</span></span>

<span data-ttu-id="a69ef-103">包括在模型中的屬性，表示 EF 具有該屬性的相關中繼資料，並會嘗試讀取和寫入，或將資料庫的值。</span><span class="sxs-lookup"><span data-stu-id="a69ef-103">Including a property in the model means that EF has metadata about that property and will attempt to read and write values from/to the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="a69ef-104">慣例</span><span class="sxs-lookup"><span data-stu-id="a69ef-104">Conventions</span></span>

<span data-ttu-id="a69ef-105">依照慣例，getter 和 setter 的公用屬性，將會包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="a69ef-105">By convention, public properties with a getter and a setter will be included in the model.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="a69ef-106">資料註釋</span><span class="sxs-lookup"><span data-stu-id="a69ef-106">Data Annotations</span></span>

<span data-ttu-id="a69ef-107">若要從模型中排除的屬性，您可以使用資料註解。</span><span class="sxs-lookup"><span data-stu-id="a69ef-107">You can use Data Annotations to exclude a property from the model.</span></span>

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

## <a name="fluent-api"></a><span data-ttu-id="a69ef-108">Fluent API</span><span class="sxs-lookup"><span data-stu-id="a69ef-108">Fluent API</span></span>

<span data-ttu-id="a69ef-109">您可以使用 Fluent API，若要從模型中排除的屬性。</span><span class="sxs-lookup"><span data-stu-id="a69ef-109">You can use the Fluent API to exclude a property from the model.</span></span>

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
