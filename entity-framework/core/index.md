---
title: 概觀 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: bc2a2676-bc46-493f-bf49-e3cc97994d57
uid: core/index
ms.openlocfilehash: d9fcafb35248b1af54e1ac707e2ff7d4e80e4aa2
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995647"
---
# <a name="entity-framework-core"></a><span data-ttu-id="016fc-102">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="016fc-102">Entity Framework Core</span></span>

<span data-ttu-id="016fc-103">Entity Framework (EF) Core 是常見 Entity Framework 資料存取技術的輕量型、可擴充且跨平台版本。</span><span class="sxs-lookup"><span data-stu-id="016fc-103">Entity Framework (EF) Core is a lightweight, extensible, and cross-platform version of the popular Entity Framework data access technology.</span></span>

<span data-ttu-id="016fc-104">EF Core 可以當成物件關聯式對應程式 (O/RM)，讓 .NET 開發人員使用 .NET 物件來處理資料庫，而可以省略大部分以往必須自行撰寫的資料存取程式碼。</span><span class="sxs-lookup"><span data-stu-id="016fc-104">EF Core can serve as an object-relational mapper (O/RM), enabling .NET developers to work with a database using .NET objects, and eliminating the need for most of the data-access code they usually need to write.</span></span>

<span data-ttu-id="016fc-105">EF Core 支援許多資料庫引擎，如需詳細資料，請參閱[資料庫提供者](providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="016fc-105">EF Core supports many database engines, see [Database Providers](providers/index.md) for details.</span></span>

<span data-ttu-id="016fc-106">如果您要了解如何撰寫程式碼，則建議使用我們的其中一個[快速入門](get-started/index.md)指南開始使用 EF Core。</span><span class="sxs-lookup"><span data-stu-id="016fc-106">If you like to learn by writing code, we'd recommend one of our [Getting Started](get-started/index.md) guides to get you started with EF Core.</span></span>

## <a name="what-is-new-in-ef-core"></a><span data-ttu-id="016fc-107">EF Core 的新功能</span><span class="sxs-lookup"><span data-stu-id="016fc-107">What is new in EF Core</span></span>

<span data-ttu-id="016fc-108">若您熟悉 EF Core，並且想要直接跳到最新版本的詳細資料：</span><span class="sxs-lookup"><span data-stu-id="016fc-108">If you are familiar with EF Core and want to jump straight into the details of the latest releases:</span></span>

- <span data-ttu-id="016fc-109">**[EF Core 2.1 的新功能](xref:core/what-is-new/ef-core-2.1)**</span><span class="sxs-lookup"><span data-stu-id="016fc-109">**[What is new in EF Core 2.1](xref:core/what-is-new/ef-core-2.1)**</span></span>
- <span data-ttu-id="016fc-110">**[將現有應用程式升級為 EF Core 2.x](xref:core/miscellaneous/1x-2x-upgrade)**</span><span class="sxs-lookup"><span data-stu-id="016fc-110">**[Upgrading existing applications to EF Core 2.x](xref:core/miscellaneous/1x-2x-upgrade)**</span></span>


## <a name="get-entity-framework-core"></a><span data-ttu-id="016fc-111">取得 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="016fc-111">Get Entity Framework Core</span></span>

<span data-ttu-id="016fc-112">請針對您想要使用的資料庫提供者，[安裝 NuGet 套件](https://docs.nuget.org/ndocs/quickstart/use-a-package)。</span><span class="sxs-lookup"><span data-stu-id="016fc-112">[Install the NuGet package](https://docs.nuget.org/ndocs/quickstart/use-a-package) for the database provider you want to use.</span></span> <span data-ttu-id="016fc-113">例如，在命令列中使用 `dotnet` 工具，於跨平台開發中安裝 SQL Server 提供者：</span><span class="sxs-lookup"><span data-stu-id="016fc-113">For example, to install the SQL Server provider in cross-platform development using `dotnet` tool in the command line:</span></span>

``` Console
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="016fc-114">或者，在 Visual Studio 中，使用套件管理員主控台：</span><span class="sxs-lookup"><span data-stu-id="016fc-114">Or in Visual Studio, using the Package Manager Console:</span></span>

``` PowerShell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```
<span data-ttu-id="016fc-115">如需可用提供者的資訊，請參閱[資料庫提供者](providers/index.md)；如需詳細安裝步驟，請參閱[安裝 EF Core](get-started/install/index.md)。</span><span class="sxs-lookup"><span data-stu-id="016fc-115">See [Database Providers](providers/index.md) for information on available providers and [Installing EF Core](get-started/install/index.md) for more detailed installation steps.</span></span>

## <a name="the-model"></a><span data-ttu-id="016fc-116">模型</span><span class="sxs-lookup"><span data-stu-id="016fc-116">The Model</span></span>

<span data-ttu-id="016fc-117">運用 EF Core，使用模型來執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="016fc-117">With EF Core, data access is performed using a model.</span></span> <span data-ttu-id="016fc-118">模型包含多個實體類別以及一個代表含資料庫之工作階段的衍生內容，可讓您查詢和儲存資料。</span><span class="sxs-lookup"><span data-stu-id="016fc-118">A model is made up of entity classes and a derived context that represents a session with the database, allowing you to query and save data.</span></span> <span data-ttu-id="016fc-119">若要深入了解，請參閱[建立模型](modeling/index.md)。</span><span class="sxs-lookup"><span data-stu-id="016fc-119">See [Creating a Model](modeling/index.md) to learn more.</span></span>

<span data-ttu-id="016fc-120">您可以從現有資料庫產生模型、撰寫符合您資料庫模型的程式碼，或使用 EF 移轉從您的模型建立資料庫 (它會隨著您模型一段時間的變更逐步形成)。</span><span class="sxs-lookup"><span data-stu-id="016fc-120">You can generate a model from an existing database, hand code a model to match your database, or use EF Migrations to create a database from your model (and evolve it as your model changes over time).</span></span>

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
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=MyDatabase;Trusted_Connection=True;");
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

## <a name="querying"></a><span data-ttu-id="016fc-121">查詢</span><span class="sxs-lookup"><span data-stu-id="016fc-121">Querying</span></span>

<span data-ttu-id="016fc-122">使用 Language Integrated Query (LINQ)，從資料庫中擷取實體類別執行個體。</span><span class="sxs-lookup"><span data-stu-id="016fc-122">Instances of your entity classes are retrieved from the database using Language Integrated Query (LINQ).</span></span> <span data-ttu-id="016fc-123">若要深入了解，請參閱[查詢資料](querying/index.md)。</span><span class="sxs-lookup"><span data-stu-id="016fc-123">See [Querying Data](querying/index.md) to learn more.</span></span>

``` csharp
using (var db = new BloggingContext())
{
    var blogs = db.Blogs
        .Where(b => b.Rating > 3)
        .OrderBy(b => b.Url)
        .ToList();
}
```

## <a name="saving-data"></a><span data-ttu-id="016fc-124">儲存資料</span><span class="sxs-lookup"><span data-stu-id="016fc-124">Saving Data</span></span>

<span data-ttu-id="016fc-125">使用您實體類別的執行個體，建立、刪除和修改資料庫中的資料。</span><span class="sxs-lookup"><span data-stu-id="016fc-125">Data is created, deleted, and modified in the database using instances of your entity classes.</span></span> <span data-ttu-id="016fc-126">若要深入了解，請參閱[儲存資料](saving/index.md)。</span><span class="sxs-lookup"><span data-stu-id="016fc-126">See [Saving Data](saving/index.md) to learn more.</span></span>

``` csharp
using (var db = new BloggingContext())
{
    var blog = new Blog { Url = "http://sample.com" };
    db.Blogs.Add(blog);
    db.SaveChanges();
}
```
