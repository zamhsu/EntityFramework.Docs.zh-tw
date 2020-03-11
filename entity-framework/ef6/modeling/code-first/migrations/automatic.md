---
title: 自動 Code First 移轉-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 0eb86787-2161-4cb4-9cb8-67c5d6e95650
ms.openlocfilehash: 2713afaf09707b7696e90464aac9945c2d82d274
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78418997"
---
# <a name="automatic-code-first-migrations"></a>自動 Code First 移轉
自動遷移可讓您使用 Code First 移轉，而不需要在專案中針對您所做的每項變更進行程式碼檔案。 並非所有變更都可以自動套用，例如，資料行重新命名需要使用以程式碼為基礎的遷移。

> [!NOTE]
> 本文假設您知道如何在基本案例中使用 Code First 移轉。 如果您沒有這麼做，則必須先閱讀[Code First 移轉](~/ef6/modeling/code-first/migrations/index.md)，才能繼續進行。

## <a name="recommendation-for-team-environments"></a>小組環境的建議

您可以散置自動和以程式碼為基礎的遷移，但在小組開發案例中不建議這樣做。 如果您是使用原始檔控制的開發人員小組的一部分，您應該使用純粹的自動遷移或單純的程式碼式遷移。 由於自動遷移的限制，我們建議您在小組環境中使用以程式碼為基礎的遷移。

## <a name="building-an-initial-model--database"></a>建置初始模型與資料庫

在開始使用移轉之前，我們需要一個專案和一個 Code First 模型。 此逐步解說中我們將使用標準 **Blog** 和 **Post** 模型。

-   建立新的**MigrationsAutomaticDemo**主控台應用程式
-   將最新版本 **EntityFramework** NuGet 套件新增至專案
    -   [工具] – **[程式庫套件管理員] –&gt; [套件管理員主控台]&gt;**
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

-   執行您的應用程式，您會看到已為您建立**MigrationsAutomaticCodeDemo BlogCoNtext**資料庫。

    ![資料庫 LocalDB](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a>啟用移轉

現在可以對我們的模型做更多變更。

-   請將一個 URL 屬性引進 Blog 類別。

``` csharp
    public string Url { get; set; }
```

如果您再次執行應用程式，您會收到 InvalidOperationException，指出*支援「BlogCoNtext」內容的模型在建立資料庫之後已經變更。請考慮使用 Code First 移轉來更新資料庫（* [ *http://go.microsoft.com/fwlink/?LinkId=238269* ](https://go.microsoft.com/fwlink/?LinkId=238269) *）。*

如例外狀況所示，現在可以開始使用 Code First 移轉。 因為我們想要使用自動遷移，所以我們將指定 **– EnableAutomaticMigrations**參數。

-   在套件管理員主控台中執行**EnableAutomaticMigrations**命令，此命令已將 [**遷移**] 資料夾新增至我們的專案。 這個新資料夾包含一個檔案：

-   **組態類別**。 這個類別可讓您設定移轉對內容的運作方式。 在此逐步解說中，我們將只使用預設組態。
    「由於專案中只有一個單一 Code First 內容，因此 Enable-Migrations 已自動填入此組態適用的內容類型。」

 

## <a name="your-first-automatic-migration"></a>您的第一個自動遷移

Code First 移轉有兩個您將先熟悉的主要命令。

-   **Add-Migration** 將根據自上次建立移轉以來您對模型所做的變更，來建立下一次移轉
-   **Update-Database** 會將任何擱置的移轉套用至資料庫

我們會避免使用「新增-遷移」（除非我們真的需要），並將焦點放在讓 Code First 移轉自動計算及套用變更。 讓我們使用**Update-database**取得 Code First 移轉，將模型的變更（新的**Blog-Ur**l 屬性）推送到資料庫。

-   在 [套件管理員主控台] 中執行 [**更新-資料庫**] 命令。

**MigrationsAutomaticDemo. BlogCoNtext**資料庫現在已更新為包含 [ **blog** ] 資料表中的 [ **Url** ] 資料行。

 

## <a name="your-second-automatic-migration"></a>第二次自動遷移

讓我們進行另一個變更，讓 Code First 移轉自動將變更推送到資料庫。

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

現在使用**Update-database**使資料庫保持在最新狀態。 這次讓我們指定 **–Verbose** 旗標，讓您可以看到 Code First 移轉正在執行的 SQL。

-   在套件管理員主控台中執行 **-Verbose** 命令。

## <a name="adding-a-code-based-migration"></a>加入以程式碼為基礎的遷移

現在讓我們來看一下，我們可能會想要使用以程式碼為基礎的遷移。

-   讓我們將**評**等屬性新增至**Blog**類別

``` csharp
    public int Rating { get; set; }
```

我們可以直接執行**更新資料庫**，將這些變更推送到資料庫。 不過，我們會新增一個不可為 null 的**blog**資料行，如果資料表中有任何現有的資料，就會為新的資料行指派資料類型的 CLR 預設值（評等是整數，所以會是**0**）。 但我們應指定預設值為 **3**，以便 **Blog** 資料表中的現有資料列以適當的評等開頭。
讓我們使用「新增-遷移」命令將這種變更寫出至以程式碼為基礎的遷移，讓我們可以進行編輯。 「**新增-遷移**」命令可讓我們為這些遷移提供一個名稱，讓我們來呼叫我們的**AddBlogRating**。

-   在 [套件管理員主控台] 中執行 [**新增-遷移 AddBlogRating** ] 命令。
-   在 [**遷移**] 資料夾中，我們現在有一個新的**AddBlogRating**遷移。 使用時間戳預先修正遷移檔案名，以協助進行排序。 讓我們編輯產生的程式碼，為 Blog 的預設值指定3。評等（下列程式碼中的第10行）

*此遷移也有一個程式碼後置檔案，該檔案會捕獲一些中繼資料。此中繼資料可讓 Code First 移轉複寫我們在此以程式碼為基礎的遷移之前所執行的自動遷移。如果另一位開發人員想要執行我們的遷移，或部署應用程式的時間，這就很重要。*

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

-   在 [套件管理員主控台] 中執行 [**更新-資料庫**] 命令。

## <a name="back-to-automatic-migrations"></a>回到自動遷移

我們現在可以隨時切換回自動遷移，以進行更簡單的變更。 Code First 移轉會根據其儲存在每個以程式碼為基礎的程式碼後置檔案中的中繼資料，負責執行自動和以程式碼為基礎的遷移。

-   讓我們在模型中新增一個 Post 屬性

``` csharp
    public string Abstract { get; set; }
```

現在我們可以使用**更新資料庫**來取得 Code First 移轉，使用自動遷移將這種變更推送至資料庫。

-   在 [套件管理員主控台] 中執行 [**更新-資料庫**] 命令。

## <a name="summary"></a>摘要

在此逐步解說中，您已瞭解如何使用自動遷移，將模型變更推送至資料庫。 您也瞭解到當您需要更多控制權時，如何在自動遷移之間 scaffold 和執行以程式碼為基礎的遷移。
