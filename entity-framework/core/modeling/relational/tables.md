---
title: 資料表對應的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c807aa4c-7845-443d-b8d0-bfc9b42691a3
uid: core/modeling/relational/tables
ms.openlocfilehash: 32c5e3cc0e498005ce8e6be1f1ee7e8ddf9b510d
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994133"
---
# <a name="table-mapping"></a><span data-ttu-id="bddb7-102">資料表對應</span><span class="sxs-lookup"><span data-stu-id="bddb7-102">Table Mapping</span></span>

> [!NOTE]  
> <span data-ttu-id="bddb7-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="bddb7-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="bddb7-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="bddb7-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="bddb7-105">資料表對應會識別應該從查詢並儲存到資料庫中的資料表資料。</span><span class="sxs-lookup"><span data-stu-id="bddb7-105">Table mapping identifies which table data should be queried from and saved to in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="bddb7-106">慣例</span><span class="sxs-lookup"><span data-stu-id="bddb7-106">Conventions</span></span>

<span data-ttu-id="bddb7-107">依照慣例，每個實體會設定為對應至具有相同名稱的資料表`DbSet<TEntity>`衍生內容上的實體公開 （expose） 的屬性。</span><span class="sxs-lookup"><span data-stu-id="bddb7-107">By convention, each entity will be set up to map to a table with the same name as the `DbSet<TEntity>` property that exposes the entity on the derived context.</span></span> <span data-ttu-id="bddb7-108">如果沒有`DbSet<TEntity>`是否包含指定之實體，會使用類別名稱。</span><span class="sxs-lookup"><span data-stu-id="bddb7-108">If no `DbSet<TEntity>` is included for the given entity, the class name is used.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="bddb7-109">資料註釋</span><span class="sxs-lookup"><span data-stu-id="bddb7-109">Data Annotations</span></span>

<span data-ttu-id="bddb7-110">若要設定的類型會對應至資料表，您可以使用資料註解。</span><span class="sxs-lookup"><span data-stu-id="bddb7-110">You can use Data Annotations to configure the table that a type maps to.</span></span>

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

<span data-ttu-id="bddb7-111">您也可以指定資料表所屬的結構描述。</span><span class="sxs-lookup"><span data-stu-id="bddb7-111">You can also specify a schema that the table belongs to.</span></span>

``` csharp
[Table("blogs", Schema = "blogging")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

## <a name="fluent-api"></a><span data-ttu-id="bddb7-112">Fluent API</span><span class="sxs-lookup"><span data-stu-id="bddb7-112">Fluent API</span></span>

<span data-ttu-id="bddb7-113">您可以使用 Fluent API 來設定類型會對應至資料表。</span><span class="sxs-lookup"><span data-stu-id="bddb7-113">You can use the Fluent API to configure the table that a type maps to.</span></span>

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

<span data-ttu-id="bddb7-114">您也可以指定資料表所屬的結構描述。</span><span class="sxs-lookup"><span data-stu-id="bddb7-114">You can also specify a schema that the table belongs to.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/TableAndSchema.cs?highlight=2)] -->
``` csharp
        modelBuilder.Entity<Blog>()
            .ToTable("blogs", schema: "blogging");
```
