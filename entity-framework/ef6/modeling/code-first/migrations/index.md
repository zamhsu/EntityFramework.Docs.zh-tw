---
title: Code First 移轉 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 36591d8f-36e1-4835-8a51-90f34f633d1e
ms.openlocfilehash: e5a91af73bab9d45b0f1f4242ce503c6b6f407f6
ms.sourcegitcommit: 159c2e9afed7745e7512730ffffaf154bcf2ff4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/03/2019
ms.locfileid: "55668696"
---
# <a name="code-first-migrations"></a>Code First 移轉
如果您使用 Code First 工作流程，則建議使用 Code First 移轉來改善應用程式的資料庫結構描述。 移轉會提供一組工具，該工具允許下列項目：

1. 建立使用 EF 模型的初始資料庫
2. 產生移轉來追蹤您對 EF 模型進行的變更
2. 使用這些變更來將您的資料庫保持最新

下列逐步解說將提供 Entity Framework 中的 Code First 移轉概觀。 您可以完成整個逐步解說，或跳至您感興趣的主題。 其中包含下列主題：

## <a name="building-an-initial-model--database"></a>建置初始模型與資料庫

在開始使用移轉之前，我們需要一個專案和一個 Code First 模型。 此逐步解說中我們將使用標準 **Blog** 和 **Post** 模型。

-   建立新 **MigrationsDemo** 主控台應用程式
-   將最新版本 **EntityFramework** NuGet 套件新增至專案
    -   [工具] –&gt; [程式庫套件管理員] –&gt; [套件管理員主控台]
    -   執行 **Install-package EntityFramework** 命令
-   使用如下所示的程式碼新增 **Model.cs** 檔案。 此程式碼會定義構成我們網域模型的單一 **Blog** 類別，以及作為 EF Code First 內容的 **BlogContext** 類別

  ``` csharp
      using System.Data.Entity;
      using System.Collections.Generic;
      using System.ComponentModel.DataAnnotations;
      using System.Data.Entity.Infrastructure;

      namespace MigrationsDemo
      {
          public class BlogContext : DbContext
          {
              public DbSet<Blog> Blogs { get; set; }
          }

          public class Blog
          {
              public int BlogId { get; set; }
              public string Name { get; set; }
          }
      }
  ```

-   現在我們有了一個模型，即可用來執行資料存取。 使用如下所示的程式碼更新 **Program.cs** 檔案。

  ``` csharp
      using System;
      using System.Collections.Generic;
      using System.Linq;
      using System.Text;

      namespace MigrationsDemo
      {
          class Program
          {
              static void Main(string[] args)
              {
                  using (var db = new BlogContext())
                  {
                      db.Blogs.Add(new Blog { Name = "Another Blog " });
                      db.SaveChanges();

                      foreach (var blog in db.Blogs)
                      {
                          Console.WriteLine(blog.Name);
                      }
                  }

                  Console.WriteLine("Press any key to exit...");
                  Console.ReadKey();
              }
          }
      }
  ```

-   執行您的應用程式，而且您將會看到 **MigrationsCodeDemo.BlogContext** 資料庫已建立。

    ![資料庫  LocalDB](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a>啟用移轉

現在可以對我們的模型做更多變更。

-   請將一個 URL 屬性引進 Blog 類別。

``` csharp
    public string Url { get; set; }
```

如果您再次執行該應用程式，您將會收到 InvalidOperationException，指出*自資料庫建立以來，支援 'BlogContext' 內容的模型已變更。請考慮使用 Code First 移轉來更新資料庫」(* [*http://go.microsoft.com/fwlink/?LinkId=238269*](https://go.microsoft.com/fwlink/?LinkId=238269)*)。*

如例外狀況所示，現在可以開始使用 Code First 移轉。 第一個步驟是為內容啟用移轉。

-   在套件管理員主控台中執行 **Enable-migrations** 命令

    此命令會將 **Migrations** 資料夾新增至專案。 這個新資料夾包含兩個檔案：

-   **組態類別**。 這個類別可讓您設定移轉對內容的運作方式。 在此逐步解說中，我們將只使用預設組態。
    「由於專案中只有一個單一 Code First 內容，因此 Enable-Migrations 已自動填入此組態適用的內容類型。」
-   **InitialCreate 移轉**。 產生此移轉是因為在我們啟用移轉之前，Code First 已為我們建立了資料庫。 這個包含 scaffold 移轉中的程式碼，表示已在資料庫中建立的物件。 在本案例中，即是包含 **BlogId** 和 **Name** 資料行的 **Blog** 資料表。 檔案名稱包含時間戳記，以協助排序。
    「如果尚未建立資料庫，則不會將此 InitialCreate 移轉新增至專案中。反之，在我們第一次呼叫 Add-Migration 時，建立這些資料表的程式碼將會建立至新的移轉中。」

### <a name="multiple-models-targeting-the-same-database"></a>針對相同資料庫的多個模型

使用 EF6 之前的版本時，只能使用一個 Code First 模型來產生和管理資料庫結構描述。 這是每個資料庫單一 **\_\_MigrationsHistory** 資料表的結果，無法識別哪些項目屬於哪一個模型。

從 EF6 開始，**Configuration** 類別包含 **ContextKey** 屬性。 這可以作為每個 Code First 模型的唯一識別碼。 **\_\_MigrationsHistory** 資料表中的對應資料行，允許來自多個模型的項目共用該資料表。 根據預設，此屬性設定為內容的完整名稱。

## <a name="generating--running-migrations"></a>產生及執行移轉

Code First 移轉有兩個您將先熟悉的主要命令。

-   **Add-Migration** 將根據自上次建立移轉以來您對模型所做的變更，來建立下一次移轉
-   **Update-Database** 會將任何擱置的移轉套用至資料庫

我們需要建立移轉以處理我們新增的新 URL 屬性。 **Add-migration** 命令可讓我們為這些移轉命名，現在我們將其命名為 **AddBlogUrl**。

-   在套件管理員主控台中執行 **Add-Migration AddBlogUrl** 命令
-   現在，我們在 **Migrations** 資料夾中已經具有 **AddBlogUrl** 移轉。 移轉檔案名稱已前置時間戳記，以協助排序

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddBlogUrl : DbMigration
        {
            public override void Up()
            {
                AddColumn("dbo.Blogs", "Url", c => c.String());
            }

            public override void Down()
            {
                DropColumn("dbo.Blogs", "Url");
            }
        }
    }
```

現在我們可以編輯或新增項目至此移轉，但目前一切看來良好。 讓我們使用 **Update-database** 來將此移轉套用至資料庫。

-   在套件管理員主控台中執行 **Update-Database** 命令
-   Code First 移轉將會比較 **Migrations** 資料夾中與已套用至資料庫的移轉。 它會發現 **AddBlogUrl** 移轉必須先套用，並執行該移轉。

**MigrationsDemo.BlogContext** 資料庫現在已經更新，並已包含 **Blog** 資料表中的 **Url** 資料行。

## <a name="customizing-migrations"></a>自訂移轉

到目前為止，我們已在不進行任何變更的情況下產生並執行了移轉。 現在讓我們看看編輯預設產生的程式碼。

-   現在來對我們的模型進行更多變更，讓我們將新的 **Rating** 屬性新增至 **Blog** 類別

``` csharp
    public int Rating { get; set; }
```

-   同時也新增新的 **Post** 類別

``` csharp
    public class Post
    {
        public int PostId { get; set; }
        [MaxLength(200)]
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
```

-   我們也會將 **Post** 集合新增至 **Blog** 類別，以構成 **Blog** 和 **Post** 之間關係的另一端

``` csharp
    public virtual List<Post> Posts { get; set; }
```

我們將使用 **Add-migration** 命令，讓 Code First 移轉為我們建立其認為最適合的移轉。 我們將這項移轉稱為 **AddPostClass**。

-   在套件管理員主控台中執行 **Add-Migration AddPostClass** 命令。

Code First 移轉在建立這些變更方面做得非常好，但我們可能仍想要進行某些變更：

1.  首先，讓我們為 **Posts.Title** 資料行新增唯一索引 (在下列程式碼中的第 22 和 29 行中新增)。
2.  我們也將新增一個不可為 null 的 **Blogs.Rating** 資料行。 如果資料表中沒有任何現有資料，則會為新資料行指派資料類型的 CLR 預設值 (Rating 為整數，因此為 **0**)。 但我們應指定預設值為 **3**，以便 **Blog** 資料表中的現有資料列以適當的評等開頭。
    (您可以看到下列程式碼第 24 行指定的預設值)

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddPostClass : DbMigration
        {
            public override void Up()
            {
                CreateTable(
                    "dbo.Posts",
                    c => new
                        {
                            PostId = c.Int(nullable: false, identity: true),
                            Title = c.String(maxLength: 200),
                            Content = c.String(),
                            BlogId = c.Int(nullable: false),
                        })
                    .PrimaryKey(t => t.PostId)
                    .ForeignKey("dbo.Blogs", t => t.BlogId, cascadeDelete: true)
                    .Index(t => t.BlogId)
                    .Index(p => p.Title, unique: true);

                AddColumn("dbo.Blogs", "Rating", c => c.Int(nullable: false, defaultValue: 3));
            }

            public override void Down()
            {
                DropIndex("dbo.Posts", new[] { "Title" });
                DropIndex("dbo.Posts", new[] { "BlogId" });
                DropForeignKey("dbo.Posts", "BlogId", "dbo.Blogs");
                DropColumn("dbo.Blogs", "Rating");
                DropTable("dbo.Posts");
            }
        }
    }
```

已編輯的移轉已準備好，現在請使用 **Update-Database** 將資料庫保持最新狀態。 這次讓我們指定 **–Verbose** 旗標，讓您可以看到 Code First 移轉正在執行的 SQL。

-   在套件管理員主控台中執行 **Update-Database –Verbose** 命令。

## <a name="data-motion--custom-sql"></a>資料動作/自訂 SQL

到目前為止，我們已經探討了不變更或移動任何資料的移轉作業，現在讓我們看一下需要移動某些資料的部分。 目前還沒有對資料動作的原生支援，但我們可以在指令碼的任何位置上執行一些任意 SQL 命令。

-   讓我們將 **Post.Abstract** 屬性新增至模型。 稍後，我們將使用 **Content** 資料行開頭的一些文字，為現有文章預先填入 **Abstract**。

``` csharp
    public string Abstract { get; set; }
```

我們將使用 **Add-migration** 命令，讓 Code First 移轉為我們建立其認為最適合的移轉。

-   在套件管理員主控台中執行 **Add-Migration AddPostAbstract** 命令。
-   產生的移轉會處理結構描述變更，但我們也希望為每個文章使用其內容的前 100 個字元來預先填入 **Abstract** 資料行。 我們可以在新增資料行後，透過下拉到 SQL 並執行 **UPDATE** 陳述式來完成此操作。
    (在以下的程式碼第 12 行中新增)

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddPostAbstract : DbMigration
        {
            public override void Up()
            {
                AddColumn("dbo.Posts", "Abstract", c => c.String());

                Sql("UPDATE dbo.Posts SET Abstract = LEFT(Content, 100) WHERE Abstract IS NULL");
            }

            public override void Down()
            {
                DropColumn("dbo.Posts", "Abstract");
            }
        }
    }
```

已編輯的移轉看起來一切良好，現在請使用 **Update-Database** 將資料庫保持最新狀態。 我們將會指定 **–Verbose** 旗標，讓我們能夠查看為資料庫執行的 SQL。

-   在套件管理員主控台中執行 **Update-Database –Verbose** 命令。

## <a name="migrate-to-a-specific-version-including-downgrade"></a>移轉至特定版本 (包括降級)

到目前為止我們已一律升級至最新的移轉，但有時您可能會希望升級或降級至特定的移轉。

假設我們想要在執行 **AddBlogUrl** 移轉後，將資料庫移轉到其原先的狀態。 我們可以使用 **–TargetMigration** 參數來降級至此移轉。

-   在套件管理員主控台中執行 **Update-Database –TargetMigration:AddBlogUrl** 命令。

此命令會為我們的  **AddBlogAbstract** 和  **AddPostClass** 移轉執行向下指令碼。

如果您想要一路向前復原為空白資料庫，則您可以使用 **Update-Database –TargetMigration: $InitialDatabase** 命令。

## <a name="getting-a-sql-script"></a>取得 SQL 指令碼

如果其他開發人員希望在其電腦上進行這些變更，他們可以在我們檢查對原始檔控制的變更後同步。 一旦他們擁有了我們的新移轉，他們就可以執行 Update-Database 命令以在本機套用變更。 但是，如果我們想將這些變更推送至測試伺服器並生產，我們可能需要能夠遞交給 DBA 的 SQL 指令碼。

-   請執行 **Update-database** 命令，但這次請指定 **–Script** 旗標，以便將變更寫入至指令碼，而非套用。 我們也會指定來源和目標移轉以產生指令碼。 我們希望指令碼從空的資料庫 (**$InitialDatabase**) 移轉至最新版本 (移轉 **AddPostAbstract**)。
    「如果您未指定目標移轉，則移轉會使用最新的移轉作為目標。如果您未指定來源移轉，則移轉會使用資料庫的目前狀態。」
-   在套件管理員主控台中執行 **Update-Database -Script -SourceMigration: $InitialDatabase -TargetMigration:AddPostAbstract** 命令

Code First 移轉將執行移轉管線，但不會實際套用變更，而是將其寫入 .sql 檔案中。 一旦產生指令碼，即會在 Visual Studio 中開啟，供您檢視或儲存。

### <a name="generating-idempotent-scripts"></a>產生等冪指令碼

從 EF6 開始，如果指定 **–SourceMigration $InitialDatabase**，則產生的指令碼	將為「等冪」。 等冪指令碼可以將目前任何版本的資料庫升級至最新版本 (如果使用 **-TargetMigration**，則可以升級指定的版本)。 產生的指令碼包括用於檢查 **\_\_MigrationsHistory** 資料表的邏輯，並僅套用先前未套用的變更。

## <a name="automatically-upgrading-on-application-startup-migratedatabasetolatestversion-initializer"></a>在應用程式啟動時自動升級 (MigrateDatabaseToLatestVersion 初始設定式)

如果您要部署應用程式，則可能希望在應用程式啟動時自動升級資料庫 (透過套用任何擱置的移轉)。 您可以藉由註冊 **MigrateDatabaseToLatestVersion** 資料庫初始設定式來執行此操作。 資料庫初始設定式只會包含一些用來確定資料庫已正確設定的邏輯。 第一次在應用程式處理序 (**AppDomain**) 中使用內容時會執行此邏輯。

我們可以更新 **Program.cs** 檔案 (如下所示) 在我們使用內容 (第 14 行) 之前為 BlogContext 設定 **MigrateDatabaseToLatestVersion** 初始設定式。 請注意，您也需要為 **System.Data.Entity** 命名空間 (第 5 行) 新增使用陳述式。

當我們建立此初始設定式的執行個體時，我們需要指定內容類型 ( **BlogContext**) 和移轉組態 (**Configuration**) - 移轉組態是在我們啟用移轉時新增至 **Migrations** 資料夾的類別。

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Data.Entity;
    using MigrationsDemo.Migrations;

    namespace MigrationsDemo
    {
        class Program
        {
            static void Main(string[] args)
            {
                Database.SetInitializer(new MigrateDatabaseToLatestVersion<BlogContext, Configuration>());

                using (var db = new BlogContext())
                {
                    db.Blogs.Add(new Blog { Name = "Another Blog " });
                    db.SaveChanges();

                    foreach (var blog in db.Blogs)
                    {
                        Console.WriteLine(blog.Name);
                    }
                }

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }
        }
    }
```

現在每當我們應用程式執行時，將首先檢查其目標資料庫是否為最新，如果不是，則會套用任何擱置的移轉。
