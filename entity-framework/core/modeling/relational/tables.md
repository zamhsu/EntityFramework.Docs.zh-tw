---
title: 資料表對應-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c807aa4c-7845-443d-b8d0-bfc9b42691a3
uid: core/modeling/relational/tables
ms.openlocfilehash: 62dce317b901bc862b3c7d20ed1d176805bb24dd
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71196964"
---
# <a name="table-mapping"></a><span data-ttu-id="c45b1-102">資料表對應</span><span class="sxs-lookup"><span data-stu-id="c45b1-102">Table Mapping</span></span>

> [!NOTE]  
> <span data-ttu-id="c45b1-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="c45b1-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="c45b1-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="c45b1-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="c45b1-105">資料表對應會識別應該在資料庫中從查詢和儲存哪些資料表資料。</span><span class="sxs-lookup"><span data-stu-id="c45b1-105">Table mapping identifies which table data should be queried from and saved to in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="c45b1-106">慣例</span><span class="sxs-lookup"><span data-stu-id="c45b1-106">Conventions</span></span>

<span data-ttu-id="c45b1-107">依照慣例，每個實體都會設定成對應至與 `DbSet<TEntity>` 屬性同名的資料表，該屬性會在衍生內容上公開實體。</span><span class="sxs-lookup"><span data-stu-id="c45b1-107">By convention, each entity will be set up to map to a table with the same name as the `DbSet<TEntity>` property that exposes the entity on the derived context.</span></span> <span data-ttu-id="c45b1-108">`DbSet<TEntity>`如果指定的實體未包含，則會使用類別名稱。</span><span class="sxs-lookup"><span data-stu-id="c45b1-108">If no `DbSet<TEntity>` is included for the given entity, the class name is used.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="c45b1-109">資料註釋</span><span class="sxs-lookup"><span data-stu-id="c45b1-109">Data Annotations</span></span>

<span data-ttu-id="c45b1-110">您可以使用資料批註來設定型別所對應的資料表。</span><span class="sxs-lookup"><span data-stu-id="c45b1-110">You can use Data Annotations to configure the table that a type maps to.</span></span>

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

<span data-ttu-id="c45b1-111">您也可以指定資料表所屬的架構。</span><span class="sxs-lookup"><span data-stu-id="c45b1-111">You can also specify a schema that the table belongs to.</span></span>

``` csharp
[Table("blogs", Schema = "blogging")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

## <a name="fluent-api"></a><span data-ttu-id="c45b1-112">Fluent API</span><span class="sxs-lookup"><span data-stu-id="c45b1-112">Fluent API</span></span>

<span data-ttu-id="c45b1-113">您可以使用流暢的 API 來設定型別對應的資料表。</span><span class="sxs-lookup"><span data-stu-id="c45b1-113">You can use the Fluent API to configure the table that a type maps to.</span></span>

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

<span data-ttu-id="c45b1-114">您也可以指定資料表所屬的架構。</span><span class="sxs-lookup"><span data-stu-id="c45b1-114">You can also specify a schema that the table belongs to.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Relational/TableAndSchema.cs?highlight=2)] -->
``` csharp
        modelBuilder.Entity<Blog>()
            .ToTable("blogs", schema: "blogging");
```
