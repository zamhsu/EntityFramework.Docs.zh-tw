---
title: Code First 至新的資料庫-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 2df6cb0a-7d8b-4e28-9d05-e2b9a90125af
ms.openlocfilehash: bb44a3300bc8ffc9d7050c4784e7b76b29c61796
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994614"
---
# <a name="code-first-to-a-new-database"></a>Code First 至新的資料庫
本影片以及逐步說明的逐步解說提供目標的新資料庫的 Code First 開發的簡介。 此案例包含目標資料庫不存在和 Code First 會建立或 Code First 會加入新的空白資料庫太資料表。 程式碼第一次可讓您定義您的模型使用 C\#或 VB.Net 類別。 其他設定 （選擇性） 可以使用屬性，在您的類別和屬性，或使用 fluent API 來執行。

## <a name="watch-the-video"></a>觀看影片
這段影片提供目標的新資料庫的 Code First 開發的簡介。 此案例包含目標資料庫不存在和 Code First 會建立或 Code First 會加入新的空白資料庫太資料表。 程式碼第一次可讓您定義您使用 C# 或 VB.Net 類別的模型。 其他設定 （選擇性） 可以使用屬性，在您的類別和屬性，或使用 fluent API 來執行。

**主講人**[Rowan Miller](http://romiller.com/)

**視訊**: [WMV](http://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv) | [MP4](http://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)

# <a name="pre-requisites"></a>必要條件

您必須至少是 Visual studio 2010 或 Visual Studio 2012 安裝完成本逐步解說。

如果您使用 Visual Studio 2010，您也必須有[NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)安裝。

## <a name="1-create-the-application"></a>1.建立應用程式

為了簡單起見，我們要建置基本的主控台應用程式，會使用 Code First 來執行資料存取。

-   開啟 Visual Studio
-   **檔案-&gt;新增-&gt;專案...**
-   選取  **Windows**從左側的功能表和**主控台應用程式**
-   請輸入**CodeFirstNewDatabaseSample**做為名稱
-   選取 [確定]

## <a name="2-create-the-model"></a>2.建立模型

讓我們定義非常簡單的模型，使用類別。 我們會只定義它們的 Program.cs 檔案中，但在真實世界應用程式，您會將出您的類別分割成個別的檔案和潛在不同的專案。

下方程式類別定義，在 Program.cs 中加入下列兩個類別。

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

您會注意到，我們將會讓兩個導覽屬性 （Blog.Posts 和 Post.Blog） 虛擬。 這可讓 Entity Framework 的消極式載入功能。 消極式載入會表示，這些屬性的內容會自動從資料庫載入當您嘗試存取它們。

## <a name="3-create-a-context"></a>3.建立內容

現在就可以開始定義 衍生的內容，表示與資料庫，讓我們可以查詢和儲存資料的工作階段。 我們會定義的內容衍生自 System.Data.Entity.DbContext 並公開 （expose） 的具類型的 DbSet&lt;TEntity&gt;我們的模型中每個類別。

我們現在正在使用 Entity Framework 中的類型，因此我們需要加入 EntityFramework NuGet 套件。

-   **專案 –&gt;管理 NuGet 封裝...**
    注意： 如果您沒有**管理 NuGet 套件...** 您應該安裝的選項[最新版的 NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)
-   選取 [ **Online** ] 索引標籤
-   選取  **EntityFramework**封裝
-   按一下 **安裝**

加入 using 陳述式 System.Data.Entity Program.cs 的頂端。

``` csharp
using System.Data.Entity;
```

在 Program.cs 中的文章類別下方新增下列衍生的內容。

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```

以下是什麼 Program.cs 現在應該包含的完整清單。

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

這是我們要開始儲存和擷取資料的所有程式碼。 很顯然有相當大背後運作原理，並讓我們看一下，待會兒但請先讓我們來觀看實作示範。

## <a name="4-reading--writing-data"></a>4.讀取和寫入資料

在 Program.cs 中實作的 Main 方法，如下所示。 此程式碼會建立我們的內容的新執行個體，然後用它來插入新的部落格 然後它會使用 LINQ 查詢來擷取依字母順序排序項目所使用的資料庫中的所有部落格。

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

您現在可以執行應用程式，並加以測試。

```
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
### <a name="wheres-my-data"></a>我的資料位於何處？

依照慣例 DbContext 已為您建立資料庫。

-   如果本機 SQL Express 執行個體可用的 （使用 Visual Studio 2010 的預設安裝） 然後 Code First 已資料庫上建立該執行個體
-   如果沒有 SQL Express 則 Code First 會試著使用[LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) （使用 Visual Studio 2012 的預設安裝）
-   衍生的內容，在本例中是完整格式名稱來命名資料庫**CodeFirstNewDatabaseSample.BloggingContext**

這些是預設慣例有 Code First 會使用將資料庫變更的各種方式，詳細資訊位於**DbContext 會探索模型和資料庫連接**主題。
您可以連接到此 Visual Studio 中使用伺服器總管 中的資料庫

-   **檢視-&gt;伺服器總管**
-   以滑鼠右鍵按一下**資料連接**，然後選取**新增連接...**
-   如果您尚未連線至資料庫從伺服器總管之前您必須選取 Microsoft SQL Server 做為資料來源

    ![SelectDataSource](~/ef6/media/selectdatasource.png)

-   連接到 LocalDB 或 SQL Express，何者而定，您已安裝

我們現在可以檢查 Code First 建立的結構描述。

![SchemaInitial](~/ef6/media/schemainitial.png)

DbContext 出哪些類別，以查看我們所定義的 DbSet 屬性包含在模型中。 然後，它會使用一組預設的 Code First 慣例決定資料表和資料行的名稱，判斷資料型別中，尋找主索引鍵等。 稍後在本逐步解說中，我們將探討如何覆寫這些慣例。

## <a name="5-dealing-with-model-changes"></a>5.處理模型變更

現在就可以開始對我們的模型進行一些變更，當我們做出這些變更，我們也需要更新資料庫結構描述。 若要這樣做我們要使用的簡稱稱為 Code First 移轉或移轉的功能。

移轉可讓我們將說明如何升級 （及降級） 資料庫結構描述的步驟的已排序的集合。 每個步驟，稱為移轉時，包含一些程式碼，描述要套用變更。 

第一個步驟是針對我們為 [bloggingcontext] 啟用 Code First 移轉。

-   **工具-&gt;程式庫套件管理員-&gt;套件管理員主控台**
-   在套件管理員主控台中執行 **Enable-migrations** 命令
-   新的 [移轉] 資料夾已新增至我們的專案包含兩個項目：
    -   **Configuration.cs** – 這個檔案包含將用於移轉的移轉設定為 [bloggingcontext]。 我們不需要變更此逐步解說中的任何項目，但以下是您可以指定種子資料，註冊提供者，其他資料庫，變更命名空間的移轉會產生等。
    -   **&lt;時間戳記&gt;\_InitialCreate.cs** – 這是您第一次移轉，它代表已套用到資料庫，才從空的資料庫，其中包括部落格和文章資料表的變更. 雖然我們讓 Code First 自動建立這些資料表，既然我們已選擇加入這些已轉換成移轉的移轉。 程式碼第一次有也記錄在此移轉已套用我們本機資料庫。 檔案名稱上的時間戳記用來排序之用。

    現在讓我們對我們的模型進行變更、 加入部落格類別中的 Url 屬性：

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }

    public virtual List<Post> Posts { get; set; }
}
```

-   執行**Add-migration AddUrl**命令在套件管理員主控台。
    新增移轉命令會檢查自最後一個移轉後變更，並則的任何變更，找到新的移轉。 我們可以為移轉指定的名稱;在此情況下，我們會呼叫移轉 'AddUrl'。
    Scaffold 程式碼說我們要加入的 Url 資料行，可以保存字串資料，為 dbo。部落格的資料表。 如有需要我們可以編輯的 scaffold 程式碼，但在此情況下不需要。

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

-   執行**Update-database**命令在套件管理員主控台。 此命令會將任何擱置中的移轉套用至資料庫。 讓移轉只會套用我們新的 AddUrl 移轉已套用我們為 InitialCreate 移轉。
    提示： 您可以使用 **– Verbose**切換呼叫以查看針對資料庫執行的 SQL 更新資料庫時。

新的 [Url] 資料行現在已新增至部落格資料庫的資料表中:

![SchemaWithUrl](~/ef6/media/schemawithurl.png)

## <a name="6-data-annotations"></a>6.資料註釋

到目前為止我們只是已讓探索模型使用其預設慣例，EF，但那里要作為的時間類別不遵循慣例，以及我們必須要能夠執行進一步的組態。 有兩個選項，這個項目;我們將探討在本節中的資料註解，然後在下一節中的 fluent API。

-   讓我們加入我們的模型中的使用者類別

``` csharp
public class User
{
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   我們也需要將一組加入至我們的衍生內容

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }
}
```

-   如果我們嘗試新增移轉會得到錯誤指出 「*EntityType 'User' 已定義任何索引鍵。定義索引鍵為此 EntityType。 」* 因為 EF 有沒有辦法知道使用者名稱應該使用者的主索引鍵。
-   我們現在使用資料註解，因此我們需要新增 using 陳述式，在 Program.cs 的頂端

```
using System.ComponentModel.DataAnnotations;
```

-   現在的使用者名稱屬性，來識別它是主索引鍵加上註解

``` csharp
public class User
{
    [Key]
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   使用**Add-migration AddUser**命令來建立要套用這些移轉的結構變更至資料庫
-   執行**Update-database**命令，以將新的移轉套用至資料庫

新的資料表現在已新增至資料庫：

![SchemaWithUsers](~/ef6/media/schemawithusers.png)

是支援的 EF 的註解的完整清單：

-   [KeyAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.keyattribute)
-   [StringLengthAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute)
-   [MaxLengthAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.maxlengthattribute)
-   [ConcurrencyCheckAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.concurrencycheckattribute)
-   [使用 RequiredAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute)
-   [TimestampAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute)
-   [ComplexTypeAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.complextypeattribute)
-   [ColumnAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.columnattribute)
-   [TableAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.tableattribute)
-   [InversePropertyAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.inversepropertyattribute)
-   [ForeignKeyAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.foreignkeyattribute)
-   [DatabaseGeneratedAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.databasegeneratedattribute)
-   [NotMappedAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.notmappedattribute)

## <a name="7-fluent-api"></a>7.Fluent API

上一節中我們探討了以補充或覆寫慣例所偵測到使用資料註解。 若要設定模型的其他方式是透過 Code First fluent API。

大部分的模型組態可使用簡單的資料註解。 Fluent API 是更進階的方式來指定涵蓋資料註解可以另外執行一些更進階的組態無法使用資料註解的一切的模型組態。 資料註釋和 fluent API 可以一起使用。

若要存取 fluent API 您覆寫在 DbContext 的 OnModelCreating 方法。 假設我們想要重新命名 User.DisplayName，會顯示儲存在資料行\_名稱。

-   覆寫為 [bloggingcontext] 上的 OnModelCreating 方法，以下列程式碼

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

-   使用**Add-migration ChangeDisplayName**命令來建立要套用這些移轉的結構變更至資料庫。
-   執行**Update-database**命令，以將新的移轉套用至資料庫。

{3&gt;displayname&lt;3} 資料行現在已重新命名以顯示\_名稱：

![SchemaWithDisplayNameRenamed](~/ef6/media/schemawithdisplaynamerenamed.png)

## <a name="summary"></a>總結

在此逐步解說中，我們討論過使用新的資料庫的 Code First 開發。 我們會定義使用類別的模型，然後使用該模型來建立資料庫和儲存和擷取資料。 建立資料庫之後我們會使用 Code First 移轉變更我們發展的模型結構描述。 我們也了解如何將模型設定為使用資料註釋和 Fluent API。
