---
title: "快速概觀 - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: bc2a2676-bc46-493f-bf49-e3cc97994d57
ms.technology: entity-framework-core
uid: core/index
ms.openlocfilehash: c76b4cd318151b502c549fa0a82800f9987ed94c
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="entity-framework-core-quick-overview"></a>Entity Framework Core 快速概觀

Entity Framework (EF) Core 是常見 Entity Framework 資料存取技術的輕量型、可擴充且跨平台版本。

EF Core 是一種物件關聯式對應程式 (O/RM)，可讓 .NET 開發人員使用 .NET 物件來處理資料庫。 它不需要開發人員通常需要撰寫的大部分資料存取程式碼。 EF Core 支援許多資料庫引擎，如需詳細資料，請參閱[資料庫提供者](providers/index.md)。

如果您要了解如何撰寫程式碼，則建議使用我們的其中一個[快速入門](get-started/index.md)指南開始使用 EF Core。

## <a name="what-is-new-in-ef-core"></a>EF Core 的新功能

若您熟悉 EF Core，並且想要直接跳到最新版本的詳細資料：

- **[EF Core 2.1 的新功能 (目前處於預覽階段)](xref:core/what-is-new/ef-core-2.1)**
- **[EF Core 2.0 的新功能 (最新發行版本)](xref:core/what-is-new/ef-core-2.0)**
- **[將現有應用程式升級為 EF Core 2.0](xref:core/miscellaneous/1x-2x-upgrade)**


## <a name="get-entity-framework-core"></a>取得 Entity Framework Core

請針對您想要使用的資料庫提供者，[安裝 NuGet 套件](https://docs.nuget.org/ndocs/quickstart/use-a-package)。 例如， 在命令列中使用 `dotnet` 工具，於跨平台開發中安裝 SQL Server 提供者：

``` Console
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

或者，在 Visual Studio 中，使用套件管理員主控台：

``` PowerShell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```
如需可用提供者的資訊，請參閱[資料庫提供者](providers/index.md)；如需詳細安裝步驟，請參閱[安裝 EF Core](get-started/install/index.md)。

## <a name="the-model"></a>模型

運用 EF Core，使用模型來執行資料存取。 模型包含多個實體類別以及一個代表含資料庫之工作階段的衍生內容，可讓您查詢和儲存資料。 若要深入了解，請參閱[建立模型](modeling/index.md)。

您可以從現有資料庫產生模型、撰寫符合您資料庫模型的程式碼，或使用 EF 移轉從您的模型建立資料庫 (它會隨著您模型一段時間的變更逐步形成)。

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

## <a name="querying"></a>查詢

使用 Language Integrated Query (LINQ)，從資料庫中擷取實體類別執行個體。 若要深入了解，請參閱[查詢資料](querying/index.md)。

``` csharp
using (var db = new BloggingContext())
{
    var blogs = db.Blogs
        .Where(b => b.Rating > 3)
        .OrderBy(b => b.Url)
        .ToList();
}
```

## <a name="saving-data"></a>儲存資料

使用您實體類別的執行個體，建立、刪除和修改資料庫中的資料。 若要深入了解，請參閱[儲存資料](saving/index.md)。

``` csharp
using (var db = new BloggingContext())
{
    var blog = new Blog { Url = "http://sample.com" };
    db.Blogs.Add(blog);
    db.SaveChanges();
}
```
