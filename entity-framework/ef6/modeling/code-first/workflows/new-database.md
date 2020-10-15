---
title: Code First 新增至新資料庫-EF6
description: Code First 至 Entity Framework 6 中的新資料庫
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/workflows/new-database
ms.openlocfilehash: 9d0082ac5226fff066d3e18c9164e2230c84b285
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065014"
---
# <a name="code-first-to-a-new-database"></a>新資料庫的 Code First
這段影片和逐步解說將提供以新的資料庫為目標的 Code First 開發簡介。 這種情況包括以不存在的資料庫為目標，Code First 將會建立，或是 Code First 將新的資料表加入的空資料庫。 Code First 可讓您使用 C 或 VB.Net 類別來定義您的模型 \# 。 您可以選擇性地使用類別和屬性上的屬性，或使用流暢的 API 來執行其他設定。

## <a name="watch-the-video"></a>觀賞影片
這段影片提供以新的資料庫為目標的 Code First 開發簡介。 這種情況包括以不存在的資料庫為目標，Code First 將會建立，或是 Code First 將新的資料表加入的空資料庫。 Code First 可讓您使用 c # 或 VB.Net 類別來定義您的模型。 您可以選擇性地使用類別和屬性上的屬性，或使用流暢的 API 來執行其他設定。

**主講人**[Rowan Miller](https://romiller.com/)

**影片**： [wmv](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv)的  |  [MP4](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v)  |  [wmv .wmv (ZIP) ](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)

## <a name="pre-requisites"></a>必要條件

您必須至少安裝 Visual Studio 2010 或 Visual Studio 2012，才能完成此逐步解說。

如果您使用 Visual Studio 2010，也需要安裝 [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) 。

## <a name="1-create-the-application"></a>1. 建立應用程式

為了簡單起見，我們將建立使用 Code First 來執行資料存取的基本主控台應用程式。

-   開啟 Visual Studio
-   **檔案- &gt; 新增- &gt; 專案 .。。**
-   從左側功能表和**主控台應用程式**選取 [ **Windows** ]
-   輸入 **CodeFirstNewDatabaseSample** 作為名稱
-   選取 [確定]

## <a name="2-create-the-model"></a>2. 建立模型

讓我們使用類別來定義非常簡單的模型。 我們只是在 Program.cs 檔中定義它們，但在真實世界的應用程式中，您會將類別分成不同的檔案，而且可能是個別的專案。

在 Program.cs 中的程式類別定義下方，新增下列兩個類別。

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }

    public virtual List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public int BlogId { get; set; }
    public virtual Blog Blog { get; set; }
}
```

您會注意到，我們會將兩個導覽屬性 (Blog 和 Post. Blog) virtual。 這會啟用 Entity Framework 的消極式載入功能。 消極式載入表示當您嘗試存取這些屬性的內容時，將會自動從資料庫載入這些屬性的內容。

## <a name="3-create-a-context"></a>3. 建立內容

現在是時候定義衍生的內容，它代表與資料庫的會話，讓我們可以查詢和儲存資料。 我們會定義衍生自 DbCoNtext 的內容，並 &lt; &gt; 針對模型中的每個類別公開具類型的 DbSet TEntity。

我們現在開始使用 Entity Framework 中的型別，因此我們需要新增 EntityFramework NuGet 套件。

-   **專案- &gt; 管理 NuGet 套件 .。。**
    注意：如果您沒有 [**管理 NuGet 套件 ...** ] 選項您應該安裝 [最新版本的 NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)
-   選取 [ **線上** ] 索引標籤
-   選取 **EntityFramework** 套件
-   按一下 [安裝]

在 Program.cs 頂端加入 system.string 的 using 語句。

``` csharp
using System.Data.Entity;
```

在 Program.cs 的 Post 類別底下，加入下列衍生內容。

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```

以下是 Program.cs 現在應該包含的完整清單。

``` csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data.Entity;

namespace CodeFirstNewDatabaseSample
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Name { get; set; }

        public virtual List<Post> Posts { get; set; }
    }

    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }

    public class BloggingContext : DbContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
    }
}
```

這就是開始儲存和取出資料所需的所有程式碼。 顯然在幕後有很大的進展，我們先來看看這一點，但首先我們要看看它的運作方式。

## <a name="4-reading--writing-data"></a>4. 讀取 & 寫入資料

在 Program.cs 中執行 Main 方法，如下所示。 這段程式碼會建立內容的新實例，然後使用它來插入新的 Blog。 然後，它會使用 LINQ 查詢來從資料庫中依標題的字母順序來取得所有的 Blog。

``` csharp
class Program
{
    static void Main(string[] args)
    {
        using (var db = new BloggingContext())
        {
            // Create and save a new Blog
            Console.Write("Enter a name for a new Blog: ");
            var name = Console.ReadLine();

            var blog = new Blog { Name = name };
            db.Blogs.Add(blog);
            db.SaveChanges();

            // Display all Blogs from the database
            var query = from b in db.Blogs
                        orderby b.Name
                        select b;

            Console.WriteLine("All blogs in the database:");
            foreach (var item in query)
            {
                Console.WriteLine(item.Name);
            }

            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

您現在可以執行應用程式並進行測試。

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
### <a name="wheres-my-data"></a>我的資料在哪裡？

依慣例，DbCoNtext 已為您建立資料庫。

-   如果有本機 SQL Express 實例可用 (預設會隨 Visual Studio 2010) 安裝然後 Code First 已在該實例上建立資料庫
-   如果無法使用 SQL Express，Code First 將會嘗試並使用預設安裝的 [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) (搭配 Visual Studio 2012) 
-   資料庫是以衍生內容的完整名稱命名，在我們的案例中為 **CodeFirstNewDatabaseSample。 BloggingCoNtext**

這些只是預設慣例，而且有各種方式可變更 Code First 使用的資料庫，而 **DbCoNtext 如何探索模型和資料庫連接** 主題中提供詳細資訊。
您可以使用中的伺服器總管連接到這個資料庫 Visual Studio

-   **View- &gt; 伺服器總管**
-   以滑鼠右鍵按一下 [**資料連線**]，然後選取 [**加入連接 ...** ]。
-   如果您還沒有從伺服器總管連接到資料庫，則必須選取 Microsoft SQL Server 作為資料來源

    ![選取資料來源](~/ef6/media/selectdatasource.png)

-   連接至 LocalDB 或 SQL Express （視您已安裝的版本而定）

我們現在可以檢查 Code First 建立的架構。

![架構初始](~/ef6/media/schemainitial.png)

DbCoNtext 藉由查看我們所定義的 DbSet 屬性，來處理要包含在模型中的類別。 然後，它會使用一組預設的 Code First 慣例來決定資料表和資料行名稱、判斷資料類型、尋找主鍵等等。 稍後在此逐步解說中，我們將探討如何覆寫這些慣例。

## <a name="5-dealing-with-model-changes"></a>5. 處理模型變更

現在是時候對我們的模型進行一些變更，當我們進行這些變更時，我們也需要更新資料庫架構。 為了達到此目的，我們將使用稱為 Code First 移轉的功能，或簡短的遷移。

遷移可讓我們擁有一組已排序的步驟，說明如何升級 (和降級) 我們的資料庫架構。 上述每個步驟（稱為「遷移」）都包含一些程式碼，描述要套用的變更。 

第一個步驟是啟用 BloggingCoNtext 的 Code First 移轉。

-   **工具-連結 &gt; 庫封裝管理員- &gt; 封裝管理員主控台**
-   在套件管理員主控台中執行 **Enable-migrations** 命令
-   專案中已加入新的 [遷移] 資料夾，其中包含兩個專案：
    -   **Configuration.cs** –這個檔案包含遷移將用來遷移 BloggingCoNtext 的設定。 我們不需要變更此逐步解說中的任何內容，但您可以在這裡指定種子資料、註冊其他資料庫的提供者、變更在其中產生遷移的命名空間等等。
    -   ** &lt; 時間戳記 &gt; \_ InitialCreate.cs** –這是您第一次的遷移，它代表已套用至資料庫的變更，使其成為包含 blog 和 post 資料表的空資料庫。 雖然我們會讓 Code First 自動為我們建立這些資料表，但現在我們已選擇要進行遷移，並已轉換成遷移。 Code First 也會記錄在我們的本機資料庫中，表示已套用此遷移。 檔案名的時間戳記是用於排序用途。

    現在讓我們對模型進行變更，並將 Url 屬性新增至 Blog 類別：

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }

    public virtual List<Post> Posts { get; set; }
}
```

-   在封裝管理員主控台中執行 [ **新增-遷移 AddUrl** ] 命令。
    Add-Migration 命令會檢查自您上一次遷移後的變更，並使用所找到的任何變更 scaffold 新的遷移。 我們可以為遷移提供一個名稱;在此情況下，我們會呼叫遷移 ' AddUrl '。
    Scaffold 程式碼指出我們需要將 Url 資料行（可保存字串資料）新增至 dbo。Blog 資料表。 如有需要，我們可以編輯 scaffold 程式碼，但在此情況下不需要這麼做。

``` csharp
namespace CodeFirstNewDatabaseSample.Migrations
{
    using System;
    using System.Data.Entity.Migrations;

    public partial class AddUrl : DbMigration
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

-   在封裝管理員主控台中執行 **更新資料庫** 命令。 此命令會將任何擱置中的遷移套用至資料庫。 我們的 InitialCreate 遷移已套用，因此遷移只會套用新的 AddUrl 遷移。
    秘訣：呼叫 Update-Database 時，您可以使用 **-Verbose** 參數來查看針對資料庫執行的 SQL。

新的 Url 資料行會加入至資料庫中的 Blog 資料表：

![具有 Url 的架構](~/ef6/media/schemawithurl.png)

## <a name="6-data-annotations"></a>6. 資料批註

到目前為止，我們只是讓 EF 使用其預設慣例來探索模型，但在某些情況下，我們的類別不會遵循慣例，因此我們必須能夠執行進一步的設定。 有兩個選項可供選擇：在下一節中，我們將探討這一節中的資料批註，以及流暢的 API。

-   讓我們將使用者類別新增至我們的模型

``` csharp
public class User
{
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   我們也需要將集合新增至我們的衍生內容

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }
}
```

-   如果我們嘗試新增遷移，則會收到錯誤，指出「*EntityType ' User ' 沒有定義索引鍵。定義此 EntityType 的索引鍵。* 」 因為 EF 無法得知使用者名稱應該是使用者的主要金鑰。
-   我們現在要使用資料批註，因此我們需要在 Program.cs 的頂端加入 using 語句

```csharp
using System.ComponentModel.DataAnnotations;
```

-   現在將 [使用者名稱] 屬性加上批註，以識別它是主要金鑰

``` csharp
public class User
{
    [Key]
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   使用 **Add-遷移 AddUser** 命令來 scaffold 遷移，以將這些變更套用至資料庫
-   執行 **Update-database** 命令，將新的遷移套用至資料庫

現在會將新的資料表加入至資料庫：

![使用者的架構](~/ef6/media/schemawithusers.png)

EF 支援的批註完整清單如下：

-   [KeyAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.keyattribute)
-   [StringLengthAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute)
-   [MaxLengthAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.maxlengthattribute)
-   [ConcurrencyCheckAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.concurrencycheckattribute)
-   [RequiredAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute)
-   [TimestampAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute)
-   [ComplexTypeAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.complextypeattribute)
-   [ColumnAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.columnattribute)
-   [TableAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.tableattribute)
-   [InversePropertyAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.inversepropertyattribute)
-   [ForeignKeyAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.foreignkeyattribute)
-   [DatabaseGeneratedAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.databasegeneratedattribute)
-   [NotMappedAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.notmappedattribute)

## <a name="7-fluent-api"></a>7. 流暢的 API

在上一節中，我們探討了如何使用資料批註來補充或覆寫慣例偵測到的內容。 設定模型的另一種方式是透過 Code First 的流暢 API。

大部分的模型設定都可以使用簡單的資料批註來完成。 流暢的 API 是指定模型設定的更先進方式，其中涵蓋資料批註可以進行的所有動作，以及資料批註無法進行的一些更先進的設定。 資料批註和流暢的 API 可以一起使用。

若要存取流暢的 API，您可以覆寫 DbCoNtext 中的 OnModelCreating 方法。 假設我們想要重新命名資料行，讓使用者的 DisplayName 儲存在顯示名稱中 \_ 。

-   使用下列程式碼覆寫 BloggingCoNtext 上的 OnModelCreating 方法

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Entity<User>()
            .Property(u => u.DisplayName)
            .HasColumnName("display_name");
    }
}
```

-   使用 [ **新增-遷移 ChangeDisplayName** ] 命令 scaffold 要將這些變更套用至資料庫的遷移。
-   執行 **Update-database** 命令，將新的遷移套用至資料庫。

DisplayName 資料行現在已重新命名為顯示 \_ 名稱：

![顯示名稱已重新命名的架構](~/ef6/media/schemawithdisplaynamerenamed.png)

## <a name="summary"></a>摘要

在這個逐步解說中，我們探討了如何使用新的資料庫進行 Code First 開發。 我們使用類別定義了模型，然後使用該模型來建立資料庫，並儲存和取出資料。 建立資料庫之後，我們使用 Code First 移轉在模型演進時變更架構。 我們也已瞭解如何使用資料批註和流暢 API 來設定模型。
