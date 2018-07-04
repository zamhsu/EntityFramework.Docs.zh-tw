---
title: 包含與排除類型-EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: cbe6935e-2679-4b77-8914-a8d772240cf1
ms.technology: entity-framework-core
uid: core/modeling/included-types
ms.openlocfilehash: a8d7293a144968d2506bdcc76e55a1a0b1e3fd4b
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052598"
---
# <a name="including--excluding-types"></a><span data-ttu-id="f990b-102">包含與排除類型</span><span class="sxs-lookup"><span data-stu-id="f990b-102">Including & Excluding Types</span></span>

<span data-ttu-id="f990b-103">包括模型表示 EF 具有相關的中繼資料類型，而且將嘗試讀取和寫入自/至資料庫的執行個體中的型別。</span><span class="sxs-lookup"><span data-stu-id="f990b-103">Including a type in the model means that EF has metadata about that type and will attempt to read and write instances from/to the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="f990b-104">慣例</span><span class="sxs-lookup"><span data-stu-id="f990b-104">Conventions</span></span>

<span data-ttu-id="f990b-105">依照慣例，會以公開的型別`DbSet`上您的內容屬性都包含在您的模型。</span><span class="sxs-lookup"><span data-stu-id="f990b-105">By convention, types that are exposed in `DbSet` properties on your context are included in your model.</span></span> <span data-ttu-id="f990b-106">此外，型別所述`OnModelCreating`方法也會包含。</span><span class="sxs-lookup"><span data-stu-id="f990b-106">In addition, types that are mentioned in the `OnModelCreating` method are also included.</span></span> <span data-ttu-id="f990b-107">最後，找到以遞迴方式瀏覽探索到的類型的導覽屬性的任何型別也會包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="f990b-107">Finally, any types that are found by recursively exploring the navigation properties of discovered types are also included in the model.</span></span>

<span data-ttu-id="f990b-108">**例如，在下列程式碼清單會探索所有的三種類型：**</span><span class="sxs-lookup"><span data-stu-id="f990b-108">**For example, in the following code listing all three types are discovered:**</span></span>

* <span data-ttu-id="f990b-109">`Blog`因為它會顯示在`DbSet`內容上的屬性</span><span class="sxs-lookup"><span data-stu-id="f990b-109">`Blog` because it is exposed in a `DbSet` property on the context</span></span>

* <span data-ttu-id="f990b-110">`Post`因為它透過找到`Blog.Posts`導覽屬性</span><span class="sxs-lookup"><span data-stu-id="f990b-110">`Post` because it is discovered via the `Blog.Posts` navigation property</span></span>

* <span data-ttu-id="f990b-111">`AuditEntry`因為它被提及中`OnModelCreating`</span><span class="sxs-lookup"><span data-stu-id="f990b-111">`AuditEntry` because it is mentioned in `OnModelCreating`</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/IncludedTypes.cs?highlight=3,7,16)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<AuditEntry>();
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog { get; set; }
}

public class AuditEntry
{
    public int AuditEntryId { get; set; }
    public string Username { get; set; }
    public string Action { get; set; }
}
```

## <a name="data-annotations"></a><span data-ttu-id="f990b-112">資料註釋</span><span class="sxs-lookup"><span data-stu-id="f990b-112">Data Annotations</span></span>

<span data-ttu-id="f990b-113">若要從模型中排除的型別，您可以使用資料註解。</span><span class="sxs-lookup"><span data-stu-id="f990b-113">You can use Data Annotations to exclude a type from the model.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/DataAnnotations/Samples/IgnoreType.cs?highlight=9)] -->
``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public BlogMetadata Metadata { get; set; }
}

[NotMapped]
public class BlogMetadata
{
    public DateTime LoadedFromDatabase { get; set; }
}
```

## <a name="fluent-api"></a><span data-ttu-id="f990b-114">關於 fluent 應用程式開發介面</span><span class="sxs-lookup"><span data-stu-id="f990b-114">Fluent API</span></span>

<span data-ttu-id="f990b-115">您可以使用 fluent 應用程式開發的應用程式開發介面，以排除模型中的型別。</span><span class="sxs-lookup"><span data-stu-id="f990b-115">You can use the Fluent API to exclude a type from the model.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/IgnoreType.cs?highlight=7)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Ignore<BlogMetadata>();
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public BlogMetadata Metadata { get; set; }
}

public class BlogMetadata
{
    public DateTime LoadedFromDatabase { get; set; }
}
```
