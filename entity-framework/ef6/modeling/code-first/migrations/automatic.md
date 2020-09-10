---
title: 自動 Code First 移轉-EF6
description: Entity Framework 6 中的自動 Code First 移轉
author: divega
ms.date: 10/23/2016
ms.assetid: 0eb86787-2161-4cb4-9cb8-67c5d6e95650
uid: ef6/modeling/code-first/migrations/automatic
ms.openlocfilehash: 541adf5aed517685b54d675730996c12ff0c824a
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618076"
---
# <a name="automatic-code-first-migrations"></a>自動 Code First 移轉
自動遷移可讓您使用 Code First 移轉，而不需要在專案中為您所做的每項變更建立程式碼檔案。 並非所有變更都可以自動套用-例如，資料行重新命名需要使用以程式碼為基礎的遷移。

> [!NOTE]
> 本文假設您知道如何在基本案例中使用 Code First 移轉。 如果您沒有這麼做，就必須先閱讀 [Code First 移轉](xref:ef6/modeling/code-first/migrations/index) ，再繼續進行操作。

## <a name="recommendation-for-team-environments"></a>小組環境的建議

您可以散置自動和以程式碼為基礎的遷移，但不建議在小組開發案例中使用。 如果您是使用原始檔控制之開發人員小組的一部分，您應該使用純粹自動遷移或單純以程式碼為基礎的遷移。 由於自動遷移的限制，我們建議您在小組環境中使用以程式碼為基礎的遷移。

## <a name="building-an-initial-model--database"></a>建置初始模型與資料庫

在開始使用移轉之前，我們需要一個專案和一個 Code First 模型。 此逐步解說中我們將使用標準 **Blog** 和 **Post** 模型。

-   建立新的 **MigrationsAutomaticDemo** 主控台應用程式
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

-   執行您的應用程式，您會看到為您建立了 **MigrationsAutomaticCodeDemo BlogCoNtext** 資料庫。

    ![資料庫 LocalDB](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a>啟用移轉

現在可以對我們的模型做更多變更。

-   請將一個 URL 屬性引進 Blog 類別。

``` csharp
    public string Url { get; set; }
```

如果您再次執行該應用程式，您將會收到 InvalidOperationException，指出*自資料庫建立以來，支援 'BlogContext' 內容的模型已變更。請考慮使用 Code First 移轉更新資料庫 (* [ *http://go.microsoft.com/fwlink/?LinkId=238269* ](https://go.microsoft.com/fwlink/?LinkId=238269) *)。*

如例外狀況所示，現在可以開始使用 Code First 移轉。 因為我們想要使用自動遷移，所以會指定 **– EnableAutomaticMigrations** 參數。

-   在封裝管理員主控台中執行 [ **啟用-遷移-EnableAutomaticMigrations** ] 命令，此命令已將 [ **遷移** ] 資料夾新增至我們的專案。 這個新資料夾包含一個檔案：

-   **組態類別**。 這個類別可讓您設定移轉對內容的運作方式。 在此逐步解說中，我們將只使用預設組態。
    「由於專案中只有一個單一 Code First 內容，因此 Enable-Migrations 已自動填入此組態適用的內容類型。」 

 

## <a name="your-first-automatic-migration"></a>您的第一個自動遷移

Code First 移轉有兩個您將先熟悉的主要命令。

-   **Add-Migration** 將根據自上次建立移轉以來您對模型所做的變更，來建立下一次移轉
-   **Update-Database** 會將任何擱置的移轉套用至資料庫

除非我們真的需要) ，並將焦點放在讓 Code First 移轉自動計算和套用變更，否則我們將會避免使用「新增-遷移 (」。 讓我們使用 **Update-database** 來取得 Code First 移轉將變更推送至我們的模型 (新的 **Blog**l 屬性) 至資料庫。

-   在封裝管理員主控台中執行 **更新資料庫** 命令。

**MigrationsAutomaticDemo. BlogCoNtext**資料庫現在已更新為包含 [ **blog** ] 資料表中的 [ **Url** ] 資料行。

 

## <a name="your-second-automatic-migration"></a>第二個自動遷移

讓我們進行其他變更，讓 Code First 移轉自動將變更推送到資料庫。

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

現在使用 **更新資料庫** ，讓資料庫保持在最新狀態。 這次讓我們指定 **–Verbose** 旗標，讓您可以看到 Code First 移轉正在執行的 SQL。

-   在套件管理員主控台中執行 **-Verbose** 命令。

## <a name="adding-a-code-based-migration"></a>新增以程式碼為基礎的遷移

現在讓我們看看，我們可能想要使用以程式碼為基礎的遷移。

-   讓我們將 **評** 等屬性新增至 **Blog** 類別

``` csharp
    public int Rating { get; set; }
```

我們可以直接執行 **更新資料庫** ，將這些變更推送至資料庫。 但是，我們要新增一個不可為 null 的 **blog** 資料行，如果資料表中有任何現有的資料，則會將新資料行的 CLR 預設資料類型指派給資料類型 (評等為整數，因此為 **0**) 。 但我們應指定預設值為 **3**，以便 **Blog** 資料表中的現有資料列以適當的評等開頭。
讓我們使用 [新增-遷移] 命令將這種變更寫入至以程式碼為基礎的遷移，讓我們可以進行編輯。 [ **新增-遷移** ] 命令可讓我們將名稱提供給這些遷移，讓我們直接呼叫我們的 **AddBlogRating**。

-   在封裝管理員主控台中執行 [ **新增-遷移 AddBlogRating** ] 命令。
-   在 [ **遷移** ] 資料夾中，我們現在有新的 **AddBlogRating** 遷移。 使用時間戳預先修正遷移檔案名，以協助進行排序。 讓我們編輯產生的程式碼，為 Blog (第10行的程式碼片段指定預設值 3) 

*遷移也具有可捕獲某些中繼資料的程式碼後端檔案。此中繼資料可讓 Code First 移轉複寫我們在這個以程式碼為基礎的遷移之前所執行的自動遷移。如果另一位開發人員想要執行我們的遷移或部署應用程式的時候，這點很重要。*

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

-   在封裝管理員主控台中執行 **更新資料庫** 命令。

## <a name="back-to-automatic-migrations"></a>返回自動遷移

我們現在可以切換回自動遷移，以進行更簡單的變更。 Code First 移轉會根據每個以程式碼為基礎之遷移的程式碼後端檔案中儲存的中繼資料，以正確的順序來執行自動和以程式碼為基礎的遷移。

-   讓我們將 Post. Abstract 屬性新增至我們的模型

``` csharp
    public string Abstract { get; set; }
```

現在我們可以使用 **更新資料庫** 來取得 Code First 移轉使用自動遷移將這種變更推送至資料庫。

-   在封裝管理員主控台中執行 **更新資料庫** 命令。

## <a name="summary"></a>摘要

在這個逐步解說中，您已瞭解如何使用自動遷移將模型變更推送至資料庫。 您也已瞭解如何在需要更多控制時，于自動遷移之間 scaffold 和執行以程式碼為基礎的遷移。
