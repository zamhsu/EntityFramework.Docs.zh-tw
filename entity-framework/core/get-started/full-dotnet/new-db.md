---
title: .NET Framework 快速入門 - 新資料庫 - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 52b69727-ded9-4a7b-b8d5-73f3acfbbad3
ms.technology: entity-framework-core
uid: core/get-started/full-dotnet/new-db
ms.openlocfilehash: bd7054c6834ae11bfdc352d63654e4304771e432
ms.sourcegitcommit: 507a40ed050fee957bcf8cf05f6e0ec8a3b1a363
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2018
---
# <a name="getting-started-with-ef-core-on-net-framework-with-a-new-database"></a>在 .NET Framework 上使用 EF Core 搭配新資料庫的使用者入門

在本逐步解說中，您將建置主控台應用程式，該應用程式將使用 Entity Framework 對 Microsoft SQL Server 資料庫執行基本資料存取。 您將使用移轉，以便從您的模型來建立資料庫。

> [!TIP]  
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/FullNet/ConsoleApp.NewDb) \(英文\)。

## <a name="prerequisites"></a>必要條件

若要完成此逐步解說，必須符合下列必要條件：

* [Visual Studio 2017](https://www.visualstudio.com/downloads/)

* [最新版本的 NuGet 套件管理員](https://dist.nuget.org/index.html) \(英文\)

* [最新版本的 Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell)

## <a name="create-a-new-project"></a>建立新專案

* 開啟 Visual Studio

* [檔案] > [新增] > [專案]...

* 從左側功能表選取 [範本] > [Visual C#] > [Windows 傳統桌面]

* 選取 [主控台應用程式 (.NET Framework)] 專案範本

* 請確認您的目標是 **.NET Framework 4.5.1** 或更新版本

* 提供專案名稱，然後按一下 [確定]

## <a name="install-entity-framework"></a>安裝 Entity Framework

若要使用 EF Core，請針對您要作為目標的資料庫提供者來安裝套件。 本逐步解說會使用 SQL Server。 如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。

* [工具] > [NuGet 套件管理員] > [套件管理員主控台]

* 執行 `Install-Package Microsoft.EntityFrameworkCore.SqlServer`

稍後在本逐步解說中，我們將使用部分 Entity Framework 工具來維護資料庫。 因此，我們也將安裝工具套件。

* 執行 `Install-Package Microsoft.EntityFrameworkCore.Tools`

## <a name="create-your-model"></a>建立您的模型

現在就來定義組成模型的內容和實體類別。

* [專案] > [加入類別]...

* 輸入 *Model.cs* 作為名稱，然後按一下 [確定]

* 以下列程式碼來取代檔案的內容

<!-- [!code-csharp[Main](samples/core/GetStarted/FullNet/ConsoleApp.NewDb/Model.cs)] -->
``` csharp
using Microsoft.EntityFrameworkCore;
using System.Collections.Generic;

namespace EFGetStarted.ConsoleApp
{
    public class BloggingContext : DbContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;");
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

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
}
```

> [!TIP]  
> 在實際的應用程式中，您會將每個類別放在個別的檔案中，然後將連接字串放在 `App.Config` 檔案中，並使用 `ConfigurationManager` 來讀取。 為了簡單起見，我們在此教學課程中將所有項目都放在單一程式碼檔案中。

## <a name="create-your-database"></a>建立您的資料庫

一旦有模型之後，就可以使用移轉來建立資料庫。

* [工具] –> [NuGet 套件管理員] –> [套件管理員主控台]

* 執行 `Add-Migration MyFirstMigration` 來建立移轉的結構，以針對您的模型建立一組初始的資料表。

* 執行 `Update-Database` 以將新的移轉套用至資料庫。 因為您的資料庫尚未存在，因此會在套用移轉之前為您建立。

> [!TIP]  
> 如果日後要對您的模型進行變更，則可以使用 `Add-Migration` 命令來建立新移轉的結構，以對資料庫做出相對應的結構描述變更。 檢查完已建立結構的程式碼之後 (並進行任何必要的變更)，就可以使用 `Update-Database` 命令將變更套用至資料庫。
>
>EF 在資料庫中使用 `__EFMigrationsHistory` 資料表，以確認已經套用至資料庫的移轉。

## <a name="use-your-model"></a>使用您的模型

您現在可以使用您的模型來執行資料存取。

* 開啟 *Program.cs*

* 以下列程式碼來取代檔案的內容

<!-- [!code-csharp[Main](samples/core/GetStarted/FullNet/ConsoleApp.NewDb/Program.cs)] -->
``` csharp
using System;

namespace EFGetStarted.ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            using (var db = new BloggingContext())
            {
                db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/adonet" });
                var count = db.SaveChanges();
                Console.WriteLine("{0} records saved to database", count);

                Console.WriteLine();
                Console.WriteLine("All blogs in database:");
                foreach (var blog in db.Blogs)
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

![影像](_static/output-new-db.png)
