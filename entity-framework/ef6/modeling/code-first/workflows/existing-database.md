---
title: 現有的資料庫-EF6 的 code First
author: divega
ms.date: 2016-10-23
ms.assetid: a7e60b74-973d-4480-868f-500a3899932e
ms.openlocfilehash: 29f959265e0fd0d5e14c156519e6931fd8da0677
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995086"
---
# <a name="code-first-to-an-existing-database"></a>Code First 至現有的資料庫
本影片以及逐步說明的逐步解說提供以現有的資料庫為目標的 Code First 開發的簡介。 程式碼第一次可讓您定義您的模型使用 C\#或 VB.Net 類別。 （選擇性） 其他設定可以使用屬性，在您的類別和屬性，或使用 fluent API 來執行。

## <a name="watch-the-video"></a>觀看影片
這段影片[現已在 Channel 9 上](http://channel9.msdn.com/blogs/ef/code-first-to-existing-database-ef6-1-onwards-)。

## <a name="pre-requisites"></a>必要條件

您必須擁有**Visual Studio 2012**或是**Visual Studio 2013**安裝才能完成此逐步解說。

您也需要新版**6.1** （或更新版本） 的**Entity Framework Tools for Visual Studio**安裝。 請參閱[取得 Entity Framework](~/ef6/fundamentals/install.md)如需安裝最新版的 Entity Framework 工具的詳細資訊。

## <a name="1-create-an-existing-database"></a>1.建立現有的資料庫

通常當您的目標將已建立，現有的資料庫，但在此逐步解說，我們需要建立可存取的資料庫。

接下來產生的資料庫。

-   開啟 Visual Studio
-   **檢視-&gt;伺服器總管**
-   以滑鼠右鍵按一下**資料連線-&gt;新增連接...**
-   如果您尚未連線至資料庫，以從**伺服器總管**您必須選取才能**Microsoft SQL Server**做為資料來源

    ![SelectDataSource](~/ef6/media/selectdatasource.png)

-   連線到您的 LocalDB 執行個體，然後輸入**部落格**做為資料庫名稱

    ![LocalDBConnection](~/ef6/media/localdbconnection.png)

-   選取  **確定**而且會要求您想要建立新資料庫，請選取**是**

    ![CreateDatabaseDialog](~/ef6/media/createdatabasedialog.png)

-   新資料庫現在會出現在 [伺服器總管] 中，按一下滑鼠右鍵，然後選取**新查詢**
-   將下列 SQL 複製到新的查詢，然後以滑鼠右鍵按一下查詢並選取**Execute**

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

## <a name="2-create-the-application"></a>2.建立應用程式

為了簡單起見，我們要建置基本的主控台應用程式，會使用 Code First 來執行資料存取：

-   開啟 Visual Studio
-   **檔案-&gt;新增-&gt;專案...**
-   選取  **Windows**從左側的功能表和**主控台應用程式**
-   請輸入**CodeFirstExistingDatabaseSample**做為名稱
-   選取 [確定]

 

## <a name="3-reverse-engineer-model"></a>3.反向工程模型

我們將利用 Entity Framework Tools for Visual Studio 可協助我們產生一些初始的程式碼，以對應至資料庫。 這些工具只會產生您也可以輸入以手動方式視您的程式碼。

-   **專案-&gt;加入新項目...**
-   選取 **資料**從左側功能表，然後**ADO.NET 實體資料模型**
-   請輸入**為 [bloggingcontext]** 作為名稱，然後按一下 **[確定]**
-   這會啟動**Entity Data Model 精靈**
-   選取  **Code First 從資料庫**，按一下 **下一步**

    ![WizardOneCFE](~/ef6/media/wizardonecfe.png)

-   選取您在第一節中建立資料庫的連接，然後按一下**下一步**

    ![WizardTwoCFE](~/ef6/media/wizardtwocfe.png)

-   按一下核取方塊旁**資料表**匯入的所有資料表，並按一下**完成**

    ![WizardThreeCFE](~/ef6/media/wizardthreecfe.png)

反向工程程序完成的項目數目將已加入至專案，讓我們看一下什麼已加入。

### <a name="configuration-file"></a>組態檔

App.config 檔案已新增至專案，此檔案包含現有資料庫的連接字串。

``` xml
<connectionStrings>
  <add  
    name="BloggingContext"  
    connectionString="data source=(localdb)\mssqllocaldb;initial catalog=Blogging;integrated security=True;MultipleActiveResultSets=True;App=EntityFramework"  
    providerName="System.Data.SqlClient" />
</connectionStrings>
```

*您也會發現其他某些設定組態檔中的，這些是預設 EF 設定會告訴程式碼第一次建立資料庫的位置。因為我們在我們的應用程式中對應至現有的資料庫將會忽略這些設定。*

### <a name="derived-context"></a>在衍生的內容

A**為 [bloggingcontext]** 類別已新增至專案。 內容代表資料庫中，讓我們可以查詢和儲存資料的工作階段。
內容會公開**DbSet&lt;TEntity&gt;** 我們的模型中每個類型。 您也會注意到預設的建構函式呼叫基底建構函式，使用**名稱 =** 語法。 這會告訴程式碼第一個要用於此內容的連接字串應該從組態檔中載入。

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

*您應該一律使用**名稱 =** 當您在組態檔中使用的連接字串的語法。這可確保，如果連接字串不存在則 Entity Framework 將會擲回而非依照慣例建立新的資料庫。*

### <a name="model-classes"></a>模型類別

最後，**部落格**並**Post**類別也已新增至專案。 這些是我們的模型所組成的網域類別。 您會看到資料註釋套用至類別，來指定組態，Code First 慣例會不符合現有資料庫的結構。 例如，您會看到**StringLength**上的註釋**Blog.Name**並**Blog.Url**因為它們有最大長度為**200**中資料庫 (第一個程式碼的預設值是使用資料庫提供者-支援的上限長度**nvarchar （max)** SQL Server 中)。

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

## <a name="4-reading--writing-data"></a>4.讀取和寫入資料

現在，我們就可以開始使用它來存取部分資料的模型。 實作**Main**方法中的**Program.cs** ，如下所示。 這段程式碼會建立我們的內容的新執行個體，然後用它來插入新**部落格**。 然後，使用 LINQ 查詢來擷取所有**部落格**從資料庫依字母順序排序**標題**。

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
.NET Framework Blog
ADO.NET Blog
The Visual Studio Blog
Press any key to exit...
```
 
## <a name="what-if-my-database-changes"></a>如果我的資料庫變更嗎？

Code First 資料庫精靈 被設計來產生起始點的一組類別，您可以稍後調整和修改。 如果您的資料庫結構描述變更您可以手動編輯類別或執行另一個反向工程，以覆寫的類別。

## <a name="using-code-first-migrations-to-an-existing-database"></a>使用 Code First 移轉到現有的資料庫

如果您想要使用現有的資料庫中的 Code First 移轉，請參閱[Code First 移轉到現有的資料庫](~/ef6/modeling/code-first/migrations/existing-database.md)。

## <a name="summary"></a>總結

在此逐步解說中，我們討論過使用現有資料庫的 Code First 開發。 我們會使用 Entity Framework Tools for Visual Studio 進行反向工程的一組對應到資料庫，而且可用來儲存和擷取資料的類別。
