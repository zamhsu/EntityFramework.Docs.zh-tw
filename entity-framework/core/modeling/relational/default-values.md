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
# <a name="default-values"></a><span data-ttu-id="6ff5b-102">預設值</span><span class="sxs-lookup"><span data-stu-id="6ff5b-102">Default Values</span></span>

> [!NOTE]  
> <span data-ttu-id="6ff5b-103">本節中的設定是一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="6ff5b-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="6ff5b-104">當您安裝的關聯式資料庫提供者，如下所示的擴充方法會變成可用 (因為共用*Microsoft.EntityFrameworkCore.Relational*封裝)。</span><span class="sxs-lookup"><span data-stu-id="6ff5b-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="6ff5b-105">將會插入在插入新資料列，但未指定值的資料行的值為資料行的預設值。</span><span class="sxs-lookup"><span data-stu-id="6ff5b-105">The default value of a column is the value that will be inserted if a new row is inserted but no value is specified for the column.</span></span>

## <a name="conventions"></a><span data-ttu-id="6ff5b-106">慣例</span><span class="sxs-lookup"><span data-stu-id="6ff5b-106">Conventions</span></span>

<span data-ttu-id="6ff5b-107">依照慣例，未設定預設值。</span><span class="sxs-lookup"><span data-stu-id="6ff5b-107">By convention, a default value is not configured.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="6ff5b-108">資料註釋</span><span class="sxs-lookup"><span data-stu-id="6ff5b-108">Data Annotations</span></span>

<span data-ttu-id="6ff5b-109">您可以設定使用資料註解的預設值。</span><span class="sxs-lookup"><span data-stu-id="6ff5b-109">You can not set a default value using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="6ff5b-110">關於 fluent 應用程式開發介面</span><span class="sxs-lookup"><span data-stu-id="6ff5b-110">Fluent API</span></span>

<span data-ttu-id="6ff5b-111">您可以使用 fluent 應用程式開發的應用程式開發介面來指定屬性的預設值。</span><span class="sxs-lookup"><span data-stu-id="6ff5b-111">You can use the Fluent API to specify the default value for a property.</span></span>

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

<span data-ttu-id="6ff5b-112">您也可以指定用來計算預設值是 SQL 片段。</span><span class="sxs-lookup"><span data-stu-id="6ff5b-112">You can also specify a SQL fragment that is used to calculate the default value.</span></span>

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
