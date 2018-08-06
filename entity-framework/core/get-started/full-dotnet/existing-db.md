---
title: .NET Framework 快速入門 - 現有的資料庫 - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: a29a3d97-b2d8-4d33-9475-40ac67b3b2c6
ms.technology: entity-framework-core
uid: core/get-started/full-dotnet/existing-db
ms.openlocfilehash: 39e77ab8c124df67458cc5fa6db2882b65943ebe
ms.sourcegitcommit: 4467032fd6ca223e5965b59912d74cf88a1dd77f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2018
ms.locfileid: "39388464"
---
# <a name="getting-started-with-ef-core-on-net-framework-with-an-existing-database"></a>在 .NET Framework 上使用 EF Core 搭配現有資料庫的使用者入門

在本逐步解說中，您將建置主控台應用程式，該應用程式將使用 Entity Framework 對 Microsoft SQL Server 資料庫執行基本資料存取。 您將會使用反向工程，根據現有的資料庫來建立 Entity Framework 模型。

> [!TIP]  
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb) \(英文\)。

## <a name="prerequisites"></a>必要條件

若要完成此逐步解說，必須符合下列必要條件：

* [Visual Studio 2017](https://www.visualstudio.com/downloads/) - 至少 15.3 版

* [最新版本的 NuGet 套件管理員](https://dist.nuget.org/index.html) \(英文\)

* [最新版本的 Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell)

* [部落格資料庫](#blogging-database)

### <a name="blogging-database"></a>部落格資料庫

本教學課程使用您 LocalDb 執行個體上的**部落格**資料庫作為現有的資料庫。

> [!TIP]  
> 如果您在另一個教學課程中已經建立**部落格**資料庫，則可以跳過這些步驟。

* 開啟 Visual Studio

* [工具] > [連線到資料庫]...

* 選取 [Microsoft SQL Server]，並按一下 [繼續]

* 輸入 **(localdb)\mssqllocaldb** 作為**伺服器名稱**

* 輸入 **master** 作為**資料庫名稱**，並按一下 [確定]

* master 資料庫現在會顯示在 [伺服器總管] 中的 [資料連線] 下

* 以滑鼠右鍵按一下 [伺服器總管] 中的資料庫，並選取 [新增查詢]

* 將下面列出的指令碼複製到查詢編輯器中

* 以滑鼠右鍵按一下查詢編輯器，然後選取 [執行]

[!code-sql[Main](../_shared/create-blogging-database-script.sql)]

## <a name="create-a-new-project"></a>建立新專案

* 開啟 Visual Studio

* [檔案] > [新增] > [專案]...

* 從左側功能表選取 [範本] > [Visual C#] > [Windows]

* 選取 [主控台應用程式] 專案範本

* 請確認您的目標為 **.NET Framework 4.6.1** 或更新版本

* 提供專案名稱，然後按一下 [確定]

## <a name="install-entity-framework"></a>安裝 Entity Framework

若要使用 EF Core，請針對您要作為目標的資料庫提供者來安裝套件。 本逐步解說會使用 SQL Server。 如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。

* [工具] > [NuGet 套件管理員] > [套件管理員主控台]

* 執行 `Install-Package Microsoft.EntityFrameworkCore.SqlServer`

為了要從現有的資料庫啟用反向工程，我們也需要安裝幾個其他的套件。

* 執行 `Install-Package Microsoft.EntityFrameworkCore.Tools`

## <a name="reverse-engineer-your-model"></a>針對您的模型進行反向工程

現在就可以根據您現有的資料庫來建立 EF 模型。

* [工具] –> [NuGet 套件管理員] –> [套件管理員主控台]

* 執行下列命令，以便從現有的資料庫來建立模型

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

反向工程程序會根據現有資料庫的結構描述，建立實體類別和衍生的內容。 實體類別是簡單的 C# 物件，代表您要查詢和儲存的資料。

<!-- [!code-csharp[Main](samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/Blog.cs)] -->
``` csharp
using System;
using System.Collections.Generic;

namespace EFGetStarted.ConsoleApp.ExistingDb
{
    public partial class Blog
    {
        public Blog()
        {
            Post = new HashSet<Post>();
        }

        public int BlogId { get; set; }
        public string Url { get; set; }

        public virtual ICollection<Post> Post { get; set; }
    }
}
```

內容代表資料庫的工作階段，並可讓您查詢和儲存實體類別的執行個體。

<!-- [!code-csharp[Main](samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/BloggingContext.cs)] -->
``` csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Metadata;

namespace EFGetStarted.ConsoleApp.ExistingDb
{
    public partial class BloggingContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            #warning To protect potentially sensitive information in your connection string, you should move it out of source code. See http://go.microsoft.com/fwlink/?LinkId=723263 for guidance on storing connection strings.
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;");
        }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Blog>(entity =>
            {
                entity.Property(e => e.Url).IsRequired();
            });

            modelBuilder.Entity<Post>(entity =>
            {
                entity.HasOne(d => d.Blog)
                    .WithMany(p => p.Post)
                    .HasForeignKey(d => d.BlogId);
            });
        }

        public virtual DbSet<Blog> Blog { get; set; }
        public virtual DbSet<Post> Post { get; set; }
    }
}
```

## <a name="use-your-model"></a>使用您的模型

您現在可以使用您的模型來執行資料存取。

* 開啟 *Program.cs*

* 以下列程式碼來取代檔案的內容

<!-- [!code-csharp[Main](samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/Program.cs)] -->
``` csharp
using System;

namespace EFGetStarted.ConsoleApp.ExistingDb
{
    class Program
    {
        static void Main(string[] args)
        {
            using (var db = new BloggingContext())
            {
                db.Blog.Add(new Blog { Url = "http://blogs.msdn.com/adonet" });
                var count = db.SaveChanges();
                Console.WriteLine("{0} records saved to database", count);

                Console.WriteLine();
                Console.WriteLine("All blogs in database:");
                foreach (var blog in db.Blog)
                {
                    Console.WriteLine(" - {0}", blog.Url);
                }
            }
        }
    }
}
```

* 偵錯 > 啟動但不偵錯

您會看到有一個部落格儲存至資料庫，然後所有部落格的詳細資料會列印至主控台。

![影像](_static/output-existing-db.png)
