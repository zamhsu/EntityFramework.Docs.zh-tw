---
title: Entity Framework Core 概觀 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: bc2a2676-bc46-493f-bf49-e3cc97994d57
uid: core/index
ms.openlocfilehash: e736251753134b716e64f24f6c517ed9f66a7db4
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181319"
---
# <a name="entity-framework-core"></a><span data-ttu-id="699d5-102">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="699d5-102">Entity Framework Core</span></span>

<span data-ttu-id="699d5-103">Entity Framework (EF) Core 是常見 Entity Framework 資料存取技術的輕量型、可擴充、[開放原始碼](https://github.com/aspnet/EntityFrameworkCore)且跨平台版本。</span><span class="sxs-lookup"><span data-stu-id="699d5-103">Entity Framework (EF) Core is a lightweight, extensible, [open source](https://github.com/aspnet/EntityFrameworkCore) and cross-platform version of the popular Entity Framework data access technology.</span></span>

<span data-ttu-id="699d5-104">EF Core 可以當成物件關聯式對應程式 (O/RM)，讓 .NET 開發人員使用 .NET 物件來處理資料庫，而可以省略大部分以往必須自行撰寫的資料存取程式碼。</span><span class="sxs-lookup"><span data-stu-id="699d5-104">EF Core can serve as an object-relational mapper (O/RM), enabling .NET developers to work with a database using .NET objects, and eliminating the need for most of the data-access code they usually need to write.</span></span>

<span data-ttu-id="699d5-105">EF Core 支援許多資料庫引擎，如需詳細資料，請參閱[資料庫提供者](providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="699d5-105">EF Core supports many database engines, see [Database Providers](providers/index.md) for details.</span></span>

## <a name="the-model"></a><span data-ttu-id="699d5-106">模型</span><span class="sxs-lookup"><span data-stu-id="699d5-106">The Model</span></span>

<span data-ttu-id="699d5-107">運用 EF Core，使用模型來執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="699d5-107">With EF Core, data access is performed using a model.</span></span> <span data-ttu-id="699d5-108">模型包含多個實體類別以及一個代表含資料庫之工作階段的內容物件，可讓您查詢和儲存資料。</span><span class="sxs-lookup"><span data-stu-id="699d5-108">A model is made up of entity classes and a context object that represents a session with the database, allowing you to query and save data.</span></span> <span data-ttu-id="699d5-109">若要深入了解，請參閱[建立模型](modeling/index.md)。</span><span class="sxs-lookup"><span data-stu-id="699d5-109">See [Creating a Model](modeling/index.md) to learn more.</span></span>

<span data-ttu-id="699d5-110">您可以從現有資料庫產生模型、撰寫符合您資料庫的模型程式碼，或使用[EF 移轉](managing-schemas/migrations/index.md)來從您的模型建立資料庫，然後讓它隨著您模型的變更逐步發展。</span><span class="sxs-lookup"><span data-stu-id="699d5-110">You can generate a model from an existing database, hand code a model to match your database, or use [EF Migrations](managing-schemas/migrations/index.md) to create a database from your model, and then evolve it as your model changes over time.</span></span>

``` csharp
using Microsoft.EntityFrameworkCore;
using System.Collections.Generic;

namespace Intro
{
    public class BloggingContext : DbContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(
                @"Server=(localdb)\mssqllocaldb;Database=Blogging;Integrated Security=True");
        }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Url { get; set; }
        public int Rating { get; set; }
        public List<Post> Posts { get; set; }
    }

    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
}
```

## <a name="querying"></a><span data-ttu-id="699d5-111">查詢</span><span class="sxs-lookup"><span data-stu-id="699d5-111">Querying</span></span>

<span data-ttu-id="699d5-112">使用 Language Integrated Query (LINQ)，從資料庫中擷取實體類別執行個體。</span><span class="sxs-lookup"><span data-stu-id="699d5-112">Instances of your entity classes are retrieved from the database using Language Integrated Query (LINQ).</span></span> <span data-ttu-id="699d5-113">若要深入了解，請參閱[查詢資料](querying/index.md)。</span><span class="sxs-lookup"><span data-stu-id="699d5-113">See [Querying Data](querying/index.md) to learn more.</span></span>

``` csharp
using (var db = new BloggingContext())
{
    var blogs = db.Blogs
        .Where(b => b.Rating > 3)
        .OrderBy(b => b.Url)
        .ToList();
}
```

## <a name="saving-data"></a><span data-ttu-id="699d5-114">儲存資料</span><span class="sxs-lookup"><span data-stu-id="699d5-114">Saving Data</span></span>

<span data-ttu-id="699d5-115">使用您實體類別的執行個體，建立、刪除和修改資料庫中的資料。</span><span class="sxs-lookup"><span data-stu-id="699d5-115">Data is created, deleted, and modified in the database using instances of your entity classes.</span></span> <span data-ttu-id="699d5-116">若要深入了解，請參閱[儲存資料](saving/index.md)。</span><span class="sxs-lookup"><span data-stu-id="699d5-116">See [Saving Data](saving/index.md) to learn more.</span></span>

``` csharp
using (var db = new BloggingContext())
{
    var blog = new Blog { Url = "http://sample.com" };
    db.Blogs.Add(blog);
    db.SaveChanges();
}
```

## <a name="next-steps"></a><span data-ttu-id="699d5-117">後續步驟</span><span class="sxs-lookup"><span data-stu-id="699d5-117">Next steps</span></span>

<span data-ttu-id="699d5-118">如需簡介教學課程，請參閱 [Entity Framework Core 使用者入門](get-started/index.md)。</span><span class="sxs-lookup"><span data-stu-id="699d5-118">For introductory tutorials, see [Getting Started with Entity Framework Core](get-started/index.md).</span></span>

