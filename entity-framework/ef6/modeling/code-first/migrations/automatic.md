---
title: 自動的 Code First 移轉 EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 0eb86787-2161-4cb4-9cb8-67c5d6e95650
ms.openlocfilehash: 2713afaf09707b7696e90464aac9945c2d82d274
ms.sourcegitcommit: 269c8a1a457a9ad27b4026c22c4b1a76991fb360
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2018
ms.locfileid: "46283910"
---
# <a name="automatic-code-first-migrations"></a>自動的 Code First 移轉
自動移轉，可讓您使用 Code First 移轉，而不需要每個您所做的變更在您的專案中的程式碼檔案。 並非所有的變更可以自動套用-資料行重新命名，例如需要使用程式碼為基礎的移轉。

> [!NOTE]
> 本文假設您知道如何在基本案例中使用 Code First 移轉。 如果不這麼做，則您將需要閱讀[Code First 移轉](~/ef6/modeling/code-first/migrations/index.md)再繼續進行。

## <a name="recommendation-for-team-environments"></a>適用於小組環境建議

您可以顛倒自動和程式碼為基礎的移轉，但建議您不要在小組開發案例中。 如果您是使用原始檔控制的開發人員小組的一部分您應該使用完全自動移轉或純程式碼型的移轉。 指定的自動移轉的限制，我們建議在小組環境中使用程式碼為基礎的移轉。

## <a name="building-an-initial-model--database"></a>建置初始模型與資料庫

在開始使用移轉之前，我們需要一個專案和一個 Code First 模型。 此逐步解說中我們將使用標準 **Blog** 和 **Post** 模型。

-   建立新**MigrationsAutomaticDemo**主控台應用程式
-   將最新版本 **EntityFramework** NuGet 套件新增至專案
    -   [工具] –&gt; [程式庫套件管理員] –&gt; [套件管理員主控台]
    -   執行 **Install-package EntityFramework** 命令
-   使用如下所示的程式碼新增 **Model.cs** 檔案。 此程式碼會定義構成我們網域模型的單一 **Blog** 類別，以及作為 EF Code First 內容的 **BlogContext** 類別

  ``` csharp
      using System.Data.Entity;
      using System.Collections.Generic;
      using System.ComponentModel.DataAnnotations;
      using System.Data.Entity.Infrastructure;

      namespace MigrationsAutomaticDemo
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

      namespace MigrationsAutomaticDemo
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

-   執行您的應用程式，您會看到**MigrationsAutomaticCodeDemo.BlogContext**為您建立資料庫。

    ![資料庫 LocalDB](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a>啟用移轉

現在可以對我們的模型做更多變更。

-   請將一個 URL 屬性引進 Blog 類別。

``` csharp
    public string Url { get; set; }
```

如果您再次執行該應用程式，您將會收到 InvalidOperationException，指出*自資料庫建立以來，支援 'BlogContext' 內容的模型已變更。請考慮使用 Code First 移轉來更新資料庫」(* [*http://go.microsoft.com/fwlink/?LinkId=238269*](https://go.microsoft.com/fwlink/?LinkId=238269)*)。*

如例外狀況所示，現在可以開始使用 Code First 移轉。 因為我們想要使用自動移轉，所以我們要指定 **– EnableAutomaticMigrations**切換。

-   執行**Enable-migrations – EnableAutomaticMigrations**已新增在套件管理員主控台這個命令的命令**移轉**專案的資料夾。 這個新的資料夾包含一個檔案：

-   **組態類別**。 這個類別可讓您設定移轉對內容的運作方式。 在此逐步解說中，我們將只使用預設組態。
    「由於專案中只有一個單一 Code First 內容，因此 Enable-Migrations 已自動填入此組態適用的內容類型。」

 

## <a name="your-first-automatic-migration"></a>您第一次的自動移轉

Code First 移轉有兩個您將先熟悉的主要命令。

-   **Add-Migration** 將根據自上次建立移轉以來您對模型所做的變更，來建立下一次移轉
-   **Update-Database** 會將任何擱置的移轉套用至資料庫

我們要避免使用 Add-migration （除非我們真的需要），並著重於讓 Code First 移轉自動計算，並套用變更。 讓我們使用**Update-database**若要取得 Code First 移轉，將變更推送至我們的模型 (新**Blog.Ur**l 屬性) 到資料庫。

-   執行**Update-database**命令在套件管理員主控台。

**MigrationsAutomaticDemo.BlogContext**資料庫現在會更新以包含**Url**中的資料行**部落格**資料表。

 

## <a name="your-second-automatic-migration"></a>第二個的自動移轉

讓我們進行另一次變更，並讓我們，自動將變更推送至資料庫的 Code First 移轉。

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

現在，使用**Update-database**使資料庫保持最新狀態。 這次讓我們指定 **–Verbose** 旗標，讓您可以看到 Code First 移轉正在執行的 SQL。

-   在套件管理員主控台中執行 **-Verbose** 命令。

## <a name="adding-a-code-based-migration"></a>加入程式碼基礎的移轉

現在讓我們看看我們可能會想要使用的程式碼為基礎的移轉的項目。

-   讓我們新增**分級**屬性設**部落格**類別

``` csharp
    public int Rating { get; set; }
```

我們可以執行**Update-database**將這些變更推送至資料庫。 不過，我們將新增一個不可為 null **Blogs.Rating**資料行中，如果資料表中沒有任何現有的資料將會取得指派給它的 CLR 預設值是新的資料行的資料類型 (評等是整數，就是**0**). 但我們應指定預設值為 **3**，以便 **Blog** 資料表中的現有資料列以適當的評等開頭。
讓我們使用新增移轉命令，來撰寫出這項變更與程式碼為基礎的移轉，讓我們可以進行編輯。 **Add-migration**命令可讓我們為這些移轉的名稱，讓我們只要呼叫我們**AddBlogRating**。

-   執行**Add-migration AddBlogRating**命令在套件管理員主控台。
-   在 **移轉**資料夾，我們現在新增了**AddBlogRating**移轉。 預先移轉檔案名稱被固定時間戳記的來協助進行排序。 讓我們編輯產生的程式碼，以指定的預設值為 3 Blog.Rating (在下列程式碼中的行 10)

*移轉也都擷取一些中繼資料的程式碼後置檔案。此中繼資料可複寫我們執行此程式碼為基礎的移轉之前，先自動移轉的 Code First 移轉。如果另一個開發人員想要執行我們的移轉或部署我們的應用程式的時候，這很重要。*

``` csharp
    namespace MigrationsAutomaticDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddBlogRating : DbMigration
        {
            public override void Up()
            {
                AddColumn("Blogs", "Rating", c => c.Int(nullable: false, defaultValue: 3));
            }

            public override void Down()
            {
                DropColumn("Blogs", "Rating");
            }
        }
    }
```

已編輯的移轉看起來一切良好，現在請使用 **Update-Database** 將資料庫保持最新狀態。

-   執行**Update-database**命令在套件管理員主控台。

## <a name="back-to-automatic-migrations"></a>回到自動移轉

現在，我們可自行切換回到自動移轉，我們更簡單的變更。 Code First 移轉會負責執行正確的順序，根據它會將儲存在每個程式碼為基礎的移轉的程式碼後置檔案的中繼資料的自動與程式碼為基礎的移轉。

-   讓我們將 Post.Abstract 屬性新增至我們的模型

``` csharp
    public string Abstract { get; set; }
```

現在我們可以使用**Update-database**取得 Code First 移轉，將這項變更推送到使用自動移轉的資料庫。

-   執行**Update-database**命令在套件管理員主控台。

## <a name="summary"></a>總結

在本逐步解說，您可了解如何使用自動移轉，推送模型變更至資料庫。 您也看到了如何建立和執行程式碼型的移轉，之間自動移轉，當您需要更多的控制。
