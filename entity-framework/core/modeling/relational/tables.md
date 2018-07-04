---
title: 資料表對應的 EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: c807aa4c-7845-443d-b8d0-bfc9b42691a3
ms.technology: entity-framework-core
uid: core/modeling/relational/tables
ms.openlocfilehash: 73957d9c77e6856bfb65e10e6b373c337101f7d9
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052728"
---
# <a name="table-mapping"></a><span data-ttu-id="a33e4-102">資料表對應</span><span class="sxs-lookup"><span data-stu-id="a33e4-102">Table Mapping</span></span>

> [!NOTE]  
> <span data-ttu-id="a33e4-103">本節中的設定是一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="a33e4-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="a33e4-104">當您安裝的關聯式資料庫提供者，如下所示的擴充方法會變成可用 (因為共用*Microsoft.EntityFrameworkCore.Relational*封裝)。</span><span class="sxs-lookup"><span data-stu-id="a33e4-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="a33e4-105">資料表對應會識別應該要從查詢並儲存到資料庫中的資料表資料。</span><span class="sxs-lookup"><span data-stu-id="a33e4-105">Table mapping identifies which table data should be queried from and saved to in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="a33e4-106">慣例</span><span class="sxs-lookup"><span data-stu-id="a33e4-106">Conventions</span></span>

<span data-ttu-id="a33e4-107">依照慣例，每個實體會對應至具有相同名稱做為資料表的安裝程式`DbSet<TEntity>`公開衍生內容上的實體的屬性。</span><span class="sxs-lookup"><span data-stu-id="a33e4-107">By convention, each entity will be setup to map to a table with the same name as the `DbSet<TEntity>` property that exposes the entity on the derived context.</span></span> <span data-ttu-id="a33e4-108">如果沒有`DbSet<TEntity>`是否包含指定之實體，使用此類別名稱。</span><span class="sxs-lookup"><span data-stu-id="a33e4-108">If no `DbSet<TEntity>` is included for the given entity, the class name is used.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="a33e4-109">資料註釋</span><span class="sxs-lookup"><span data-stu-id="a33e4-109">Data Annotations</span></span>

<span data-ttu-id="a33e4-110">若要設定類型會對應至資料表，您可以使用資料註解。</span><span class="sxs-lookup"><span data-stu-id="a33e4-110">You can use Data Annotations to configure the table that a type maps to.</span></span>

``` csharp
using System.ComponentModel.DataAnnotations.Schema;
```
``` csharp
[Table("blogs")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

<span data-ttu-id="a33e4-111">您也可以指定資料表所屬的結構描述。</span><span class="sxs-lookup"><span data-stu-id="a33e4-111">You can also specify a schema that the table belongs to.</span></span>

``` csharp
[Table("blogs", Schema = "blogging")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

## <a name="fluent-api"></a><span data-ttu-id="a33e4-112">關於 fluent 應用程式開發介面</span><span class="sxs-lookup"><span data-stu-id="a33e4-112">Fluent API</span></span>

<span data-ttu-id="a33e4-113">若要設定類型會對應至資料表，您可以使用 fluent 應用程式開發的應用程式開發介面。</span><span class="sxs-lookup"><span data-stu-id="a33e4-113">You can use the Fluent API to configure the table that a type maps to.</span></span>

``` csharp
using Microsoft.EntityFrameworkCore;
```
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .ToTable("blogs");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

<span data-ttu-id="a33e4-114">您也可以指定資料表所屬的結構描述。</span><span class="sxs-lookup"><span data-stu-id="a33e4-114">You can also specify a schema that the table belongs to.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/TableAndSchema.cs?highlight=2)] -->
``` csharp
        modelBuilder.Entity<Blog>()
            .ToTable("blogs", schema: "blogging");
```
