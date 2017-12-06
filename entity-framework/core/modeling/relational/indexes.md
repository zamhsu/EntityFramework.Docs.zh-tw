---
title: "索引的 EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 4581e7ba-5e7f-452c-9937-0aaf790ba10a
ms.technology: entity-framework-core
uid: core/modeling/relational/indexes
ms.openlocfilehash: 683b580bb155e0416f13c5d63e3280078fbcee21
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="indexes"></a><span data-ttu-id="71065-102">索引</span><span class="sxs-lookup"><span data-stu-id="71065-102">Indexes</span></span>

> [!NOTE]  
> <span data-ttu-id="71065-103">本節中的設定是一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="71065-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="71065-104">當您安裝的關聯式資料庫提供者，如下所示的擴充方法會變成可用 (因為共用*Microsoft.EntityFrameworkCore.Relational*封裝)。</span><span class="sxs-lookup"><span data-stu-id="71065-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="71065-105">關聯式資料庫中的索引對應到相同的概念與 Entity Framework 的核心中的索引。</span><span class="sxs-lookup"><span data-stu-id="71065-105">An index in a relational database maps to the same concept as an index in the core of Entity Framework.</span></span>

## <a name="conventions"></a><span data-ttu-id="71065-106">慣例</span><span class="sxs-lookup"><span data-stu-id="71065-106">Conventions</span></span>

<span data-ttu-id="71065-107">根據慣例，索引會命名為`IX_<type name>_<property name>`。</span><span class="sxs-lookup"><span data-stu-id="71065-107">By convention, indexes are named `IX_<type name>_<property name>`.</span></span> <span data-ttu-id="71065-108">針對複合索引`<property name>`變成屬性名稱的底線分隔清單。</span><span class="sxs-lookup"><span data-stu-id="71065-108">For composite indexes `<property name>` becomes an underscore separated list of property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="71065-109">資料註釋</span><span class="sxs-lookup"><span data-stu-id="71065-109">Data Annotations</span></span>

<span data-ttu-id="71065-110">索引不可以使用資料註解來設定。</span><span class="sxs-lookup"><span data-stu-id="71065-110">Indexes can not be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="71065-111">關於 fluent 應用程式開發介面</span><span class="sxs-lookup"><span data-stu-id="71065-111">Fluent API</span></span>

<span data-ttu-id="71065-112">您可以使用 fluent 應用程式開發的應用程式開發介面來設定索引的名稱。</span><span class="sxs-lookup"><span data-stu-id="71065-112">You can use the Fluent API to configure the name of an index.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/IndexName.cs?highlight=9)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .HasIndex(b => b.Url)
            .HasName("Index_Url");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```
