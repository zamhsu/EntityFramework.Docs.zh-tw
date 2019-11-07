---
title: 資料表對應-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c807aa4c-7845-443d-b8d0-bfc9b42691a3
uid: core/modeling/relational/tables
ms.openlocfilehash: 474c49aca4c65cd5d58b184b1f3c2d30e7abff84
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656095"
---
# <a name="table-mapping"></a><span data-ttu-id="24f36-102">資料表對應</span><span class="sxs-lookup"><span data-stu-id="24f36-102">Table Mapping</span></span>

> [!NOTE]  
> <span data-ttu-id="24f36-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="24f36-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="24f36-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="24f36-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="24f36-105">資料表對應會識別應該在資料庫中從查詢和儲存哪些資料表資料。</span><span class="sxs-lookup"><span data-stu-id="24f36-105">Table mapping identifies which table data should be queried from and saved to in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="24f36-106">慣例</span><span class="sxs-lookup"><span data-stu-id="24f36-106">Conventions</span></span>

<span data-ttu-id="24f36-107">依照慣例，每個實體都會設定成對應至與 `DbSet<TEntity>` 屬性同名的資料表，該屬性會在衍生內容上公開實體。</span><span class="sxs-lookup"><span data-stu-id="24f36-107">By convention, each entity will be set up to map to a table with the same name as the `DbSet<TEntity>` property that exposes the entity on the derived context.</span></span> <span data-ttu-id="24f36-108">如果指定的實體未包含任何 `DbSet<TEntity>`，則會使用類別名稱。</span><span class="sxs-lookup"><span data-stu-id="24f36-108">If no `DbSet<TEntity>` is included for the given entity, the class name is used.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="24f36-109">資料註釋</span><span class="sxs-lookup"><span data-stu-id="24f36-109">Data Annotations</span></span>

<span data-ttu-id="24f36-110">您可以使用資料批註來設定型別所對應的資料表。</span><span class="sxs-lookup"><span data-stu-id="24f36-110">You can use Data Annotations to configure the table that a type maps to.</span></span>

``` csharp
using System.ComponentModel.DataAnnotations.Schema;

[Table("blogs")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

<span data-ttu-id="24f36-111">您也可以指定資料表所屬的架構。</span><span class="sxs-lookup"><span data-stu-id="24f36-111">You can also specify a schema that the table belongs to.</span></span>

``` csharp
[Table("blogs", Schema = "blogging")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

## <a name="fluent-api"></a><span data-ttu-id="24f36-112">Fluent API</span><span class="sxs-lookup"><span data-stu-id="24f36-112">Fluent API</span></span>

<span data-ttu-id="24f36-113">您可以使用流暢的 API 來設定型別對應的資料表。</span><span class="sxs-lookup"><span data-stu-id="24f36-113">You can use the Fluent API to configure the table that a type maps to.</span></span>

``` csharp
using Microsoft.EntityFrameworkCore;

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

<span data-ttu-id="24f36-114">您也可以指定資料表所屬的架構。</span><span class="sxs-lookup"><span data-stu-id="24f36-114">You can also specify a schema that the table belongs to.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/TableAndSchema.cs?name=Table&highlight=2)]
