---
title: Code First 至現有的資料庫-EF6
description: Code First 至 Entity Framework 6 中的現有資料庫
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/workflows/existing-database
ms.openlocfilehash: 2305236f68cfdc9f6e1582cca1163abf380b4075
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066417"
---
# <a name="code-first-to-an-existing-database"></a>Code First 至現有的資料庫
這段影片和逐步解說將提供以現有資料庫為目標的 Code First 開發簡介。 Code First 可讓您使用 C 或 VB.Net 類別來定義您的模型 \# 。 您也可以使用類別和屬性上的屬性，或使用流暢的 API，來執行其他設定。

## <a name="watch-the-video"></a>觀賞影片
這段影片 [現在可在 Channel 9 上](https://channel9.msdn.com/blogs/ef/code-first-to-existing-database-ef6-1-onwards-)取得。

## <a name="pre-requisites"></a>必要條件

您必須安裝 **Visual Studio 2012** 或 **Visual Studio 2013** ，才能完成此逐步解說。

您也需要安裝 Visual Studio 版本 **6.1** (或更新版本的 **Entity Framework Tools**) 。 如需安裝最新版本 Entity Framework Tools 的詳細資訊，請參閱 [取得 Entity Framework](xref:ef6/fundamentals/install) 。

## <a name="1-create-an-existing-database"></a>1. 建立現有的資料庫

通常當您將目標設為現有的資料庫時，就會建立該資料庫，但在此逐步解說中，我們需要建立要存取的資料庫。

讓我們繼續產生資料庫。

-   開啟 Visual Studio
-   **View- &gt; 伺服器總管**
-   以滑鼠右鍵按一下 [**資料連線- &gt; 新增連接 ...** ]
-   如果您還沒有從 **伺服器總管** 連接到資料庫，則必須選取 **Microsoft SQL Server** 作為資料來源

    ![選取資料來源](~/ef6/media/selectdatasource.png)

-   連接到您的 LocalDB 實例，並輸入 [程式 **撰寫** ] 作為資料庫名稱

    ![LocalDB 連接](~/ef6/media/localdbconnection.png)

-   選取 **[確定]** ，系統會詢問您是否要建立新的資料庫，請選取 **[是]** 。

    ![[建立資料庫] 對話方塊](~/ef6/media/createdatabasedialog.png)

-   新資料庫現在會出現在伺服器總管中，以滑鼠右鍵按一下該資料庫，然後選取 [追加**查詢**]。
-   將下列 SQL 複製到新的查詢，然後以滑鼠右鍵按一下查詢並選取 [**執行**]

``` SQL
CREATE TABLE [dbo].[Blogs] (
    [BlogId] INT IDENTITY (1, 1) NOT NULL,
    [Name] NVARCHAR (200) NULL,
    [Url]  NVARCHAR (200) NULL,
    CONSTRAINT [PK_dbo.Blogs] PRIMARY KEY CLUSTERED ([BlogId] ASC)
);

CREATE TABLE [dbo].[Posts] (
    [PostId] INT IDENTITY (1, 1) NOT NULL,
    [Title] NVARCHAR (200) NULL,
    [Content] NTEXT NULL,
    [BlogId] INT NOT NULL,
    CONSTRAINT [PK_dbo.Posts] PRIMARY KEY CLUSTERED ([PostId] ASC),
    CONSTRAINT [FK_dbo.Posts_dbo.Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [dbo].[Blogs] ([BlogId]) ON DELETE CASCADE
);

INSERT INTO [dbo].[Blogs] ([Name],[Url])
VALUES ('The Visual Studio Blog', 'http://blogs.msdn.com/visualstudio/')

INSERT INTO [dbo].[Blogs] ([Name],[Url])
VALUES ('.NET Framework Blog', 'http://blogs.msdn.com/dotnet/')
```

## <a name="2-create-the-application"></a>2. 建立應用程式

為了簡單起見，我們將建立一個基本的主控台應用程式，使用 Code First 來執行資料存取：

-   開啟 Visual Studio
-   **檔案- &gt; 新增- &gt; 專案 .。。**
-   從左側功能表和**主控台應用程式**選取 [ **Windows** ]
-   輸入 **CodeFirstExistingDatabaseSample** 作為名稱
-   選取 [確定]

 

## <a name="3-reverse-engineer-model"></a>3. 反轉工程模型

我們將使用 Visual Studio 的 Entity Framework Tools，以協助我們產生一些初始程式碼來對應到資料庫。 這些工具只會產生程式碼，如果您想要的話，也可以手動輸入。

-   **專案- &gt; 加入新專案 .。。**
-   從左側功能表中選取 [ **資料** ]，然後 **ADO.NET 實體資料模型**
-   輸入 **BloggingCoNtext** 做為名稱，然後按一下 **[確定]**
-   這會啟動 **實體資料模型 Wizard**
-   **從資料庫選取 Code First** ，然後按 **[下一步]**

    ![Wizard One CFE](~/ef6/media/wizardonecfe.png)

-   選取您在第一個區段中建立之資料庫的連接，然後按 [ **下一步]**

    ![Wizard 兩 CFE](~/ef6/media/wizardtwocfe.png)

-   按一下 [**資料表**] 旁的核取方塊以匯入所有資料表，然後按一下 **[完成]** 。

    ![Wizard 三 CFE](~/ef6/media/wizardthreecfe.png)

當反向工程程式完成之後，專案中會加入一些專案，讓我們看看已新增的內容。

### <a name="configuration-file"></a>組態檔

專案中已加入 App.config 檔案，此檔案包含現有資料庫的連接字串。

``` xml
<connectionStrings>
  <add  
    name="BloggingContext"  
    connectionString="data source=(localdb)\mssqllocaldb;initial catalog=Blogging;integrated security=True;MultipleActiveResultSets=True;App=EntityFramework"  
    providerName="System.Data.SqlClient" />
</connectionStrings>
```

*您也會注意到設定檔中的其他設定，這些都是預設的 EF 設定，告訴 Code First 在何處建立資料庫。因為我們會對應到現有的資料庫，所以我們的應用程式會忽略這些設定。*

### <a name="derived-context"></a>衍生內容

已將 **BloggingCoNtext** 類別加入至專案。 內容代表與資料庫的會話，可讓我們查詢和儲存資料。
此內容會針對模型中的每個類型公開**DbSet &lt; TEntity &gt; ** 。 您也會注意到，預設的函式會使用 **name =** 語法來呼叫基底函式。 這會告訴 Code First 應從設定檔載入要用於此內容的連接字串。

``` csharp
public partial class BloggingContext : DbContext
    {
        public BloggingContext()
            : base("name=BloggingContext")
        {
        }

        public virtual DbSet<Blog> Blogs { get; set; }
        public virtual DbSet<Post> Posts { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
        }
    }
```

*當您在設定檔中使用連接字串時，您應該一律使用 **name =** 語法。這可確保如果連接字串不存在，Entity Framework 將會擲回，而不是依照慣例建立新的資料庫。*

### <a name="model-classes"></a>模型類別

最後，也已將 **Blog** 和 **Post** 類別新增至專案。 這些是組成模型的網域類別。 您會看到套用至類別的資料批註來指定設定，其中 Code First 慣例不會與現有資料庫的結構一致。 例如，您會在**Blog.Name**和**Blog**上看到**StringLength**批註，因為它們在資料庫中的最大長度為**200** (Code First 預設值是使用資料庫提供者所支援的上限長度-Nvarchar (SQL Server) 中的**最大) ** 。

``` csharp
public partial class Blog
{
    public Blog()
    {
        Posts = new HashSet<Post>();
    }

    public int BlogId { get; set; }

    [StringLength(200)]
    public string Name { get; set; }

    [StringLength(200)]
    public string Url { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}
```

## <a name="4-reading--writing-data"></a>4. 讀取 & 寫入資料

現在我們已有模型，您可以使用它來存取某些資料。 在**Program.cs**中執行**Main**方法，如下所示。 這段程式碼會建立內容的新實例，然後使用它來插入新的 **Blog**。 然後，它會使用 LINQ 查詢來從資料庫中依**標題**的字母順序來取得所有的**blog** 。

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
.NET Framework Blog
ADO.NET Blog
The Visual Studio Blog
Press any key to exit...
```
 
## <a name="what-if-my-database-changes"></a>如果我的資料庫有所變更，該怎麼辦？

[資料庫的 Code First wizard] 是設計用來產生類別的起始點集合，然後您可以進行調整和修改。 如果您的資料庫架構變更，您可以手動編輯類別，或執行另一個反向工程來覆寫類別。

## <a name="using-code-first-migrations-to-an-existing-database"></a>使用現有資料庫的 Code First 移轉

如果您想要搭配現有的資料庫使用 Code First 移轉，請參閱 [Code First 移轉至現有的資料庫](xref:ef6/modeling/code-first/migrations/existing-database)。

## <a name="summary"></a>摘要

在這個逐步解說中，我們探討了如何使用現有的資料庫進行 Code First 開發。 我們使用了 Visual Studio 的 Entity Framework Tools，針對對應至資料庫的一組類別進行反向工程，並可用來儲存和取出資料。
